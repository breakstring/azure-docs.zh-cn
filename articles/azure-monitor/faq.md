---
title: Azure Monitor 常见问题解答 | Microsoft Docs
description: 有关 Azure Monitor 常见问题的解答。
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2020
ms.openlocfilehash: a8d3ded1d11a350ff53ffda71348b2cc707760b8
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008411"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Azure Monitor 常见问题解答

这份 Microsoft 常见问题解答列出了 Azure Monitor 的常见问题。 如果有任何其他问题，请转到[论坛](/answers/questions/topics/single/24223.html)并发布问题。 当某个问题经常被问到时，我们会将该问题添加到本文中，以便可以轻松快捷地找到该问题。


## <a name="general"></a>常规

### <a name="what-is-azure-monitor"></a>说明是 Azure Monitor？
[Azure Monitor](overview.md) 是 Azure 中的一项服务，可通过它监视 Azure、其他云环境或本地中应用程序和服务的性能和可用性。 Azure Monitor 将多个源中的数据收集到一个公共数据平台，你可在这里分析趋势和异常。 Azure Monitor 中的丰富功能可帮助你快速确定和应对可能会影响你的应用程序的关键情况。

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Azure Monitor、Log Analytics 和 Application Insights 之间有何区别？
2018 年 9 月，Microsoft 将 Azure Monitor、Log Analytics 和 Application Insights 合并到单个服务中，以便对应用程序及其依赖的组件提供强大的端到端监视功能。 Log Analytics 和 Application Insights 中的功能保持不变，但一些功能已更名为 Azure Monitor，目的是更好地反映其新范围。 Log Analytics 的日志数据引擎和查询语言现在称为 Azure Monitor 日志。 请参阅 [Azure Monitor 术语更新](terminology.md)。

### <a name="what-does-azure-monitor-cost"></a>Azure Monitor 的费用是多少？
自动启用的 Azure Monitor 功能（如收集指标和活动日志）免费提供。 存在与其他功能（例如日志查询和警报）相关的费用。 有关详细定价信息，请参阅 [Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)。

### <a name="how-do-i-enable-azure-monitor"></a>如何启用 Azure Monitor？
在你创建新的 Azure 订阅时就会启用 Azure Monitor，并自动收集[活动日志](./platform/platform-logs-overview.md)和平台[指标](platform/data-platform-metrics.md)。 创建[诊断设置](platform/diagnostic-settings.md)可收集有关 Azure 资源操作的更多详细信息，添加[监视解决方案](insights/solutions.md)和[见解](./monitor-reference.md)可为特定服务收集的收集数据提供额外的分析。 

### <a name="how-do-i-access-azure-monitor"></a>如何访问 Azure Monitor？
可通过 Azure 门户中的“监视”菜单访问各项 Azure Monitor 功能和数据。 通过不同 Azure 服务的菜单的“监视”部分，可访问相同的工具，其中数据经过筛选，指向特定的资源。 也可通过 CLI、PowerShell 和 REST API 针对各种场景来访问 Azure Monitor 数据。

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Azure Monitor 是否有本地版本？
否。 Azure Monitor 是一种用于处理和存储大量数据的可缩放式云服务，但它可监视本地和其他云中的资源。

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>Azure Monitor 能否监视本地资源？
可以，除了从 Azure 资源收集监视数据以外，Azure Monitor 还可从其他云和本地的虚拟机和应用程序中收集数据。 请参阅 [Azure Monitor 的监视数据源](platform/data-sources.md)。

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Azure Monitor 是否与 System Center Operations Manager 集成？
可将现有 System Center Operations Manager 管理组连接到 Azure Monitor，从而将来自代理的数据收集到 Azure Monitor 日志中。 这样，你就可使用日志查询和解决方案来分析从代理收集到的数据。 你还可配置现有 System Center Operations Manager 代理，将数据直接发送到 Azure Monitor。 请参阅[将 Operations Manager 连接到 Azure Monitor](platform/om-agents.md)。

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Azure Monitor 使用哪些 IP 地址？
有关代理和其他外部资源访问 Azure Monitor 所需的 IP 地址和端口的列表，请参阅 [Application Insights 和 Log Analytics 使用的 IP 地址](app/ip-addresses.md)。 

## <a name="monitoring-data"></a>监视数据

### <a name="where-does-azure-monitor-get-its-data"></a>Azure Monitor 从何处获取数据？
Azure Monitor 从各种源（包括来自 Azure 平台的日志和指标，自定义应用程序和在虚拟机上运行的代理）收集数据。 其他服务（例如 Azure 安全中心和网络观察程序）会将数据收集到 Log Analytics 工作区中，以便能够使用 Azure Monitor 数据进行分析。 你也可使用日志或指标的 REST API 将自定义数据发送到 Azure Monitor。 请参阅 [Azure Monitor 的监视数据源](platform/data-sources.md)。

