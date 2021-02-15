---
title: Azure ExpressRoute：监视、指标和警报
description: 了解使用 Azure Monitor（其中集中了 Azure 的所有指标、警报和诊断日志）的 Azure ExpressRoute 监视、指标和警报。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/11/2020
ms.author: duau
ms.openlocfilehash: 7a5da35da35b2f447256bc742681ccd7a7d403da
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091543"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute 监视、指标和警报

本文可帮助你使用 Azure Monitor 了解 ExpressRoute 监视、指标和警报。 Azure Monitor 是整个 Azure 中所有指标、警报和诊断日志的一站式商店。
 
>[!NOTE]
>建议不要使用 **经典指标**。
>

## <a name="expressroute-metrics"></a>ExpressRoute 指标

若要查看“指标”，请导航到“Azure Monitor”页，单击“指标”。    若要查看 ExpressRoute 指标，请按资源类型“ExpressRoute 线路”进行筛选   。 若要查看 **Global Reach** 指标，请按资源类型“ExpressRoute 线路”进行筛选，然后选择一个已启用 Global Reach 的 ExpressRoute 线路资源。  若要查看 **ExpressRoute Direct** 指标，请按“ExpressRoute 端口”筛选资源类型。  

选择指标后，将应用默认聚合。 （可选）可以应用拆分，它将显示具有不同维度的指标。

### <a name="available-metrics"></a>可用指标

|**指标**|**类别**|**维度**|**功能**|
| --- | --- | --- | --- |
|ARP 可用性|可用性|<ui><li>对等机（主要/辅助 ExpressRoute 路由器）</ui></li><ui><li> 对等互连类型（专用/公共/Microsoft）</ui></li>|ExpressRoute|
|BGP 可用性|可用性|<ui><li> 对等机（主要/辅助 ExpressRoute 路由器）</ui></li><ui><li> 对等互连类型</ui></li>|ExpressRoute|
|BitsInPerSecond|交通|<ui><li> 对等互连类型 (ExpressRoute)</ui></li><ui><li>链路 (ExpressRoute Direct)</ui></li>|<li>ExpressRoute</li><li>ExpressRoute Direct</li><ui><li>ExpressRoute 网关连接</ui></li>|
|BitsOutPerSecond|交通| <ui><li>对等互连类型 (ExpressRoute)</ui></li><ui><li> 链路 (ExpressRoute Direct) |<ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li><ui><li>ExpressRoute 网关连接</ui></li>|
|CPU 使用率|性能| <ui><li>实例</ui></li>|ExpressRoute 虚拟网络网关|
|每秒数据包数|性能| <ui><li>实例</ui></li>|ExpressRoute 虚拟网络网关|
|播发到对等方的路由计数 |可用性| <ui><li>实例</ui></li>|ExpressRoute 虚拟网络网关|
|从对等方获知的路由计数 |可用性| <ui><li>实例</ui></li>|ExpressRoute 虚拟网络网关|
|路由更改频率 |可用性| <ui><li>实例</ui></li>|ExpressRoute 虚拟网络网关|
|虚拟网络中的 Vm 数 |可用性| 不适用 |ExpressRoute 虚拟网络网关|
|GlobalReachBitsInPerSecond|交通|<ui><li>对等互连线路密钥（服务密钥）</ui></li>|Global Reach|
|GlobalReachBitsOutPerSecond|交通|<ui><li>对等互连线路密钥（服务密钥）</ui></li>|Global Reach|
|AdminState|物理连接|链接|ExpressRoute Direct|
|LineProtocol|物理连接|链接|ExpressRoute Direct|
|RxLightLevel|物理连接|<ui><li>链路</ui></li><ui><li>通道</ui></li>|ExpressRoute Direct|
|TxLightLevel|物理连接|<ui><li>链路</ui></li><ui><li>通道</ui></li>|ExpressRoute Direct|
>[!NOTE]
>只有在至少建立了一个 Global Reach 连接的情况下，才能使用 *GlobalGlobalReachBitsInPerSecond* 和 *GlobalGlobalReachBitsOutPerSecond*。
>

## <a name="circuits-metrics"></a>线路指标

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>进位和出位 - 所有对等互连的指标

可以查看给定 ExpressRoute 线路上所有对等互连的指标。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="线路指标":::

### <a name="bits-in-and-out---metrics-per-peering"></a>进位和出位 - 每个对等互连的指标

可以查看专用、公共和 Microsoft 对等互连的指标（以位/秒为单位）。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="每个对等互连的指标":::

### <a name="bgp-availability---split-by-peer"></a>BGP 可用性 - 按对等机拆分  

可以查看跨对等互连和对等机（主要和辅助 ExpressRoute 路由器）的近实时 BGP 可用性。 此仪表板显示为专用对等互连启动的主要 BGP 会话以及为专用对等互连关闭的第二个 BGP 会话。 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="每个对等机的 BGP 可用性":::

