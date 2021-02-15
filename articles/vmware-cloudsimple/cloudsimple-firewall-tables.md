---
title: Azure VMware 解决方案（按 CloudSimple）-防火墙表
description: 了解 CloudSimple 私有云防火墙表和防火墙规则，包括在每个防火墙表上创建的默认规则。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8c44c39f66a0a0161eea8a7e9656bbe0e3d1015c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88140864"
---
# <a name="firewall-tables-overview"></a>防火墙表概述

防火墙表列出了筛选进出私有云资源的网络流量的规则。 可以将防火墙表应用到 VLAN/子网。 规则控制源网络或 IP 地址与目标网络或 IP 地址之间的网络流量。

## <a name="firewall-rules"></a>防火墙规则

下表描述了防火墙规则中的参数。

| 属性 | 详细信息 |
| ---------| --------|
| **名称** | 唯一标识防火墙规则及其用途的名称。 |
| **Priority** | 介于100和4096之间的数字，其中100是最高优先级。 规则按优先顺序处理。 当流量遇到规则匹配时，将停止规则处理。 因此，优先级较低的规则不会处理具有较高优先级的规则。  请注意避免规则冲突。 |
| **状态跟踪** | 跟踪可以是无状态 (私有云、Internet 或 VPN) 或有状态 (公共 IP) 。  |
| **协议** | 选项包括 Any、TCP 或 UDP。 如果需要 ICMP，请使用 Any。 |
| **方向** | 该规则是应用到入站还是出站流量。 |
| **操作** | 允许或拒绝规则中定义的流量类型。 |
| **Source** | IP 地址、无类别的域间路由 (CIDR) 块 (10.0.0.0/24，例如) 或 Any。  通过指定范围、服务标记或应用程序安全组，您可以创建更少的安全规则。 |
| **源端口** | 从中产生网络流量的端口。  可以指定单个端口或端口范围，例如443或8000-8080。 指定范围可以减少创建的安全规则数。 |
| **目标** | IP 地址、无类别的域间路由 (CIDR) 块 (10.0.0.0/24，例如) 或 Any。  通过指定范围、服务标记或应用程序安全组，您可以创建更少的安全规则。  |
| **目标端口** | 网络流量流动到的端口。  可以指定单个端口或端口范围，例如443或8000-8080。 指定范围可以减少创建的安全规则数。|

### <a name="stateless"></a>无状态

无状态规则仅查找单个数据包，并根据规则对其进行筛选。  
相反方向的流量可能需要其他规则。  对以下点之间的流量使用无状态规则：

* 私有云的子网
* 本地子网和私有云子网
* 来自私有云的 Internet 流量

### <a name="stateful"></a>有状态

 有状态规则知道通过它的连接。 将为现有连接创建流记录。 是允许还是拒绝通信取决于流记录的连接状态。  使用此规则类型作为公共 IP 地址来筛选来自 Internet 的流量。

### <a name="default-rules"></a>默认规则

将在每个防火墙表上创建以下默认规则。

|优先级|名称|状态跟踪|方向|流量类型|协议|源|Source Port|目标|Destination Port|操作|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|允许-全到 internet|有状态|出站|公共 IP 或 internet 流量|All|Any|任意|任意|任意|允许|
|65001|拒绝-从 internet|有状态|入站|公共 IP 或 internet 流量|All|Any|任意|任意|任意|拒绝|
|65002|允许-全部到 intranet|无状态|出站|私有云内部或 VPN 流量|All|Any|任意|任意|任意|Allow|
|65003|allow-内部-intranet|无状态|入站|私有云内部或 VPN 流量|All|Any|任意|任意|任意|Allow|

## <a name="next-steps"></a>后续步骤

* [设置防火墙表和规则](firewall.md)
