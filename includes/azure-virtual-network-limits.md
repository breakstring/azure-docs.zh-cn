---
title: include 文件
description: include 文件
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 01/14/2020
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 59329fccda77f16e4a595e9b1789ef684c5cbf95
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98256443"
---
### <a name="networking-limits---azure-resource-manager"></a><a name="azure-resource-manager-virtual-networking-limits"></a>网络限制 - Azure 资源管理器
以下限制仅适用于通过每个订阅的每个区域的 Azure 资源管理器进行管理的网络资源。 了解如何[针对订阅限制查看当前资源使用情况](../articles/networking/check-usage-against-limits.md)。

> [!NOTE]
> 我们最近将所有默认限制提高到了最大限制。 如果没有最大限制列，则资源没有可调整的限制。 如果过去已通过客户支持提高了这些上限，因此在以下表中看不到更新的限制，可[免费提交联机客户支持请求](../articles/azure-resource-manager/templates/error-resource-quota.md)

| 资源 | 限制 | 
| --- | --- |
| 虚拟网络 |1,000 |
| 每个虚拟网络的子网数 |3,000 |
| 每个虚拟网络的虚拟网络对等互连数 |500 |
| [每个虚拟网络的虚拟网关（VPN 网关）](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [每个虚拟网络的虚拟网关（ExpressRoute 网关）](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| 每个虚拟网络的 DNS 服务器数 |20 个 |
| 每个虚拟网络的专用 IP 地址数 |65,536 |
| 每个网络接口的专用 IP 地址数 |256 |
| 每个虚拟机的专用 IP 地址数 |256 |
| 每个网络接口的公共 IP 地址数 |256 |
| 每个虚拟机的公共 IP 地址数 |256 |
| [虚拟机或角色实例的单 NIC 并发 TCP 或 UDP 流数](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-active-connections-recommendations) |500,000 |
| 网络接口卡数 |65,536 |
| 网络安全组 |5,000 |
| 每个 NSG 的 NSG 规则数 |1,000 |
| 为安全组中的源或目标指定的 IP 地址和范围数 |4,000 |
| 应用程序安全组 |3,000 |
| 每个 IP 配置和每个 NIC 的应用程序安全组数 |20 个 |
| 每个应用程序安全组的 IP 配置数 |4,000 |
| 可在网络安全组的所有安全规则中指定的应用程序安全组数 |100 |
| 用户定义路由表数 |200 |
| 每个路由表的用户定义的路由数 |400 |
| 每个 Azure VPN 网关的点到站点根证书数 |20 个 |
| 虚拟网络 TAP |100 |
| 每个虚拟网络 TAP 的网络接口 TAP 配置 |100 |

#### <a name="public-ip-address-limits"></a><a name="publicip-address"></a>公共 IP 地址限制
| 资源 | 默认限制 | 最大限制 |
| --- | --- | --- |
| 公共 IP 地址<sup>1</sup> | 基本版为 10。 | 请联系支持人员。 |
| 静态公共 IP 地址<sup>1</sup> | 基本版为 10。 | 请联系支持人员。 |
| 标准公共 IP 地址<sup>1</sup> | 10 | 请联系支持人员。 |
| [每个资源组的公共 IP 地址](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md#microsoftnetwork) | 800 | 请联系支持人员。 | 
| 公共 IP 前缀 | 受订阅中的标准公共 IP 数限制 | 请联系支持人员。 |
| 公共 IP 前缀长度 | /28 | 请联系支持人员。 |

<sup>1</sup>公共 IP 地址的默认限制根据产品/服务类别类型（例如免费试用、即用即付、CSP）而有所不同。 例如，企业协议订阅数的默认值为 1000。

#### <a name="load-balancer-limits"></a><a name="load-balancer"></a>负载均衡器限制
以下限制仅适用于通过每个订阅的每个区域的 Azure Resource Manager 进行管理的网络资源。 了解如何[针对订阅限制查看当前资源使用情况](../articles/networking/check-usage-against-limits.md)。

**标准负载均衡器**

| 资源                                | 限制         |
|-----------------------------------------|-------------------------------|
| 负载均衡器                          | 1,000                         |
| 每个资源的规则数                      | 1,500                         |
| 每个 NIC 的规则数（在 NIC 上的所有 IP 上） | 300                           |
| 前端 IP 配置数              | 600                           |
| 后端池大小                       | 单个虚拟网络 1000 个 IP 配置 |
| 每个负载均衡器的后端资源数 <sup>1<sup> | 250                   |
| 高可用性端口                 | 每个内部前端 1 个       |
| 每个负载均衡器的出站规则        | 600                           |
| 每个 VM 的负载均衡器数                   | 2（1 个公共，1 个内部）   |

<sup>1</sup>限制是最多 150 种资源，采用独立虚拟机资源、可用性集资源和虚拟机规模集放置组的任意组合。

**基本负载均衡器**

| 资源                                | 限制        |
|-----------------------------------------|------------------------------|
| 负载均衡器                          | 1,000                        |
| 每个资源的规则数                      | 250                          |
| 每个 NIC 的规则数（在 NIC 上的所有 IP 上） | 300                          |
| 前端 IP 配置数              | 200                          |
| 后端池大小                       | 单个可用性集 300 个 IP 配置 |
| 每个负载均衡器的可用性集数     | 1                            |
| 每个 VM 的负载均衡器数                   | 2（1 个公共，1 个内部）  |

<a name="virtual-networking-limits-classic"></a>以下限制仅适用于每个订阅通过经典部署模型托管的网络资源。 了解如何[针对订阅限制查看当前资源使用情况](../articles/networking/check-usage-against-limits.md)。

| 资源 | 默认限制 | 最大限制 |
| --- | --- | --- |
| 虚拟网络 |100 |100 |
| 本地网络站点 |20 |50 |
| 每个虚拟网络的 DNS 服务器数 |20 |20 |
| 每个虚拟网络的专用 IP 地址数 |4,096 |4,096 |
| 虚拟机或角色实例的单 NIC 并发 TCP 或 UDP 流数 |如果 NIC 至少有两个，则为 500,000（至多 1,000,000）。 |如果 NIC 至少有两个，则为 500,000（至多 1,000,000）。 |
| 网络安全组 (NSG) |200 |200 |
| 每个 NSG 的 NSG 规则数 |200 |1,000 |
| 用户定义路由表数 |200 |200 |
| 每个路由表的用户定义的路由数 |400 |400 |
| 公共 IP 地址 (动态) |500 |500 |
| 保留的公共 IP 地址 |500 |500 |
| 每个部署的公共 IP |5 |联系支持人员 |
| 每个部署的专用 IP（内部负载均衡） |1 |1 |
| 终结点访问控制列表 (ACL) |50 |50 |
