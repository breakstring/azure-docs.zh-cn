---
title: 用于调用 web Api 的 web API |Microsoft
titleSuffix: Microsoft identity platform
description: 了解如何构建调用 Web API 的 Web API。
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
ms.openlocfilehash: 2c69cea7055476bcc4c4a28cea0a0cf8c8f66f88
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753463"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>调用 Web API 的 Web API：调用 API

有了令牌后，就可以调用受保护的 Web API 了。 通常从 Web API 的控制器或页面调用下游 API。

## <a name="controller-code"></a>控制器代码

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

使用 Microsoft.Identity.Web 时，有 3 种使用方案：

- [选项 1：通过 Microsoft Graph SDK 调用 Microsoft Graph](#option-1-call-microsoft-graph-with-the-sdk)
- [选项 2：在使用帮助程序类的情况下调用下游 Web API](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [选项 3：在不使用帮助程序类的情况下调用下游 Web API](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>选项 1：使用 SDK 调用 Microsoft Graph

在此方案中，你已如[代码配置](scenario-web-api-call-api-app-configuration.md#option-1-call-microsoft-graph)中指定的那样将 `.AddMicrosoftGraph()` 添加到 Startup.cs 中，现可直接将 `GraphServiceClient` 注入控制器或页构造函数中，以便在操作中使用。 以下示例 Razor 页面显示已登录用户的照片。

```CSharp
 [Authorize]
 [AuthorizeForScopes(Scopes = new[] { "user.read" })]
 public class IndexModel : PageModel
 {
     private readonly GraphServiceClient _graphServiceClient;

     public IndexModel(GraphServiceClient graphServiceClient)
     {
         _graphServiceClient = graphServiceClient;
     }

     public async Task OnGet()
     {
         var user = await _graphServiceClient.Me.Request().GetAsync();
         try
         {
             using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
             {
                 byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                 ViewData["photo"] = Convert.ToBase64String(photoByte);
             }
             ViewData["name"] = user.DisplayName;
         }
         catch (Exception)
         {
             ViewData["photo"] = null;
         }
     }
 }
```

#### <a name="option-2-call-a-downstream-web-api-with-the-helper-class"></a>选项 2：在使用帮助程序类的情况下调用下游 Web API

在此方案中，你已如[代码配置](scenario-web-api-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph)中指定的那样将 `.AddDownstreamWebApi()` 添加到 Startup.cs 中，现可直接将 `IDownstreamWebApi` 服务注入控制器或页构造函数中并在操作中使用它：

```CSharp
 [Authorize]
 [AuthorizeForScopes(ScopeKeySection = "TodoList:Scopes")]
 public class TodoListController : Controller
 {
     private IDownstreamWebApi _downstreamWebApi;
     private const string ServiceName = "TodoList";

     public TodoListController(IDownstreamWebApi downstreamWebApi)
     {
         _downstreamWebApi = downstreamWebApi;
     }

     public async Task<ActionResult> Details(int id)
     {
         var value = await _downstreamWebApi.CallWebApiForUserAsync(
             ServiceName,
             options =>
             {
                 options.RelativePath = $"me";
             });
         return View(value);
     }
```

`CallWebApiForUserAsync` 方法还具有强类型的泛型重写，使你能够直接接收对象。 例如，下面的方法收到一个 `Todo` 实例，该实例是 Web API 返回的 JSON 的强类型表示形式。

```CSharp
 // GET: TodoList/Details/5
 public async Task<ActionResult> Details(int id)
 {
     var value = await _downstreamWebApi.CallWebApiForUserAsync<object, Todo>(
         ServiceName,
         null,
         options =>
         {
             options.HttpMethod = HttpMethod.Get;
             options.RelativePath = $"api/todolist/{id}";
         });
     return View(value);
 }
```

#### <a name="option-3-call-a-downstream-web-api-without-the-helper-class"></a>选项 3：在不使用帮助程序类的情况下调用下游 Web API

如果已决定使用 `ITokenAcquisition` 服务手动获取令牌，现在需要使用令牌。 在这种情况下，以下代码继续演示[调用 Web API 的 Web API：获取应用的令牌](scenario-web-api-call-api-acquire-token.md)中显示的示例代码。 该代码在 API 控制器的操作中调用。 它调用下游 API（名为 *todolist*）。

 获取令牌后，将其用作持有者令牌以调用下游 API。

```csharp
 private async Task CallTodoListService(string accessToken)
 {
  // After the token has been returned by Microsoft.Identity.Web, add it to the HTTP authorization header before making the call to access the todolist service.
 _httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

 // Call the todolist service.
 HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
 // ...
 }
 ```

# <a name="java"></a>[Java](#tab/java)

以下代码继续演示[调用 Web API 的 Web API：获取应用的令牌](scenario-web-api-call-api-acquire-token.md)中显示的示例代码。 该代码在 API 控制器的操作中调用。 它调用下游 API MS Graph。

获取令牌后，将其用作持有者令牌以调用下游 API。

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
我们尚未编写在 MSAL Python 中演示此流的示例。

---

## <a name="next-steps"></a>后续步骤

转到此方案中的下一篇文章：[转向生产](scenario-web-api-call-api-production.md)。
