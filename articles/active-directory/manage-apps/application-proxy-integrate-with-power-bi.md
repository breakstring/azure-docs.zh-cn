---
title: 使用 Azure AD 应用程序代理启用对 Power BI 的远程访问
description: 介绍有关如何将本地 Power BI 与 Azure AD 应用程序代理集成的基础知识。
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/25/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9f0959ff51714e8a7e69f58a3e3abeab71e8359
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259366"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>使用 Azure AD 应用程序代理启用对 Power BI 移动版的远程访问

本文介绍如何使用 Azure AD 应用程序代理来启用 Power BI 移动应用程序连接到 Power BI 报表服务器 (PBIRS) 并 SQL Server Reporting Services (SSRS) 2016 和更高版本。 通过这种集成，远离企业网络的用户可以从 Power BI 移动应用程序访问其 Power BI 报表，并通过 Azure AD 身份验证进行保护。 此保护包括条件访问和多重身份验证等 [安全权益](application-proxy-security.md#security-benefits) 。

## <a name="prerequisites"></a>必备条件

本文假定你已部署了报表服务和 [应用程序代理](application-proxy-add-on-premises-application.md)。

- 启用应用程序代理需要在 Windows server 上安装连接器并完成 [必备组件](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) ，使连接器能够与 Azure AD 服务通信。
- 发布 Power BI 时，建议使用相同的内部和外部域。 若要了解有关自定义域的详细信息，请参阅 [在应用程序代理中使用自定义域](./application-proxy-configure-custom-domain.md)。
- 此集成可用于 **Power BI 移动版 iOS 和 Android** 应用程序。

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>步骤1：配置 Kerberos 约束委派 (KCD) 

对于使用 Windows 身份验证的本地应用程序，可以通过 Kerberos 身份验证协议以及一项称为 Kerberos 约束委派 (KCD) 的功能来实现单一登录 SSO。 配置后，KCD 允许应用程序代理连接器获取用户的 Windows 令牌，即使该用户尚未直接登录到 Windows 也是如此。 若要了解有关 KCD 的详细信息，请参阅 [Kerberos 约束委派概述](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)) 和 [kerberos 约束委派，以便通过应用程序代理对应用进行单一登录](application-proxy-configure-single-sign-on-with-kcd.md)。

Reporting Services 端无其他需要配置的内容。 只需确保具有有效的服务主体名称 (SPN) ，才能进行正确的 Kerberos 身份验证。 另外，请确保为协商身份验证启用了 Reporting Services 服务器。

若要为 Reporting services 设置 KCD，请继续执行以下步骤。

### <a name="configure-the-service-principal-name-spn"></a>将服务主体名称配置 (SPN) 

SPN 是使用 Kerberos 身份验证的服务的唯一标识符。 需要确保为 Report Server 提供了正确的 HTTP SPN。 有关如何为报表服务器配置正确的服务主体名称 (SPN) 的信息，请参阅[为报表服务器注册服务主体名称 (SPN)](/sql/reporting-services/report-server/register-a-service-principal-name-spn-for-a-report-server)。
可以结合 -L 选项运行 Setspn 命令，来验证是否已添加 SPN。 若要详细了解该命令，请参阅 [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx)。

### <a name="enable-negotiate-authentication"></a>启用协商身份验证

若要使 Report Server 可以使用 Kerberos 身份验证，请将 Report Server 的身份验证类型配置为 RSWindowsNegotiate。 使用 rsreportserver.config 文件配置此设置。

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

有关详细信息，请参阅[修改 Reporting Services 配置文件](/sql/reporting-services/report-server/modify-a-reporting-services-configuration-file-rsreportserver-config)和[在报表服务器上配置 Windows 身份验证](/sql/reporting-services/security/configure-windows-authentication-on-the-report-server)。

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>确保连接器受信任，可以委派添加到 Reporting Services 应用程序池帐户的 SPN
配置 KCD，以便 Azure AD 应用程序代理服务可将用户标识委派到 Reporting Services 应用程序池帐户。 可通过让应用程序代理连接器检索已在 Azure AD 中进行身份验证的用户的 Kerberos 票证，配置 KCD。 然后，该服务器会将上下文传递给目标应用程序，或在这种情况下 Reporting Services。

若要配置 KCD，请针对每个连接器计算机重复以下步骤：

1. 以域管理员身份登录到域控制器，然后打开 **Active Directory 用户和计算机**"。
2. 找到运行连接器的计算机。
3. 双击此计算机，然后选择“委派”选项卡。
4. 将委派设置设为“信任此计算机仅委派指定服务”。 然后，选择 " **使用任何身份验证协议**"。
5. 依次选择“添加”和“用户或计算机”。
6. 输入 Reporting Services 所用的服务帐户。 这是在 Reporting Services 配置中将 SPN 添加到其中的帐户。
7. 单击“确定”。 若要保存更改，请再次单击“确定”。

有关详细信息，请参阅 [通过应用程序代理对应用进行单一登录的 Kerberos 约束委派](application-proxy-configure-single-sign-on-with-kcd.md)。

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>步骤2：通过 Azure AD 应用程序代理发布报表服务

现在可以配置 Azure AD 应用程序代理了。

