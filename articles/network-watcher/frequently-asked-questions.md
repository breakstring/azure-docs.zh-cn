---
title: 有关 Azure 网络观察程序的常见问题解答 (FAQ) | Microsoft Docs
description: 本文解答有关 Azure 网络观察程序服务的常见问题。
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: e7585880b98f62f819ff344c82846c2cfb1fd620
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019816"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>有关 Azure 网络观察程序的常见问题解答 (FAQ)
[Azure 网络观察程序](./network-watcher-monitoring-overview.md)服务提供一套工具用于监视、诊断 Azure 虚拟网络中的资源、查看其指标，以及为其启用或禁用日志。 本文解答有关该服务的常见问题。

## <a name="general"></a>常规

### <a name="what-is-network-watcher"></a>什么是网络观察程序？
网络观察程序用于监视和修复 IaaS（基础结构即服务）组件的网络运行状况，其中包括虚拟机、虚拟网络、应用程序网关、负载均衡器，以及 Azure 网络中的其他资源。 它不是用于监视 PaaS（平台即服务）基础结构或获取 Web/移动分析数据的解决方案。

### <a name="what-tools-does-network-watcher-provide"></a>网络观察程序提供哪些工具？
网络观察程序提供三个主要功能集
* 监视
  * [拓扑视图](./view-network-topology.md)显示虚拟网络中的资源及其相互关系。
  * [连接监视器](./connection-monitor.md)可用于监视 VM 与另一网络资源之间的连接和延迟。
  * [网络性能监视器](../azure-monitor/insights/network-performance-monitor.md)可用于监视混合网络体系结构、Expressroute 线路和服务/应用程序终结点之间的连接和延迟。  
* 诊断
  * [IP 流验证](./network-watcher-ip-flow-verify-overview.md)可用于在 VM 级别检测流量筛选问题。
  * [下一跃点](./network-watcher-next-hop-overview.md)可帮助验证流量路由和检测路由问题。
  * [连接故障排除](./network-watcher-connectivity-portal.md)可以在 VM 与另一网络资源之间实现一次性连接和延迟检查。
  * [数据包捕获](./network-watcher-packet-capture-overview.md)可用于捕获虚拟网络中 VM 上的所有流量。
  * [VPN 故障排除](./network-watcher-troubleshoot-overview.md)针对 VPN 网关和连接运行多项诊断检查，以帮助调试问题。
* 日志记录
  * [NSG 流日志](./network-watcher-nsg-flow-logging-overview.md)可用于在[网络安全组 (NSG)](../virtual-network/network-security-groups-overview.md) 中记录所有流量
  * [流量分析](./traffic-analytics.md)处理 NSG 流日志数据，使你能够可视化、查询、分析和了解网络流量。


有关更多详细信息，请参阅[网络观察程序概述页](./network-watcher-monitoring-overview.md)。