### <a name="arp-availability---split-by-peering"></a>ARP 可用性 - 按对等互连拆分  

可以查看跨对等互连和对等机（主要和辅助 ExpressRoute 路由器）的近实时 [ARP](./expressroute-troubleshooting-arp-resource-manager.md) 可用性。 此仪表板显示跨两个对等机启动的专用对等互连 ARP 会话，该会话在跨 Microsoft 对等互连时会彻底关闭。 已跨两个对等机利用了默认聚合（平均）。  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="每个对等机的 ARP 可用性":::

## <a name="expressroute-direct-metrics"></a>ExpressRoute Direct 指标

### <a name="admin-state---split-by-link"></a>管理状态 - 按链路拆分

可以查看 ExpressRoute Direct 端口对的每个链路的管理状态。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="ER 直接管理状态":::

### <a name="bits-in-per-second---split-by-link"></a>每秒传入位数 - 按链路拆分

可以查看 ExpressRoute Direct 端口对的跨两个链路的每秒传入位数。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="每秒的 ER 直接位数":::

### <a name="bits-out-per-second---split-by-link"></a>每秒传出位数 - 按链路拆分

还可以查看 ExpressRoute Direct 端口对的跨两个链路的每秒传出位数。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="每秒的 ER 直接位数":::

### <a name="line-protocol---split-by-link"></a>线路协议 - 按链路拆分

可以查看 ExpressRoute Direct 端口对的跨每个链路的线路协议。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="ER 直接连线协议":::

### <a name="rx-light-level---split-by-link"></a>Rx 轻型级别 - 按链路拆分

可以查看每个端口的 Rx 轻型级别（ExpressRoute Direct 端口的轻型级别是 **接收**）。 正常的 Rx 轻型级别通常在 -10 到 0 dBm 范围内

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="ER 直接行 Rx 浅色级别":::

### <a name="tx-light-level---split-by-link"></a>Tx 轻型级别 - 按链路拆分

可以查看每个端口的 Tx 轻型级别（ExpressRoute Direct 端口的轻型级别为 **传输**）。 正常的 Tx 轻型级别通常在 -10 到 0 dBm 范围内

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="ER 直接行 Tx 电平":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>ExpressRoute 虚拟网络网关指标

### <a name="cpu-utilization---split-instance"></a>CPU 使用率 - 拆分实例

可以查看网关实例的 CPU 使用率。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="CPU 拆分":::

### <a name="packets-per-second---split-by-instance"></a>每秒数据包数 - 按实例拆分

可以查看每秒遍历网关的数据包数。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="每秒数据包数 - 拆分":::

### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>按实例播发到对等互连的路由计数

可以查看播发到 ExpressRoute 线路的路由数。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="播发到对等方的路由计数":::

### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>从对等方拆分的按实例获知的路由计数

您可以查看从 ExpressRoute 线路接收的路由数。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="从对等方获知的路由计数":::

### <a name="frequency-of-routes-change---split-by-instance"></a>路由更改频率-按实例拆分

可以查看路由在网关上的更改频率。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="路由的频率已更改":::

### <a name="number-of-vms-in-the-virtual-network"></a>虚拟网络中的 Vm 数

可以查看虚拟网络中的虚拟机数。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="虚拟网络中的虚拟机数":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute 网关连接（以位/秒为单位）

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="网关连接":::

## <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute 网关连接的警报

1. 若要配置警报，请导航到 **Azure Monitor**，然后选择“警报”。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="alerts":::
2. 单击“+选择目标”，然后选择 ExpressRoute 网关连接资源。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="目标":::
3. 定义警报详细信息。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="操作组":::
4. 定义和添加操作组。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="添加操作组":::

## <a name="alerts-based-on-each-peering"></a>基于每个对等互连的警报

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="每个对等":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>为线路上的活动日志配置警报

在 **警报条件** 中，可以选择“活动日志”作为信号类型并选择“信号”。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="活动日志":::

## <a name="additional-metrics-in-log-analytics"></a>Log Analytics 中的其他指标

还可以通过导航到 ExpressRoute 线路资源并选择“日志”选项卡来查看 ExpressRoute 指标。对于你查询的任何指标，输出将包含以下列。

|**列**|类型|**说明**|
| --- | --- | --- |
|TimeGrain|字符串|PT1M（每分钟推送一次指标值）|
|Count|real|通常等于 2（每个 MSEE 每分钟推送一个指标值）|
|最低配置|real|两个 MSEE 推送的两个指标值中的最小值|
|最大值|real|两个度量值的最大值 Msee|
|平均值|real|等于 (最小值 + 最大值)/2|
|总计|real|来自两个 MSEE 的两个指标值的总和（所查询指标的需关注的主要值）|
  
## <a name="next-steps"></a>后续步骤

配置 ExpressRoute 连接。
  
* [创建和修改线路](expressroute-howto-circuit-arm.md)
* [创建和修改对等配置](expressroute-howto-routing-arm.md)
* [将 VNet 链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)
