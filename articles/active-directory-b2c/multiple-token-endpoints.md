---
title: 将基于 OWIN 的 web Api 迁移到 b2clogin.com
titleSuffix: Azure AD B2C
description: 了解如何在将应用程序迁移到 b2clogin.com 时，启用 .NET web API 以支持多个令牌颁发者颁发的令牌。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/31/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c362ce256259606c85af0a7e13ccde1715bb012b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953927"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom"></a>将基于 OWIN 的 web API 迁移到 b2clogin.com

本文介绍了在 web Api 中启用多个令牌颁发者支持的技术，该技术实现 [.net (OWIN) 的开放 Web 界面 ](http://owin.org/)。 将 Azure Active Directory B2C (Azure AD B2C) Api 及其应用程序从 *login.microsoftonline.com* 迁移到 *b2clogin.com* 时，支持多个令牌终结点非常有用。

通过在接受 b2clogin.com 和 login.microsoftonline.com 颁发的令牌的 API 中添加支持，你可以分阶段迁移 web 应用程序，然后从 API 中删除 login.microsoftonline.com 颁发的令牌支持。

以下各节提供了一个示例，说明如何在使用 [MICROSOFT OWIN][katana] 中间件组件 (Katana) 的 web API 中启用多个颁发者。 尽管代码示例特定于 Microsoft OWIN 中间件，但一般方法应该适用于其他 OWIN 库。

> [!NOTE]
> 本文适用于当前已部署的 Api 和应用程序，以及 `login.microsoftonline.com` 要迁移到建议终结点的应用程序的 Azure AD B2C 客户 `b2clogin.com` 。 如果要设置新应用程序，请按指示使用 [b2clogin.com](b2clogin.md) 。

## <a name="prerequisites"></a>先决条件

在继续执行本文中的步骤之前，需要准备好以下 Azure AD B2C 资源：

* [用户流](tutorial-create-user-flows.md)或在租户中创建的[自定义策略](custom-policy-get-started.md)

## <a name="get-token-issuer-endpoints"></a>获取令牌颁发者终结点

首先需要获取要在 API 中支持的每个颁发者的令牌颁发者终结点 Uri。 若要获取 Azure AD B2C 租户支持的 *b2clogin.com* 和 *login.microsoftonline.com* 终结点，请在 Azure 门户中使用以下过程。

首先选择一个现有的用户流：

1. 导航到[Azure 门户](https://portal.azure.com)中的 Azure AD B2C 租户
1. 在 " **策略**" 下，选择 " **用户流 (策略")**
1. 选择现有策略（例如 *B2C_1_signupsignin1*），然后选择 "**运行用户流**"
1. 在页面顶部附近的 " **运行用户流** " 标题下，选择超链接以导航到该用户流的 OpenID connect 发现终结点。

    ![Azure 门户的“立即运行”页中的“已知 URI”超链接](media/multi-token-endpoints/portal-01-policy-link.png)

1. 在浏览器中打开的页上，记下 `issuer` 值，例如：

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. 使用 " **选择域** " 下拉箭头选择其他域，然后再次执行前面的两个步骤，并记录其 `issuer` 值。

现在，应记录两个与类似的 Uri：

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>自定义策略

如果你有自定义策略而不是用户流，则可以使用类似的过程来获取颁发者 Uri。

1. 导航到 Azure AD B2C 租户
1. 选择 **标识体验框架**
1. 选择一个信赖方策略，例如 *B2C_1A_signup_signin*
1. 使用 " **选择域** " 下拉箭头选择域，例如 *yourtenant.b2clogin.com*
1. 选择已在 **OpenID connect 发现终结点** 下显示的超链接
1. 记录 `issuer` 值
1. 为另一个域执行步骤4-6，例如 *login.microsoftonline.com*

## <a name="get-the-sample-code"></a>获取示例代码

现在，你已拥有两个令牌终结点 Uri，你需要更新你的代码以指定这两个终结点都是有效的颁发者。 若要完成示例，请下载或克隆示例应用程序，然后更新示例以支持这两个终结点作为有效的颁发者。

下载存档： [active-directory-b2c-dotnet-webapp-and-webapi-master.zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>在 web API 中启用多个颁发者

在本部分中，将更新代码以指定两个令牌颁发者终结点都是有效的。

1. 在 Visual Studio 中打开 **B2C-WebAPI-DotNet** 解决方案
1. 在 **TaskService** 项目中，在编辑器中打开 * TaskService \\ App_Start \\ **Startup.Auth.cs** _ 文件
1. 将下面的 `using` 指令添加到文件的顶部：

    `using System.Collections.Generic;`
1. 将 [`ValidIssuers`][validissuers] 属性添加到 [`TokenValidationParameters`][tokenvalidationparameters] 定义，并指定在上一节中记录的两个 uri：

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters` 由 MSAL.NET 提供，由 * 中的下一部分 _Startup 代码的 OWIN 中间件使用。 指定多个有效的颁发者后，OWIN 应用程序管道就会注意到这两个令牌终结点都是有效的颁发者。

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

如前所述，其他 OWIN 库通常提供一种类似的功能，用于支持多个颁发者。 尽管提供每个库的示例超出了本文的范围，但对于大多数库，您可以使用类似的技术。

## <a name="switch-endpoints-in-web-app"></a>切换 web 应用中的终结点

由于 web API 现在支持两个 Uri，因此你现在需要更新 web 应用程序，以便从 b2clogin.com 终结点检索令牌。

例如，可以通过修改 `ida:AadInstance` _ TaskWebApp * * 项目的 *TaskWebApp \\ **Web.config** _ 文件* 中的值，将示例 web 应用程序配置为使用新的终结点。

更改 `ida:AadInstance` TaskWebApp 的 *Web.config* 中的值，使其引用 `{your-b2c-tenant-name}.b2clogin.com` 而不是 `login.microsoftonline.com` 。

早于:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

 (替换 `{your-b2c-tenant}` 为 B2C 租户) 的名称：

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

当在 web 应用的执行过程中构造终结点字符串时，将在请求令牌时使用基于 b2clogin.com 的终结点。

## <a name="next-steps"></a>后续步骤

本文介绍了如何配置 web API 来实现 Microsoft OWIN 中间件 (Katana) ，以接受来自多个颁发者终结点的令牌。 你可能会注意到，TaskService 和 TaskWebApp 项目的 *Web.Config* 文件中有一些其他字符串，如果你要针对自己的租户生成并运行这些项目，则需要更改这些项目。 如果你想要在操作中查看项目，则欢迎你对其进行修改，但在本文的讨论范围外，此操作的完整演练超出了本文的范围。

有关 Azure AD B2C 发出的不同类型的安全令牌的详细信息，请参阅 [Azure Active Directory B2C 中的令牌概述](tokens-overview.md)。

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: /aspnet/aspnet/overview/owin-and-katana/
[validissuers]: /dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: /dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters