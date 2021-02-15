---
title: 估算 Azure Functions 中的消耗计划成本
description: 了解如何更好地估算在 Azure 的消耗计划中运行函数应用时可能产生的成本。
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 430804d478df718f51ae1da9adb6693f597157a9
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934877"
---
# <a name="estimating-consumption-plan-costs"></a>估算消耗计划成本

对于在 Azure Functions 中运行的应用，目前有三种类型的托管计划，每种计划都有自己的定价模型： 

| 计划 | 说明 |
| ---- | ----------- |
| [**消耗**](consumption-plan.md) | 只根据函数应用的运行时间收费。 此计划包括基于订阅的[免费授予][定价页]。|
| [**高级**](functions-premium-plan.md) | 提供与“消耗”计划相同的功能和缩放机制，但具有增强的性能和 VNET 访问权限。 成本取决于所选的定价层。 若要了解详细信息，请参阅 [Azure Functions 高级计划](functions-premium-plan.md)。 |
| [**专用（应用服务）**](dedicated-plan.md) <br/>（基本或更高层） | 需要在专用 VM 或隔离的环境中运行、自定义映像，或想要使用超额的应用服务计划容量时。 使用[常规应用服务计划计费](https://azure.microsoft.com/pricing/details/app-service/)。 成本取决于所选的定价层。|

选择对函数性能和成本要求最有利的计划。 若要了解详细信息，请参阅 [Azure Functions 的缩放和托管](functions-scale.md)。

本文仅涉及到消耗计划，因为此计划产生可变的成本。 本文将取代[消耗计划成本计费常见问题解答](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)一文。

Durable Functions 也可以在消耗计划中运行。 若要详细了解使用 Durable Functions 时的成本注意事项，请参阅 [Durable Functions 计费](./durable/durable-functions-billing.md)。

## <a name="consumption-plan-costs"></a>消耗计划成本

单个函数执行的执行成本以“GB 秒”来度量。   执行成本是通过将其内存用量与执行时间相结合计算得出的。 函数的运行时间越长，其成本越高；同理，函数消耗的内存越多，其成本越高。 

假设函数使用的内存量保持恒定。 在这种情况下，进行简单的相乘即可计算成本。 例如，假设函数运行了 3 秒，消耗了 0.5 GB。 那么，执行成本为 `0.5GB * 3s = 1.5 GB-seconds`。 

由于内存用量会不断变化，计算结果实质上是不同时间的内存用量的积分。  进行这种计算时，系统会按固定的时间间隔对进程（及其子进程）的内存用量采样。 如[定价页]中所述，内存用量将向上舍入到最近似的 128-MB 桶。 如果进程使用 160 MB，则你需要按 256 MB 的用量付费。 计算中会考虑并发性，即，同一进程中的多个并发函数执行。

> [!NOTE]
> 尽管执行成本中不直接考虑 CPU 用量，但如果该用量会影响函数的执行时间，则也会影响成本。

对于 HTTP 触发的函数，在函数代码开始执行之前发生错误时，不会收取执行费用。 这意味着由于 API 密钥验证或应用服务身份验证/授权功能，来自平台的 401 响应不会计入你的执行成本。 同样，在函数处理请求之前，5xx 状态代码响应在平台中发生时不会计入成本。 在函数代码开始执行后，平台生成的 5xx 响应仍算作执行，即使函数代码未引发错误。

## <a name="other-related-costs"></a>其他相关成本

在估算任何计划中运行函数的总体成本时，请记住，函数运行时将使用其他多个 Azure 服务，而每个服务单独计费。 计算函数应用的定价时，对于与其他 Azure 服务集成的任何触发器和绑定，需要创建这些附加的服务并为其付费。 

对于在消耗计划中运行的函数，总成本是函数的执行成本加上带宽和附加服务的成本。 

估算函数应用和相关服务的总体成本时，请使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=functions)。 

