---
title: Azure Monitor 中的指标 | Microsoft Docs
description: 介绍了 Azure Monitor 中的指标，即能够支持准实时方案的轻型监视数据。
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: b7af6b489aeb919fd1db0e45ddba58a21b1c3633
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/24/2020
ms.locfileid: "97760125"
---
# <a name="azure-monitor-metrics-overview"></a>Azure Monitor 指标概述
Azure Monitor 指标是 Azure Monitor 的一项功能，它将[受监视的资源](../monitor-reference.md)中的数值数据收集到时序数据库。 指标是定期收集的数值，用于描述系统在某一特定时间的某些情况。 Azure Monitor 中的指标是轻型数据，能够支持准实时方案，这让其在发出警报和快速检测问题方面特别有用。 您可以使用指标资源管理器以交互方式分析它们，在值超过阈值时主动收到通知警报，或者在工作簿或仪表板中将其可视化。


> [!NOTE]
> Azure Monitor 指标是支持 Azure Monitor 的数据平台的一半。 另一半是 [Azure Monitor 日志](data-platform-logs.md)它收集和组织日志和性能数据，并允许使用丰富的查询语言进行分析。 指标比 Azure Monitor 日志中的数据更为轻量，并且能够支持准实时方案，因此特别适用于对问题发出警报并快速进行检测。 不过，指标只能将数值数据存储在特定的结构中，而日志可以存储各种不同的数据类型，每个数据类型都有自己的结构。 还可使用日志查询对日志数据执行复杂的分析，只是无法分析指标数据。


## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Azure Monitor 指标有何用途？
下表列出了在 Azure Monitor 中指标的不同使用方式。

