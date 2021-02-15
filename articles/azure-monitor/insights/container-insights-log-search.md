---
title: 如何从用于容器的 Azure Monitor 查询日志 | Microsoft Docs
description: 用于容器的 Azure Monitor 收集指标和日志数据，本文介绍了这些记录并包含了示例查询。
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 9bfa63a49da33289b8c811007f210e6546579d9d
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033555"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>如何从用于容器的 Azure Monitor 查询日志

适用于容器的 Azure Monitor 从容器主机和容器收集性能指标、清单数据和运行状况状态信息。 每三分钟收集一次数据，并将其转发到 Azure Monitor 中的 Log Analytics 工作区。 此数据可用于 Azure Monitor 中的[查询](../log-query/log-query-overview.md)。 此数据可应用于包括迁移计划、容量分析、发现和按需性能故障排除在内的方案。

## <a name="container-records"></a>容器记录

下表提供了为容器 Azure Monitor 收集的记录的详细信息。 有关列说明的列表，请参阅 [ContainerInventory](/azure/azure-monitor/reference/tables/containerinventory) 和 [ContainerLog](/azure/azure-monitor/reference/tables/containerlog) 表的引用。

| 数据 | 数据源 | 数据类型 | 字段 |
|------|-------------|-----------|--------|
| 容器库存 | kubelet | `ContainerInventory` | TimeGenerated、Computer、Name、ContainerHostname、Image、ImageTag、ContainerState、ExitCode、EnvironmentVar、Command、CreatedTime、StartedTime、FinishedTime、SourceSystem、ContainerID、ImageID |
| 容器日志 | Docker | `ContainerLog` | TimeGenerated、Computer、image ID、Name、LogEntrySource、LogEntry、SourceSystem、ContainerID |
| 容器节点清单 | Kube API | `ContainerNodeInventory`| TimeGenerated、计算机、ClassName_s、DockerVersion_s、OperatingSystem_s、Volume_s、Network_s、NodeRole_s、OrchestratorType_s、InstanceID_g、SourceSystem|
| Kubernetes 群集中的 Pod 清单 | Kube API | `KubePodInventory` | TimeGenerated、计算机、ClusterId、ContainerCreationTimeStamp、PodUid、PodCreationTimeStamp、ContainerRestartCount、PodRestartCount、PodStartTime、ContainerStartTime、ServiceName、ControllerKind、ControllerName、ContainerStatus、ContainerStatusReason、ContainerID、ContainerName、Name、PodLabel、Namespace、PodStatus、ClusterName、PodIp、SourceSystem |
| Kubernetes 群集节点部分清单 | Kube API | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes 事件 | Kube API | `KubeEvents` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Kubernetes 群集中的服务 | Kube API | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Kubernetes 群集节点部分的性能指标 | 从 cAdvisor 获取使用指标，从 Kube api 获取限制 | Perf &#124; where ObjectName == "K8SNode" | Computer、ObjectName、CounterName（cpuAllocatableNanoCores、memoryAllocatableBytes、cpuCapacityNanoCores、memoryCapacityBytes、memoryRssBytes、cpuUsageNanoCores、memoryWorkingsetBytes、restartTimeEpoc）、CounterValue、TimeGenerated、CounterPath、SourceSystem | 
| Kubernetes 群集容器部分的性能指标 | 从 cAdvisor 获取使用指标，从 Kube api 获取限制 | Perf &#124; where ObjectName == "K8SContainer" | CounterName（cpuRequestNanoCores、memoryRequestBytes、cpuLimitNanoCores、memoryWorkingSetBytes、restartTimeEpoch、cpuUsageNanoCores、memoryRssBytes）、CounterValue、TimeGenerated、CounterPath、SourceSystem | 
| 自定义指标 ||`InsightsMetrics` | Computer、Name、Namespace、Origin、SourceSystem、Tags<sup>1</sup>、TimeGenerated、Type、Va、_ResourceId | 

<sup>1</sup> Tags 属性表示对应指标的[多个维度](../platform/data-platform-metrics.md#multi-dimensional-metrics)。 有关 `InsightsMetrics` 表中收集和存储的指标的详细信息以及记录属性的说明，请参阅 [InsightsMetrics 概述](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md)。

## <a name="search-logs-to-analyze-data"></a>搜索日志以分析数据

Azure Monitor 日志有助于查找趋势、诊断瓶颈、预测或关联有助于确定是否最优执行当前群集配置的数据。 提供预定义日志搜索，可直接使用，也可通过自定义来按自己想要的方式返回信息。

通过从预览窗格的“在分析中查看”下拉列表中选择“查看 Kubernetes 事件日志”或“查看容器日志”选项，对工作区中的数据执行交互式分析 。 “日志搜索”页面在用户所处的 Azure 门户页面的右侧显示。

![在 Log Analytics 中分析数据](./media/container-insights-analyze/container-health-log-search-example.png)

转发到工作区的容器日志输出为 STDOUT 和 STDERR。 由于 Azure Monitor 正在监视 Azure 托管的 Kubernetes (AKS)，目前因生成了大量数据而不收集 Kube-system。 

### <a name="example-log-search-queries"></a>日志搜索查询示例

从一两个示例开始生成查询，然后修改它们以适应需求的做法通常很有用。 可使用以下示例查询进行试验，帮助生成更高级的查询：

| 查询 | 说明 | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | 列出容器的所有生命周期信息| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Kubernetes 事件|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | 映像清单 | 
| 选择“折线图”显示选项：<br> 性能<br> &#124; where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" &#124; summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 容器 CPU | 
| 选择“折线图”显示选项：<br> 性能<br> &#124; where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes" &#124; summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 容器内存 |
| InsightsMetrics<br> &#124; where Name == "requests_count"<br> &#124; summarize Val=any(Val) by TimeGenerated=bin(TimeGenerated, 1m)<br> &#124; sort by TimeGenerated asc<br> &#124; project RequestsPerMinute = Val - prev(Val), TimeGenerated <br> &#124; render barchart  | 每分钟请求数（按照自定义指标） |

## <a name="query-prometheus-metrics-data"></a>查询 Prometheus 指标数据

以下示例是一个 Prometheus 指标查询，显示每个节点每个磁盘每秒的磁盘读取次数。

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

若要查看 Azure Monitor 擦除的按命名空间筛选的 Prometheus 指标，请指定“prometheus”。 下面是一个示例查询，演示如何从 `default` kubernetes 命名空间查看 Prometheus 指标。

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Prometheus 数据也可直接按名称查询。

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>查询配置或抓取错误

为了调查任何配置或抓取错误，下面的示例查询将返回 `KubeMonAgentEvents` 表中的信息性事件。

```
KubeMonAgentEvents | where Level != "Info" 
```

输出显示类似于以下示例的结果：

![通过代理记录信息性事件的查询结果](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>后续步骤

用于容器的 Azure Monitor 不包含预定义的警报集。 请查看[使用用于容器的 Azure Monitor 创建性能警报](./container-insights-log-alerts.md)，了解如何针对高 CPU 和内存利用率创建建议的警报以支持 DevOps 或操作流程和过程。
