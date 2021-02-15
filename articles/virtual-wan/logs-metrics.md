---
title: 日志和指标
titleSuffix: Azure Virtual WAN
description: 了解 Azure 虚拟 WAN 日志和指标
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/05/2020
ms.author: cherylmc
ms.openlocfilehash: 62979a2cbe2a5912476ca65327a06eef9c36c1cb
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127883"
---
# <a name="azure-virtual-wan-logs-and-metrics"></a>Azure 虚拟 WAN 日志和指标

你可以使用 Azure Monitor 监视 Azure 虚拟 WAN。 虚拟 WAN 是一个网络服务，其中整合了多种网络、安全和路由功能，提供单一操作界面。 虚拟 WAN VPN 网关、ExpressRoute 网关和 Azure 防火墙都具有通过 Azure Monitor 提供的日志记录和指标。

本文讨论了可通过门户获得的指标和诊断。 指标是能够为近实时方案提供支持的轻型数据，因此，它们特别适合用于警报和快速检测问题。

## <a name="monitoring-secured-hub-azure-firewall"></a>监视受保护的集线器 (Azure 防火墙)  

可以使用 Azure 防火墙日志监视受保护的中心。 此外，可以使用活动日志来审核对 Azure 防火墙资源执行的操作。

如果已选择使用 Azure 防火墙保护虚拟中心，则可在此处获得相关的日志和指标： [Azure 防火墙日志和指标](../firewall/logs-and-metrics.md)。

## <a name="metrics"></a>指标

Azure Monitor 中的指标是数字值，用于描述系统某些方面在特定时间的情况。 指标每分钟收集一次，可用于警报，因为可对其频繁采样。 可以使用相对简单的逻辑快速激发警报。

### <a name="site-to-site-vpn-gateways"></a>站点到站点 VPN 网关

Azure 站点到站点 VPN 网关提供以下指标：

* **网关带宽** –网关的平均站点到站点聚合带宽（以每秒字节数为单位）。
* **隧道带宽** –隧道的平均带宽（以字节/秒为单位）。
* **隧道传出字节** 数–隧道的传出字节数。 
* **隧道传出数据包** –隧道的传出数据包计数。 
* **隧道传出 TS 不匹配数据包丢弃** –隧道的流量选择器的传出数据包丢弃计数。 
* **隧道入口字节** –隧道的传入字节数。 
* **隧道入口包** –隧道的传入数据包计数。 
* **隧道入口 TS 不匹配数据包丢弃** –来自隧道的流量选择器的传入数据包丢弃计数。 

### <a name="point-to-site-vpn-gateways"></a>点到站点 VPN 网关

Azure 点到站点 VPN 网关提供以下指标：

* **网关 P2S 带宽** –网关的平均点到站点聚合带宽（以字节/秒为单位）。
* **P2S 连接计数** -网关的点到站点连接计数。

### <a name="azure-expressroute-gateways"></a>Azure ExpressRoute 网关

Azure ExpressRoute 网关提供以下指标：

* **BitsInPerSecond** – Bits 引入 Azure per second。
* **BitsOutPerSecond** – Bits 传出 Azure per second。

### <a name="view-gateway-metrics"></a><a name="metrics-steps"></a>查看网关指标

以下步骤可帮助你查找和查看指标：

1. 在门户中，导航到具有网关的虚拟中心。

2. 选择“VPN (站点到站点)”以查找站点到站点网关，选择“ExpressRoute”以查找 ExpressRoute 网关，或选择“用户 VPN (点到站点)”以查找点到站点网关。 在页面上，你可以看到网关信息。 复制此信息。 稍后你将使用它通过 Azure Monitor 来查看诊断。

3. 选择“指标”。

   :::image type="content" source="./media/logs-metrics/metrics.png" alt-text="屏幕截图显示了站点到站点 VPN 窗格，其中选择了“在 Azure Monitor 中查看”。":::