|  |  |
|:---|:---|
| **分析** | 使用[指标资源管理器](metrics-charts.md)分析图表上收集的指标，并比较来自不同资源的指标。 |
| **Alert** | 配置[指标警报规则](alerts-metric.md)，以在指标值超过阈值时发送通知或执行[自动化操作](action-groups.md)。 |
| **可视化** | 将指标资源管理器中的图表固定到 [Azure 仪表板](../learn/tutorial-app-dashboards.md)。<br>创建[工作簿](./workbooks-overview.md)，以与交互式报表中的多组数据合并。将查询结果导出到 [Grafana](grafana-plugin.md)，以利用它的仪表板，并与其他数据源合并。 |
| **自动化** |  使用[自动缩放](autoscale-overview.md)并根据超过阈值的指标值来增加或减少资源。 |
| **检索** | 使用 [PowerShell cmdlet](/powershell/module/az.applicationinsights) 通过命令行来访问指标值<br>使用 [REST API](rest-api-walkthrough.md) 通过自定义应用来访问指标值。<br>使用 [CLI](/cli/azure/monitor/metrics) 通过命令行来访问指标值。 |
| **导出** | [将指标路由到日志](./resource-logs.md#send-to-azure-storage)，以将 Azure Monitor 指标中的数据与 Azure Monitor 日志中的数据一起进行分析，并将指标值存储超过 93 天。<br>将指标流式传输到[事件中心](stream-monitoring-data-event-hubs.md)，以路由到外部系统。 |
| **存档** | 出于符合性、审核或脱机报告目的，对资源的性能或运行状况历史记录进行 [存档](./platform-logs-overview.md)。 |

![指标概述](media/data-platform-metrics/metrics-overview.png)


## <a name="data-collection"></a>数据收集
Azure Monitor 从三个基本源收集指标。 只要在 Azure Monitor 指标数据库中收集了这些指标，就可以一起评估它们，而不管它们的数据源是什么。

**Azure 资源**。 平台指标由 Azure 资源创建，可用于洞察这些资源的运行状况和性能。 每种资源创建[一组非重复性指标](metrics-supported.md)，无需进行任何配置。 平台指标每分钟从 Azure 资源中收集一次，除非在指标的定义中另有规定。 

**应用程序**。 指标由 Application Insights 为受监视的应用程序创建，可帮助检测性能问题，以及跟踪应用程序的用法趋势。 此类指标包括“服务器响应时间”和“浏览器异常”等值。 

**虚拟机代理**。 指标从虚拟机的来宾操作系统收集。 使用 [Windows 诊断扩展 (WAD)](./diagnostics-extension-overview.md) 为 Windows 虚拟机启用来宾 OS 指标，并使用 [InfluxData Telegraf 代理](https://www.influxdata.com/time-series-platform/telegraf/)为 Linux 虚拟机启用来宾 OS 指标。

**自定义指标**。 除了自动可用的标准指标之外，还可以定义指标。 可以在 Application Insights 监视的[应用中定义自定义指标](../app/api-custom-events-metrics.md)，也可以使用[自定义指标 API](metrics-store-custom-rest-api.md) 为 Azure 服务创建自定义指标。

- 有关将数据发送到 Azure Monitor 指标的数据源的完整列表，请参阅 [Azure Monitor 监视哪些内容？](../monitor-reference.md)。

## <a name="metrics-explorer"></a>指标资源管理器
使用[指标资源管理器](metrics-charts.md)以交互方式分析指标数据库中的数据，并绘制随时间变化的多个指标的值图表。 可以将图表固定到仪表板，以便使用其他可视化效果来查看它们。 还可以使用 [Azure 监视 REST API](rest-api-walkthrough.md) 检索指标。

![指标资源管理器](media/data-platform-metrics/metrics-explorer.png)

- 请参阅 [Azure Monitor 指标资源管理器入门](metrics-getting-started.md)以开始使用指标资源管理器。

## <a name="data-structure"></a>数据结构
Azure Monitor 指标收集的数据存储在更适合分析带有时间戳的数据的时序数据库中。 每组指标值都是具有以下属性的时序：

* 值的收集时间
* 与值关联的资源
* 充当指标类别的命名空间
* 指标名称
* 值本身
* 一些指标可能会有多个维度，如[多维指标](#multi-dimensional-metrics)中所述。 自定义指标最多可以包含 10 个维度。

## <a name="multi-dimensional-metrics"></a>多维指标
指标数据面临的一大挑战是，通常只有有限的信息来为收集的值提供上下文。 Azure Monitor 通过多维指标解决了这一挑战。 指标维度是携带附加数据来描述指标值的名称/值对。 例如，指标“可用磁盘空间”可能有名为“驱动器”且值为“C:”和“D:”的维度，此维度可便于查看所有驱动器的可用磁盘空间，也可便于单独查看每个驱动器的可用磁盘空间。

以下示例演示了名为“网络吞吐量”的假设指标的两个数据集。 第一个数据集不包含维度。 第二个数据集使用两个维度（_IP 地址_ 和 _方向_）显示值：

### <a name="network-throughput"></a>网络吞吐量

| 时间戳     | 指标值 |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8 Kbps |
| 8/9/2017 8:15 | 1,141.4 Kbps |
| 8/9/2017 8:16 | 1,110.2 Kbps |

上述不包含维度的指标只能够回答类似“在某个给定时间我的网络吞吐量是多少？”的基本问题

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>网络吞吐量 + 两个维度（“IP 地址”和“方向”）

| 时间戳     | “IP”维度   | “方向”维度 | 指标值|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP="192.168.5.2" | Direction="Send"    | 646.5 Kbps |
| 8/9/2017 8:14 | IP="192.168.5.2" | Direction="Receive" | 420.1 Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Direction="Send"    | 150.0 Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Direction="Receive" | 115.2 Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | Direction="Send"    | 515.2 Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | Direction="Receive" | 371.1 Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Direction="Send"    | 155.0 Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Direction="Receive" | 100.1 Kbps |

此指标可以回答类似“每个 IP 地址的网络吞吐量是多少？”，以及“相对于收到的数据，发送的数据有多少？”的问题 与不包含维度的指标相比，多维指标具有更多分析值和诊断值。

## <a name="retention-of-metrics"></a>指标保留期
对于 Azure 中的大多数资源，指标的存储时间为 93 天。 有一些例外情况：

**来宾 OS 指标**
-   经典来宾 OS 指标。 这些性能计数器由 [Windows 诊断扩展 (WAD)](./diagnostics-extension-overview.md) 或 [Linux 诊断扩展 (LAD)](../../virtual-machines/extensions/diagnostics-linux.md) 收集，并路由到 Azure 存储帐户。 这些指标的保留期为 14 天。
-   发送到 Azure Monitor 指标的来宾 OS 指标。 这些性能计数器由 [Windows 诊断扩展 (WAD)](diagnostics-extension-overview.md) 收集，并发送到 [Azure Monitor 数据接收器](diagnostics-extension-overview.md#data-destinations)，或通过 Linux 计算机上的 [InfluxData Telegraf 代理](https://www.influxdata.com/time-series-platform/telegraf/)收集。 这些指标的保留期为 93 天。
-   Log Analytics 代理收集的来宾 OS 指标。 这些性能计数器由 Log Analytics 代理收集，并发送到 Log Analytics 工作区。 这些指标的保留期为 31 天，最多可延长到 2 年。

**基于 Application Insights 日志的指标**。 
- 在后台，[基于日志的指标](../app/pre-aggregated-metrics-log-metrics.md)转换为日志查询。 它们的保留期与基础日志中的事件的保留期一致。 对于 Application Insights 资源，日志的存储时间为 90 天。


> [!NOTE]
> 可以[将 Azure Monitor 资源的平台指标发送到 Log Analytics 工作区](./resource-logs.md#send-to-azure-storage)，以了解长期趋势。





## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Monitor 数据平台](data-platform.md)。
- 了解 [Azure Monitor 中的日志数据](data-platform-logs.md)。
- 了解适用于 Azure 中不同资源的[监视数据](data-sources.md)。

