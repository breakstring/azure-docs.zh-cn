---
title: Azure Monitor 中的 Log Analytics 代理数据源
description: 数据源定义 Azure Monitor 从代理和其他已连接的源收集的日志数据。  本文介绍有关 Azure Monitor 如何使用数据源的概念，详细解释如何配置数据源，并对不同的可用数据源进行概要介绍。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: ad5e91a6dcdb61e09a64e61a27f12148ec28168e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000736"
---
# <a name="log-analytics-agent-data-sources-in-azure-monitor"></a>Azure Monitor 中的 Log Analytics 代理数据源
Azure Monitor 通过 [Log Analytics](log-analytics-agent.md) 代理从虚拟机中收集的数据由你在 [Log Analytics 工作区](data-platform-logs.md)上配置的数据源定义。   每个数据源将创建具有某种特殊类型的记录，而每个类型都具有自己的一组属性。

> [!IMPORTANT]
> 本文介绍 [Log Analytics 代理](log-analytics-agent.md)（Azure Monitor 使用的代理之一）的数据源。 其他代理收集的数据不同，且配置也不同。 有关可用代理及其可收集的数据的列表，请参阅 [Azure Monitor 代理概述](agents-overview.md)。

![日志数据收集](media/agent-data-sources/overview.png)

> [!IMPORTANT]
> 本文中所述的数据源仅适用于运行 Log Analytics 代理的虚拟机。 

## <a name="summary-of-data-sources"></a>数据源概要介绍
下表列出了 Log Analytics 代理当前提供的代理数据源。  每个数据源都链接到一篇单独的文章，提供该数据源的详细信息。   它还提供了有关收集方法和收集频率的信息。 


| 数据源 | 平台 | Log Analytics 代理 | Operations Manager 代理 | Azure 存储 | 需要 Operations Manager？ | Operations Manager 代理数据通过管理组发送 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [自定义日志](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | 到达时 |
| [自定义日志](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | 到达时 |
| [IIS 日志](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |依赖于日志文件滚动更新设置 |
| [性能计数器](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |根据计划，最小值为 10 秒 |
| [性能计数器](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |根据计划，最小值为 10 秒 |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |来自 Azure 存储：10 分钟；来自代理：到达时 |
| [Windows 事件日志](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | 到达时 |


## <a name="configuring-data-sources"></a>配置数据源
若要为 Log Analytics 代理配置数据源，请转到 Azure 门户中的“Log Analytics 工作区”菜单，然后选择一个工作区。 依次单击“高级设置”、“数据”。 选择要配置的数据源。 可以打开上表中的链接来访问每个数据源的文档及其配置的详细信息。

任何配置都会传递到已连接到该工作区的所有代理。  不能从此配置中排除任何已连接的代理。

![配置 Windows 事件](media/agent-data-sources/configure-events.png)



## <a name="data-collection"></a>数据收集
数据源配置会在几分钟内传送到与 Azure Monitor 直接连接的各个代理。  指定的数据从代理收集，并按特定于每个数据源的时间间隔直接传送到 Azure Monitor。  请参阅每个数据源的文档以了解详情。

对于已连接管理组中的 System Center Operations Manager 代理，数据源配置默认以每 5 分钟的间隔转换成管理包并传送到管理组。  代理会下载任何其他的管理包，并收集指定的数据。 根据数据源的不同，数据或者被发送到管理服务器，再由管理服务器转发到 Azure Monitor；或者不通过管理服务器，由代理将数据发送到 Azure Monitor。 有关详细信息，请参阅 [Azure 中的监视解决方案的数据收集详细信息](../monitor-reference.md)。  可以在[配置与 System Center Operations Manager 的集成](om-agents.md)中阅读有关连接 Operations Manager 和 Azure Monitor 以及修改配置传送频率的详细信息。

如果代理无法连接到 Azure Monitor 或 Operations Manager，将继续收集在建立连接时传送的数据。  如果数据量达到客户端的最大缓存大小，或者如果代理无法在 24 小时内建立连接，则可能会丢失数据。

## <a name="log-records"></a>日志记录
Azure Monitor 收集的所有日志数据都作为记录存储在工作区中。  按不同数据源收集的记录具有其自己的属性集，并由其“**类型**”属性来识别。  有关每种记录类型的详细信息，请参阅每个数据源和解决方案的相关文档。

## <a name="next-steps"></a>后续步骤
* 了解[监视解决方案](../insights/solutions.md)如何将功能添加到 Azure Monitor，以及如何将数据收集到工作区中。
* 了解[日志查询](../log-query/log-query-overview.md)以便分析从数据源和监视解决方案中收集的数据。  
* 配置[警报](alerts-overview.md)以便主动向你通知从数据源和监视解决方案中收集的关键数据。
