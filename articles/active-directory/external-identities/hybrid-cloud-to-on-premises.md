---
title: 向 B2B 用户授予对本地应用的访问权限-Azure AD
description: 演示如何使用 Azure AD B2B 协作为云 B2B 用户提供对本地应用的访问权限。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 10/30/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd91d1d2c9f5a4a413f9ea64cfdef649823d0f09
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131014"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>向 Azure AD 中的 B2B 用户授予对本地应用程序的访问权限

使用 Azure Active Directory (Azure AD) B2B 协作功能邀请合作伙伴组织中的来宾用户加入 Azure AD 的组织现在可以向这些 B2B 用户提供本地应用的访问权限。 这些本地应用可以结合 Kerberos 约束委派 (KCD) 使用基于 SAML 的身份验证或 Windows 集成身份验证 (IWA)。

## <a name="access-to-saml-apps"></a>对 SAML 应用的访问权限

如果本地应用使用基于 SAML 的身份验证，则你可以通过 Azure 门户轻松向 Azure AD B2B 协作用户提供这些应用。

必须执行以下两个操作：

- 按照 [配置基于 saml 的单一登录](../manage-apps/configure-saml-single-sign-on.md)中所述，使用 SAML 来集成应用。 请务必记下所用的“登录 URL”值。 
-  在将 **Azure Active Directory** 配置为身份验证源的情况下，使用 Azure AD 应用程序代理发布本地应用。 有关说明，请参阅[使用 Azure AD 应用程序代理发布应用程序](../manage-apps/application-proxy-add-on-premises-application.md)。 

   配置“内部 URL”设置时，请使用非库应用程序模板中指定的登录 URL。  这样，用户便可以从组织边界以外访问该应用。 应用程序代理对本地应用执行 SAML 单一登录。
 
   ![显示本地应用设置 - 内部 URL 和身份验证](media/hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>访问 IWA 和 KCD 应用

若要向 B2B 用户提供对 Windows 集成身份验证和 Kerberos 约束委派保护的本地应用程序的访问权限，需要以下组件：

- **通过 Azure AD 应用程序代理的身份验证** 。 B2B 用户必须能够在本地应用程序中进行身份验证。 为此，必须通过 Azure AD 应用程序代理发布本地应用。 有关详细信息，请参阅 [教程：通过应用程序代理添加用于远程访问的本地应用程序](../manage-apps/application-proxy-add-on-premises-application.md)。
- **通过本地目录中的 B2B 用户对象授权** 。 应用程序必须能够执行用户访问权限检查，并授予对正确资源的访问权限。 IWA 和 KCD 要求本地 Windows Server Active Directory 中有一个用户对象才能完成此授权。 根据 [KCD 的单一登录工作原理](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works)中所述，应用程序代理需要使用此用户对象来模拟用户并获取应用程序的 Kerberos 令牌。 

   > [!NOTE]
   > 配置 Azure AD 应用程序代理时，请确保已将 " **委派的登录标识** " 设置为 "Windows 集成身份验证的单一登录配置" 中的 " **用户主体名称** (默认)  (IWA) 。

   对于 B2B 用户方案，可以使用两种方法在本地目录中创建授权所需的来宾用户对象：

   - Microsoft Identity Manager (MIM) 和 Microsoft Graph 的 MIM 管理代理。 
   - [PowerShell 脚本](#create-b2b-guest-user-objects-through-a-script-preview)。 使用脚本是一种更轻便的解决方案，因为不需要 MIM。 

下图从较高层面描绘了 Azure AD 应用程序代理如何结合本地目录中生成的 B2B 用户对象，向 B2B 用户授予对本地 IWA 和 KCD 应用的访问权限。 下图详细描述了带编号的步骤。

![MIM 和 B2B 脚本解决方案示意图](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  合作伙伴组织（Fabrikam 租户）中的用户受邀加入 Contoso 租户。
2.  在 Contoso 租户中创建来宾用户对象（例如，UPN 为 guest_fabrikam.com#EXT#@contoso.onmicrosoft.com 的用户对象）。
3.  通过 MIM 或 B2B PowerShell 脚本从 Contoso 导入 Fabrikam 来宾。
4.  通过 MIM 或 B2B PowerShell 脚本在本地目录 Contoso.com 中创建 Fabrikam 来宾用户对象 (Guest#EXT#) 的表示形式或“足迹”。
5.  来宾用户访问本地应用程序 app.contoso.com。
6.  使用 Kerberos 约束委派通过应用程序代理对身份验证请求授权。 
7.  由于本地存在来宾用户对象，因此身份验证成功。

### <a name="lifecycle-management-policies"></a>生命周期管理策略

可以通过生命周期管理策略管理本地 B2B 用户对象。 例如：

- 可以针对来宾用户设置多重身份验证 (MFA) 策略，以便在应用程序代理身份验证期间使用 MFA。 有关详细信息，请参阅 [B2B 协作用户的条件访问](conditional-access.md)。
- 针对云 B2B 用户执行的任何赞助、访问评审、帐户验证等操作将应用到本地用户。 例如，如果通过生命周期管理策略删除了云用户，则也可以通过 MIM 同步或 Azure AD Connect 同步删除本地用户。有关详细信息，请参阅 [使用 Azure AD 访问评审管理来宾访问权限](../governance/manage-guest-access-with-access-reviews.md)。

### <a name="create-b2b-guest-user-objects-through-mim"></a>通过 MIM 创建 B2B 来宾用户对象

有关如何使用 MIM 2016 Service Pack 1 和 Microsoft Graph 的 MIM 管理代理在本地目录中创建来宾用户对象的信息，请参阅[使用 Microsoft Identity Manager (MIM) 2016 SP1 和 Azure 应用程序代理实现 Azure AD 企业到企业 (B2B) 协作](/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario)。

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>通过脚本创建 B2B 来宾用户对象（预览版）

可以从一个 PowerShell 示例脚本着手，在本地 Active Directory 中创建来宾用户对象。

可以从 [适用于 Microsoft Identity Manager 2016 和 Forefront Identity Manager 2010 R2 的连接器](https://www.microsoft.com/download/details.aspx?id=51495)下载脚本和自述文件。 在下载包中，选择 **要拉取 AZURE AD B2B 用户 on-prem.zip文件的脚本和自述** 文件。

使用该脚本之前，请确保查看相关自述文件中的先决条件和重要注意事项。 此外，请知道该脚本仅用作示例。 开发团队或合作伙伴在运行该脚本之前，必须对其进行自定义和审阅。

## <a name="license-considerations"></a>许可证注意事项

请确保获取有权访问本地应用的外部来宾用户的正确客户端访问许可证 (CAL)。 有关详细信息，请参阅[客户端访问许可证和管理许可证](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx)的“外部连接器”部分。 有关具体的许可需求，请咨询 Microsoft 代表或当地经销商。

## <a name="next-steps"></a>后续步骤

- [适用于混合组织的 Azure Active Directory B2B 协作](hybrid-organizations.md)

- 有关 Azure AD Connect 的概述，请参阅[将本地目录与 Azure Active Directory 进行集成](../hybrid/whatis-hybrid-identity.md)。