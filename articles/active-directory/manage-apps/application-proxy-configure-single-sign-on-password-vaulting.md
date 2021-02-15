---
title: 使用 Azure AD 应用程序代理进行应用的单一登录 | Microsoft 文档
description: 在 Azure 门户中使用 Azure AD 应用程序代理为已发布的本地应用程序启用单一登录。
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/12/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0cb2830c019635e9020a4b586bdc370450fddb0
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99253996"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>使用应用程序代理通过密码存储进行单一登录

Azure Active Directory 应用程序代理还可以发布本地应用程序供远程员工进行安全访问，从而提高工作效率。 在 Azure 门户中，还可以设置针对这些应用的单一登录 (SSO)。 用户只需使用 Azure AD 进行身份验证，无需再次登录便可访问企业应用程序。

应用程序代理支持多种[单一登录模式](sso-options.md#choosing-a-single-sign-on-method)。 基于密码的登录适合使用用户名/密码组合进行身份验证的应用程序。 当你为应用程序配置基于密码的登录时，用户必须登录到本地应用程序一次。 之后，Azure Active Directory 会存储登录信息，并在用户远程访问应用程序时自动向应用程序提供该信息。

应该已经通过应用程序代理发布和测试了应用。 否则，请执行[使用 Azure AD 应用程序代理发布应用程序](application-proxy-add-on-premises-application.md)中的步骤，再返回到此处。

## <a name="set-up-password-vaulting-for-your-application"></a>为应用程序设置密码存储

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 选择 " **Azure Active Directory**  >  **企业应用程序**" "  >  **所有应用程序**"。
1. 从列表中选择要设置 SSO 的应用。  
1. 选择“应用程序代理”。 
1. 将 " **预身份验证类型** " 更改为 " **Passthrough** " 并选择 " **保存**"。 稍后，你可以再次切换回 **Azure Active Directory** 类型！ 
1. 选择“单一登录”。

   ![在应用的 "概述" 页中选择 "单一登录"](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

1. 对于 SSO 模式，请选择“基于密码的登录”。
1. 对于登录 URL，请输入用户在公司网络外登录应用时在其中输入用户名和密码的页面的 URL。 该 URL 可能是通过应用程序代理发布应用时创建的外部 URL。

   ![选择“基于密码的登录”并输入 URL](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

1. 选择“保存”。
1. 选择“应用程序代理”。 
1. 将 " **预身份验证类型** " 更改为 " **Azure Active Directory** "，然后选择 " **保存**"。 
1. 选择 " **用户和组**"。
1. 选择 " **添加用户**"，将用户分配到应用程序。 
1. 若要预定义用户的凭据，请选中 "用户名" 旁边的框，然后选择 " **更新凭据**"。
1. 选择 " **Azure Active Directory**  >  **应用注册**  >  **所有应用程序**"。
1. 从列表中，选择你配置了密码 SSO 的应用。
1. 选择“品牌”  。 
1. 从 "密码 SSO" 页上的 "**登录 url** " 更新 **主页 url** ，然后选择 "**保存**"。  



<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>测试应用

中转到 "我的应用" 门户。 使用你的凭据登录 (或使用 access) 设置的测试帐户的凭据。 成功登录后，单击该应用的图标。 这可能会触发 "我的应用" 安全登录浏览器扩展的安装。 如果用户预定义了凭据，则应自动对应用进行身份验证，否则你必须首次指定用户名或密码。 

## <a name="next-steps"></a>后续步骤

- 了解如何通过其他方式实现[单一登录](what-is-single-sign-on.md)
- 了解[使用 Azure AD 应用程序代理远程访问应用时的安全注意事项](application-proxy-security.md)