### <a name="how-does-network-watcher-pricing-work"></a>网络观察程序如何定价？
有关网络观察程序组件及其定价，请访问[定价页](https://azure.microsoft.com/pricing/details/network-watcher/)。

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>哪些区域支持/提供网络观察程序？
可以在 [Azure 服务可用性](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)页上查看最新的区域可用性

### <a name="which-permissions-are-needed-to-use-network-watcher"></a>使用网络观察程序需要哪些权限？
请查看[使用网络观察程序所需的 Azure RBAC 权限](./required-rbac-permissions.md)的列表。 若要部署资源，需要对 NetworkWatcherRG 的参与者权限（见下）。

### <a name="how-do-i-enable-network-watcher"></a>如何启用网络观察程序？
网络观察程序服务是为每个订阅[自动启用](https://azure.microsoft.com/updates/azure-network-watcher-will-be-enabled-by-default-for-subscriptions-containing-virtual-networks/)的。

### <a name="what-is-the-network-watcher-deployment-model"></a>网络观察程序部署模型是什么？
网络观察程序父资源是使用每个区域中的唯一实例部署的。 命名格式：NetworkWatcher_RegionName。 示例：NetworkWatcher_centralus 是“美国中部”区域的网络观察程序资源。

### <a name="what-is-the-networkwatcherrg"></a>NetworkWatcherRG 是什么？
网络观察程序资源位于自动创建的、隐藏的 **NetworkWatcherRG** 资源组中。 例如，NSG 流日志资源是网络观察程序的子资源，在 NetworkWatcherRG 中启用。

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>为什么需要安装网络观察程序扩展？ 
对于任何需要生成或拦截来自 VM 的流量的功能，网络观察程序扩展都是必需的。 

### <a name="which-features-require-the-network-watcher-extension"></a>哪些功能需要网络观察程序扩展？
数据包捕获、连接故障排除和连接监视器功能需要网络观察程序扩展。

### <a name="what-are-resource-limits-on-network-watcher"></a>网络观察程序的资源限制是怎样的？
有关所有限制，请参阅[服务限制](../azure-resource-manager/management/azure-subscription-service-limits.md#network-watcher-limits)页。  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>为何每个区域只允许一个网络观察程序实例？ 
仅需为订阅启用网络观察程序一次，即可正常使用其功能，这并不是一项服务限制。

### <a name="how-can-i-manage-the-network-watcher-resource"></a>如何管理网络观察程序资源？ 
网络观察程序资源代表网络观察程序的后端服务，由 Azure 完全托管。 客户无需管理它。 不支持移动资源等操作。 但[可以删除资源](./network-watcher-create.md#delete-a-network-watcher-in-the-portal)。 

## <a name="service-availability-and-redundancy"></a>服务可用性和冗余性 

### <a name="is-the-network-watcher-service-zone-resilient"></a>网络观察程序服务是否具有区域复原能力？ 
是的。 默认情况下，网络观察程序服务具有区域复原能力。 

### <a name="how-do-i-configure-the-network-watcher-service-to-be-zone-resilient"></a>如何配置网络观察程序服务，使其具有区域复原能力？ 
客户无需配置即可启用区域复原能力。 默认情况下，网络观察程序资源的区域复原能力可用，并由服务本身管理。 

## <a name="nsg-flow-logs"></a>NSG 流日志

### <a name="what-does-nsg-flow-logs-do"></a>NSG 流日志有什么作用？
可以通过[网络安全组 (NSG)](../virtual-network/network-security-groups-overview.md) 来合并和管理 Azure 网络资源。 使用 NSG 流日志可以通过 NSG 记录有关所有流量的 5 元组流信息。 原始流日志将写入 Azure 存储帐户，在存储帐户中，可以根据需要进一步处理、分析、查询或导出这些日志。

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>当存储帐户位于防火墙后面时，如何使用 NSG 流日志？

若要使用防火墙后面的存储帐户，必须提供一个例外，以便受信任的 Microsoft 服务访问你的存储帐户：

* 在门户或[“存储帐户”页](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)的全局搜索框中键入存储帐户的名称，导航到存储帐户
* 在“设置”部分，选择“防火墙和虚拟网络” 
* 在“允许的访问来源”中，选择“所选网络”。 然后，在“例外”下，勾选“允许受信任的 Microsoft 服务访问此存储帐户”旁边的框 
* 如果已选中，则不需进行更改。  
* 在 [NSG 流日志概述页](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)上找到目标 NSG，并启用选择了上述存储帐户的 NSG 流日志。

可以在数分钟后检查存储日志，应该会看到时间戳已更新，或者会看到新的 JSON 文件已创建。

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>当存储帐户位于服务终结点后面时，如何使用 NSG 流日志？

NSG 流日志与服务终结点兼容，无需任何额外的配置。 请参阅[有关如何在虚拟网络中启用服务终结点的教程](../virtual-network/tutorial-restrict-network-access-to-resources.md#enable-a-service-endpoint)。


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>流日志版本 1 和 2 有何区别？
流日志版本 2 引入了“流状态”的概念，并会存储有关传输的字节和数据包的信息。 [了解详细信息](./network-watcher-nsg-flow-logging-overview.md#log-format)。

## <a name="next-steps"></a>后续步骤
 - 访问我们的[文档概述页](./index.yml)，其中提供了一些网络观察程序入门教程。