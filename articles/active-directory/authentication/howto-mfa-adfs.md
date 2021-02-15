---
title: Azure AD MFA 和 ADFS Azure Active Directory 保护资源
description: 这是 Azure AD 多重身份验证页面，介绍如何开始在云中 Azure AD MFA 和 AD FS。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b42f920726d4b3046ab0c292e1090f5217e8b1f9
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743235"
---
# <a name="securing-cloud-resources-with-azure-ad-multi-factor-authentication-and-ad-fs"></a>通过 Azure AD 多重身份验证和 AD FS 保护云资源

如果你的组织与 Azure Active Directory 联合，请使用 Azure AD 多重身份验证或 Active Directory 联合身份验证服务 (AD FS) 来保护通过 Azure AD 访问的资源。 使用以下过程可以通过 Azure AD 多重身份验证或 Active Directory 联合身份验证服务来保护 Azure Active Directory 资源。

## <a name="secure-azure-ad-resources-using-ad-fs"></a>使用 AD FS 保护 Azure AD 资源

若要保护云资源，请设置声明规则，以便在用户成功执行双重验证之后，Active Directory 联合身份验证服务能够发出多重身份验证声明。 此声明将传递到 Azure AD。 按照以下过程完成各步骤：

1. 打开“AD FS 管理”。
2. 在左侧选择“信赖方信任”。
3. 右键单击 **Microsoft Office 365 标识平台** "，然后选择" **编辑声明规则**"。

   ![ADFS 控制台-信赖方信任](./media/howto-mfa-adfs/trustedip1.png)

4. 在 "颁发转换规则" 上，单击 " **添加规则**"。

   ![编辑颁发转换规则](./media/howto-mfa-adfs/trustedip2.png)

5. 在“添加转换声明规则向导”上，从下拉列表中选择“传递或筛选传入声明”，并单击“下一步”。

   ![屏幕截图显示 "添加转换声明规则向导"，您可以在其中选择声明规则模板。](./media/howto-mfa-adfs/trustedip3.png)

6. 为规则提供一个名称。 
7. 选择“身份验证方法引用”作为传入声明类型。
8. 选择“传递所有声明值”。
    ![屏幕截图显示 "添加转换声明规则向导"，可在其中选择 "传递所有声明值"。](./media/howto-mfa-adfs/configurewizard.png)
9. 单击“完成”。 关闭 AD FS 管理控制台。

## <a name="trusted-ips-for-federated-users"></a>联合用户的受信任 IP

受信任的 IP 可让管理员针对特定的 IP 地址或针对从他们自己的 Intranet 发出请求的联合用户，跳过两步验证。 以下部分介绍如何在联合用户 intranet 内发出请求时，如何配置联合用户 Azure AD 多重身份验证受信任的 Ip 以及通过双重验证进行双重验证。 这是通过将 AD FS 配置为使用“传递或筛选传入声明”模板与“公司网络内部”声明类别来实现的。

此示例使用用于我们的信赖方信任的 Microsoft 365。

### <a name="configure-the-ad-fs-claims-rules"></a>配置 AD FS 声明规则

我们要做的第一件事是配置 AD FS 声明。 创建两个声明规则，一个用于“公司网络内部”声明类型，另一个用于保持用户登录。

1. 打开“AD FS 管理”。
2. 在左侧选择“信赖方信任”。
3. 右键单击 **Microsoft Office 365 标识平台**"，然后选择"**编辑声明规则 ... "** 
    ![ADFS 控制台-编辑声明规则](./media/howto-mfa-adfs/trustedip1.png)
4. 在 "颁发转换规则" 上，单击 "**添加规则"。** 
    ![添加声明规则](./media/howto-mfa-adfs/trustedip2.png)
5. 在“添加转换声明规则向导”上，从下拉列表中选择“传递或筛选传入声明”，并单击“下一步”。
   ![屏幕截图显示 "添加转换声明规则向导"，可在其中选择 "通过" 或 "筛选传入声明"。](./media/howto-mfa-adfs/trustedip3.png)
6. 在“声明规则名称”旁边的框中，为规则指定名称。 例如：InsideCorpNet。
7. 从“传入声明类型”旁边的下拉列表中，选择“公司网络内部”。
   ![添加内部公司网络声明](./media/howto-mfa-adfs/trustedip4.png)
8. 单击“完成”。
9. 在 "颁发转换规则" 上，单击 " **添加规则**"。
10. 在“添加转换声明规则向导”上，从下拉列表中选择“使用自定义规则发送声明”，并单击“下一步”。
11. 在“声明规则名称”下的框中：输入“保持用户登录状态”。
12. 在“自定义规则”框中，输入：

```ad-fs-claim-rule
        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
    ![Create custom claim to keep users signed in](./media/howto-mfa-adfs/trustedip5.png)
```

13. 单击“完成”。
14. 单击“应用” 。
15. 单击“确定”  。
16. 关闭“AD FS 管理”。

### <a name="configure-azure-ad-multi-factor-authentication-trusted-ips-with-federated-users"></a>为联合用户配置 Azure AD 多重身份验证受信任的 Ip

创建声明后，可以开始配置受信任的 IP。

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 选择 **Azure Active Directory**  >  **安全**  >  **条件性访问**  >  **命名位置**"。
3. 在 "**条件访问-命名位置**" 边栏选项卡中，选择 "**配置 MFA 受信任的 ip** "

   ![Azure AD 条件访问命名位置配置 MFA 受信任的 Ip](./media/howto-mfa-adfs/trustedip6.png)

4. 在“服务设置”页的“受信任的 IP”下，选择“跳过对于 Intranet 上联合用户的请求的多重身份验证”。  
5. 单击 " **保存**"。

就这么简单！ 此时，联合 Microsoft 365 用户只应在来自公司 intranet 外部的声明时使用 MFA。
