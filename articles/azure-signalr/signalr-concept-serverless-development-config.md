---
title: 开发和配置 Azure Functions 应用 - Azure SignalR
description: 详细介绍如何使用 Azure Functions 与 Azure SignalR 服务来开发和配置无服务器实时应用程序
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 3d69b72012819e3d9099e447b9048fe07aea86d3
ms.sourcegitcommit: 89c0482c16bfec316a79caa3667c256ee40b163f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97858699"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>使用 Azure SignalR 服务进行 Azure Functions 开发和配置

Azure Functions 应用程序可以利用 [Azure SignalR 服务绑定](../azure-functions/functions-bindings-signalr-service.md)来添加实时功能。 客户端应用程序使用以多种语言编写的客户端 SDK 连接到 Azure SignalR 服务和接收实时消息。

本文介绍有关开发和配置与 SignalR 服务集成的 Azure 函数应用的概念。

## <a name="signalr-service-configuration"></a>SignalR 服务配置

可以不同的模式配置 Azure SignalR 服务。 与 Azure Functions 一起使用时，必须以“无服务器”模式配置服务。

在 Azure 门户中，找到 SignalR 服务资源的“设置”页。 将“服务模式”设置为“无服务器”。 

![SignalR 服务模式](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Azure Functions 开发

使用 Azure Functions 和 Azure SignalR 服务构建的无服务器实时应用程序通常需要两个 Azure 函数：

* “negotiate”函数：客户端调用该函数来获取有效的 SignalR 服务访问令牌和服务终结点 URL
* 一个或多个用于处理来自 SignalR 服务的消息、发送消息或管理组成员身份的函数

### <a name="negotiate-function"></a>negotiate 函数

客户端应用程序需要获取有效的访问令牌才能连接到 Azure SignalR 服务。 访问令牌可以是匿名的，也可以是经过身份验证的给定用户 ID。 无服务器 SignalR 服务应用程序需要使用名为“negotiate”的 HTTP 终结点来获取令牌和其他连接信息，例如 SignalR 服务终结点 URL。

使用 HTTP 触发的 Azure 函数和 *SignalRConnectionInfo* 输入绑定生成连接信息对象。 该函数必须包含以 `/negotiate` 结尾的 HTTP 路由。

通过使用 C# 中[基于类的模型](#class-based-model)，可无需执行 SignalRConnectionInfo 输入绑定，并可以轻松得多的方式添加自定义声明。 请参阅[基于类的模型中的协商体验](#negotiate-experience-in-class-based-model)

有关如何创建 negotiate 函数的详细信息，请参阅 [*SignalRConnectionInfo* 输入绑定参考](../azure-functions/functions-bindings-signalr-service-input.md)。

若要了解如何创建经过身份验证的令牌，请参阅[使用应用服务身份验证](#using-app-service-authentication)。

### <a name="handle-messages-sent-from-signalr-service"></a>处理从 SignalR 服务发送的消息

使用 SignalR 触发器绑定来处理从 SignalR 服务发送的消息。 当客户端发送消息或客户端连接或断开连接时，你可以获得通知。

有关详细信息，请参阅 [SignalR 触发器绑定参考](../azure-functions/functions-bindings-signalr-service-trigger.md)。

还需要将函数终结点配置为上游，以便在有来自客户端的消息时，服务将触发该函数。 有关如何配置上游的详细信息，请参阅此[文档](concept-upstream.md)。

### <a name="sending-messages-and-managing-group-membership"></a>发送消息和管理组成员身份

使用 *SignalR* 输出绑定将消息发送到与 Azure SignalR 服务连接的客户端。 可将消息广播到所有客户端，或者将其发送到已使用特定用户 ID 进行身份验证的或已添加到特定组的一部分客户端。

可将用户添加到一个或多个组。 还可以使用 *SignalR* 输出绑定在组中添加或删除用户。

有关详细信息，请参阅 [*SignalR* 输出绑定参考](../azure-functions/functions-bindings-signalr-service-output.md)。

### <a name="signalr-hubs"></a>SignalR 中心

SignalR 具有“中心”的概念。 每个客户端连接以及从 Azure Functions 发送的每个消息的范围限定为特定的中心。 可以使用中心将连接和消息划分到逻辑命名空间。

## <a name="class-based-model"></a>基于类的模型

基于类的模型专用于 C#。 使用基于类的模型可以拥有一致的 SignalR 服务器端编程体验。 该示例应用程序具有以下功能。

* 更少的配置工作：类名称用作 `HubName`，方法名称用作 `Event`，`Category` 根据方法名称自动确定。
* 自动参数绑定：不需要 `ParameterNames` 和属性 `[SignalRParameter]`。 参数按顺序自动绑定到 Azure Function 方法的参数上。
* 方便的输出和协商体验。

以下代码演示了这些功能：

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("negotiate")]
    public SignalRConnectionInfo Negotiate([HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req)
    {
        return Negotiate(req.Headers["x-ms-signalr-user-id"], GetClaims(req.Headers["Authorization"]));
    }

    [FunctionName(nameof(OnConnected))]
    public async Task OnConnected([SignalRTrigger]InvocationContext invocationContext, ILogger logger)
    {
        await Clients.All.SendAsync(NewConnectionTarget, new NewConnection(invocationContext.ConnectionId));
        logger.LogInformation($"{invocationContext.ConnectionId} has connected");
    }

    [FunctionName(nameof(Broadcast))]
    public async Task Broadcast([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        await Clients.All.SendAsync(NewMessageTarget, new NewMessage(invocationContext, message));
        logger.LogInformation($"{invocationContext.ConnectionId} broadcast {message}");
    }

    [FunctionName(nameof(OnDisconnected))]
    public void OnDisconnected([SignalRTrigger]InvocationContext invocationContext)
    {
    }
}
```

需要利用基于类的模型的所有函数都需是继承自 ServerlessHub 的类的方法。 示例中的类名称 `SignalRTestHub` 是中心名称。

### <a name="define-hub-method"></a>定义中心方法

所有中心方法必须具有由 `[SignalRTrigger]` 属性修饰的 `InvocationContext` 参数，并使用无参数构造函数。 然后将方法名称视为参数 event 。

默认情况下，除了方法名称外，`category=messages` 是以下名称之一：

* **OnConnected**：视为 `category=connections, event=connected`
* **OnDisconnected**：视为 `category=connections, event=disconnected`

### <a name="parameter-binding-experience"></a>参数绑定体验

在基于类的模型中，`[SignalRParameter]` 是不必要的，因为默认情况下，所有参数都标记为 `[SignalRParameter]`，除非是以下情况之一：

* 参数由绑定属性修饰。
* 参数的类型为 `ILogger` 或 `CancellationToken`
* 参数由属性 `[SignalRIgnore]` 修饰

### <a name="negotiate-experience-in-class-based-model"></a>基于类的模型中的协商体验

与使用 SignalR 输入绑定 `[SignalR]` 相比，基于类的模型中的协商更为灵活。 基类 `ServerlessHub` 具有一个方法

```cs
SignalRConnectionInfo Negotiate(string userId = null, IList<Claim> claims = null, TimeSpan? lifeTime = null)
```

此功能使用户可以在函数执行期间自定义 `userId` 或 `claims`。

## <a name="use-signalrfilterattribute"></a>使用 `SignalRFilterAttribute`

用户可以继承和实现抽象类 `SignalRFilterAttribute`。 如果 `FilterAsync` 中引发异常，会将 `403 Forbidden` 发送回客户端。

下面的示例演示如何实现仅允许 `admin` 调用 `broadcast` 的客户筛选器。

```cs
[AttributeUsage(AttributeTargets.Method, AllowMultiple = true, Inherited = true)]
internal class FunctionAuthorizeAttribute: SignalRFilterAttribute
{
    private const string AdminKey = "admin";

    public override Task FilterAsync(InvocationContext invocationContext, CancellationToken cancellationToken)
    {
        if (invocationContext.Claims.TryGetValue(AdminKey, out var value) &&
            bool.TryParse(value, out var isAdmin) &&
            isAdmin)
        {
            return Task.CompletedTask;
        }

        throw new Exception($"{invocationContext.ConnectionId} doesn't have admin role");
    }
}
```

利用属性对函数进行授权。

```cs
[FunctionAuthorize]
[FunctionName(nameof(Broadcast))]
public async Task Broadcast([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
{
}
```

## <a name="client-development"></a>客户端开发

SignalR 客户端应用程序可利用以多种语言之一编写的 SignalR 客户端 SDK 轻松连接到 Azure SignalR 服务并从中接收消息。

### <a name="configuring-a-client-connection"></a>配置客户端连接

若要连接到 SignalR 服务，客户端必须成功完成连接协商，具体包括以下步骤：

1. 向上述 HTTP 协商终结点发出请求，以获取有效的连接信息
1. 使用服务终结点 URL 以及从协商终结点获取的访问令牌连接到 SignalR 服务

SignalR 客户端 SDK 已包含执行协商握手所需的逻辑。 将协商终结点的 URL（不包括 `negotiate` 段）传递给 SDK 的 `HubConnectionBuilder`。 下面是一个 JavaScript 示例：

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

SDK 根据约定自动将 `/negotiate` 追加到 URL，然后使用该 URL 开始协商。

> [!NOTE]
> 如果在浏览器中使用 JavaScript/TypeScript SDK，则需要在函数应用中[启用跨源资源共享 (CORS)](#enabling-cors)。

有关如何使用 SignalR 客户端 SDK 的详细信息，请参阅适用于所用语言的文档：

* [.NET Standard](/aspnet/core/signalr/dotnet-client)
* [JavaScript](/aspnet/core/signalr/javascript-client)
* [Java](/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>将消息从客户端发送到服务

如果为 SignalR 资源配置了[上游](concept-upstream.md)，则可以使用任何 SignalR 客户端将消息从客户端发送到 Azure Functions。 下面是一个 JavaScript 示例：

```javascript
connection.send('method1', 'arg1', 'arg2');
```

## <a name="azure-functions-configuration"></a>Azure Functions 配置

与 Azure SignalR 服务集成的 azure Function apps 可以像使用 [持续部署](../azure-functions/functions-continuous-deployment.md)、 [zip 部署](../azure-functions/deployment-zip-push.md)和 [从包运行](../azure-functions/run-functions-from-deployment-package.md)这样的技术一样部署，如任何典型的 azure function app。

但是，对于使用 SignalR 服务绑定的应用，需要注意几个特殊事项。 如果客户端在浏览器中运行，则必须启用 CORS。 如果应用需要身份验证，则你可以将协商终结点与应用服务身份验证集成。

### <a name="enabling-cors"></a>启用 CORS

JavaScript/TypeScript 客户端向 negotiate 函数发出 HTTP 请求，以启动连接协商。 如果客户端应用程序不是托管在 Azure 函数应用所在的同一个域中，则必须在函数应用中启用跨源资源共享 (CORS)，否则浏览器会阻止请求。

#### <a name="localhost"></a>Localhost

在本地计算机上运行函数应用时，可将 `Host` 节添加到 *local.settings.json* 以启用 CORS。 在 `Host` 节中添加两个属性：

* `CORS` - 输入作为客户端应用程序来源的基本 URL
* `CORSCredentials` - 将其设置为 `true` 以允许“withCredentials”请求

示例：

```json
{
  "IsEncrypted": false,
  "Values": {
    // values
  },
  "Host": {
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
}
```

#### <a name="cloud---azure-functions-cors"></a>云 - Azure Functions CORS

若要在 Azure 函数应用中启用 CORS，请在 Azure 门户中函数应用的“平台功能”选项卡下，转到 CORS 配置屏幕。

> [!NOTE]
> CORS 配置在 Azure Functions Linux 消耗计划中尚不可用。 使用 [AZURE API 管理](#cloud---azure-api-management) 启用 CORS。

必须启用支持 Access-Control-Allow-Credentials 的 CORS 才能让 SignalR 客户端调用 negotiate 函数。 选中相应的复选框以启用 CORS。

在“允许的源”部分添加一个包含 Web 应用程序源基本 URL 的条目。

![配置 CORS](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>云 - Azure API 管理

Azure API 管理提供一个可向现有后端服务添加功能的 API 网关。 可以使用 API 管理将 CORS 添加到函数应用。 它提供按操作定价并授予每月免费额度的消耗层。

请参阅 API 管理文档来了解如何[导入 Azure 函数应用](../api-management/import-function-app-as-api.md)。 导入后，可以添加一个入站策略来启用支持 Access-Control-Allow-Credentials 的 CORS。

```xml
<cors allow-credentials="true">
  <allowed-origins>
    <origin>https://azure-samples.github.io</origin>
  </allowed-origins>
  <allowed-methods>
    <method>GET</method>
    <method>POST</method>
  </allowed-methods>
  <allowed-headers>
    <header>*</header>
  </allowed-headers>
  <expose-headers>
    <header>*</header>
  </expose-headers>
</cors>
```

将 SignalR 客户端配置为使用 API 管理 URL。

### <a name="using-app-service-authentication"></a>使用应用服务身份验证

Azure Functions 提供内置身份验证，支持 Facebook、Twitter、Microsoft 帐户、Google 和 Azure Active Directory 等常用访问接口。 此功能可与 *SignalRConnectionInfo* 绑定集成，以便与已使用用户 ID 进行身份验证的 Azure SignalR 服务建立连接。 应用程序可以使用 *SignalR* 输出绑定来发送以该用户 ID 为目标的消息。

在 Azure 门户中函数应用的“平台功能”选项卡上，打开“身份验证/授权”设置窗口。  遵循[应用服务身份验证](../app-service/overview-authentication-authorization.md)文档使用所选的标识提供者配置身份验证。

配置后，经过身份验证的 HTTP 请求将包含 `x-ms-client-principal-name` 和 `x-ms-client-principal-id` 标头，而这些标头分别包含经过身份验证的标识的用户名和用户 ID。

可以在 *SignalRConnectionInfo* 绑定配置中使用这些标头来创建经过身份验证的连接。 下面是一个使用 `x-ms-client-principal-id` 标头的 C# negotiate 函数示例。

```csharp
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

然后，可以通过设置 SignalR 消息的 `UserId` 属性向该用户发送消息。

```csharp
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

有关其他语言的信息，请参阅 Azure Functions 参考文章 [Azure SignalR 服务绑定](../azure-functions/functions-bindings-signalr-service.md)。

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用 Azure Functions 开发和配置无服务器 SignalR 服务应用程序。 请尝试使用 [SignalR 服务概述页](index.yml)上的某篇快速入门或教程自行创建应用程序。
