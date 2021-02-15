---
title: 为 Azure 流分析作业设置监视警报
description: 本文介绍如何使用 Azure 门户为 Azure 流分析作业设置监视和警报。
author: jseb225
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.custom: contperf-fy21q1
ms.date: 06/21/2019
ms.openlocfilehash: 7884f8baa24180fcb94f77a45c3457ba62d3f351
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018133"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>为 Azure 流分析作业设置警报

必须监视 Azure 流分析作业，以确保作业持续正常运行。 本文介绍如何针对要监视的常见方案设置警报。 

可以通过门户或[以编程方式](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a)基于操作日志数据定义指标的规则。

## <a name="set-up-alerts-in-the-azure-portal"></a>在 Azure 门户中设置警报
### <a name="get-alerted-when-a-job-stops-unexpectedly"></a>作业意外停止时将收到警报

以下示例演示如何针对作业进入失败状态设置警报。 建议对所有作业设置此警报。

1. 在 Azure 门户中，打开要为其创建警报的流分析作业。

2. 在“作业”页上，导航到“监视”部分。   

3. 选择“指标”，然后选择“新建警报规则”。 

   ![Azure 门户流分析警报设置](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. 流分析作业名称应会自动显示在“资源”下。 单击“添加条件”，然后选择“配置信号逻辑”下的“所有管理操作”。  

   ![选择流分析警报的信号名称](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. 在“配置信号逻辑”下，将“事件级别”更改为“所有”，将“状态”更改为“失败”。     将“事件发起者”保留空白，然后选择“完成”。 

   ![配置流分析警报的信号逻辑](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. 选择现有的操作组或创建新组。 本示例创建了名为 **TIDashboardGroupActions** 的新操作组，其中包含一个“电子邮件”操作，该操作可将电子邮件发送到具有“所有者”Azure 资源管理器角色的用户。 

   ![为 Azure 流分析作业设置警报](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. “资源”、“条件”和“操作组”都应该有对应的条目。   请注意，为了触发警报，需要满足所定义的条件。 例如，可以每 5 分钟检测一次某个指标在过去 15 分钟的平均值。

   ![屏幕截图显示了包含“资源”、“条件”和“操作组”的“创建规则”对话框。](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   在“警报详细信息”中添加 **警报规则名称**、**说明** 和 **资源组**，然后单击“创建警报规则”创建流分析作业的规则。 

   ![屏幕截图显示了带有“警报详细信息”的“创建规则”对话框。](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>要监视的方案

建议监视以下警报，以了解流分析作业的性能。 在过去 5 分钟时段内，应每隔一分钟评估这些指标。

|指标|条件|时间聚合|阈值|纠正措施|
|-|-|-|-|-|
|SU% 利用率|大于|最大值|80|有多个因素可以提高 SU% 利用率。 可以使用查询并行化进行缩放，或者增加流单元数。 有关详细信息，请参阅[利用 Azure 流分析中的查询并行化](stream-analytics-parallelization.md)。|
|运行时错误|大于|总计|0|检查活动或资源日志，并对输入、查询或输出进行相应更改。|
|水印延迟|大于|最大值|当此指标在过去 15 分钟的平均值大于延迟容限（以秒为单位）时。 如果未修改延迟容限，默认值将设置为 5 秒。|尝试增加 SU 数量或将查询并行化。 有关 SU 的详细信息，请参阅[了解和调整流单元](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job)。 有关并行化查询的详细信息，请参阅[利用 Azure 流分析中的查询并行化](stream-analytics-parallelization.md)。|
|输入反序列化错误|大于|总计|0|检查活动或资源日志，并对输入进行相应更改。 有关资源日志的详细信息，请参阅[使用资源日志排查 Azure 流分析问题](stream-analytics-job-diagnostic-logs.md)|

## <a name="next-steps"></a>后续步骤

* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](/stream-analytics-query/stream-analytics-query-language-reference)