4. 在“指标”页上，你可以查看感兴趣的指标。

   :::image type="content" source="./media/logs-metrics/metrics-page.png" alt-text="屏幕截图显示了“指标”页面，其中突出显示了类别。":::

## <a name="diagnostic-logs"></a><a name="diagnostic"></a>诊断日志

### <a name="site-to-site-vpn-gateways"></a>站点到站点 VPN 网关

Azure 站点到站点 VPN 网关提供以下诊断：

* **网关诊断日志** -特定于网关的诊断，例如运行状况、配置、服务更新以及其他诊断。
* **隧道诊断日志** –这些日志是与 ipsec 隧道相关的日志，例如站点到站点 IPsec 隧道、协商 SAs、断开连接原因以及其他诊断的连接和断开连接事件。
* **路由诊断日志** –这些日志与静态路由、BGP、路由更新以及其他诊断事件相关。
* **Ike 诊断日志** –适用于 IPsec 连接的特定于 ike 的诊断。

### <a name="point-to-site-vpn-gateways"></a>点到站点 VPN 网关

Azure 点到站点 VPN 网关提供以下诊断：

* **网关诊断日志** -特定于网关的诊断，例如运行状况、配置、服务更新以及其他诊断。
* **Ike 诊断日志** –适用于 IPsec 连接的特定于 ike 的诊断。
* **P2S 诊断日志** –这些是用户 VPN (点到站点) P2S 配置和客户端事件。 它们包括客户端连接/断开连接、VPN 客户端地址分配以及其他诊断。

### <a name="view-diagnostic-logs"></a><a name="diagnostic-steps"></a>查看诊断日志

以下步骤可帮助你查找和查看诊断：

1. 在门户中，导航到你的虚拟 WAN 资源。 在门户的“虚拟 WAN”页面的“概览”部分，选择“基本信息”以展开视图并获取资源组信息。 复制资源组信息。

   :::image type="content" source="./media/logs-metrics/3.png" alt-text="屏幕截图显示了“概览”部分，其中有一个箭头指向“复制”按钮。":::

2. 在“监视”部分，导航到资源组。 选择“诊断设置”，然后输入资源信息。 这是你在本文前面的步骤 2 中从[查看网关指标](#metrics-steps)部分复制的资源信息。

   :::image type="content" source="./media/logs-metrics/4.png" alt-text="屏幕截图显示了“监视”部分，其中有一个箭头指向“资源”下拉列表。":::

3. 在结果页上，选择“+ 添加诊断设置”，然后选择一个选项。 你可以选择发送到 Log Analytics、流式传输到事件中心，或者直接存档到某个存储帐户。

   :::image type="content" source="./media/logs-metrics/5.png" alt-text="“指标”页":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Log Analytics 示例查询

日志位于“Azure Log Analytics 工作区”中。 你可以在 Log Analytics 中设置查询。 以下示例包含一个查询，用于获取站点到站点路由诊断。

```AzureDiagnostics | where Category == "RouteDiagnosticLog"```

根据需要，替换下面位于 **= =** 之后的值。

* "GatewayDiagnosticLog"
* "IKEDiagnosticLog"
* "P2SDiagnosticLog"
* "TunnelDiagnosticLog"
* "RouteDiagnosticLog"

## <a name="activity-logs"></a><a name="activity-logs"></a>活动日志

默认情况下会收集活动日志条目，可在 Azure 门户中查看这些条目。 可以使用 Azure 活动日志（以前称为操作日志和审核日志）查看提交到 Azure 订阅的所有操作。 

## <a name="next-steps"></a>后续步骤

* 若要了解如何监视 Azure 防火墙日志和指标，请参阅[教程：监视 Azure 防火墙日志](../firewall/firewall-diagnostics.md)。
* 若要详细了解 Azure Monitor 中的指标，请参阅 [Azure Monitor 中的指标](../azure-monitor/platform/data-platform-metrics.md)。
