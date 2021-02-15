---
title: Durable Functions 中的任务中心 - Azure
description: 了解在 Azure Functions 的 Durable Functions 扩展中什么是任务中心。 了解如何任务中心。
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: 26234039c77601bc1d29beeebd3fcb8461d6d6c9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009511"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Durable Functions 中的任务中心 (Azure Functions)

[Durable Functions](durable-functions-overview.md) 中的 *任务中心* 是用于业务流程的 Azure 存储资源的逻辑容器。 只有当业务流程协调程序函数与活动函数属于同一任务中心时，它们才能彼此进行交互。

如果多个函数应用共享存储帐户，则必须使用单独的任务中心名称配置每个函数应用。 一个存储帐户可以包含多个任务中心。 下图说明了在共享和专用存储帐户中每个函数应用有一个任务中心。

![说明共享和专用存储帐户的关系图。](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure 存储资源

任务中心包含以下存储资源：

* 一个或多个控制队列。
* 一个工作项队列。
* 一个历史记录表。
* 一个实例表。
* 一个包含一个或多个租用 blob 的存储容器。
* 包含大的消息有效负载（如果适用）的存储容器。

当业务流程协调程序实体或活动函数运行时或调度它们运行时，将自动在默认 Azure 存储帐户中创建所有这些资源。 [性能和缩放](durable-functions-perf-and-scale.md)一文介绍了如何使用这些资源。

## <a name="task-hub-names"></a>任务中心名称

任务中心由符合以下规则的名称标识：

* 仅包含字母数字字符
* 以字母开头
* 最小长度为 3 个字符，最大长度为 45 个字符

任务中心名称在 host.json 文件中声明，如以下示例所示：

### <a name="hostjson-functions-20"></a>host.json (Functions 2.0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub"
    }
  }
}
```

### <a name="hostjson-functions-1x"></a>host.json (Functions 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

还可以使用应用设置配置任务中心，如以下 `host.json` 示例文件所示：

### <a name="hostjson-functions-10"></a>host.json (Functions 1.0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.json (Functions 2.0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "%MyTaskHub%"
    }
  }
}
```

任务中心名称将设置为 `MyTaskHub` 应用设置的值。 以下 `local.settings.json` 演示了如何将 `MyTaskHub` 设置定义为 `samplehubname`：

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

以下代码演示如何编写使用[业务流程客户端绑定](durable-functions-bindings.md#orchestration-client)来处理配置为应用设置的任务中心的函数：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [DurableClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    object eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

> [!NOTE]
> 前面的 C# 示例适用于 Durable Functions 2.x。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`function.json` 文件中的任务中心属性通过应用设置进行设置：

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

# <a name="python"></a>[Python](#tab/python)

`function.json` 文件中的任务中心属性通过应用设置进行设置：

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

---

任务中心名称必须以字母开头且只能包含字母和数字。 如果未指定，则会使用默认的任务中心名称，如下表所示：

| Durable 扩展版本 | 默认的任务中心名称 |
| - | - |
| 2.x | 在 Azure 中部署时，任务中心名称派生自函数应用的名称。 在 Azure 外部运行时，默认的任务中心名称为 `TestHubName`。 |
| 1.x | 所有环境的默认任务中心名称为 `DurableFunctionsHub`。 |

有关扩展版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

> [!NOTE]
> 当共享存储帐户中有多个任务中心时，名称用于将一个任务中心与其他任务中心区分开来。 如果有多个函数应用共享一个共享存储帐户，则必须在 host.json 文件中为每个任务中心显式配置不同的名称。 否则多个函数应用会相互竞争消息，这可能会导致未定义的行为，例如业务流程意外“卡”在 `Pending` 或 `Running` 状态。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何处理业务流程版本控制](durable-functions-versioning.md)
