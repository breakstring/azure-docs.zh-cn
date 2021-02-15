---
title: include 文件
description: include 文件
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 11/06/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: d479c3087b971aa17cf145e0111890da07381eab
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2020
ms.locfileid: "94386578"
---
| 资源 | 限制 |
| --- | --- |
| 数据吞吐量 |30 Gbps<sup>1</sup> |
|规则|10,000。 已组合所有规则类型。|
|最高 DNAT 规则数目|单个公共 IP 地址 298 个。<br>任何其他公共 IP 地址都有助于可用的 SNAT 端口，但会减少可用的 DNAT 规则数。 例如，两个公共 IP 地址允许 297 个 DNAT 规则。 如果同时为 TCP 和 UDP 配置规则的协议，则会将其计为两个规则。|
|最小 AzureFirewallSubnet 大小 |/26|
|网络和应用程序规则的端口范围|1 - 65535|
|公共 IP 地址|最多 250 个。 所有公共 IP 地址都可以在 DNAT 规则中使用，它们都对可用的 SNAT 端口有影响。|
|IP 组中的 IP 地址|每个防火墙最多 100 个 IP 组。<br>每个 IP 组最多 5000 个单独的 IP 地址或 IP 前缀。
|路由表|默认情况下，AzureFirewallSubnet 使用其 NextHopType 值设置为“Internet”的 0.0.0.0/0 路由。<br><br>Azure 防火墙必须具有直接的 Internet 连接。 如果 AzureFirewallSubnet 知道通过 BGP 的本地网络的默认路由，则必须将其替代为 0.0.0.0/0 UDR，将 NextHopType 值设置为 Internet 以保持 Internet 直接连接 。 默认情况下，Azure 防火墙不支持强制的安全加密链路连接到本地网络。<br><br>但是，如果你的配置要求强制的安全加密链路连接到本地网络，Microsoft 将基于具体的情况提供支持。 请联系支持人员，以便我们可以查看你的情况。 如果接受，我们将允许你的订阅，并确保保持所需的防火墙 Internet 连接。|
|网络规则中的 FQDN|为了获得良好的性能，每个防火墙的所有网络规则中的 FQDN 不要超过 1000 个。|

<sup>1</sup>如果需要增加这些限制，请与 Azure 支持部门联系。
