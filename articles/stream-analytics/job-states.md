---
title: Azure 流分析作业状态
description: 本文介绍了流分析作业的四种不同状态：正在运行、已停止、已降级和失败。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: dd298fae148b9e1e51072e98a4e0e5ece10967f3
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020445"
---
# <a name="azure-stream-analytics-job-states"></a>Azure 流分析作业状态

在任何给定时间，流分析作业可以是四种状态之一：正在运行、已停止、已降级或失败。 可以在 Azure 门户的流分析作业的“概览”页上找到作业的状态。 

| 状态 | 说明 | 建议的操作 |
| --- | --- | --- |
| **正在运行** | 作业在来自已定义输入源的 Azure 读取事件上运行，对其进行处理并将结果写入配置的输出接收器。 | 最佳做法是通过监视[关键指标](./stream-analytics-set-up-alerts.md#scenarios-to-monitor)来跟踪作业的性能。 |
| **已停止** | 作业已停止，不处理事件。 | 不可用 | 
| **已降级** | 输入和输出连接可能存在间歇性问题。 这些错误称为暂时性错误，可能会使作业进入“已降级”状态。 流分析会立即尝试从此类错误恢复，然后回到“正在运行”状态（在数分钟内）。 之所以发生这些错误，可能是因为网络问题、其他 Azure 资源的可用性问题、反序列化错误，等等。当作业处于降级状态时，其性能可能受影响。| 可以查看[诊断或活动日志](./stream-analytics-job-diagnostic-logs.md#debugging-using-activity-logs)，详细了解这些暂时性错误的原因。 如果发生此类反序列化错误，建议采取纠正措施，确保不会导致事件异常。 如果作业总是达到资源使用率限制，可尝试提高 SU 数或[将作业并行化](./stream-analytics-parallelization.md)。 在其他情况下，如果你无法采取任何措施，流分析会尝试恢复到“正在运行”状态。 <br> 可以使用[水印延迟](./stream-analytics-set-up-alerts.md#scenarios-to-monitor)指标来了解这些暂时性错误是否会影响作业的性能。|
| 失败 | 作业遇到严重错误，导致状态变为“失败”。 事件无法读取和处理。 作业最终处于失败状态的常见原因是运行时错误。 | 可以[配置警报](./stream-analytics-set-up-alerts.md#set-up-alerts-in-the-azure-portal)，这样当作业进入“失败”状态时，系统会通知你。 <br> <br>可以使用[活动和资源日志](./stream-analytics-job-diagnostic-logs.md#debugging-using-activity-logs)进行调试，以便确定根本原因并解决问题。|

## <a name="next-steps"></a>后续步骤
* [为 Azure 流分析作业设置警报](stream-analytics-set-up-alerts.md)
* [可以在流分析中使用的指标](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics)
* [使用活动和资源日志排除故障](./stream-analytics-job-diagnostic-logs.md)