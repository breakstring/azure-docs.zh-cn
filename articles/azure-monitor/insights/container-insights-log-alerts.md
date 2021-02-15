---
title: 用于容器的 Azure Monitor 中的日志警报 | Microsoft Docs
description: 本文介绍如何从用于容器的 Azure Monitor 中创建内存和 CPU 使用率的自定义日志警报。
ms.topic: conceptual
ms.date: 01/05/2021
ms.openlocfilehash: 131f5ebc0f72afce381b4b82d6fe50a5d5e37123
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901498"
---
# <a name="how-to-create-log-alerts-from-azure-monitor-for-containers"></a>如何从用于容器的 Azure Monitor 中创建日志警报

用于容器的 Azure Monitor 可监视部署到托管或自托管 Kubernetes 群集的容器工作负载的性能。 为了针对重要内容发出警报，本文介绍如何在以下场景中使用 AKS 群集创建基于日志的警报：

- 当群集节点上的 CPU 或内存利用率超过阈值时
- 当控制器中任何容器上的 CPU 或内存利用率超过阈值时（与相应资源中设置的限制相比）
- “未就绪”状态节点计数
- “失败”、“挂起”、“未知”、“正在运行”或“成功”Pod 阶段计数    
- 当群集节点上的可用磁盘空间超过阈值时

若要针对群集节点上的 CPU 或内存利用率过高或可用磁盘空间不足发出警报，请使用提供的查询来创建指标警报或指标度量警报。 虽然指标警报的延迟比日志警报低，但日志警报提供了高级查询和更复杂的功能。 日志警报查询通过使用 now 运算符并将时间往过去推一个小时，将某个日期/时间与当前时间进行比较。 （用于容器的 Azure Monitor 以协调世界时 (UTC) 格式存储所有日期。）

如果你不熟悉 Azure Monitor 警报，请在开始之前参阅 [Microsoft Azure 中的警报概述](../platform/alerts-overview.md)。 若要详细了解使用日志查询的警报，请参阅 [Azure Monitor 中的日志警报](../platform/alerts-unified-log.md)。 有关指标警报的详细信息，请参阅 [Azure Monitor 中的指标警报](../platform/alerts-metric-overview.md)。

## <a name="resource-utilization-log-search-queries"></a>资源利用率日志搜索查询

