---
title: Application Insights 中的智能检测 - 失败异常 | Microsoft 文档
description: 将针对到 Web 应用的失败请求速率的异常变化向用户发出警报，并提供诊断分析。 无需进行配置。
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yalavi
ms.openlocfilehash: 978b63d74e6be4104ff53eef66e9633c78b90eb8
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510628"
---
# <a name="smart-detection---failure-anomalies"></a>智能检测 - 失败异常
如果 Web 应用的失败请求速率出现异常上升，那么 [Application Insights](./app-insights-overview.md) 会近乎实时地自动通知你。 它会对 HTTP 请求速率或报告为失败的依赖项调用的异常上升进行检测。 对于请求而言，失败的请求通常是响应代码为 400 或更高的请求。 为了帮助会审和诊断问题，通知详情中会提供失败及相关应用程序数据的特征分析。 还提供指向 Application Insights 门户的链接，以供进一步诊断。 该功能不需要任何设置或配置，因为它使用机器学习算法来预测正常的失败率。

此功能适用于在云或你自己的服务器上托管的任何 Web 应用，以及生成应用程序请求或依赖项数据的任何 web 应用。 例如，如果你的辅助角色调用 [TrackRequest()](./api-custom-events-metrics.md#trackrequest) 或 [TrackDependency()](./api-custom-events-metrics.md#trackdependency)。

在设置[适用于项目的 Application Insights](./app-insights-overview.md) 后，如果应用生成特定最低数据量，在进行切换和发送警报前，智能检测失败异常将花费 24 小时来了解应用的正常行为。

下面是一个示例警报：

:::image type="content" source="./media/proactive-failure-diagnostics/013.png" alt-text="显示有关故障的群集分析的示例智能检测警报。" lightbox="./media/proactive-failure-diagnostics/013.png":::

警报详细信息将告诉你：

* 相较于正常应用行为的失败率。
* 受影响的用户数，因此你知道需要有多担心。
* 与失败关联的特征模式。 在此示例中，有特定的响应代码、请求名称（操作）和应用程序版本。 这会立即通知在代码中开始查找的位置。 其他可能性可能是特定的浏览器或客户端操作系统。
* 似乎与特征失败相关联的异常、日志跟踪和依赖项失败（数据库或其他外部组件）。
* 直接指向 Application Insights 中数据的相关搜索的链接。

## <a name="benefits-of-smart-detection"></a>智能检测的优点
普通[指标警报](../platform/alerts-log.md)会通知你可能存在问题。 但是，智能检测将开始诊断工作，并执行以往都需要你自行完成的大量分析。 结果将整齐地打包，以帮助你快速找到问题的根源。

## <a name="how-it-works"></a>工作原理
智能检测可监视从应用收到的数据，特别是失败率。 此规则计算 `Successful request` 属性为 False 的请求数，和 `Successful call` 属性为 False 的依赖项调用数。 对于请求而言，默认情况下，`Successful request == (resultCode < 400)`（除非已将自定义代码写入[筛选器](./api-filtering-sampling.md#filtering)或生成自己的 [TrackRequest](./api-custom-events-metrics.md#trackrequest) 调用）。 

应用性能具有典型的行为模式。 某些请求或依赖项调用更容易出现失败，而且总体失败率可能会随着负载的增加而上升。 智能检测使用机器学习来查找这些异常。

由于数据从 Web 应用提供给 Application Insights，因此智能检测会将当前行为与过去几天看到的模式进行比较。 如果通过与先前性能比较观察到失败率中有异常上升，将触发分析。

分析触发后，服务将对失败的请求执行群集分析，以尝试标识特征化失败的值的模式。 

在上面的示例中，分析发现大多数失败都是关于特定结果代码、请求名称、服务器 URL 主机和角色实例。 

当使用这些调用检测服务时，分析器查找与已标识群集中的请求关联的异常和依赖项失败，以及与这些请求关联的任何跟踪日志的示例。

生成的分析以警报形式发送给用户，除非已将它配置为不这样做。

与[手动设置的警报](../platform/alerts-log.md)一样，你可以检查触发警报的状态（如果问题已修复，则可以解决此问题）。 在 Application Insights 资源的“警报”页配置警报规则。 但与其他警报不同，无需设置或配置智能检测。 如果需要，可以禁用它或更改其目标电子邮件地址。

### <a name="alert-logic-details"></a>警报逻辑详细信息

警报是由我们的专有机器学习算法触发的，因此我们不能共享确切的实现细节。 话虽如此，但我们知道你有时需要详细了解基础逻辑的工作原理。 确定是否应当触发警报时评估的主要因素包括： 

* 对 20 分钟滚动时间窗口中的请求/依赖项的失败百分比进行分析。
* 将过去 20 分钟的失败百分比与过去 40 分钟和过去 7 天的失败率进行比较，并寻找超过标准偏差 x 倍的重大偏差。
* 对最小失败百分比使用自适应限制，该限制根据应用的请求/依赖项的数量而变化。
* 如果 8-24 小时内不再检测到问题，则有逻辑可以自动解决已触发的警报监视器条件。
  注意：在当前设计中。 解决智能检测警报后，将不会发送通知或操作。 可以在 Azure 门户中检查是否已解决智能检测警报。

## <a name="configure-alerts"></a>配置警报

可以从端口或者使用 Azure 资源管理器禁用智能检测警报规则（[请参阅模板示例](./proactive-arm-config.md)）。

该警报规则是使用名为“Application Insights 智能检测”的关联[操作组](../platform/action-groups.md)创建的（该操作组包含电子邮件和 webhook 操作），并且可以进行扩展以在警报触发时触发其他操作。

> [!NOTE]
> 该警报规则现在默认向与订阅的监视查阅者和监视参与者角色关联的用户发送电子邮件通知。 [此处](./proactive-email-notification.md)介绍了有关详细信息。
> 通过该警报规则发送的通知需遵循[常见警报架构](../platform/alerts-common-schema.md)。
>

打开“警报”页。 包括失败异常警报规则以及已手动设置的任何警报，并可以查看其当前是否处于警报状态。

:::image type="content" source="./media/proactive-failure-diagnostics/021.png" alt-text="在 Application Insights 资源页上，单击“警报”磁贴，然后单击“管理警报规则”。" lightbox="./media/proactive-failure-diagnostics/021.png":::

单击警报以配置它。

:::image type="content" source="./media/proactive-failure-diagnostics/032.png" alt-text="规则配置屏幕。" lightbox="./media/proactive-failure-diagnostics/032.png":::

请注意，你可以禁用或删除失败异常警报规则，但不能在同一 Application Insights 资源上创建另一个同样的规则。

## <a name="example-of-failure-anomalies-alert-webhook-payload"></a>失败异常警报 Webhook 有效负载示例

```json
{
    "properties": {
        "essentials": {
            "severity": "Sev3",
            "signalType": "Log",
            "alertState": "New",
            "monitorCondition": "Resolved",
            "monitorService": "Smart Detector",
            "targetResource": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule",
            "targetResourceName": "test-rule",
            "targetResourceGroup": "test-group",
            "targetResourceType": "microsoft.insights/components",
            "sourceCreatedId": "1a0a5b6436a9b2a13377f5c89a3477855276f8208982e0f167697a2b45fcbb3e",
            "alertRule": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.alertsmanagement/smartdetectoralertrules/failure anomalies - test-rule",
            "startDateTime": "2019-10-30T17:52:32.5802978Z",
            "lastModifiedDateTime": "2019-10-30T18:25:23.1072443Z",
            "monitorConditionResolvedDateTime": "2019-10-30T18:25:26.4440603Z",
            "lastModifiedUserName": "System",
            "actionStatus": {
                "isSuppressed": false
            },
            "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls."
        },
        "context": {
            "DetectionSummary": "An abnormal rise in failed request rate",
            "FormattedOccurenceTime": "2019-10-30T17:50:00Z",
            "DetectedFailureRate": "50.0% (200/400 requests)",
            "NormalFailureRate": "0.0% (over the last 30 minutes)",
            "FailureRateChart": [
                [
                    "2019-10-30T05:20:00Z",
                    0
                ],
                [
                    "2019-10-30T05:40:00Z",
                    100
                ],
                [
                    "2019-10-30T06:00:00Z",
                    0
                ],
                [
                    "2019-10-30T06:20:00Z",
                    0
                ],
                [
                    "2019-10-30T06:40:00Z",
                    100
                ],
                [
                    "2019-10-30T07:00:00Z",
                    0
                ],
                [
                    "2019-10-30T07:20:00Z",
                    0
                ],
                [
                    "2019-10-30T07:40:00Z",
                    100
                ],
                [
                    "2019-10-30T08:00:00Z",
                    0
                ],
                [
                    "2019-10-30T08:20:00Z",
                    0
                ],
                [
                    "2019-10-30T08:40:00Z",
                    100
                ],
                [
                    "2019-10-30T17:00:00Z",
                    0
                ],
                [
                    "2019-10-30T17:20:00Z",
                    0
                ],
                [
                    "2019-10-30T09:00:00Z",
                    0
                ],
                [
                    "2019-10-30T09:20:00Z",
                    0
                ],
                [
                    "2019-10-30T09:40:00Z",
                    100
                ],
                [
                    "2019-10-30T10:00:00Z",
                    0
                ],
                [
                    "2019-10-30T10:20:00Z",
                    0
                ],
                [
                    "2019-10-30T10:40:00Z",
                    100
                ],
                [
                    "2019-10-30T11:00:00Z",
                    0
                ],
                [
                    "2019-10-30T11:20:00Z",
                    0
                ],
                [
                    "2019-10-30T11:40:00Z",
                    100
                ],
                [
                    "2019-10-30T12:00:00Z",
                    0
                ],
                [
                    "2019-10-30T12:20:00Z",
                    0
                ],
                [
                    "2019-10-30T12:40:00Z",
                    100
                ],
                [
                    "2019-10-30T13:00:00Z",
                    0
                ],
                [
                    "2019-10-30T13:20:00Z",
                    0
                ],
                [
                    "2019-10-30T13:40:00Z",
                    100
                ],
                [
                    "2019-10-30T14:00:00Z",
                    0
                ],
                [
                    "2019-10-30T14:20:00Z",
                    0
                ],
                [
                    "2019-10-30T14:40:00Z",
                    100
                ],
                [
                    "2019-10-30T15:00:00Z",
                    0
                ],
                [
                    "2019-10-30T15:20:00Z",
                    0
                ],
                [
                    "2019-10-30T15:40:00Z",
                    100
                ],
                [
                    "2019-10-30T16:00:00Z",
                    0
                ],
                [
                    "2019-10-30T16:20:00Z",
                    0
                ],
                [
                    "2019-10-30T16:40:00Z",
                    100
                ],
                [
                    "2019-10-30T17:30:00Z",
                    50
                ]
            ],
            "ArmSystemEventsRequest": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourceGroups/test-group/providers/microsoft.insights/components/test-rule/query?query=%0d%0a++++++++++++++++systemEvents%0d%0a++++++++++++++++%7c+where+timestamp+%3e%3d+datetime(%272019-10-30T17%3a20%3a00.0000000Z%27)+%0d%0a++++++++++++++++%7c+where+itemType+%3d%3d+%27systemEvent%27+and+name+%3d%3d+%27ProactiveDetectionInsight%27+%0d%0a++++++++++++++++%7c+where+dimensions.InsightType+in+(%275%27%2c+%277%27)+%0d%0a++++++++++++++++%7c+where+dimensions.InsightDocumentId+%3d%3d+%27718fb0c3-425b-4185-be33-4311dfb4deeb%27+%0d%0a++++++++++++++++%7c+project+dimensions.InsightOneClassTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightExceptionCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightDependencyCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightRequestCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightTraceCorrelationTable%0d%0a++++++++++++&api-version=2018-04-20",
            "LinksTable": [
                {
                    "Link": "<a href=\"https://portal.azure.com/#blade/AppInsightsExtension/ProactiveDetectionFeedBlade/ComponentId/{\"SubscriptionId\":\"4f9b81be-fa32-4f96-aeb3-fc5c3f678df9\",\"ResourceGroup\":\"test-group\",\"Name\":\"test-rule\"}/SelectedItemGroup/718fb0c3-425b-4185-be33-4311dfb4deeb/SelectedItemTime/2019-10-30T17:50:00Z/InsightType/5\" target=\"_blank\">View full details in Application Insights</a>"
                }
            ],
            "SmartDetectorId": "FailureAnomaliesDetector",
            "SmartDetectorName": "Failure Anomalies",
            "AnalysisTimestamp": "2019-10-30T17:52:32.5802978Z"
        },
        "egressConfig": {
            "displayConfig": [
                {
                    "rootJsonNode": null,
                    "sectionName": null,
                    "displayControls": [
                        {
                            "property": "DetectionSummary",
                            "displayName": "What was detected?",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "property": "FormattedOccurenceTime",
                            "displayName": "When did this occur?",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "property": "DetectedFailureRate",
                            "displayName": "Detected failure rate",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "property": "NormalFailureRate",
                            "displayName": "Normal failure rate",
                            "type": "Text",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "chartType": "Line",
                            "xAxisType": "Date",
                            "yAxisType": "Percentage",
                            "xAxisName": "",
                            "yAxisName": "",
                            "property": "FailureRateChart",
                            "displayName": "Failure rate over last 12 hours",
                            "type": "Chart",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "defaultLoad": true,
                            "displayConfig": [
                                {
                                    "rootJsonNode": null,
                                    "sectionName": null,
                                    "displayControls": [
                                        {
                                            "showHeader": false,
                                            "columns": [
                                                {
                                                    "property": "Name",
                                                    "displayName": "Name"
                                                },
                                                {
                                                    "property": "Value",
                                                    "displayName": "Value"
                                                }
                                            ],
                                            "property": "tables[0].rows[0][0]",
                                            "displayName": "All of the failed requests had these characteristics:",
                                            "type": "Table",
                                            "isOptional": false,
                                            "isPropertySerialized": true
                                        }
                                    ]
                                }
                            ],
                            "property": "ArmSystemEventsRequest",
                            "displayName": "",
                            "type": "ARMRequest",
                            "isOptional": false,
                            "isPropertySerialized": false
                        },
                        {
                            "showHeader": false,
                            "columns": [
                                {
                                    "property": "Link",
                                    "displayName": "Link"
                                }
                            ],
                            "property": "LinksTable",
                            "displayName": "Links",
                            "type": "Table",
                            "isOptional": false,
                            "isPropertySerialized": false
                        }
                    ]
                }
            ]
        }
    },
    "id": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule/providers/Microsoft.AlertsManagement/alerts/7daf8739-ca8a-4562-b69a-ff28db4ba0a5",
    "type": "Microsoft.AlertsManagement/alerts",
    "name": "Failure Anomalies - test-rule"
}
```

## <a name="triage-and-diagnose-an-alert"></a>会审和诊断警报

警报指示已检测到失败请求中有异常上升。 应用或其环境很可能存在某些问题。

若要进一步调查，单击该页面的“查看 Application Insights 中的完整详细信息”链接可直接转到[搜索页](./diagnostic-search.md)，该页面已针对相关请求、异常、依赖项或跟踪进行筛选。 

你还可以打开 [Azure 门户](https://portal.azure.com)，导航到应用的 Application Insights 资源并打开“失败”页。

单击“诊断失败”有助于获取更多信息并解决问题。

:::image type="content" source="./media/proactive-failure-diagnostics/051.png" alt-text="诊断搜索。" lightbox="./media/proactive-failure-diagnostics/051.png#lightbox":::

根据请求百分比和受影响用户数，可以确定问题的紧急程度。 在上面的示例中，将 78.5% 的失败率与 2.2% 的正常失败率比较，说明有一些不好的情况正在发生。 另一方面，只有 46 位用户受到影响。 如果它是你的应用，你能够评估情况的严重性。

在许多情况下，能够从提供的请求名称、异常、依赖项失败和跟踪数据快速诊断问题。

在此示例中，由于达到请求限制，SQL 数据库中出现异常。

:::image type="content" source="./media/proactive-failure-diagnostics/052.png" alt-text="失败请求的详细信息。" lightbox="./media/proactive-failure-diagnostics/052.png":::

## <a name="review-recent-alerts"></a>查看最近的警报

在 Application Insights 资源页面单击“警报”以获取最新触发的警报：

:::image type="content" source="./media/proactive-failure-diagnostics/070.png" alt-text="警报摘要。" lightbox="./media/proactive-failure-diagnostics/070.png":::

## <a name="whats-the-difference-"></a>区别是什么...
智能检测失败异常对其他类似但又不同的 Application Insight 功能进行补充。

* [指标警报](../platform/alerts-log.md)由你设置，并且可监视各种指标，如 CPU 占用率、请求速率、页面加载时间等。 可以将它们用于发出警告，例如在需要添加更多资源时。 相比之下，智能检测失败异常涵盖小范围的关键指标（当前仅失败请求速率），设计成一旦 Web 应用的失败请求速率相较于 Web 应用的正常行为而言增加，便会以近乎实时的方式通知你。 与指标警报不同，智能检测会自动设置和更新行为中响应更改的阈值。 智能检测还会启动诊断工作，从而为你节省解决问题的时间。

* [智能检测性能异常](proactive-performance-diagnostics.md)还使用计算机智能发现指标中的异常模式，使得你无需执行任何配置。 但与智能检测失败异常不同，智能检测性能异常的目的是查找可能不能提供很好服务的使用情况复写体分段，例如通过特定类型浏览器上的特定页面。 将每日执行分析，如果找到任何结果，则很可能紧急程度远低于警报。 相比之下，会对传入的应用程序数据连续执行失败异常分析，如果服务器失败率超出预期值，会在几分钟内通知你。

## <a name="if-you-receive-a-smart-detection-alert"></a>如果收到智能检测警报
*为什么会收到此警报？*

* 我们检测到与之前一段时间的正常基线相比，失败请求率中出现异常上升。 对失败和关联应用程序数据进行分析后，我们认为存在问题并且你应当给予关注。

*通知是否表示肯定存在问题？*

* 我们尝试针对应用中断或降级发出警报，但只有可以完全了解语义以及对应用或用户的影响。

*那么，你是否正在查看我的应用程序数据？*

* 不是。 该服务完全是自动的。 只有你会收到通知。 数据是[私有](./data-retention-privacy.md)数据。

*是否需要订阅此警报？*

* 不是。 发送请求数据的每个应用程序都有智能检测警报规则。

*是否可以取消订阅或者获取已发送至同事的通知？*

* 是，在“警报”规则中，单击“智能检测”规则可配置它。 可以禁用警报，或更改警报的收件人。

*我丢失了电子邮件。在哪里可以找到门户中的通知？*

* 在活动日志中。 在 Azure 中，打开应用的 Application Insights 资源，并选择“活动日志”。

*一些警报关于已知问题，我不希望接收它们。*

* 你可以使用[警报操作规则](../platform/alerts-action-rules.md)抑制功能。

## <a name="next-steps"></a>后续步骤
这些诊断工具可帮助检查应用中的数据：

* [指标资源管理器](../platform/metrics-charts.md)
* [搜索资源管理器](./diagnostic-search.md)
* [分析 - 功能强大的查询语言](../log-query/log-analytics-tutorial.md)

智能检测是自动执行的。 但是或许你想要设置更多的警报？

* [手动配置的指标警报](../platform/alerts-log.md)
* [可用性 Web 测试](./monitor-web-app-availability.md)