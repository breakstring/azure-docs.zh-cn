---
title: 配置调用 Web API 的 Web 应用 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何构建调用 Web API 的 Web API（应用的代码配置）
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d1ab7da9753642093505fa91ea76b9327612a6ac
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582360"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>调用 Web API 的 Web API：代码配置

注册 Web API 后，可以配置应用程序的代码。

用于配置 Web API 的代码，配置后它就可以调用下游 Web API，而后者又基于用来保护 Web API 的代码。 有关详细信息，请参阅[受保护的 Web API：应用配置](scenario-protected-web-api-app-configuration.md)。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="microsoftidentityweb"></a>Microsoft.Identity.Web

Microsoft 建议在调用下游 Web API 开发 ASP.NET Core 保护的 API 时，使用 [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) NuGet 包。 请参阅[受保护的 Web API：代码配置 | Microsoft.Identity.Web](scenario-protected-web-api-app-configuration.md#microsoftidentityweb)，在 Web API 的上下文中快速了解该库。

## <a name="client-secrets-or-client-certificates"></a>客户端密码或客户端证书

假设你的 web API 现在调用下游 web API，请在 *appsettings.js* 的文件中提供客户端密码或客户端证书。 还可添加一个部分来指定：

- 下游 Web API 的 URL
- 调用 API 所需的范围

在下面的示例中，`GraphBeta` 部分指定了这些设置。

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 },
 "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
    }
}
```

你可以提供客户端证书，而不是客户端密码。 以下代码片段演示如何使用存储在 Azure Key Vault 中的证书。

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
   ]
  },
  "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
  }
}
```

Microsoft.Identity.Web 提供了多种通过配置或代码描述证书的方法。 有关详细信息，请参阅 GitHub 上的 [Microsoft.Identity.Web wiki - 使用证书](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates)。

## <a name="startupcs"></a>Startup.cs

Web API 将需要获取下游 API 的令牌。 可通过在 `.AddMicrosoftIdentityWebApi(Configuration)` 后面添加 `.EnableTokenAcquisitionToCallDownstreamApi()` 行来指定它。 此行公开 `ITokenAcquisition` 服务，它可用于控制器/页面操作。 不过，正如你将在接下来的两个要点中看到的那样，可更简单地操作。 还需在 Startup.cs 中选择令牌缓存实现，例如 `.AddInMemoryTokenCaches()`：

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

如果你不想亲自获取令牌，Microsoft.Identity.Web 提供两种机制来从另一 API 调用下游 Web API。 选择哪种方式取决于你是要调用 Microsoft Graph 还是调用另一个 API。

### <a name="option-1-call-microsoft-graph"></a>选项 1：调用 Microsoft Graph

如果要调用 Microsoft Graph，可通过 Microsoft.Identity.Web 在 API 操作中直接使用 `GraphServiceClient`（由 Microsoft Graph SDK 公开）。 若要公开 Microsoft Graph：

1. 将 [Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) NuGet 包添加到项目中。
1. 在 *Startup.cs* 文件中 `.EnableTokenAcquisitionToCallDownstreamApi()` 的后面添加 `.AddMicrosoftGraph()`。 `.AddMicrosoftGraph()` 具有多个重写。 如果使用将配置部分作为参数的重写，代码会变为：

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>选项 2：调用下游 Web API，而不是 Microsoft Graph

若要调用下游 Web API 而不是 Microsoft Graph，请使用 Microsoft.Identity.Web 提供的 `.AddDownstreamWebApi()`，它可请求令牌并调用下游 Web API。

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

与 Web 应用一样，你可以选择各种令牌缓存实现。 有关详细信息，请参阅 GitHub 上的 [Microsoft 标识 Web - 令牌缓存序列化](https://aka.ms/ms-id-web/token-cache-serialization)。

下图显示 Microsoft.Identity.Web 的各种可能性以及它们对 Startup.cs 文件的影响 ：

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="显示 Startup.cs 中的服务配置选项的框图，说明如何调用 Web API 和指定令牌缓存实现":::

> [!NOTE]
> 若要完全了解此处的代码示例，请熟悉 [ASP.NET Core 基础知识](/aspnet/core/fundamentals)，并特别了解 [依赖关系注入](/aspnet/core/fundamentals/dependency-injection) 和 [选项](/aspnet/core/fundamentals/configuration/options)。

# <a name="java"></a>[Java](#tab/java)

代理 (OBO) 流用于获取所需的令牌来调用下游 Web API。 在此流中，Web API 从客户端应用程序接收拥有用户委托权限的持有者令牌，然后用此令牌交换另一个访问令牌来调用下游 Web API。

以下代码在 Web API 中使用 Spring Security 框架的 `SecurityContextHolder` 来获取已验证的持有者令牌。 然后，它通过 MSAL Java 库结合 `OnBehalfOfParameters` 使用 `acquireToken` 调用来获取下游 API 的令牌。 MSAL 将缓存令牌，使后续的 API 调用可以使用 `acquireTokenSilently` 获取缓存的令牌。

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="python"></a>[Python](#tab/python)

代理 (OBO) 流用于获取所需的令牌来调用下游 Web API。 在此流中，Web API 从客户端应用程序接收拥有用户委托权限的持有者令牌，然后用此令牌交换另一个访问令牌来调用下游 Web API。

Python Web API 需要使用一些中间件来验证从客户端接收的持有者令牌。 然后，Web API 可以通过调用 [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) 方法，使用 MSAL Python 库获取下游 API 的访问令牌。 有关使用此 API 的示例，请参阅 [GitHub 上 microsoft-authentication-library-for-python 的测试代码](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.2.0/tests/test_e2e.py#L429-L472)。 另请参阅在同一存储库中对[问题 53](https://github.com/AzureAD/microsoft-authentication-library-for-python/issues/53) 的讨论，以了解可以避免使用中间层应用程序的方法。

---

也可以参阅 [Node.js 和 Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/Function/MyHttpTrigger/index.js#L61) 中的 OBO 流实现示例。

## <a name="protocol"></a>协议

有关 OBO 协议的详细信息，请参阅 [Microsoft 标识平台和 OAuth 2.0 代理流](./v2-oauth2-on-behalf-of-flow.md)。

## <a name="next-steps"></a>后续步骤

转到此方案中的下一篇文章：[获取应用的令牌](scenario-web-api-call-api-acquire-token.md)。