| 相关成本 | 描述 |
| ------------ | ----------- |
| **存储帐户** | 需要为每个函数应用提供一个关联的常规用途 [Azure 存储帐户](../storage/common/storage-introduction.md#types-of-storage-accounts)，该帐户[单独计费](https://azure.microsoft.com/pricing/details/storage/)。 函数运行时在内部使用此帐户，但你也可以将其用于存储触发器和绑定。 如果你没有存储帐户，系统会在创建函数应用时创建一个存储帐户。 有关详细信息，请参阅[存储帐户要求](storage-considerations.md#storage-account-requirements)。|
| **Application Insights** | 函数依靠 [Application Insights](../azure-monitor/app/app-insights-overview.md) 为函数应用提供高性能的监视体验。 虽然未强制要求，但你应[启用 Application Insights 集成](configure-monitoring.md#enable-application-insights-integration)。 每月会免费授予遥测数据。 要了解详细信息，请参阅 [Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)。 |
| **网络带宽** | 无需为同一区域中的 Azure 服务之间的数据传输付费。 但是，将数据出站传输到另一区域或 Azure 外部可能会产生费用。 有关详细信息，请参阅[带宽定价详细信息](https://azure.microsoft.com/pricing/details/bandwidth/)。 |

## <a name="behaviors-affecting-execution-time"></a>影响执行时间的行为

函数的以下行为可能会影响执行时间：

+ **触发器和绑定**：从 [函数绑定](functions-triggers-bindings.md)读取输入以及向其写入输出所花费的时间计为执行时间。 例如，如果函数使用某个输出绑定将消息写入 Azure 存储队列，则执行时间包括将该消息写入该队列所花费的时间，而函数成本计算包括该写入时间。 

+ **异步执行**：函数等待异步请求（在 C# 中为 `await`）结果的时间计为执行时间。 “GB 秒”计算基于函数的开始和结束时间，以及该时间段内的内存用量。 计算中不考虑该时间段内发生的 CPU 活动。 也许可以使用 [Durable Functions](durable/durable-functions-overview.md) 来降低异步操作期间产生的成本。 业务流程协调程序函数中的等待时间不产生费用。

## <a name="viewing-cost-related-data"></a>查看与成本相关的数据

在 [您的发票](../cost-management-billing/understand/download-azure-invoice.md)中，可以查看所有执行的与成本相关的数据 **-函数** 和 **执行时间-函数** 以及实际计费的成本。 但是，此发票数据是过去发票周期的每月聚合。 

### <a name="function-app-level-metrics"></a>函数应用级指标

若要更好地了解函数对成本的影响，可以使用 Azure Monitor 查看函数应用当前生成的成本相关指标。 可以使用 [Azure 门户]中的 [Azure Monitor 指标资源管理器](../azure-monitor/platform/metrics-getting-started.md)或使用 REST API 来获取此数据。

#### <a name="monitor-metrics-explorer"></a>Monitor 指标资源管理器

使用 [Azure Monitor 指标资源管理器](../azure-monitor/platform/metrics-getting-started.md)可以图形格式查看消耗计划函数应用的成本相关数据。 

1. 在 [Azure 门户]顶部的“搜索服务、资源和文档”中搜索 `monitor`，然后选择“服务”下的“Monitor”。

1. 在左侧选择“指标” > “选择资源”，然后使用图像下方的设置选择你的函数应用。 

    ![选择函数应用资源](media/functions-consumption-costing/select-a-resource.png)

      
    |设置  |建议的值  |说明  |
    |---------|---------|---------|
    | 订阅    |  订阅  | 包含你的函数应用的订阅。  |
    | 资源组     | 你的资源组  | 包含你的函数应用的资源组。   |
    | 资源类型     |  应用服务 | 函数应用将作为应用服务实例显示在 Monitor 中。 |
    | 资源     |  你的函数应用  | 要监视的函数应用。        |

1. 选择“应用”以选择你的函数应用作为要监视的资源。

1. 在“指标”中，为“聚合”选择“函数执行计数”和“求和”。 这会在图表中将所选时间段内的执行计数之和相加。

    ![定义要添加到图表中的函数应用指标](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. 选择“添加指标”并重复步骤 2-4，将“函数执行单位”添加到图表中。 

生成的图表包含所选时间范围内（在本例中为 2 小时）两个执行指标的总和。

![函数执行计数和执行单位图表](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

由于执行单位数远远大于执行计数，因此图表只显示执行单位。

此图显示两小时时间段内总共消耗了 11.1 亿个 `Function Execution Units`（以“MB 毫秒”度量）。 若要转换为 GB 秒，请除以 1024000。 在此示例中，函数应用消耗了 `1110000000 / 1024000 = 1083.98` GB 秒。 可将此值乘以 [Functions 定价页][定价页]上的当前执行时间价格，得出这两个小时的成本（假设已用完了所有免费授予的执行时间）。 

#### <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/) 提供了用于检索指标的命令。 可以从本地命令环境使用 CLI，也可以直接从门户使用 [Azure Cloud Shell](../cloud-shell/overview.md)。 例如，以下 [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) 命令返回以前使用的同一时间段内的每小时数据。

请务必将 `<AZURE_SUBSCRIPTON_ID>` 替换为运行该命令的 Azure 订阅 ID。

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

此命令返回类似于以下示例的 JSON 有效负载：

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
此特定响应显示，从 `2019-09-11T21:46` 到 `2019-09-11T23:18`，应用消耗了 1110000000 MB 毫秒（1083.98 GB 秒）。

### <a name="function-level-metrics"></a>函数级指标

函数执行单位是执行时间与内存用量的组合，因此很难使用此指标来了解内存用量。 目前无法通过 Azure Monitor 获取内存数据这一指标。 但是，如果要优化应用的内存用量，可以使用 Application Insights 收集的性能计数器数据。  

如果尚未执行此操作，你可以[在函数应用中启用 Application Insights](configure-monitoring.md#enable-application-insights-integration)。 启用此集成后，你可以[在门户中查询此遥测数据](analyze-telemetry-data.md#query-telemetry-data)。 

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解监视函数应用](functions-monitoring.md)

[定价页]:https://azure.microsoft.com/pricing/details/functions/
[Azure 门户]: https://portal.azure.com
