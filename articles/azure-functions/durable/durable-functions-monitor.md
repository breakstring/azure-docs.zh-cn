---
title: Durable Functions 中的监视器 - Azure
description: 了解如何使用 Azure Functions 的 Durable Functions 扩展实现状态监视。
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: e70c50098ece516312e1e92984185624c276301b
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028414"
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Durable Functions 中的监视场景 - 天气观察程序示例

监视模式是工作流中灵活的重复过程 - 例如，反复轮询，直到满足特定的条件为止。 本文介绍使用 [Durable Functions](durable-functions-overview.md) 实现监视的示例。

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>方案概述

此示例监视某个地点的当前天气状况，如果是晴天，则通过短信通知用户。 可以使用常规的计时器触发函数来检查天气和发送提醒。 但是，此方法存在 **生存期管理** 方面的问题。 如果只应发送一条提醒，则在检测到晴天后，监视器需要禁用自身。 监视模式可以结束自身的执行，同时还具有其他优点：

* 监视器按时间间隔而不是计划运行：计时器触发器每隔一小时运行；监视器等待一小时，然后执行下一项操作。 除非有指定，否则监视器的操作不会重叠，这对于长时间运行的任务可能很重要。
* 监视器可以使用动态时间间隔：可以根据某种条件更改等待时间。
* 监视器可以在满足某种条件时终止，或者由其他进程终止。
* 监视器可以采用参数。 此示例演示如何将同一个天气监视进程应用到任何请求的地点和电话号码。
* 监视器可缩放。 由于每个监视器是一个业务流程实例，因此可以创建多个监视器，而无需创建新函数或定义更多的代码。
* 监视器可轻松集成到更大的工作流。 监视器可以是更复杂业务流程函数或[子业务流程](durable-functions-sub-orchestrations.md)的一部分。

## <a name="configuration"></a>Configuration

### <a name="configuring-twilio-integration"></a>配置 Twilio 集成

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

### <a name="configuring-weather-underground-integration"></a>配置 Weather Underground 集成

此示例涉及到使用 Weather Underground API 来检查某个地点的当前天气状况。

首先需要创建一个 Weather Underground 帐户。 可以免费创建一个 [https://www.wunderground.com/signup](https://www.wunderground.com/signup) 。 创建帐户后，需要获取 API 密钥。 可以通过访问 [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api/?MR=1) ，然后选择 "密钥设置" 来执行此操作。 Stratus Developer 计划是免费的，足以用于运行此示例。

获取 API 密钥后，将以下 **应用设置** 添加到函数应用。

| 应用设置名称 | 值说明 |
| - | - |
| **WeatherUndergroundApiKey**  | Weather Underground API 密钥。 |

## <a name="the-functions"></a>函数

本文介绍示例应用中的以下函数：

* `E3_Monitor`：定期调用的业务流程 [协调程序函数](durable-functions-bindings.md#orchestration-trigger) `E3_GetIsClear` 。 如果 `E3_GetIsClear` 返回 true，则此函数会调用 `E3_SendGoodWeatherAlert`。
* `E3_GetIsClear`：一个 [活动函数](durable-functions-bindings.md#activity-trigger) ，用于检查位置的当前天气情况。
* `E3_SendGoodWeatherAlert`：通过 Twilio 发送短信的活动函数。

### <a name="e3_monitor-orchestrator-function"></a>E3_Monitor orchestrator 函数

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=41-78,97-115)]

Orchestrator 需要监视位置，并需要一个电话号码，以便在该位置上是否明确时向发送消息。 此数据作为强类型对象传递到业务流程协调程序 `MonitorRequest` 。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**E3_Monitor** 函数对业务流程协调程序函数使用标准的 *function.json*。

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/function.json)]

实现该函数的代码如下：

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_Monitor/index.js)]

# <a name="python"></a>[Python](#tab/python)
对于 Python 上的监视模式，我们有不同的教程，请参阅 [此处](durable-functions-monitor-python.md)。

---

此业务流程协调程序函数执行以下操作：

1. 获取 **MonitorRequest**，其中包括要监视的地点，以及要将短信通知发送到的电话号码。
2. 确定监视器的过期时间。 为简便起见，本示例使用了硬编码值。
3. 调用 **E3_GetIsClear** 来确定请求的地点是否为晴天。
4. 如果是晴天，则调用 **E3_SendGoodWeatherAlert** 将短信通知发送到请求的电话号码。
5. 创建一个持久计时器，以便在下一个轮询间隔恢复业务流程。 为简便起见，本示例使用了硬编码值。
6. 继续运行，直到当前 UTC 时间经过监视器的过期时间或发送短信警报。

