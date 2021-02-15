---
title: 使用应用程序库中未列出的应用程序的 Azure AD
description: 了解如何集成 Azure AD 库中未列出的应用。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/27/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 9721679938517e38f669f78ee0f5f9f3a80e05a7
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258263"
---
# <a name="using-azure-ad-for-applications-not-listed-in-the-app-gallery"></a>使用应用程序库中未列出的应用程序的 Azure AD

在 [添加应用](add-application-portal.md) 快速入门中，你将了解如何将应用添加到 Azure AD 租户。

除了 [Azure AD 应用程序库](../saas-apps/tutorial-list.md)中的选项，还可以选择添加“非库应用程序”。 

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Azure AD 库中未列出的应用程序的功能

可以添加已存在于组织中的任何应用程序或来自供应商的任何第三方应用程序（尚不在 Azure AD 库中）。 根据[许可协议](https://azure.microsoft.com/pricing/details/active-directory/)，可以使用以下功能：

- 通过自助方式集成支持[安全断言标记语言 (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0) 标识提供者的任何应用程序（SP 发起或 IdP 发起）
- 通过自助方式集成包含 HTML 登录页并使用[基于密码的 SSO](sso-options.md#password-based-sso) 的任何 Web 应用程序
- 以自助方式连接使用[跨域身份管理系统 (SCIM) 协议进行用户预配](../app-provisioning/use-scim-to-provision-users-and-groups.md)的应用程序
- 能够将链接添加到[Office 365 应用启动器](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/)或 "[我的应用](sso-options.md#linked-sign-on)" 中的任何应用程序

如果要了解有关如何将自定义应用与 Azure AD 集成的开发人员指南，请参阅 [Azure AD 的身份验证方案](../develop/authentication-vs-authorization.md)。 开发使用新式协议（如 [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md)）对用户进行身份验证的应用时，可以使用 Azure 门户中的[应用注册](../develop/quickstart-register-app.md)体验将应用注册到 Microsoft 标识平台。

## <a name="next-steps"></a>后续步骤

- [应用管理中的快速入门系列](view-applications-portal.md)