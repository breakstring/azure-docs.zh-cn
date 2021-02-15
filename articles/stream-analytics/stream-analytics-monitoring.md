---
title: 了解 Azure 流分析中的作业监视
description: 本文介绍如何在 Azure 门户中监视 Azure 流分析作业。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2018
ms.custom: seodec18
ms.openlocfilehash: 5141c7fcfe1128574145930548f41731529c2ad8
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012455"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>了解流分析作业监视以及如何监视查询

## <a name="introduction-the-monitor-page"></a>简介：监视页
Azure 门户提供了可用于监视和排查查询和作业性能问题的关键性能指标。 若要查看这些指标，请浏览到想要查看其指标的流分析作业并查看“概览”页上的“监视”  部分。  

![流分析作业监视链接](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

此窗口如下所示：

![流分析作业监视仪表板](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>可用于流分析的指标
| 指标                 | 定义                               |
| ---------------------- | ---------------------------------------- |
| 积压的输入事件数       | 积压的输入事件的数量。 此指标的非零值意味着作业无法跟上传入事件的数量。 如果此值缓慢增长或始终为非零，则应横向扩展作业。 可以访问[了解和调整流单元](stream-analytics-streaming-unit-consumption.md)了解详细信息。 |
| 数据转换错误数 | 无法转换为预期输出架构的输出事件的数量。 可以将错误策略更改为“删除”，以删除遇到此情况的事件。 |
| 早期输入事件数       | 应用程序时间戳早于其到达时间超过 5 分钟的事件。 |
| 失败的函数请求数 | 失败的 Azure 机器学习函数（如果存在）调用数。 |
| 函数事件数        | 发送到 Azure 机器学习函数（如果存在）的事件数。 |
| 函数请求数      | Azure 机器学习函数（如果存在）的调用数。 |
| 输入反序列化错误       | 不可反序列化的输入事件数。  |
| 输入事件字节数      | 流分析作业收到的数据量（以字节为单位）。 这可以用于验证正在发送到输入源的事件。 |
| 输入事件数           | 从输入事件反序列化的记录数。 此计数不包括导致反序列化错误的传入事件。 在内部恢复和自联接等方案中，流分析可以多次引入相同的事件。 因此，如果作业包含简单的“传递”查询，则建议不要预期输入事件与输出事件的指标相匹配。 |
| 收到的输入源数       | 作业收到的消息数。 对于事件中心，消息是单个 EventData。 对于 Blob，消息是单个 Blob。 请注意，输入源在反序列化之前不计数。 如果存在反序列化错误，则输入源数可能大于输入事件数。 否则，它可能小于或等于输入事件数，因为每条消息可能包含多个事件。 |
| 延迟输入事件数      | 到达时间晚于已配置的延迟到达容错时段的事件。 详细了解 [Azure 流分析事件顺序注意事项](./stream-analytics-time-handling.md)。 |
| 无序事件数    | 收到的无序事件的数目，系统根据事件排序策略来删除这些事件，或者为其提供一个经过调整的时间戳。 这可能会受“无序容错时段”设置的影响。 |
| 输出事件数          | 流分析作业发送到输出目标的数据量，以事件计数来衡量。 |
| 运行时错误         | 与查询处理相关的错误总数（不包括引入事件或输出结果时发现的错误） |
| 流单元利用率 %       | 如果资源利用率持续超过 80%，则水印延迟增加，积压的事件数增加，请考虑增加流单元。 高利用率指示作业使用的资源数接近分配的最大资源数。 |
| 水印延迟       | 作业中所有输出的所有分区之间的最大水印延迟。 |

可以使用这些指标来[监视流分析作业的性能](./stream-analytics-set-up-alerts.md#scenarios-to-monitor)。 

## <a name="customizing-monitoring-in-the-azure-portal"></a>在 Azure 门户中自定义监视
可以在“编辑图表”设置中调整图表类型、显示的指标和时间范围。 有关详细信息，请参阅[如何自定义监视](../azure-monitor/platform/data-platform.md)。

  ![流分析查询监视器时间图](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>最新输出
对作业进行监视时需要关注的另一个数据点是最后的输出的时间（显示在“概述”页面中）。
此时间是作业的最新输出的应用程序时间（即，使用来自事件数据的时间戳的时间）。

## <a name="get-help"></a>获取帮助
如需进一步的帮助，请参阅[有关 Azure 流分析的 Microsoft 问答页](/answers/topics/azure-stream-analytics.html)

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 流分析管理 REST API 参考](/rest/api/streamanalytics/)
