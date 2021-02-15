---
title: 获取调用 Web API 的 Web 应用中的令牌 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何获取调用 Web API 的 Web 应用的令牌
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: cfd479382cb69e7355b033312e165699223fdbf0
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756303"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>调用 Web API 的 Web 应用：获取应用的令牌

你已构建了客户端应用程序对象。 现在，你将使用它获取令牌来调用 Web API。 在 ASP.NET 或 ASP.NET Core 中，调用 Web API 是在控制器中完成的。

- 使用令牌缓存获取 Web API 的令牌。 若要获取此令牌，请调用 MSAL `AcquireTokenSilent` 方法（或 Microsoft.Identity.Web 中的等效方法）。
- 调用受保护的 API，将访问令牌作为参数传递给它。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft.Identity.Web 添加了扩展方法，这些方法为调用 Microsoft Graph 或下游 Web API 提供便利服务。 若要详细了解这些方法，请参阅[调用 Web API 的 Web 应用：调用 API](scenario-web-app-call-api-call-api.md)。 使用这些帮助程序方法，你无需手动获取令牌。

但是，如果你确实想要手动获取令牌，可以通过以下代码以示例方式了解如何使用 Microsoft.Identity.Web 在主控制器中执行此操作。 它使用 REST API（而不是 Microsoft Graph SDK）调用 Microsoft Graph。 若要获取令牌以调用下游 API，可以通过控制器构造函数（如果使用 Blazor，则为页面构造函数）中的依赖项注入来注入 `ITokenAcquisition` 服务，并在控制器操作中使用该服务，从而为用户 (`GetAccessTokenForUserAsync`) 或守护程序方案中的应用程序本身 (`GetAccessTokenForAppAsync`) 获取令牌。

控制器方法受 `[Authorize]` 属性的保护，该属性确保只有经过身份验证的用户可使用 Web 应用。

```csharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions (see code below)

}
```

`ITokenAcquisition` 服务是由 ASP.NET 通过使用依赖项注入来注入的。

下面是 `HomeController` 的操作的简化代码，该操作获取令牌来调用 Microsoft Graph：

```csharp
[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

若要更好地了解此方案所需的代码，请参阅 [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 教程的阶段 2（[2-1-Web 应用调用 Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)）步骤。

在控制器操作顶部（如果你使用 Razor 模板，则为 Razor 页面顶部）的 `AuthorizeForScopes` 属性由 Microsoft.Identity.Web 提供。 它确保在需要时以增量方式要求用户提供许可。

还有其他复杂的变化形式，例如：

- 调用多个 API。
- 处理增量许可和条件访问。

[3-WebApp-multi-APIs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) 教程的第 3 章中涵盖了这些高级步骤。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

适用于 ASP.NET 的代码类似于为 ASP.NET Core 显示的代码：

- 受 [Authorize] 属性保护的控制器操作会提取控制器的 `ClaimsPrincipal` 成员的租户 ID 和用户 ID。 （ASP.NET 使用 `HttpContext.User`。）
- 然后，它会构建一个 MSAL.NET `IConfidentialClientApplication` 对象。
- 最后，它调用机密客户端应用程序的 `AcquireTokenSilent` 方法。
- 在必须进行交互的情况下，Web 应用需要质询用户（重新登录）并要求提供更多声明。

以下代码片段是从 [ms-identity-aspnet-webapp-openidconnect](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) ASP.NET MVC 代码示例中的 [HomeController.cs#L157-L192](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/257c8f96ec3ff875c351d1377b36403eed942a18/WebApp/Controllers/HomeController.cs#L157-L192) 提取的：

```C#
public async Task<ActionResult> ReadMail()
{
    IConfidentialClientApplication app = MsalAppBuilder.BuildConfidentialClientApplication();
    AuthenticationResult result = null;
    var account = await app.GetAccountAsync(ClaimsPrincipal.Current.GetMsalAccountId());
    string[] scopes = { "Mail.Read" };

    try
    {
        // try to get token silently
        result = await app.AcquireTokenSilent(scopes, account).ExecuteAsync().ConfigureAwait(false);
    }
    catch (MsalUiRequiredException)
    {
        ViewBag.Relogin = "true";
        return View();
    }

    // More code here
    return View();
}
```

有关详细信息，请参阅代码示例中 [BuildConfidentialClientApplication()](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Utils/MsalAppBuilder.cs) 和 [GetMsalAccountId](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/257c8f96ec3ff875c351d1377b36403eed942a18/WebApp/Utils/ClaimPrincipalExtension.cs#L38) 的代码


# <a name="java"></a>[Java](#tab/java)

在 Java 示例中，调用 API 的代码位于 [AuthPageController.java#L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62) 中的 getUsersFromGraph 方法中。

该方法尝试调用 `getAuthResultBySilentFlow`。 如果用户需要许可更多作用域，则该代码会处理 `MsalInteractionRequiredException` 对象来质询用户。

```java
@RequestMapping("/msal4jsample/graph/me")
public ModelAndView getUserFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
        throws Throwable {

    IAuthenticationResult result;
    ModelAndView mav;
    try {
        result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
    } catch (ExecutionException e) {
        if (e.getCause() instanceof MsalInteractionRequiredException) {

            // If the silent call returns MsalInteractionRequired, redirect to authorization endpoint
            // so user can consent to new scopes.
            String state = UUID.randomUUID().toString();
            String nonce = UUID.randomUUID().toString();

            SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

            String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                    httpRequest.getParameter("claims"),
                    "User.Read",
                    authHelper.getRedirectUriGraph(),
                    state,
                    nonce);

            return new ModelAndView("redirect:" + authorizationCodeUrl);
        } else {

            mav = new ModelAndView("error");
            mav.addObject("error", e);
            return mav;
        }
    }

    if (result == null) {
        mav = new ModelAndView("error");
        mav.addObject("error", new Exception("AuthenticationResult not found in session."));
    } else {
        mav = new ModelAndView("auth_page");
        setAccountInfo(mav, httpRequest);

        try {
            mav.addObject("userInfo", getUserInfoFromGraph(result.accessToken()));

            return mav;
        } catch (Exception e) {
            mav = new ModelAndView("error");
            mav.addObject("error", e);
        }
    }
    return mav;
}
// Code omitted here
```

# <a name="python"></a>[Python](#tab/python)

在 Python 示例中，调用 Microsoft Graph 的代码位于 [app.py#L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62) 中。

该代码将尝试从令牌缓存中获取令牌。 然后，在设置授权标头后，它将调用 Web API。 如果它无法获取令牌，则会将用户重新登录。

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>后续步骤

转到此方案中的下一篇文章：[调用 Web API](scenario-web-app-call-api-call-api.md)。