多个 orchestrator 实例可以通过多次调用 orchestrator 函数来运行。 可以指定要监视的地点，以及要将短信提醒发送到的电话号码。 最后请注意，协调器函数在等待计时器时 *未* 运行，因此不会对其收费。
### <a name="e3_getisclear-activity-function"></a>E3_GetIsClear 活动函数

与其他示例一样，帮助器活动函数是使用 `activityTrigger` 触发器绑定的正则函数。 **E3_GetIsClear** 函数使用 Weather Underground API 获取当前天气状况并确定是否为晴天。

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=80-85)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

function.json 定义如下：

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/function.json)]

实现如下。

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_GetIsClear/index.js)]

# <a name="python"></a>[Python](#tab/python)
对于 Python 上的监视模式，我们有不同的教程，请参阅 [此处](durable-functions-monitor-python.md)。

---

### <a name="e3_sendgoodweatheralert-activity-function"></a>E3_SendGoodWeatherAlert 活动函数

**E3_SendGoodWeatherAlert** 函数使用 Twilio 绑定来发送短信，告知最终用户目前的天气适合散步。

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs?range=87-96,140-205)]

> [!NOTE]
> 需要安装 `Microsoft.Azure.WebJobs.Extensions.Twilio` Nuget 包才能运行示例代码。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

该函数的 *function.json* 十分简单：

[!code-json[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/function.json)]

下面是发送短信的代码：

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E3_SendGoodWeatherAlert/index.js)]

# <a name="python"></a>[Python](#tab/python)
对于 Python 上的监视模式，我们有不同的教程，请参阅 [此处](durable-functions-monitor-python.md)。

---

## <a name="run-the-sample"></a>运行示例

使用示例中包含的 HTTP 触发型函数，可以通过发送以下 HTTP POST 请求来启动业务流程：

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "location": { "city": "Redmond", "state": "WA" }, "phone": "+1425XXXXXXX" }
```

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

**E3_Monitor** 实例启动，并查询请求位置的当前天气状况。 如果天气为晴，则调用某个活动函数来发送提醒；否则将设置计时器。 当计时器过期时，业务流程将会恢复。

可以通过在 Azure Functions 门户中查看函数日志，来了解业务流程的活动。

```
2018-03-01T01:14:41.649 Function started (Id=2d5fcadf-275b-4226-a174-f9f943c90cd1)
2018-03-01T01:14:42.741 Started orchestration with ID = '1608200bb2ce4b7face5fc3b8e674f2e'.
2018-03-01T01:14:42.780 Function completed (Success, Id=2d5fcadf-275b-4226-a174-f9f943c90cd1, Duration=1111ms)
2018-03-01T01:14:52.765 Function started (Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb)
2018-03-01T01:14:52.890 Received monitor request. Location: Redmond, WA. Phone: +1425XXXXXXX.
2018-03-01T01:14:52.895 Instantiating monitor for Redmond, WA. Expires: 3/1/2018 7:14:52 AM.
2018-03-01T01:14:52.909 Checking current weather conditions for Redmond, WA at 3/1/2018 1:14:52 AM.
2018-03-01T01:14:52.954 Function completed (Success, Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb, Duration=189ms)
2018-03-01T01:14:53.226 Function started (Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859)
2018-03-01T01:14:53.808 Function completed (Success, Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859, Duration=582ms)
2018-03-01T01:14:53.967 Function started (Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c)
2018-03-01T01:14:53.996 Next check for Redmond, WA at 3/1/2018 1:44:53 AM.
2018-03-01T01:14:54.030 Function completed (Success, Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c, Duration=62ms)
```

业务流程在达到其超时值或检测到清除 skies 时完成。 你还可以在 `terminate` 另一个函数中使用 API，或调用上面202响应中引用的 **TERMINATEPOSTURI** HTTP POST webhook。 若要使用 webhook，请将替换 `{text}` 为提前终止的原因。 HTTP POST URL 将大致如下所示：

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>后续步骤

本示例演示了如何使用 Durable Functions，通过[持久性计时器](durable-functions-timers.md)和条件逻辑来监视外部源的状态。 下一个示例演示如何使用外部事件和[持久计时器](durable-functions-timers.md)处理人工交互。

> [!div class="nextstepaction"]
> [运行人工交互示例](durable-functions-phone-verification.md)