1. 使用以下设置通过应用程序代理发布报表服务。 有关如何通过应用程序代理发布应用程序的分步说明，请参阅[使用 Azure AD 应用程序代理发布应用程序](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。
   - **内部 URL**：输入连接器可在企业网络中访问的报表服务器的 URL。 请确保此 URL 是可以从连接器安装所在的服务器进行访问的。 最佳做法是使用一级域名（如 `https://servername/`），以免通过应用程序代理发布的子路径出现问题。 例如，使用 `https://servername/`，而不是 `https://servername/reports/` 或 `https://servername/reportserver/`。
     > [!NOTE]
     > 建议使用与报表服务器的安全 HTTPS 连接。 若要详细了解操作说明，请参阅[在本机模式报表服务器上配置 SSL 连接](/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017)。
   - **外部 url**：输入 Power BI 移动应用程序将连接到的公共 url。 例如，如果使用的是自定义域，外部 URL 可能为 `https://reports.contoso.com`。 若要使用自定义域，请上传域证书，然后将 DNS 记录指向应用程序的默认 msappproxy.net 域。 有关详细步骤，请参阅[在 Azure AD 应用程序代理中使用自定义域](application-proxy-configure-custom-domain.md)。

   - **预身份验证方法**： Azure Active Directory

2. 发布应用后，请执行以下步骤来配置单一登录设置：

   a. 在门户中的应用程序页上，选择“单一登录”。

   b. 对于“单一登录模式”，选择“集成 Windows 身份验证”。

   c. 将“内部应用程序 SPN”设置为此前设置的值。

   d. 针对你要代表你的用户使用的连接器选择“委派的登录标识”。 有关详细信息，请参阅[使用不同的本地标识和云标识](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)。

   e.  以保存更改。

若要完成应用程序的设置，请访问 **"用户和组"** 部分，并分配用户以访问此应用程序。

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>步骤3：修改应用程序的回复 URI

在 Power BI 移动应用可以连接和访问报表服务之前，您必须配置在步骤2中为您自动创建的应用程序注册。

1. 在 Azure Active Directory 的“概览”页中，选择“应用注册”。
2. 在 " **所有应用程序** " 选项卡下，搜索在步骤2中创建的应用程序。
3. 依次选择应用程序和“身份验证”。
4. 根据所使用的平台，添加以下重定向 URI。

   为 Power BI 移动版 **iOS** 配置应用时，请将以下类型的重定向 uri 添加 (移动 & 桌面) ：
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`

   为 Power BI 移动版 **Android** 配置应用时，请将以下类型的重定向 uri 添加 (移动 & 桌面) ：
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D`
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > 必须添加重定向 URI，这样应用程序才能正常运行。 如果要同时为 Power BI 移动版 iOS 和 Android 配置应用，请将以下类型的重定向 URI 添加 (移动 & 桌面) ，并将其添加到为 iOS 配置的重定向 Uri 的列表： `urn:ietf:wg:oauth:2.0:oob` 。

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>步骤4：从 Power BI 移动版应用进行连接

1. 在 Power BI 移动应用中，连接到 Reporting Services 实例。 为此，请输入通过应用程序代理发布的应用程序的 **外部 URL** 。

   ![带外部 URL 的 Power BI 移动应用](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. 选择“连接”。 你将被定向到 Azure Active Directory 登录页。

3. 输入用户的有效凭据，然后选择“登录”。 你将看到 Reporting Services 服务器中的元素。

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>步骤5：为托管设备配置 Intune 策略 (可选) 

你可以使用 Microsoft Intune 来管理公司员工使用的客户端应用。 Intune 允许你使用数据加密等功能和其他访问要求。 若要详细了解如何通过 Intune 进行应用管理，请参阅 Intune 应用管理。 若要使 Power BI 移动应用程序能够使用 Intune 策略，请执行以下步骤。

1. 依次执行 " **Azure Active Directory** " 和 " **应用注册**"。
2. 注册本机客户端应用程序时，选择在步骤3中配置的应用程序。
3. 在应用程序页上，选择 " **API 权限**"。
4. 单击 " **添加权限**"。
5. 在 " **我的组织使用的 api**" 下，搜索 "Microsoft 移动应用程序管理" 并将其选中。
6. 向应用程序添加 **DeviceManagementManagedApps** 权限
7. 单击 " **授予管理员许可** " 以授予对应用程序的访问权限。
8. 请参阅 [如何创建和分配应用保护策略](/intune/app-protection-policies)来配置所需的 Intune 策略。

## <a name="troubleshooting"></a>疑难解答

如果应用程序在尝试加载超过几分钟的报表后返回错误页面，则可能需要更改超时设置。 默认情况下，应用程序代理支持长达85秒的应用程序来响应请求。 若要将此设置加长为180秒，请在应用程序的 "应用代理设置" **页中选择** "后端超时"。 有关如何创建快速可靠的报表的提示，请参阅 [Power BI 报表最佳实践](/power-bi/power-bi-reports-performance)。

使用 Azure AD 应用程序代理来启用 Power BI 移动应用程序连接到本地 Power BI 报表服务器，这种条件访问策略不支持将 Microsoft Power BI 应用作为批准的客户端应用。

## <a name="next-steps"></a>后续步骤

- [使 native client 应用程序与代理应用程序交互](application-proxy-configure-native-client-application.md)
- [在 Power BI 移动应用中查看本地报表服务器报表和 KPI](/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
