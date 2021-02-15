---
title: 使用 Azure Active Directory B2C 为开发人员帐户授权
titleSuffix: Azure API Management
description: 了解如何在 API 管理中使用 Azure Active Directory B2C 为用户授权。
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 7b586edd7adce8bcea61419005a3ce8cfc814fb3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013528"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>如何在 Azure API 管理中使用 Azure Active Directory B2C 为开发人员帐户授权

## <a name="overview"></a>概述

Azure Active Directory B2C 是一种云标识管理解决方案，适用于面向使用者的 Web 和移动应用程序。 可以使用它来管理对开发人员门户的访问。 本指南说明为了与 Azure Active Directory B2C 集成，需要在 API 管理服务中完成哪些配置。 有关使用经典 Azure Active Directory 启用对开发人员门户的访问的信息，请参阅[如何使用 Azure Active Directory 为开发人员帐户授权]。

> [!NOTE]
> 若要完成本指南中的步骤，必须先获取一个可在其中创建应用程序的 Azure Active Directory B2C 租户。 此外，需要准备好注册和登录策略。 有关详细信息，请参阅 [Azure Active Directory B2C 概述]。

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 为开发人员帐户授权

1. 若要开始，请登录到 [Azure 门户](https://portal.azure.com)并找到你的 API 管理实例。

   > [!NOTE]
   > 如果尚未创建 API 管理服务实例，请参阅 [Azure API 管理入门教程][Get started with Azure API Management]中的[创建 API 管理服务实例][Create an API Management service instance]。

1. 在“标识”下。 单击顶部的“+添加”。

   此时将在右侧显示“添加标识提供者”窗格。 选择“Azure Active Directory B2C”。
    
   ![将 AAD B2C 添加为标识提供者][api-management-howto-add-b2c-identity-provider]

1. 复制 **重定向 URL**。

   ![AAD B2C 标识提供者重定向 URL][api-management-howto-copy-b2c-identity-provider-redirect-url]

1. 在一个新选项卡中，在 Azure 门户中访问你的 Azure Active Directory B2C 租户并打开“应用程序”边栏选项卡。

   ![注册新应用程序 1][api-management-howto-aad-b2c-portal-menu]

1. 单击“添加”按钮创建新的 Azure Active Directory B2C 应用程序。

   ![注册新应用程序 2][api-management-howto-aad-b2c-add-button]

1. 在“新建应用程序”边栏选项卡中，输入应用程序的名称。 在“Web 应用/Web API”下面选择“是”，在“允许隐式流”下面选择“是”。    然后，将在步骤 3 中复制的 **重定向 URL** 粘贴到“回复 URL”文本框中。

   ![注册新应用程序 3][api-management-howto-aad-b2c-app-details]

1. 如果使用的是新开发人员门户（而不是旧的开发人员门户），请在应用程序声明中包含“给定名称”、“姓氏”和“用户的对象 ID”  。

    ![应用程序声明](./media/api-management-howto-aad-b2c/api-management-application-claims.png)

1. 单击“创建”  按钮。 创建应用程序后，它会显示在“应用程序”边栏选项卡。 单击应用程序的名称可查看其详细信息。

   ![注册新应用程序 4][api-management-howto-aad-b2c-app-created]

1. 在“属性”边栏选项卡中，将“应用程序 ID”复制到剪贴板。

   ![应用程序 ID 1][api-management-howto-aad-b2c-app-id]

1. 切换回 API 管理“添加标识提供者”窗格并将 ID 粘贴到“客户端 ID”文本框中。
    
1.  切换回 B2C 应用注册，单击“密钥”按钮，并单击“生成密钥”。 单击“保存”保存配置并显示“应用密钥”。 将该密钥复制到剪贴板。

    ![应用密钥 1][api-management-howto-aad-b2c-app-key]

1.  切换回 API 管理“添加标识提供者”窗格并将密钥粘贴到“客户端机密”文本框中。
    
1.  在“登录租户”中指定 Azure Active Directory B2C 租户的域名。

1.  使用“机构/授权”字段可以控制要使用的 Azure AD B2C 登录 URL。 将值设置为“<your_b2c_tenant_name>.b2clogin.com”。

1. 从 B2C 租户策略中指定 **注册策略** 和 **登录策略**。 或者，也可以提供“配置文件编辑策略”和“密码重置策略”。

1. 指定所需的配置后，单击“保存”。

    保存更改后，开发人员可以使用 Azure Active Directory B2C 创建新帐户并登录到开发人员门户。

## <a name="developer-portal---add-azure-ad-b2c-account-authentication"></a>开发人员门户 - 添加 Azure AD B2C 帐户身份验证

在开发人员门户中，可以使用小组件“登录按钮：OAuth”通过 AAD B2C 登录。 此小组件已包括在默认开发人员门户内容的登录页上。

尽管每当新用户使用 AAD B2C 登录时都会自动创建一个新帐户，但你可以考虑向注册页添加同一小组件。

“注册表单：OAuth”小组件表示用于注册 OAuth 的表单。

> [!IMPORTANT]
> 需要[重新发布门户](api-management-howto-developer-portal-customize.md#publish)才能使 AAD 更改生效。

## <a name="legacy-developer-portal---how-to-sign-up-with-azure-ad-b2c"></a>旧开发人员门户 - 如何使用 Azure AD B2C 注册

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

1. 若要使用 Azure Active Directory B2C 注册开发人员帐户，请打开新的浏览器窗口并转到开发人员门户。 单击“注册”按钮。

   ![开发人员门户 1][api-management-howto-aad-b2c-dev-portal]

2. 选择使用 **Azure Active Directory B2C** 注册。

   ![开发人员门户 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. 将重定向到在上一部分中配置的注册策略。 选择使用电子邮件地址或现有的某个社交帐户注册。

   > [!NOTE]
   > 如果“Azure Active Directory B2C”是发布者门户上“标识”选项卡中启用的唯一选项，将直接重定向到注册策略。

   ![开发人员门户][api-management-howto-aad-b2c-dev-portal-b2c-options]

   注册完成后，将重定向回到开发人员门户。 现已登录到 API 管理服务实例的开发人员门户。

    ![注册完成][api-management-registration-complete]

## <a name="next-steps"></a>后续步骤

*  [Azure Active Directory B2C 概述]
*  [Azure Active Directory B2C：可扩展的策略框架]
*  [使用 Microsoft 帐户作为标识提供者 Azure Active Directory B2C]
*  [在 Azure Active Directory B2C 中使用 Google 帐户作为标识提供者]
*  [在 Azure Active Directory B2C 中使用 LinkedIn 帐户作为标识提供者]
*  [在 Azure Active Directory B2C 中使用 Facebook 帐户作为标识提供者]



[api-management-howto-add-b2c-identity-provider]: ./media/api-management-howto-aad-b2c/api-management-add-b2c-identity-provider.PNG
[api-management-howto-copy-b2c-identity-provider-redirect-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-identity-provider-redirect-url.PNG
[api-management-howto-aad-b2c-portal-menu]: ./media/api-management-howto-aad-b2c/api-management-b2c-portal-menu.PNG
[api-management-howto-aad-b2c-add-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-add-button.PNG
[api-management-howto-aad-b2c-app-details]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-details.PNG
[api-management-howto-aad-b2c-app-created]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-created.PNG
[api-management-howto-aad-b2c-app-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-id.PNG
[api-management-howto-aad-b2c-client-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-id.PNG
[api-management-howto-aad-b2c-app-key]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key.PNG
[api-management-howto-aad-b2c-app-key-saved]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key-saved.PNG
[api-management-howto-aad-b2c-client-secret]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-secret.PNG
[api-management-howto-aad-b2c-allowed-tenant]: ./media/api-management-howto-aad-b2c/api-management-b2c-allowed-tenant.PNG
[api-management-howto-aad-b2c-policies]: ./media/api-management-howto-aad-b2c/api-management-b2c-policies.PNG
[api-management-howto-aad-b2c-dev-portal]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-button.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-options]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-options.PNG
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.PNG
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-b2c-security-tab.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Azure Active Directory B2C 概述]: ../active-directory-b2c/overview.md
[如何使用 Azure Active Directory 为开发人员帐户授权]: ./api-management-howto-aad.md
[Azure Active Directory B2C：可扩展的策略框架]: ../active-directory-b2c/user-flow-overview.md
[使用 Microsoft 帐户作为标识提供者 Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-microsoft-account.md
[在 Azure Active Directory B2C 中使用 Google 帐户作为标识提供者]: ../active-directory-b2c/identity-provider-google.md
[在 Azure Active Directory B2C 中使用 Facebook 帐户作为标识提供者]: ../active-directory-b2c/identity-provider-facebook.md
[在 Azure Active Directory B2C 中使用 LinkedIn 帐户作为标识提供者]: ../active-directory-b2c/identity-provider-linkedin.md

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