### <a name="what-data-is-collected-by-azure-monitor"></a>Azure Monitor 会收集哪些数据？ 
Azure Monitor 将来自各种来源的数据收集到[日志](platform/data-platform-logs.md)或[指标](platform/data-platform-metrics.md)中。 每种数据类型都有自身的相对优点，每种数据都支持 Azure Monitor 中的一组特定功能。 每个 Azure 订阅都有一个指标数据库，可根据需要创建多个 Log Analytics 工作区来收集日志。 请参阅 [Azure Monitor 数据平台](platform/data-platform.md)。

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>可在 Azure Monitor 中收集的数据量是否有上限？
可收集的指标数据量没有限制，但数据的存储时间最长为 93 天。 请参阅[指标保留期](platform/data-platform-metrics.md#retention-of-metrics)。 可收集的日志数据量没有限制，但可能受到你为 Log Analytics 工作区选择的定价层的影响。 请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/monitor/)。

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>如何访问 Azure Monitor 收集的数据？
见解和解决方案提供了自定义体验，你可以多种方式使用在 Azure Monitor 中存储的数据。 你可通过以 Kusto 查询语言 (KQL) 编写的日志查询直接使用日志数据。 在 Azure 门户中，你可编写和运行查询，并使用 Log Analytics 以交互方式分析数据。 可使用指标资源管理器分析 Azure 门户中的指标。 请参阅[在 Azure Monitor 中分析日志数据](log-query/log-query-overview.md)和 [Azure 指标资源管理器入门](platform/metrics-getting-started.md)。

## <a name="solutions-and-insights"></a>解决方案和见解

### <a name="what-is-an-insight-in-azure-monitor"></a>Azure Monitor 中的见解是指什么？
见解为特定 Azure 服务提供自定义监视体验。 它们与 Azure Monitor 中的其他功能使用相同的指标和日志，但可能会收集额外的数据，并在 Azure 门户中提供独一无二的体验。 请参阅 [Azure Monitor 中的见解](./monitor-reference.md)。

要查看 Azure 门户中的见解，请参阅“监视”菜单的“见解”部分或服务菜单的“监视”部分  。

### <a name="what-is-a-solution-in-azure-monitor"></a>Azure Monitor 中的解决方案是什么？
监视解决方案是一组打包的逻辑，用于根据 Azure Monitor 功能监视特定的应用程序或服务。 它们在 Azure Monitor 中收集日志数据，并使用 Azure 门户中的常见体验为其分析提供日志查询和视图。 请参阅 [Azure Monitor 中的监视解决方案](insights/solutions.md)。

要查看 Azure 门户中的解决方案，请单击“监视”菜单的“见解”部分中的“更多”  。 单击“添加”，将其他解决方案添加到工作区。

## <a name="logs"></a>日志

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Azure Monitor 日志与 Azure 数据资源管理器之间有何区别？
Azure 数据资源管理器是一项快速且高度可缩放的数据探索服务，适用于日志和遥测数据。 Azure Monitor 日志是基于 Azure 数据资源管理器构建的，使用相同的 Kusto 查询语言 (KQL)，但有一些细微差别。 请参阅 [Azure Monitor 日志查询语言差异](/azure/data-explorer/kusto/query/)。

### <a name="how-do-i-retrieve-log-data"></a>如何检索日志数据？
可使用以 Kusto 查询语言 (KQL) 编写的日志查询从 Log Analytics 工作区检索所有数据。 你可编写自己的查询，也可使用包含特定应用程序或服务的日志查询的解决方案和见解。 请参阅 [Azure Monitor 中的日志查询概述](log-query/log-query-overview.md)。

### <a name="can-i-delete-data-from-a-log-analytics-workspace"></a>能否从 Log Analytics 工作区中删除数据？
系统会根据数据的[保留期](platform/manage-cost-storage.md#change-the-data-retention-period)从工作区中删除数据。 出于隐私或合规性原因，你可以删除特定数据。 有关详细信息，请参阅[如何导出和删除私人数据](platform/personal-data-mgmt.md#how-to-export-and-delete-private-data)。

### <a name="is-log-analytics-storage-immutable"></a>Log Analytics 存储是否不可变？
数据库存储中的数据在引入后将无法更改，但可以通过 [*清除* API 路径删除以删除私有数据](platform/personal-data-mgmt.md#delete)。 尽管不能更改数据，但某些证书要求数据是不可变的，并且不能在存储中更改或删除。 可以通过将 [数据导出](platform/logs-data-export.md) 到配置为 [不可变存储](../storage/blobs/storage-blob-immutability-policies-manage.md)的存储帐户来实现数据永久性。

### <a name="what-is-a-log-analytics-workspace"></a>什么是 Log Analytics 工作区？
Azure Monitor 收集的所有日志数据都存储在 Log Analytics 工作区中。 工作区实质上是从各种来源收集日志数据的容器。 可为所有监视数据使用一个 Log Analytics 工作区，也可要求使用多个工作区。 请参阅[设计 Azure Monitor 日志部署](platform/design-logs-deployment.md)。

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>能否将现有 Log Analytics 工作区移动到另一个 Azure 订阅？
你可以在资源组或订阅之间移动工作区，但不能移动到其他区域。 请参阅[将 Log Analytics 工作区移动到其他订阅或资源组](platform/move-workspace.md)。

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>为什么在 Log Analytics 中看不到“查询资源管理器”按钮和“保存”按钮？

[查询范围](log-query/scope.md)设置为特定资源时，“查询资源管理器”、“保存”和“新建警报规则”按钮不可用  。 若要创建警报并保存或加载查询，Log Analytics 的范围必须设为某个工作区。 若要在工作区上下文中打开 Log Analytics，请选择“Azure Monitor”菜单中的“日志” 。 选择的是上次使用的工作区，但可以选择任何其他的工作区。 请参阅 [Azure Monitor Log Analytics 中的日志查询范围和时间范围](log-query/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>从 VM 打开 Log Analytics 时，为什么收到“要使此订阅启用此查询，需注册 Microsoft.Insights”错误？ 
将自动注册多个资源提供程序，但某些资源提供程序可能需要你手动注册。 注册的作用域始终是订阅。 有关详细信息，请参阅[资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)。

### <a name="why-am-i-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>从 VM 打开 Log Analytics 时，为何出现无访问权限错误消息？ 
若要查看 VM 日志，需获得存储 VM 日志的工作区的读取权限。 在这些示例中，管理员必须在 Azure 中为你授予相关权限。

## <a name="metrics"></a>指标

### <a name="why-are-metrics-from-the-guest-os-of-my-azure-virtual-machine-not-showing-up-in-metrics-explorer"></a>指标资源管理器中为什么没有显示 Azure 虚拟机的来宾操作系统中的指标？
Azure 资源的[平台指标](insights/monitor-azure-resource.md#monitoring-data)是自动收集的。 但是，你必须执行某些配置才能从虚拟机的来宾操作系统收集指标。 对于 Windows VM，请按照[安装并配置 Windows Azure 诊断扩展 (WAD)](platform/diagnostics-extension-windows-install.md)中所述，安装诊断扩展并配置 Azure Monitor 接收器。 对于 Linux，请按照[使用 InfluxData Telegraf 代理收集 Linux VM 的自定义指标](platform/collect-custom-metrics-linux-telegraf.md)中所述安装 Telegraf 代理。

## <a name="alerts"></a>警报

### <a name="what-is-an-alert-in-azure-monitor"></a>Azure Monitor 中的警报是指什么？
在监视数据中发现重要情况时，警报会以主动的方式通知你。 有了警报，你就可以在系统的用户注意到问题之前确定和解决这些问题。 有多种类型的警报：

- 指标 - 指标值超过阈值。
- 日志查询 - 日志查询的结果与定义的条件匹配。
- 活动日志 - 活动日志事件与定义的条件匹配。
- Web 测试 - 可用性测试的结果与定义的条件匹配。


请参阅 [Microsoft Azure 中的警报概述](platform/alerts-overview.md)。


### <a name="what-is-an-action-group"></a>什么是操作组？
操作组是可以由警报触发的通知和操作的集合。 多个警报可使用一个操作组，这样你就能够使用一组常见的通知和操作。 请参阅[在 Azure 门户中创建和管理器操作组](platform/action-groups.md)。


### <a name="what-is-an-action-rule"></a>什么是操作规则？
通过操作规则，可修改匹配特定条件的一组警报的行为。 这样，你便可以在维护时段履行禁用警报操作之类的要求。 你还可将操作组应用于一组警报，而不是将其直接应用于警报规则。 请参阅[操作规则](platform/alerts-action-rules.md)。

## <a name="agents"></a>代理

### <a name="does-azure-monitor-require-an-agent"></a>Azure Monitor 需要代理吗？
只在从虚拟机中的操作系统和工作负载收集数据时，才需要代理。 虚拟机可位于 Azure、其他云环境或本地。 请参阅 [Azure Monitor 代理的概述](platform/agents-overview.md)。


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Azure Monitor 代理之间有何区别？
Azure 诊断扩展适用于 Azure 虚拟机，它将数据收集到 Azure Monitor 指标、Azure 存储和 Azure 事件中心。 Log Analytics 代理适用于 Azure 中的虚拟机、其他云环境或本地，它将数据收集到 Azure Monitor 日志。 依赖项代理需要 Log Analytics 代理，还需要所收集的进程详细信息和依赖项。 请参阅 [Azure Monitor 代理的概述](platform/agents-overview.md)。


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>代理流量是否使用 ExpressRoute 连接？
到 Azure Monitor 的流量使用 Microsoft 对等互连 ExpressRoute 线路。 有关不同类型的 ExpressRoute 流量的说明，请参阅 [ExpressRoute 文档](../expressroute/expressroute-faqs.md#supported-services)。 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>如何确认 Log Analytics 代理能否与 Azure Monitor 通信？
在代理计算机上的控制面板中，选择 " **安全 & 设置**"，然后选择 "Microsoft Monitoring Agent"。 在“Azure Log Analytics (OMS)”选项卡下，绿色勾号图标表示代理能够与 Azure Monitor 进行通信。 黄色警告图标表示代理存在问题。 一个常见的原因是 Microsoft Monitoring Agent 服务已停止。 请使用服务控制管理器重启该服务。

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>如何停止 Log Analytics 代理与 Azure Monitor 之间的通信？
对于直接连接到 Log Analytics 的代理，请打开控制面板，然后依次选择“安全性和设置”和“Microsoft Monitoring Agent” 。 在“Azure Log Analytics (OMS)”选项卡下，删除列出的所有工作区。 在 System Center Operations Manager 中，从 Log Analytics 托管的计算机的列表中删除该计算机。 Operations Manager 会更新该代理的配置以便不再向 Log Analytics 进行报告。 

### <a name="how-much-data-is-sent-per-agent"></a>每个代理发送多少数据？
每个代理发送的数据量取决于：

* 已启用的解决方案
* 正在收集的日志和性能计数器的数量
* 日志中的数据量

有关详细信息，请参阅[使用 Azure Monitor 日志管理使用情况和成本](platform/manage-cost-storage.md)。

对于能够运行 WireData 代理的计算机，可以使用以下查询了解正在发送的数据量：

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>将数据发送到 Azure Monitor 时，Microsoft 管理代理 (MMA) 使用多少网络带宽？
带宽是已发送数据量的一个函数。 数据通过网络发送时会被压缩。


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>当 Log Analytics 代理中的数据收集停止时，我如何收到通知？

当数据收集停止时，可以使用[新建日志警报](platform/alerts-metric.md)中所述的步骤收到通知。 将以下设置用于警报规则：

- **定义警报条件**：将 Log Analytics 工作区指定为资源目标。
- **警报条件** 
   - **信号名称**：自定义日志搜索
   - **搜索查询**：`Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **警报逻辑**：依据是“结果数”、条件是“大于”、阈值是“0”
   - **计算依据**：“时间(以分钟为单位)”是 30，“频率(以分钟为单位)”是 10
- **定义警报详细信息** 
   - **名称**：数据收集已停止
   - **严重性**：警告

指定现有或新的[操作组](platform/action-groups.md)，以便当日志警报匹配条件时，如果检测信号丢失超过 15 分钟，你将收到通知。


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Azure Monitor 代理的防火墙要求是什么？
有关防火墙要求的详细信息，请参阅[网络防火墙要求](platform/log-analytics-agent.md#network-requirements)。


## <a name="visualizations"></a>可视化效果

### <a name="why-cant-i-see-view-designer"></a>为什么看不到视图设计器？

只有用户在 Log Analytics 工作区中具有“参与者”权限或更高权限时，他们才可使用视图设计器。

## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>配置问题
*我在设置以下内容时遇到问题：*

* [.NET 应用](app/asp-net-troubleshoot-no-data.md)
* [监视已经在运行的应用](app/monitor-performance-live-website-now.md#troubleshoot)
* [Azure 诊断](platform/diagnostics-extension-to-application-insights.md)
* [Java Web 应用](app/java-troubleshoot.md)

我无法从服务器获取任何数据：

* [设置防火墙异常](app/ip-addresses.md)
* [设置 ASP.NET 服务器](app/monitor-performance-live-website-now.md)
* [设置 Java 服务器](app/java-agent.md)

*应该部署多少个 Application Insights 资源：*

* [如何设计 Application Insights 部署：一个还是多个 Application Insights 资源？](app/separate-resources.md)

### <a name="can-i-use-application-insights-with-"></a>是否可以将 Application Insights 用于...？

* [Azure VM 或 Azure 虚拟机规模集中 IIS 服务器上的 Web 应用](app/azure-vm-vmss-apps.md)
* [IIS 服务器上的 Web 应用 - 本地或在 VM 中](app/asp-net.md)
* [Java Web 应用](app/java-get-started.md)
* [Node.js 应用](app/nodejs.md)
* [Azure 上的 Web 应用](app/azure-web-apps.md)
* [Azure 上的云服务](app/cloudservices.md)
* [在 Docker 中运行的应用服务器](./azure-monitor-app-hub.yml)
* [单页 Web 应用](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Windows 桌面应用](app/windows-desktop.md)
* [其他平台](app/platforms.md)

### <a name="is-it-free"></a>是否免费？

是的，对于实验用途。 在基本定价计划中，应用程序可以发送一些每月免费数据量。 免费的限额足以满足开发和为少量用户发布应用的需求。 可以设置上限，以防止所使用的数据量超出指定数据量。

较大的遥测量按 Gb 收费。 我们提供了一些有关如何[限制费用](app/pricing.md)的提示。

企业计划对每个 Web 服务器节点每天发送的遥测进行收费。 如果要大规模使用连续导出，此计划非常合适。

[阅读定价计划](https://azure.microsoft.com/pricing/details/application-insights/)。

### <a name="how-much-does-it-cost"></a>费用是多少？

* 在 Application Insights 资源中打开“使用情况和估算成本”页面。 此处提供了一个最近使用情况的图表。 如果愿意，可以设置数据上限。
* 打开 [Azure 计费边栏选项卡](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview)查看所有资源的帐单。

### <a name="what-does-application-insights-modify-in-my-project"></a><a name="q14"></a>Application Insights 在我的项目中修改哪些内容？
详细信息取决于项目类型。 对于 Web 应用程序：

* 将这些文件添加到项目：
  * ApplicationInsights.config
  * ai.js
* 安装以下 NuGet 包：
  * *Application Insights API* - 核心 API
  * *适用于 Web 应用程序 的 Application Insights API* - 用于从服务器发送遥测
  * *适用于 JavaScript 应用程序的 Application Insights API* - 用于从客户端发送遥测
* 这些包包括以下程序集：
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* 将项目插入：
  * Web.config
  * packages.config
* （仅限新项目 - 如果[将 Application Insights 添加到现有项目][start]，则必须手动执行此操作。）在客户端和服务器代码中插入代码片段，以使用 Application Insights 资源 ID 初始化它们。 例如，在 MVC 应用中，代码插入到主页 Views/Shared/\_Layout.cshtml 中

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>如何从较早的 SDK 版本升级？
请参阅[发行说明](app/release-notes.md)了解对应于应用程序类型的 SDK。

### <a name="how-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>如何更改项目向哪个 Azure 资源发送数据？
在解决方案资源管理器中，右键单击 `ApplicationInsights.config` 并选择“更新 Application Insights”。 可在 Azure 中将数据发送到现有或新资源。 更新向导更改 ApplicationInsights.config 中的检测密钥，该密钥确定服务器 SDK 将数据发送到何处。 除非取消选中“更新全部”，否则它还将在网页中出现密钥的位置更改密钥。

### <a name="do-new-azure-regions-require-the-use-of-connection-strings"></a>新的 Azure 区域是否需要使用连接字符串？

新的 Azure 区域要求使用连接字符串而不是检测密钥。 [连接字符串](./app/sdk-connection-string.md)用于标识要与遥测数据关联的资源。 它还允许你修改可供你的资源将其用作遥测目标的终结点。 你需要复制连接字符串，并将其添加到应用程序的代码或环境变量中。

### <a name="can-i-use-providersmicrosoftinsights-componentsapiversions0-in-my-azure-resource-manager-deployments"></a>能否在 Azure 资源管理器部署中使用 `providers('Microsoft.Insights', 'components').apiVersions[0]`？

不建议使用此方法来填充 API 版本。 最新版本可表示可能包含中断性变更的预览版本。 即使使用较新的非预览版本，API 版本也并不总是与现有模板向后兼容；在某些情况下，API 版本可能不适用于部分订阅。

### <a name="what-is-status-monitor"></a>什么是状态监视器？

它是可以在 IIS Web 服务器中使用的桌面应用，用于帮助在 Web 应用中配置 Application Insights。 它不会收集遥测：不配置应用时，可以停止此监视器。 

[了解详细信息](app/monitor-performance-live-website-now.md#questions)。

### <a name="what-telemetry-is-collected-by-application-insights"></a>Application Insights 收集哪些遥测？

从服务器 Web 应用：

* HTTP 请求
* [依赖项](app/asp-net-dependencies.md)。 调用: SQL 数据库; 对外部服务的 HTTP 调用; Azure Cosmos DB、表、blob 存储和队列。 
* [异常](app/asp-net-exceptions.md)和堆栈跟踪。
* [性能计数器](app/performance-counters.md) - 如果使用[状态监视器](app/monitor-performance-live-website-now.md)、[适用于应用服务的 Azure 监视](app/azure-web-apps.md)、[适用于 VM 或虚拟机规模集的 Azure 监视](app/azure-vm-vmss-apps.md)或 [Application Insights 收集编写器](app/java-collectd.md)。
* [自定义编码的事件和指标](app/api-custom-events-metrics.md)。
* [跟踪日志](app/asp-net-trace-logs.md)（如果配置相应的收集器）。

从[客户端网页](app/javascript.md)：

* [页面视图计数](app/usage-overview.md)
* [AJAX 调用](app/asp-net-dependencies.md)，从运行脚本发出的请求。
* 页面视图加载数据
* 用户和会话计数
* [已经过身份验证的用户 ID](app/api-custom-events-metrics.md#authenticated-users)

从其他源中（如果有配置）：

* [Azure 诊断](platform/diagnostics-extension-to-application-insights.md)
* [导入到 Analytics](platform/data-collector-api.md)
* [Log Analytics](platform/data-collector-api.md)
* [Logstash](platform/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>是否可以筛选或修改某些遥测？

可以，可在服务器中编写：

* 用于在从应用发送属性前，对属性进行筛选并将其添加到所选遥测项的遥测处理器。
* 用于将属性添加到所有遥测项的遥测初始化器。

深入了解 [ASP.NET](app/api-filtering-sampling.md) 或 [Java](app/java-filter-telemetry.md)。

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>如何计算国家/地区、市/县和其他地理位置数据？

使用 [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/) 查找 Web 客户端的 IP 地址（IPv4 或 IPv6）。

* 浏览器遥测：收集发送方的 IP 地址。
* 服务器遥测：Application Insights 模块收集客户端 IP 地址。 如果设置了 `X-Forwarded-For`，则不会进行收集。
* 若要详细了解如何在 Application Insights 中收集 IP 地址和地理位置数据，请参阅[此文](./app/ip-collection.md)。

可以配置 `ClientIpHeaderTelemetryInitializer`，从不同的标头获取 IP 地址。 例如，在某些系统中，代理、负载均衡器或 CDN 会将其移动到 `X-Originating-IP`。 [了解详细信息](https://apmtips.com/posts/2016-07-05-client-ip-address/)。

可以[使用 Power BI](app/export-power-bi.md ) 在映射中显示请求遥测。


### <a name="how-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>数据在门户中保留多长时间？ 是否安全？
请参阅[数据保留和隐私][data]。

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>服务器或设备与 Azure 断开连接后，Application Insight 的遥测数据会怎么样？

所有 SDK（包括 Web SDK）都包含“可靠传输”。 当服务器或设备与 Azure 断开连接后，遥测数据[本地存储在文件系统](./app/data-retention-privacy.md#does-the-sdk-create-temporary-local-storage) (Server SDK) 或 HTML5 会话存储 (Web SDK) 中。 SDK 会定期重新尝试发送此遥测数据，直到引入服务将其视为“过时”（日志 48 小时后过时，指标 30 分钟后过时）。 过时的遥测将被删除。 在某些情况下（例如本地存储已满时），将不进行重试。


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>是否可能会在遥测中发送个人数据？

如果代码发送了此类数据，则有可能。 如果堆栈跟踪中的变量包括个人数据，也可能发生这种情况。 开发团队应进行风险评估，确保妥善处理个人数据。 [深入了解数据保留和隐私](app/data-retention-privacy.md)。

**所有** 八进制数的客户端 web 地址始终设置为 0，查找异地位置属性。

默认情况下，[Application Insights JavaScript SDK](app/javascript.md) 不会在其自动完成中包含任何个人数据。 但是，应用程序中使用的一些个人数据可能会被 SDK 获取（例如，`window.title` 中的全名或 XHR URL 查询参数中的帐户 ID）。 对于自定义个人数据掩码，请添加[遥测初始化表达式](app/api-filtering-sampling.md#javascript-web-applications)。

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>可在网页源中查看检测密钥。

* 这是监视解决方案的常见做法。
* 它不能用于窃取数据。
* 但可用于倾斜数据或触发警报。
* 我们未曾听说有任何客户遇到了此类问题。

可以：

* 将两个单独的检测密钥（独立的 Application Insights 资源）分别用于客户端和服务器数据。 或
* 编写一个在服务器中运行的代理，并使 Web 客户端通过该代理发送数据。

### <a name="how-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>如何在诊断搜索中查看 POST 数据？
我们不自动记录 POST 数据，但可以使用 TrackTrace 调用：将数据放置在消息参数中。 相比对字符串属性的限制，这具有更长的大小限制，尽管你无法对其进行筛选。

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>应使用单个 Application Insights 资源还是多个 Application Insights 资源？

对单个业务系统中的所有组件或角色使用单个资源。 对开发、测试和发布版本以及独立应用程序使用单独的资源。

* [请参阅此处的讨论](app/separate-resources.md)
* [示例 - 辅助角色和 Web 角色的云服务](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>如何动态更改检测密钥？

* [在此处讨论](app/separate-resources.md)
* [示例 - 辅助角色和 Web 角色的云服务](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>什么是用户和会话计数？

* JavaScript SDK 在 Web 客户端上设置了用于识别返回用户的用户 cookie，以及用于对活动进行分组的会话 cookie。
* 如果没有客户端脚本，可以[在服务器设置 cookie](https://apmtips.com/posts/2016-07-09-tracking-users-in-api-apps/)。
* 如果一个真实的用户在不同的浏览器中使用站点，或者使用私密/隐身浏览，或使用不同的计算机，则会进行多次计数。
* 若要识别跨计算机和浏览器登录的用户，请添加对 [setAuthenticatedUserContext()](app/api-custom-events-metrics.md#authenticated-users) 的调用。

### <a name="how-does-application-insights-generate-device-information-browser-os-language-model"></a>Application Insights 如何 (浏览器、操作系统、语言、型号) 生成设备信息？

浏览器传递请求的 HTTP 标头中的用户代理字符串，Application Insights 摄取服务使用 [UA 分析器](https://github.com/ua-parser/uap-core) 生成您在数据表和体验中看到的字段。 因此，Application Insights 用户无法更改这些字段。

如果用户或企业禁止在浏览器设置中发送用户代理，则偶尔可能会丢失或不准确的数据。 此外， [UA 分析器正则表达式](https://github.com/ua-parser/uap-core/blob/master/regexes.yaml) 可能不包括所有设备信息，或者 Application Insights 可能未采用最新更新。

### <a name="have-i-enabled-everything-in-application-insights"></a><a name="q17"></a> 我是否已在 Application Insights 中启用所有内容？
| 应看到 | 如何获取 | 为何需要它 |
| --- | --- | --- |
| 可用性图表 |[Web 测试](app/monitor-web-app-availability.md) |知道 Web 应用已启动 |
| 服务器应用性能：响应时间、... |[将 Application Insights 添加到项目](app/asp-net.md)或[在服务器上安装 AI 状态监视器](app/monitor-performance-live-website-now.md)（或编写自己的代码以[跟踪依赖项](app/api-custom-events-metrics.md#trackdependency)） |检测性能问题 |
| 依赖项遥测 |[在服务器上安装 AI 状态监视器](app/monitor-performance-live-website-now.md) |诊断数据库或其他外部组件问题 |
| 获取异常的堆栈跟踪 |[在代码中插入 TrackException 调用](app/asp-net-exceptions.md)（但有些会自动报告） |检测和诊断异常 |
| 搜索日志跟踪 |[添加日志记录适配器](app/asp-net-trace-logs.md) |诊断异常、性能问题 |
| 客户端使用基础知识：页面视图、会话、... |[网页中的 JavaScript 初始值设定项](app/javascript.md) |使用情况分析 |
| 客户端自定义指标 |[在网页中跟踪调用](app/api-custom-events-metrics.md) |增强用户体验 |
| 服务器自定义指标 |[在服务器中跟踪调用](app/api-custom-events-metrics.md) |商业智能 |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>为什么搜索图表和指标图表中的计数不一致？

[采样](app/sampling.md)减少了实际从应发送到门户的遥测项（请求、自定义事件等）数量。 在搜索中，看到的是实际接收到的项数。 在显示事件计数的指标图表中，看到的是发生的原始事件数。 

传输的每个项都携带一个 `itemCount` 属性，此属性显示该项表示的原始事件数量。 若要观察操作中的采样，可以在分析中运行此查询：

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```

### <a name="how-do-i-move-an-application-insights-resource-to-a-new-region"></a>如何将 Application Insights 资源移动到新区域？

当前不支持将现有 Application Insights 资源从一个区域移动到另一个区域。 你收集的历史数据无法迁移到新区域。 唯一的部分解决方法是：

1. 在新区域中创建全新的 Application Insights 资源（[经典](app/create-new-resource.md)或[基于工作区](./app/create-workspace-resource.md)）。
2. 重新创建特定于新资源中原始资源的所有唯一自定义项。
3. 修改应用程序以使用新区域资源的[检测密钥](app/create-new-resource.md#copy-the-instrumentation-key)或[连接字符串](app/sdk-connection-string.md)。  
4. 测试以确认所有内容是否都按预期处理新的 Application Insights 资源。 
5. 此时，你可以删除原始资源，这会导致所有历史数据丢失。 你也可以保留原始资源，用于在其数据保留设置期间进行历史报告。

通常需要在新区域中为资源手动重新创建或更新的唯一自定义项包括但不限于：

- 重新创建自定义仪表板和工作簿。 
- 重新创建或更新任何自定义日志/指标警报的范围。 
- 重新创建可用性警报。
- 重新创建用户访问新资源所需的任何 Azure 基于角色的访问控制 (Azure RBAC) 自定义设置。 
- 复制涉及引入采样、数据保留、每日上限和自定义指标启用的设置。 可通过“使用情况和预估成本”窗格控制这些设置。
- 依赖 API 密钥（如[版本注释](./app/annotations.md)、[实时指标安全控制通道](app/live-stream.md#secure-the-control-channel)等）的任何集成。你将需要生成新的 API 密钥并更新关联的集成。 
- 需要重新配置经典资源中的连续导出。
- 需要重新配置基于工作区资源中的诊断设置。

> [!NOTE]
> 如果在新区域中创建的资源要用于替换经典资源，我们建议探索[创建基于工作区的新资源](app/create-workspace-resource.md)或[将现有资源迁移至基于工作区的资源](app/convert-classic-resource.md)的好处。 

### <a name="automation"></a>自动化

#### <a name="configuring-application-insights"></a>配置 Application Insights

可以使用 Azure 资源监视器[编写 PowerShell 脚本](app/powershell.md)：

* 创建并更新 Application Insights 资源。
* 设置定价计划。
* 获取检测密钥。
* 添加指标警报。
* 添加可用性测试。

无法设置指标资源管理器报表，或设置连续导出。

#### <a name="querying-the-telemetry"></a>查询遥测

使用 [REST API](https://dev.applicationinsights.io/) 运行 [Analytics](./log-query/log-query-overview.md) 查询。

### <a name="how-can-i-set-an-alert-on-an-event"></a>如何设置事件警报？

Azure 警报仅出现在指标上。 创建一个每当事件发生时都跨越值阈值的自定义指标。 然后在该指标上设置警报。 每次指标在任一方向超过阈值时，你都将收到通知；无论初始值高低，首次超过时都不会收到通知；总是有几分钟的延迟。

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Azure Web 应用和 Application Insights 之间是否有数据传输费用？

* 如果 Azure Web 应用托管在具有 Application Insights 集合终结点的数据中心内，则不收取费用。 
* 如果主机数据中心没有集合终结点，那么应用的遥测会产生 [ Azure 传出费用](https://azure.microsoft.com/pricing/details/bandwidth/)。

这与 Application Insights 资源的托管位置无关。 它只取决于终结点的分布情况。

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>是否可以将遥测数据发送到 Application Insights 门户？

建议使用我们的 SDK 并使用 [SDK API](app/api-custom-events-metrics.md)。 为各种[平台](app/platforms.md)提供了相应的 SDK 变体。 这些 SDK 可处理缓冲、压缩、限制以及重试等。 但是，[引入架构](https://github.com/microsoft/ApplicationInsights-dotnet/tree/master/BASE/Schema/PublicSchema)和[终结点协议](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md)是公共的。

### <a name="can-i-monitor-an-intranet-web-server"></a>是否可以监视 Intranet Web 服务器？

可以，但你需要通过防火墙例外或代理重定向来允许流量到我们的服务。
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


在[此处](app/ip-addresses.md)查看服务和 IP 地址的完整列表。

#### <a name="firewall-exception"></a>防火墙例外

允许 Web 服务器将遥测发送到我们的终结点。 

#### <a name="gateway-redirect"></a>网关重定向

通过重写配置中的终结点，将流量从服务器路由到 Intranet 上的网关。 如果配置中不存在这些“终结点”属性，则这些类将使用下面示例 ApplicationInsights.config 中显示的默认值。 

你的网关应将流量路由到我们的终结点的基址。 在你的配置中，将默认值替换为 `http://<your.gateway.address>/<relative path>`。


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>使用默认终结点的示例 ApplicationInsights.config：
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

> [!NOTE]
> 自 v2.6.0 开始，具备 ApplicationIdProvider 功能。



#### <a name="proxy-passthrough"></a>代理传递

可配置计算机级别或应用程序级别代理来实现代理传递。
有关详细信息，请参阅 dotnet 关于 [DefaultProxy](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings) 的文章。
 
 示例 Web.config：
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>是否可以在 Intranet 服务器上运行可用性 Web 测试？

我们的 [Web 测试](app/monitor-web-app-availability.md)可在遍布全球的各个接入点上运行。 可运用以下两种解决方案：

* 防火墙门 - 允许从[长且可更改的 Web 测试代理列表](app/ip-addresses.md)中请求自己的服务器。
* 编写自己的代码，从 Intranet 内部向服务器发送定期请求。 可以为此运行 Visual Studio Web 测试。 测试人员可以使用 TrackAvailability() API 将结果发送到 Application Insights。

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>收集遥测数据需要多长时间？

大多数 Application Insights 数据的延迟小于 5 分钟。 有些数据可能需要更长的时间，通常是较大的日志文件。 有关详细信息，请参阅 [Application Insights SLA](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/)。



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md

### <a name="http-502-and-503-responses-are-not-always-captured-by-application-insights"></a>HTTP 502 和 503 响应并不总是由 Application Insights 捕获

“502 错误的网关”和“503 服务不可用”错误并不总是由 Application Insights 捕获。 如果仅将客户端 JavaScript 用于监视，则这是预期的行为，因为错误响应会在包含 HTML 标头且会呈现监视性的 JavaScript 代码片段的页面之前返回。 

如果从启用了服务器端监视的服务器发送了 502 或 503 响应，则 Application Insights SDK 会收集这些错误。 

但某些情况下，即使在应用程序的 Web 服务器上启用了服务器端监视，也不会由 Application Insights 捕获 502 或 503 错误。 许多新式 Web 服务器不允许客户端直接通信，而是使用反向代理之类的解决方案在客户端和前端 Web 服务器之间来回传递信息。 

在这种情况下，由于反向代理层出现问题，可能会将 502 或 503 响应返回到客户端，该响应不会被 Application Insights 直接捕获。 可能需要将日志从反向代理转发到 Log Analytics，并创建自定义规则来检查 502/503 响应，这样有助于检测该层中的问题 若要详细了解 502 和 503 错误的常见原因，请参阅 Azure 应用服务[故障排除文章中的“502 错误的网关”和“503 服务不可用”](../app-service/troubleshoot-http-502-http-503.md)。     


## <a name="opentelemetry"></a>OpenTelemetry

### <a name="what-is-opentelemetry"></a>什么是 OpenTelemetry

一种新的可观测性开源标准。 更多信息请访问 [https://opentelemetry.io/](https://opentelemetry.io/)。

### <a name="why-is-microsoft--azure-monitor-investing-in-opentelemetry"></a>Microsoft/Azure Monitor 为什么要对 OpenTelemetry 进行投资？

我们基于以下三个原因认为它可以更好地为客户服务：
   1. 实现对更多客户方案的支持。
   2. 工具无需担心供应商锁定。
   3. 提高客户透明度和参与度。

它还符合 Microsoft 的[拥抱开源](https://opensource.microsoft.com/)策略。

### <a name="what-additional-value-does-opentelemetry-give-me"></a>OpenTelemetry 可以给我带来哪些附加价值？

除上述原因外，OpenTelemetry 在大规模部署方面更为高效，并提供跨语言的一致设计/配置。

### <a name="how-can-i-test-out-opentelemetry"></a>如何测试 OpenTelemetry？

在 [https://aka.ms/AzMonOtel](https://aka.ms/AzMonOtel) 注册，加入我们的 Azure Monitor Application Insights 早期采用者社区。

### <a name="what-does-ga-mean-in-the-context-of-opentelemetry"></a>在 OpenTelemetry 中，GA 意味着什么？

OpenTelemetry 社区在[此处](https://medium.com/opentelemetry/ga-planning-f0f6d7b5302)定义了正式发布 (GA)。 但是，OpenTelemetry 的“GA”并不意味着与现有 Application Insights SDK 具有功能奇偶一致性。 Azure Monitor 将继续向需要[预聚合指标](app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)、[实时指标](app/live-stream.md)、[自适应采样](app/sampling.md#adaptive-sampling)、[探查器](app/profiler-overview.md)和[快照调试程序](app/snapshot-debugger.md)功能的客户推荐我们当前的 Application Insights SDK，直到 OpenTelemetry SDK 达到功能成熟。

### <a name="can-i-use-preview-builds-in-production-environments"></a>我是否可以在生产环境中使用预览版？

不建议这样做。 有关详细信息，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 。

### <a name="whats-the-difference-between-opentelemetry-sdk-and-auto-instrumentation"></a>OpenTelemetry SDK 和自动检测之间有何区别？

OpenTelemetry 规范定义了 [SDK](https://github.com/open-telemetry/opentelemetry-specification/blob/master/specification/glossary.md#telemetry-sdk)。 简而言之，“SDK”是一种特定于语言的包，可跨应用程序的各个组件收集遥测数据并将数据通过导出程序发送到 Azure Monitor。

自动检测（有时称为字节码注入、无代码或基于代理）的概念指在不更改代码的情况下检测应用程序的功能。 例如，查看 [OpenTelemetry Java 自动检测自述文件](https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/master/README.md)了解详细信息。

### <a name="whats-the-opentelemetry-collector"></a>什么是 OpenTelemetry Collector？

相应 [GitHub 自述文件](https://github.com/open-telemetry/opentelemetry-collector#opentelemetry-collector)中介绍了 OpenTelemetry Collector。 目前，Microsoft 不使用 OpenTelemetry Collector，而是依赖发送到 Azure Monitor Application Insights 的直接导出程序。

### <a name="whats-the-difference-between-opencensus-and-opentelemetry"></a>OpenCensus 和 OpenTelemetry 之间有何区别？

[OpenCensus](https://opencensus.io/) 是 [OpenTelemetry](https://opentelemetry.io/) 的前身。 Microsoft 帮助整合 [OpenTracing](https://opentracing.io/) 和 OpenCensus 用于创建 OpenTelemetry，OpenTelemetry 是全球唯一的可观测性标准。 Azure Monitor 当前[生产推荐的 Python SDK](app/opencensus-python.md) 基于 OpenCensus，但最终所有 Azure Monitor SDK 都将基于 OpenTelemetry。


## <a name="azure-monitor-for-containers"></a>用于容器的 Azure Monitor

### <a name="what-does-other-processes-represent-under-the-node-view"></a>节点视图下的“其他进程”表示什么？

“其他进程”旨在帮助你清楚了解节点上资源使用率较高的根本原因。 这样，你就能将容器化流程与非容器化流程的使用量区分开。

这些其他进程是哪些进程？ 

它们是在节点上运行的非容器化进程。  

如何对其进行计算？

其他进程 = CAdvisor 中的总使用量 - 容器化进程的使用量 

其他进程包括：

- 自行托管的或托管 Kubernetes 非容器化进程 

- 容器运行时进程  

- Kubelet  

- 节点上运行的系统进程 

- 节点硬件或 VM 上运行的其他非 Kubernetes 工作负载 

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>查询 ContainerLog 表时，没看到填充有 Image 和 Name 属性值。

对于代理版本 ciprod12042019 及更高版本，为了尽量减少收集日志数据时产生的费用，默认情况下不会为每行日志填充这两个属性。 有两种方法可用于查询表，查询包含这些属性及其值：

#### <a name="option-1"></a>选项 1 

联接其他表，在结果中包含这些属性值。

联接 ContainerID 属性，将查询修改为包含 ```ContainerInventory``` 表中的 Image 和 ImageTag 属性。 通过在 ContainerID 属性上进行联接，可以包含 KubepodInventory 表的 ContaineName 字段中的 Name 属性（与以前在 ```ContainerLog``` 表中显示的相同）。 这是建议选项。

下面是一个详细查询示例，说明了如何使用联接来获取这些字段值。

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

#### <a name="option-2"></a>方法 2

为每行容器日志的这些属性重新启用收集功能。

如果第一种方法因涉及到更改查询而不便使用，可通过在代理配置映射中启用设置 ```log_collection_settings.enrich_container_logs``` 来重新开始收集这些字段，如[数据收集配置设置](insights/container-insights-agent-config.md)中所述。

> [!NOTE]
> 建议不要对节点数超过 50 个的大型群集使用第二种方法，因为它将从群集中的每个节点生成 API 服务器调用来执行该扩充。 该方法还会增加收集到的每行日志的数据大小。

### <a name="can-i-view-metrics-collected-in-grafana"></a>能否在 Grafana 中查看收集的指标？

用于容器的 Azure Monitor 支持在 Grafana 仪表板中查看 Log Analytics 工作区中存储的指标。 我们提供了一个模板，你可从 Grafana 的[仪表板存储库](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker)中下载以供入门和参考，它可帮助你了解如何从受监视的群集查询其他数据，来在自定义 Grafana 仪表板中直观显示。 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>能否通过用于容器的 Azure Monitor 监视 AKS-Engine 群集？

用于容器的 Azure Monitor 支持监视部署到 Azure 上托管的 AKS-Engine（以前称为 ACS-Engine）群集的容器工作负载。 要进一步了解详细信息并概要了解为此方案启用监视功能所需的步骤，请参阅[对 AKS-Engine 使用用于容器的 Azure Monitor](https://github.com/microsoft/OMS-docker/tree/aks-engine)。

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>为什么在 Log Analytics 工作区中看不到数据？

如果每天在特定时间都无法查看 Log Analytics 工作区中的任何数据，则你可能已达到默认的 500 MB 限制或为控制每天要收集的数据量而指定的每日上限。 达到当天的限制后，数据收集将停止且只能在第二天恢复。 若要查看数据使用情况并根据预期的使用模式更新到其他定价层，请参阅[日志数据使用情况和成本](platform/manage-cost-storage.md)。 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>ContainerInventory 表中指定了哪些容器状态？

ContainerInventory 表包含已停止和正在运行的容器的信息。 此表由代理中的一个工作流进行填充，该工作流查询 Docker 中的所有容器（正在运行的和已停止的），并将该数据转发到 Log Analytics 工作区。
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>如何解决“缺少订阅注册”错误？

如果收到“缺少 Microsoft.OperationsManagement 的订阅注册”错误，可通过在定义工作区的订阅中注册资源提供程序 Microsoft.OperationsManagement 来解决它 。 可以在[此处](../azure-resource-manager/templates/error-register-resource-provider.md)找到介绍如何执行此操作的文档。

### <a name="is-there-support-for-kubernetes-rbac-enabled-aks-clusters"></a>是否支持启用了 Kubernetes RBAC 的 AKS 群集？

容器监视解决方案不支持 Kubernetes RBAC，但用于容器的 Azure Monitor 支持 Kubernetes RBAC。 在显示这些群集的数据的边栏选项卡上，解决方案详细信息页可能不会显示正确的信息。

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>如何通过 Helm 为 kube-system 命名空间中的容器启用日志收集？

默认情况下，kube-system 命名空间中的容器的日志收集被禁用。 可以通过在 omsagent 上设置一个环境变量来启用日志收集。 有关详细信息，请参阅 GitHub 上的[用于容器的 Azure Monitor](https://aka.ms/azuremonitor-containers-helm-chart) 页面。 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>如何将 omsagent 更新为最新发布的版本？

若要了解如何升级代理，请参阅[代理管理](insights/container-insights-manage-agent.md)。

### <a name="how-do-i-enable-multi-line-logging"></a>如何启用多行日志记录？

当前，用于容器的 Azure Monitor 不支持多行日志记录，但提供了暂时解决方案。 你可以将所有服务配置为以 JSON 格式进行写入，然后 Docker/Moby 会在单个行上写入它们。

例如，可以将你的日志包装为一个 JSON 对象，如示例 node.js 应用程序的以下示例中所示：

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

查询该数据时，该数据与用于日志的 Azure Monitor 中的以下示例类似：

```
LogEntry : ({"Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

要详细了解此问题，请查看以下 [GitHub 链接](https://github.com/moby/moby/issues/22920)。

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>如何解决在启用实时日志时遇到的 Azure AD 错误？ 

你可能会看到以下错误：在请求中指定的回复 URL 与为应用程序“<application ID\>”配置的回复 URL 不匹配。 有关此问题的解决方案，可参阅[如何使用用于容器的 Azure Monitor 实时查看容器数据](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication)一文。 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>在加入后为何无法升级群集？

如果在为 AKS 群集启用用于容器的 Azure Monitor 之后，删除了群集向其发送数据的 Log Analytics 工作区，则在尝试升级群集时会失败。 要解决此情况，必须禁用再重新启用监视功能，使其引用订阅中的其他有效工作区。 在你重新尝试升级群集时，应该就会处理并成功完成。  

### <a name="which-ports-and-domains-do-i-need-to-openallow-for-the-agent"></a>需要为代理打开/允许哪些端口和域？

请参阅[网络防火墙要求](insights/container-insights-onboard.md#network-firewall-requirements)，了解使用Azure、Azure 美国政府和 Azure 中国世纪互联云的容器化代理所需的代理和防火墙配置信息。


## <a name="azure-monitor-for-vms"></a>用于 VM 的 Azure Monitor

### <a name="can-i-onboard-to-an-existing-workspace"></a>是否可以载入到现有工作区？
如果虚拟机已连接到 Log Analytics 工作区，则在加入用于 VM 的 Azure Monitor 时，你可以继续使用该工作区，前提是该工作区位于某个 [受支持的区域](insights/vminsights-configure-workspace.md#supported-regions)中。


### <a name="can-i-onboard-to-a-new-workspace"></a>是否可以载入到新工作区？ 
如果 VM 当前未连接到现有的 Log Analytics 工作区，则你需要创建一个新工作区来存储数据。 如果通过 Azure 门户为用于 VM 的 Azure Monitor 配置了单个 Azure VM，则系统会自动创建新的默认工作区。

如果选择使用基于脚本的方法，可在[使用 Azure PowerShell 或资源管理器模板启用用于 VM 的 Azure Monitor](./insights/vminsights-enable-powershell.md) 一文中找到这些步骤。 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>如果 VM 已开始向现有工作区报告，我该怎么做？
如果你已开始从虚拟机收集数据，则可能已将虚拟机配置为向现有的 Log Analytics 工作区报告数据。  只要该工作区在某个受支持区域中，就可以在该现有工作区中启用用于 VM 的 Azure Monitor。  目前，如果使用的工作区不在受支持的区域中，则无法加入用于 VM 的 Azure Monitor。  我们将力求支持更多的区域。


### <a name="why-did-my-vm-fail-to-onboard"></a>为何我的 VM 无法载入？
从 Azure 门户载入 Azure VM 时，将执行以下步骤：

* 创建默认的 Log Analytics 工作区（如果已选择该选项）。
* 使用 VM 扩展在 Azure VM 上安装 Log Analytics 代理（如果确定有必要）。  
* 使用某个扩展在 Azure VM 上安装用于 VM 的 Azure Monitor 映射依赖项代理（如果确定有必要）。 

在载入过程中，我们会检查上述每个步骤的状态，并在门户中返回通知状态。 配置工作区和安装代理通常需要 5 到 10 分钟。 在门户中查看监视数据需要额外花费 5 到 10 分钟时间。  

如果在启动载入后有消息指出 VM 需要载入，则 VM 最长需要花费 30 分钟来完成该过程。 


### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>我在我 VM 的性能图表中看不到任何或部分数据
性能图表已更新为使用 InsightsMetrics 表中存储的数据。  要查看这些图表中的数据，需要进行升级，使用新的 VM 见解解决方案。  有关其他信息，请参阅[正式发布版常见问题解答](insights/vminsights-ga-release-faq.md)。

如果在磁盘表或某些性能图表中未看到性能数据，则原因可能是未在工作区中配置性能计数器。 若要解决此问题，请运行以下 [PowerShell 脚本](./insights/vminsights-enable-powershell.md)。


### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>用于 VM 的 Azure Monitor 映射功能与服务映射有何不同？
用于 VM 的 Azure Monitor 映射功能基于服务映射，但存在以下差异：

* 可以从“VM”边栏选项卡，或者 Azure Monitor 中的“用于 VM 的 Azure Monitor”访问“映射”视图。
* “映射”中的连接现在可单击，所选连接的边侧面板中会显示连接指标数据的视图。
* 可以使用一个新 API 创建映射，以便更好地支持更复杂的映射。
* 受监视 VM 现在包含在客户端组节点中，圆环图显示该组中受监视与不受监视虚拟机的比例。  展开该组后，还可以在该图表中筛选计算机列表。
* 受监视虚拟机现在包含在服务器端口组节点中，圆环图显示该组中受监视与不受监视虚拟机的比例。  展开该组后，还可以在该图表中筛选计算机列表。
* 映射样式已更新，与 Application Insights 中的应用映射更一致。
* 侧面板已更新，但尚未包含服务映射中支持的整套集成功能 -“更新管理”、“更改跟踪”、“安全性”和“服务台”。 
* 用于选择要映射的组和计算机的选项已更新，现在支持订阅、资源组、Azure 虚拟机规模集和云服务。
* 无法在用于 VM 的 Azure Monitor 映射功能中创建新的服务映射计算机组。  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>为何我的性能图表显示虚线？
这有几个原因。  如果数据集合中存在间隙，则我们会绘制虚线。  如果为启用的性能计数器修改了数据采样频率（默认设置为每隔 60 秒收集数据），并且为图表选择较窄的时间范围，而采样频率小于图表中使用的桶大小（例如，采样频率为每隔 10 分钟，图表上的每个桶为 5 分钟），则可能会在图表中看到虚线。  在这种情况下，选择查看更宽的时间范围应该可使图表线条显示为实线而不是虚线。

### <a name="are-groups-supported-with-azure-monitor-for-vms"></a>用于 VM 的 Azure Monitor 是否支持组？
是，一旦安装依赖项代理，我们就会从 VM 中收集信息，以根据订阅、资源组、虚拟机规模集和云服务显示组。  如果你一直在使用服务映射并创建了计算机组，则也会显示这些组。  如果你为正在查看的工作区创建了计算机组，那么这些组也会显示在组筛选器中。 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>如何查看聚合性能图表中驱动第 95 百分位线条的详细信息？
默认情况下，列表已经过排序，会显示所选指标的第 95 百分位值最大的 VM，但“可用内存”图表除外，其中显示第 5 百分位值最小的计算机。  单击图表会打开“前 N 项列表”视图，其中已选择相应的指标。

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>“映射”功能如何处理不同 VNet 和子网中的重复 IP？
如果跨子网和 VNet 复制 VM 或 Azure 虚拟机规模集的 IP 范围，可能导致用于 VM 的 Azure Monitor 映射显示错误的信息。 这是一个已知问题，我们正在研究可改进此体验的选项。

### <a name="does-map-feature-support-ipv6"></a>“映射”功能是否支持 IPv6？
“映射”功能目前仅支持 IPv4，我们正在研究 IPv6 的支持。 我们还支持 IPv6 隧道内部的 IPv4。

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>加载资源组或其他大型组的映射时，难以查看映射
尽管我们已对“映射”做出改进，使其能够处理大型和复杂的配置，但我们认识到，映射中可能包含大量以群集方式工作的节点和连接。  我们将持续致力增强支持以提高可伸缩性。   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>为何“性能”选项卡上的网络图表不同于 Azure VM“概述”页上的网络图表？

Azure VM 的概述页基于来宾 VM 中的活动的主机度量值显示图表。  Azure VM“概述”中的网络图表仅显示计费的网络流量。  这不包括虚拟网络间的流量。  为用于 VM 的 Azure Monitor 显示的数据和图表基于来宾 VM 中的数据，网络图表显示入站和出站到该 VM 的所有 TCP/IP 流量，包括虚拟网络之间的流量。

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>如何测量 VMConnection 中存储且在连接面板和工作簿中显示的数据的响应时间？

响应时间为近似值。 我们不会检测应用程序的代码，因此不会真正知道请求的开始时间和响应到达的时间。 相反，我们会观察在连接上发送的数据及之后该连接上返回的数据。 代理会跟踪这些发送和接收并尝试对它们进行配对：一个发送序列后跟一个接收序列就被解释为一个请求/响应对。 这些操作之间的时间是响应时间。 它将包括网络延迟和服务器处理时间。

该近似值非常适合基于请求/响应的协议，即在连接上发出单个请求，到达的也是单个响应。 这种情况适用于 HTTP(S)（不带管道），但不适用于其他协议。

### <a name="are-there-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>如果我处于 Log Analytics 免费定价计划中，是否有限制？
如果已经为 Azure Monitor 配置了使用 *免费* 定价层的 Log Analytics 工作区，则用于 VM 的 Azure Monitor 的映射功能将仅支持已连接到工作区的五台计算机。 如果已有五台 VM 连接到免费工作区，你断开了其中一台 VM 的连接，然后连接到一台新的 VM，则新的 VM 将不受监视并且不会反映在“映射”页上。  

此情况下，在你打开 VM 并从左侧窗格中选择“见解”时，甚至在 VM 上安装它后，系统都将通过“立即尝试”选项向你发出提示 。  但是，如果此 VM 未加入到用于 VM 的 Azure Monitor，则不会像通常情况下那样通过选项向你发出提示。 


## <a name="next-steps"></a>后续步骤
如果未在此处找到问题的答案，可查看以下论坛，了解其他问题和解答。

- [Log Analytics](/answers/topics/azure-monitor.html)
- [Application Insights](/answers/topics/azure-monitor.html)

有关 Azure Monitor 的一般性反馈，请访问[反馈论坛](https://feedback.azure.com/forums/34192--general-feedback)。