本部分所述的查询支持每种警报方案。 本文[创建警报](#create-an-alert-rule)部分的步骤 7 中使用了这些查询。

以下查询每隔一分钟计算平均 CPU 利用率作为成员节点的平均 CPU 利用率。  

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

以下查询每隔一分钟计算平均内存利用率作为成员节点的平均内存利用率。

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```
>[!IMPORTANT]
>以下查询使用占位符值 \<your-cluster-name> 和 \<your-controller-name> 来表示群集和控制器。 设置警报时，请将这些占位符替换为环境特定的值。

以下查询每隔一分钟计算控制器中所有容器的平均 CPU 利用率，作为控制器中每个容器实例的平均 CPU 利用率。 度量值是针对容器设置的限制百分比。

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

以下查询每隔一分钟计算控制器中所有容器的平均内存利用率，作为控制器中每个容器实例的平均内存利用率。 度量值是针对容器设置的限制百分比。

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

以下查询返回处于“就绪”和“未就绪”状态的所有节点和计数。 

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
以下查询基于所有阶段返回 Pod 阶段计数：“失败”、“挂起”、“未知”、“正在运行”或“成功”。      

```kusto
let endDateTime = now(); 
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | summarize PodStatus=any(PodStatus) by TimeGenerated, PodUid, ClusterName
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>若要针对特定的 Pod 阶段（例如“挂起”、“失败”或“未知”）发出警报，请修改查询的最后一行。   例如，若要针对“失败计数”发出警报，请使用： <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

以下查询返回可用空间超过 90% 的已用群集节点磁盘。 若要获取群集 ID，请首先运行以下查询并从 `ClusterId` 属性中复制值：

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>创建警报规则

本部分逐步介绍如何使用用于容器的 Azure Monitor 提供的性能数据来创建指标度量预警规则。 可以将此基本过程与各种日志查询一起使用，以发出针对不同性能计数器的警报。 首先使用前面提供的某个日志搜索查询。 若要使用 ARM 模板进行创建，请参阅[使用 Azure 资源模板创建日志警报的示例](../platform/alerts-log-create-templates.md)。

>[!NOTE]
>遵循以下过程针对容器资源利用率创建警报规则需要根据[切换日志警报的 API 首选项](../platform/alerts-log-api-switch.md)中所述，切换到新的日志警报 API。
>

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在 Azure 门户中，搜索并选择“Log Analytics 工作区”。
3. 在 Log Analytics 工作区列表中，选择支持“用于容器的 Azure Monitor”的工作区。 
4. 在左侧窗格中，选择“日志”，打开“Azure Monitor 日志”页面。 可以使用此页面来编写和执行 Azure 日志查询。
5. 在“日志”页面上，将先前提供的某个[查询](#resource-utilization-log-search-queries)粘贴到“搜索查询”字段中，然后选择“运行”以验证结果  。 如果不执行此步骤，则无法选择“+ 新警报”选项。
6. 选择“+ 新警报”，创建日志警报。
7. 在“条件”部分，选择预定义的自定义日志条件“每当自定义日志搜索为 \<logic undefined> 时” 。 系统会自动选择“自定义日志搜索”信号类型，因为我们要直接从 Azure Monitor 日志页创建警报规则。  
8. 将前面提供的某个[查询](#resource-utilization-log-search-queries)粘贴到“搜索查询”字段中。
9. 按如下所述配置警报：

    1. 从“基于”下拉列表中选择“指标度量” 。 指标度量将为查询中其值超过指定阈值的每个对象创建一个警报。
    1. 对于“条件”，选择“大于”，并输入 **75** 作为 CPU 和内存利用率警报的初始基线 **阈值**。 对于磁盘空间不足警报，输入 **90**。 或输入符合条件的其他值。
    1. 在“触发警报的条件”部分选择“连续违规”。  从下拉列表中选择“大于”并输入 **2**。
    1. 若要针对容器 CPU 或内存利用率配置警报，请在“聚合依据”下选择“容器名称”。  若要配置群集节点磁盘不足警报，请选择 **ClusterId**。
    1. 在“评估依据”部分，将“时段”值设置为 **60 分钟**。  该规则将每隔 5 分钟运行一次，返回从当前时间算起过去一小时内创建的记录。 将时段设置为较宽的时限可以适应潜在的数据延迟。 这也可以确保查询返回数据，以避免漏报，导致警报永远不会激发。

10. 选择“完成”以完成警报规则。
11. 在“警报规则名称”字段中输入一个名称。 填写“说明”以提供有关该警报的详细信息。 从提供的选项中选择适当的严重性级别。
12. 若要立即激活该警报规则，请接受“创建后启用规则”选项的默认值。
13. 选择现有的 **操作组** 或创建新组。 此步骤确保每次触发警报时都执行相同的操作。 请根据 IT 或 DevOps 运营团队管理事件的方式进行配置。
14. 选择“创建警报规则”以完成警报规则。 该警报会立即开始运行。

## <a name="next-steps"></a>后续步骤

- 请参阅[日志查询示例](container-insights-log-search.md#search-logs-to-analyze-data)，以查看预定义的查询，以及用于发警报、可视化或分析群集的评估或自定义示例。

- 若要详细了解 Azure Monitor 以及如何监视 Kubernetes 群集的其他方面，请参阅[查看 Kubernetes 群集性能](container-insights-analyze.md)和[查看 Kubernetes 群集运行状况](./container-insights-overview.md)。
