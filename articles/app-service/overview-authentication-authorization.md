---
title: 身份验证和授权
description: 了解 Azure 应用服务和 Azure Functions 中内置的身份验证和授权支持，并了解它如何保护应用，防止未经授权的访问。
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 07/08/2020
ms.reviewer: mahender
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: 1b95b1e96dc26fb72338518fc969c69b035d5f68
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095230"
---
# <a name="authentication-and-authorization-in-azure-app-service-and-azure-functions"></a>Azure 应用服务和 Azure Functions 中的身份验证和授权

Azure 应用服务提供内置的身份验证和授权支持。只需在 Web 应用、RESTful API、移动后端和 [Azure Functions](../azure-functions/functions-overview.md) 中编写少量的代码或根本无需编写代码，就能让用户登录和访问数据。 本文介绍应用服务如何帮助简化应用的身份验证和授权。

安全身份验证和授权需要深入了解安全性，包括联合身份验证、加密、 [JSON web 令牌 (JWT) ](https://wikipedia.org/wiki/JSON_Web_Token) 管理、 [授予类型](https://oauth.net/2/grant-types/)，等等。 应用服务提供这些实用工具，让你将更多的时间和精力花费在为客户提供业务价值上。

> [!IMPORTANT]
> 你并非必须使用此功能进行身份验证和授权。 可以在所选的 Web 框架中使用捆绑的安全功能，也可以编写自己的实用程序。 但请记住，[Chrome 80 针对 Cookie 对其实现 SameSite 的方式进行了中断性变更](https://www.chromestatus.com/feature/5088147346030592)（发布日期在 2020 年 3 月左右）；自定义远程身份验证或依赖于跨站点 Cookie 发布的其他方案可能会在客户端 Chrome 浏览器更新时中断。 解决方法很复杂，因为需要针对不同的浏览器支持不同的 SameSite 行为。 
>
> 应用服务托管的 ASP.NET Core 2.1 及更高版本已针对此中断性变更进行了修补，并且会相应地处理 Chrome 80 和更低版本的浏览器。 此外，我们还在整个 2020 年 1 月在应用服务实例上部署了 ASP.NET Framework 4.7.2 的同一修补程序。 有关详细信息，请参阅 [Azure 应用服务 SameSite Cookie 更新](https://azure.microsoft.com/updates/app-service-samesite-cookie-update/)。
>

> [!NOTE]
> “身份验证/授权”功能有时也称为“简单身份验证/授权”。

> [!NOTE]
> 启用此功能会导致对应用程序的所有非安全 HTTP 请求自动重定向到 HTTPS，而不管[强制实施 HTTPS](configure-ssl-bindings.md#enforce-https) 所需的应用服务配置设置如何。 如果需要，可以通过[身份验证/授权设置配置文件](app-service-authentication-how-to.md#configuration-file-reference)中的 `requireHttps` 设置禁用此功能，但需注意确保不通过非安全 HTTP 连接传输安全令牌。

有关特定于本机移动应用的信息，请参阅[使用 Azure 应用服务对移动应用进行用户身份验证和授权](/previous-versions/azure/app-service-mobile/app-service-mobile-auth)。

## <a name="how-it-works"></a>工作原理

### <a name="on-windows"></a>在 Windows 上

身份验证和授权模块在应用程序代码所在的同一沙盒中运行。 启用后，每个传入的 HTTP 请求将通过此模块，然后由应用程序代码处理。

![一个体系结构图，显示请求被站点沙盒中的进程拦截，该进程与标识提供者进行交互，然后再允许流量发往已部署的站点](media/app-service-authentication-overview/architecture.png)

此模块为应用处理多项操作：

- 使用指定的提供程序对用户进行身份验证
- 验证、存储和刷新令牌
- 管理经过身份验证的会话
- 将标识信息插入请求标头

此模块独立于应用程序代码运行，使用应用设置进行配置。 不需要任何 SDK、特定语言，或者对应用程序代码进行更改。 

### <a name="on-containers"></a>在容器上

身份验证和授权模块在独立于应用程序代码的单独容器中运行。 使用所谓的 [代表模式](/azure/architecture/patterns/ambassador)，它与传入流量交互，以执行与 Windows 相同的功能。 由于它不在进程内运行，因此不能与特定的语言框架直接集成;但是，应用需要的相关信息通过使用请求标头（如下所述）进行传递。

### <a name="userapplication-claims"></a>用户/应用程序声明

对于所有语言框架，应用服务都通过将传入令牌（无论是来自经过身份验证的最终用户还是来自客户端应用程序）中的声明注入请求标头，使其可供代码使用。 对于 ASP.NET 4.6 应用，应用服务会在 [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) 中填充经过身份验证的用户声明，使你能够遵循标准的 .NET 代码模式（包括 `[Authorize]` 属性）。 同样，对于 PHP 应用，应用服务会填充 `_SERVER['REMOTE_USER']` 变量。 对于 Java 应用，[可从 Tomcat servlet 访问](configure-language-java.md#authenticate-users-easy-auth)声明。

对于 [Azure Functions](../azure-functions/functions-overview.md)，没有为 .NET 代码填充 `ClaimsPrincipal.Current`，但你仍然可以在请求标头中找到用户声明，也可通过请求上下文甚至通过绑定参数来获取 `ClaimsPrincipal` 对象。 有关详细信息，请参阅[使用客户端标识](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities)。

有关详细信息，请参阅[访问用户声明](app-service-authentication-how-to.md#access-user-claims)。

> [!NOTE]
> 目前，ASP.NET Core 不支持为当前用户填充身份验证/授权功能。 但是，确实存在一些[第三方开源中间件组件](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)，可以帮助填补这一空白。
>

### <a name="token-store"></a>令牌存储

应用服务提供内置的令牌存储，这是与 Web 应用、API 或本机移动应用的用户相关联的令牌存储库。 对任何提供程序启用身份验证时，此令牌存储可立即供应用使用。 如果应用程序代码需要代表用户访问这些提供程序中的数据，例如： 

- 发布到经过身份验证的用户的 Facebook 时间线
- 使用 Microsoft Graph API 读取用户的公司数据

通常，必须编写代码才能在应用程序中收集、存储和刷新这些令牌。 使用令牌存储，只需在需要令牌时才[检索令牌](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)；当令牌失效时，可以[告知应用服务刷新令牌](app-service-authentication-how-to.md#refresh-identity-provider-tokens)。 

将为经身份验证的会话缓存 ID 令牌、访问令牌和刷新令牌，它们只能由关联的用户访问。  

如果不需要在应用中使用令牌，可以在应用的“身份验证/授权”页中禁用令牌存储。

### <a name="logging-and-tracing"></a>日志记录和跟踪

如果[启用应用程序日志记录](troubleshoot-diagnostic-logs.md)，将在日志文件中直接看到身份验证和授权跟踪。 如果出现意外的身份验证错误，查看现有的应用程序日志即可方便找到所有详细信息。 如果启用[失败请求跟踪](troubleshoot-diagnostic-logs.md)，可以确切地查看身份验证和授权模块在失败请求中发挥的作用。 在跟踪日志中，找到对名为 `EasyAuthModule_32/64` 的模块的引用。 

## <a name="identity-providers"></a>标识提供者

应用服务使用 [联合标识](https://en.wikipedia.org/wiki/Federated_identity)，其中第三方标识提供者为你管理用户标识和身份验证流。 默认提供五个标识提供者： 

| 提供程序 | 登录终结点 |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Microsoft 帐户](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |
| 任何 [OpenID Connect](https://openid.net/connect/) 提供程序（预览版） | `/.auth/login/<providerName>` |

对其中一个提供程序启用身份验证和授权时，其登录终结点可用于用户身份验证，以及验证来自提供程序的身份验证令牌。 可以轻松为用户提供其中任意数量的登录选项。

存在[旧版可扩展性路径][custom-auth]，用于与其他标识提供者或自定义身份验证/授权解决方案集成，但是不建议使用， 而应考虑使用 OpenID Connect 支持。

## <a name="authentication-flow"></a>身份验证流

身份验证流对于所有提供程序是相同的，但根据是否要使用提供程序的 SDK 登录而有所差别：

- 不使用提供程序 SDK：应用程序向应用服务委托联合登录。 浏览器应用通常采用此方案，这可以防止向用户显示提供程序的登录页。 服务器代码管理登录过程，因此，此流也称为“服务器导向流”或“服务器流”。  此方案适用于浏览器应用。 它也适用于使用移动应用客户端 SDK 登录用户的本机应用，因为 SDK 会打开 Web 视图，使用应用服务身份验证将用户登录。 
- 使用提供程序 SDK：应用程序手动将用户登录到提供程序，然后将身份验证令牌提交给应用服务进行验证。 无浏览器应用通常采用此方案，这可以防止向用户显示提供程序的登录页。 应用程序代码管理登录过程，因此，此流也称为“客户端导向流”或“客户端流”。  此方案适用于 REST API、[Azure Functions](../azure-functions/functions-overview.md) 和 JavaScript 浏览器客户端，以及在登录过程中需要更高灵活性的浏览器应用。 它还适用于使用提供程序 SDK 登录用户的本机移动应用。

> [!NOTE]
> 对于应用服务中受信任浏览器应用对应用服务或 [Azure Functions](../azure-functions/functions-overview.md) 中另一 REST API 的调用，可以使用服务器导向流对其进行身份验证。 有关详细信息，请参阅[在应用服务中自定义身份验证和授权](app-service-authentication-how-to.md)。
>

下表说明了身份验证流的步骤。

| 步骤 | 不使用提供程序 SDK | 使用提供程序 SDK |
| - | - | - |
| 1.将用户登录 | 将客户端重定向到 `/.auth/login/<provider>`。 | 客户端代码直接使用提供程序的 SDK 将用户登录，并接收身份验证令牌。 有关详细信息，请参阅提供程序文档。 |
| 2.身份验证后 | 提供程序将客户端重定向到 `/.auth/login/<provider>/callback`。 | 客户端代码[将来自提供程序的令牌发布到](app-service-authentication-how-to.md#validate-tokens-from-providers)`/.auth/login/<provider>` 进行验证。 |
| 3.建立经过身份验证的会话 | 应用服务将经过身份验证的 Cookie 添加到响应中。 | 应用服务将自身的身份验证令牌返回给客户端代码。 |
| 4.提供经过身份验证的内容 | 客户端在后续请求中包含身份验证 Cookie（由浏览器自动处理）。 | 客户端代码在 `X-ZUMO-AUTH` 标头中提供身份验证令牌（由移动应用客户端 SDK 自动处理）。 |

对于客户端浏览器，应用服务可自动将所有未经身份验证的用户定向到 `/.auth/login/<provider>`。 还可以向用户提供一个或多个 `/.auth/login/<provider>` 链接，让他们使用所选的提供程序登录到你的应用。

<a name="authorization"></a>

## <a name="authorization-behavior"></a>授权行为

在 [Azure 门户](https://portal.azure.com)中，当传入请求未经过身份验证时，可以使用多种行为配置应用服务授权。

![一个屏幕截图，显示“请求未通过身份验证时要采取的操作”下拉列表](media/app-service-authentication-overview/authorization-flow.png)

以下标题介绍了选项。

### <a name="allow-anonymous-requests-no-action"></a>允许匿名请求(无操作)

此选项将对未经身份验证的流量的授权交给应用程序代码处理。 对于经过身份验证的请求，应用服务还会在 HTTP 标头中一起传递身份验证信息。 

使用此选项可以更灵活地处理匿名请求。 例如，可以向用户[提供多个登录提供程序](app-service-authentication-how-to.md#use-multiple-sign-in-providers)。 但是，必须编写代码。 

### <a name="allow-only-authenticated-requests"></a>仅允许经过身份验证的请求

选项是“使用 \<provider> 登录”。 应用服务将所有匿名请求重定向到所选提供程序的 `/.auth/login/<provider>`。 如果匿名请求来自本机移动应用，则返回的响应为 `HTTP 401 Unauthorized`。

使用此选项不需要在应用中编写任何身份验证代码。 可以通过检查用户的声明来处理精细授权，例如角色特定的授权（请参阅[访问用户声明](app-service-authentication-how-to.md#access-user-claims)）。

> [!CAUTION]
> 以这种方式限制访问适用于对应用的所有调用，对于想要主页公开可用的应用程序来说，这可能是不可取的，就像在许多单页应用程序中一样。

> [!NOTE]
> 默认情况下，Azure AD 租户中的任何用户都可以从 Azure AD 请求应用程序的令牌。 若要仅允许一组定义的用户访问应用，可以[在 Azure AD 中配置应用程序](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md)。

## <a name="more-resources"></a>更多资源

* [教程：在访问 Azure 存储和 Microsoft Graph 的 web 应用中对用户进行身份验证和授权](scenario-secure-app-authentication-app-service.md)
* [教程：在 Azure 应用服务 (Windows) 中对用户进行端到端身份验证和授权](tutorial-auth-aad.md)  
* [教程：在适用于 Linux 的 Azure 应用服务中对用户进行端到端身份验证和授权](./tutorial-auth-aad.md?pivots=platform-linux%3fpivots%3dplatform-linux)  
* [在应用服务中自定义身份验证和授权](app-service-authentication-how-to.md)
* [.NET Core 与 Azure 应用服务 EasyAuth 的集成（第三方）](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)
* [使用 .NET Core 进行 Azure 应用服务身份验证（第三方）](https://github.com/kirkone/KK.AspNetCore.EasyAuthAuthentication)

特定于提供程序的操作方法指南：

* [How to configure your app to use Azure Active Directory login][AAD]
* [如何将应用配置为使用 Facebook 登录][Facebook]
* [如何将应用配置为使用 Google 登录][Google]
* [How to configure your app to use Microsoft Account login][MSA]
* [如何将应用配置为使用 Twitter 登录][Twitter]
* [如何将应用配置为使用 OpenID Connect 提供程序（预览版）进行登录][OIDC]
* [如何将应用配置为使用 Apple (预览版的登录) ][Apple]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md
[OIDC]: configure-authentication-provider-openid-connect.md
[Apple]: configure-authentication-provider-apple.md

[custom-auth]: /previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#custom-auth

[ADAL-Android]: /previous-versions/azure/app-service-mobile/app-service-mobile-android-how-to-use-client-library#adal
[ADAL-iOS]: /previous-versions/azure/app-service-mobile/app-service-mobile-ios-how-to-use-client-library#adal
[ADAL-dotnet]: /previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library#adal
