---
title: Azure 网络安全组概述
titlesuffix: Azure Virtual Network
description: 了解网络安全组。 网络安全组可帮助你筛选 Azure 资源之间的网络流量。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: kumud
ms.reviewer: kumud
ms.custom: contperf-fy21q1
ms.openlocfilehash: 4e23c6f25145724a5300c9e5cdcb55431fb0b4f2
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028761"
---
# <a name="network-security-groups"></a>网络安全组
<a name="network-security-groups"></a>

可以使用 Azure 网络安全组来筛选 Azure 虚拟网络中出入 Azure 资源的网络流量。 网络安全组包含[安全规则](#security-rules)，这些规则可允许或拒绝多种 Azure 资源的入站和出站网络流量。 可以为每项规则指定源和目标、端口以及协议。

本文介绍网络安全组规则的属性、应用的[默认安全规则](#default-security-rules)，以及可以修改以创建[扩充安全规则](#augmented-security-rules)的规则属性。

## <a name="security-rules"></a><a name="security-rules"></a> 安全规则

一个网络安全组包含零个或者不超过 Azure 订阅[限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)的任意数量的规则。 每个规则指定以下属性：

|属性  |说明  |
|---------|---------|
|名称|网络安全组中的唯一名称。|
|优先级 | 介于 100 和 4096 之间的数字。 规则按优先顺序进行处理。先处理编号较小的规则，因为编号越小，优先级越高。 一旦流量与某个规则匹配，处理即会停止。 因此，不会处理优先级较低（编号较大）的、其属性与高优先级规则相同的所有规则。|
|源或目标| 可以是任何值，也可以是单个 IP 地址、无类别域际路由 (CIDR) 块（例如 10.0.0.0/24）、服务标记或应用程序安全组。 如果为 Azure 资源指定一个地址，请指定分配给该资源的专用 IP 地址。 在 Azure 针对入站流量将公共 IP 地址转换为专用 IP 地址后，系统会处理网络安全组，然后由 Azure 针对出站流量将专用 IP 地址转换为公共 IP 地址。 . 指定范围、服务标记或应用程序安全组可以减少创建的安全规则数。 在一个规则中指定多个单独的 IP 地址和范围（不能指定多个服务标记或应用程序组）的功能称为[扩充式安全规则](#augmented-security-rules)。 只能在通过资源管理器部署模型创建的网络安全组中创建扩充式安全规则。 在通过经典部署模型创建的网络安全组中，不能指定多个 IP 地址和 IP 地址范围。|
|协议     | TCP、UDP、ICMP 或 Any。|
|方向| 该规则是应用到入站还是出站流量。|
|端口范围     |可以指定单个端口或端口范围。 例如，可以指定 80 或 10000-10005。 指定范围可以减少创建的安全规则数。 只能在通过资源管理器部署模型创建的网络安全组中创建扩充式安全规则。 在通过经典部署模型创建的网络安全组中，不能在同一个安全规则中指定多个端口或端口范围。   |
|操作     | 允许或拒绝        |

在允许或拒绝流量之前，将使用 5 元组信息（源、源端口、目标、目标端口和协议）按优先级对网络安全组安全规则进行评估。 不能创建两个具有相同优先级和方向的安全规则。 将为现有连接创建流记录。 是允许还是拒绝通信取决于流记录的连接状态。 流记录允许网络安全组有状态。 例如，如果针对通过端口 80 访问的任何地址指定了出站安全规则，则不需要指定入站安全规则来响应出站流量。 如果通信是从外部发起的，则只需指定入站安全规则。 反之亦然。 如果允许通过某个端口发送入站流量，则不需要指定出站安全规则来响应通过该端口发送的流量。

删除启用了流的安全规则时，现有连接不一定会中断。 当连接停止并且至少几分钟内在任一方向都没有流量流过时，流量流会中断。

在网络安全组中创建的安全规则存在数量限制。 有关详细信息，请参阅 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

### <a name="default-security-rules"></a><a name="default-security-rules"></a> 默认安全规则

Azure 在你所创建的每个网络安全组中创建以下默认规则：

#### <a name="inbound"></a>入站

##### <a name="allowvnetinbound"></a>AllowVNetInBound

|优先级|Source|源端口|目标|目标端口|协议|访问|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|任意|允许|

##### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|优先级|Source|源端口|目标|目标端口|协议|访问|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|任意|允许|

##### <a name="denyallinbound"></a>DenyAllInbound

|优先级|Source|源端口|目标|目标端口|协议|访问|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|任意|拒绝|

#### <a name="outbound"></a>出站

##### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|优先级|Source|源端口| 目标 | 目标端口 | 协议 | 访问 |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | 任意 | 允许 |

##### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|优先级|Source|源端口| 目标 | 目标端口 | 协议 | 访问 |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | 任意 | 允许 |

##### <a name="denyalloutbound"></a>DenyAllOutBound

|优先级|Source|源端口| 目标 | 目标端口 | 协议 | 访问 |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | 任意 | 拒绝 |

在“源”和“目标”列表中，“VirtualNetwork”、“AzureLoadBalancer”和“Internet”是[服务标记](service-tags-overview.md)，而不是 IP 地址。    在“协议”列中，**Any** 包含 TCP、UDP 和 ICMP。 创建规则时，可以指定 TCP、UDP、ICMP 或 Any。 “源”和“目标”列中的“0.0.0.0/0”表示所有地址。  Azure 门户、Azure CLI 或 PowerShell 等客户端可以使用“*”或任何字符来表示此表达式。
 
不能删除默认规则，但可以通过创建更高优先级的规则来替代默认规则。

### <a name="augmented-security-rules"></a><a name="augmented-security-rules"></a> 扩充式安全规则

扩充式安全规则简化了虚拟网络的安全定义，可让我们以更少的规则定义更大、更复杂的网络安全策略。 可将多个端口和多个显式 IP 地址和范围合并成一个易于理解的安全规则。 可在规则的源、目标和端口字段中使用扩充式规则。 若要简化安全规则定义的维护，可将扩充式安全规则与[服务标记](service-tags-overview.md)或[应用程序安全组](#application-security-groups)合并。 可在规则中指定的地址、范围和端口的数量存在限制。 有关详细信息，请参阅 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

#### <a name="service-tags"></a>服务标记

服务标记代表给定 Azure 服务中的一组 IP 地址前缀。 它有助于将频繁更新网络安全规则的复杂性降至最低。

有关详细信息，请参阅 [Azure 服务标记](service-tags-overview.md)。 有关如何使用存储服务标记限制网络访问的示例，请参阅[限制对 PaaS 资源的网络访问](tutorial-restrict-network-access-to-resources.md)。

#### <a name="application-security-groups"></a>应用程序安全组

使用应用程序安全组可将网络安全性配置为应用程序结构的固有扩展，从而可以基于这些组将虚拟机分组以及定义网络安全策略。 可以大量重复使用安全策略，而无需手动维护显式 IP 地址。 若要了解详细信息，请参阅[应用程序安全组](application-security-groups.md)。

## <a name="azure-platform-considerations"></a>Azure 平台注意事项

- **主机节点的虚拟 IP**：基本的基础结构服务（如 DHCP、DNS、IMDS 和运行状况监视）通过虚拟化主机 IP 地址 168.63.129.16 和 169.254.169.254 提供。 这些 IP 地址属于 Microsoft，是仅有的用于所有区域的虚拟化 IP 地址，没有其他用途。 有效安全规则和有效路由不会包括这些平台规则。 若要替代此基本基础结构通信，可以在网络安全组规则上使用以下[服务标记](service-tags-overview.md)创建一个安全规则来拒绝流量：AzurePlatformDNS、AzurePlatformIMDS、AzurePlatformLKM。 了解如何[诊断网络流量筛选](diagnose-network-traffic-filter-problem.md)和[诊断网络路由](diagnose-network-routing-problem.md)。
- **许可（密钥管理服务）** ：在虚拟机中运行的 Windows 映像必须获得许可。 为了确保许可，会向处理此类查询的密钥管理服务主机服务器发送请求。 该请求是通过端口 1688 以出站方式提出的。 对于使用[默认路由 0.0.0.0/0](virtual-networks-udr-overview.md#default-route) 配置的部署，此平台规则会被禁用。
- **负载均衡池中的虚拟机**：应用的源端口和地址范围来自源计算机，而不是来自负载均衡器。 目标端口和地址范围是目标计算机的，而不是负载均衡器的。
- **Azure 服务实例**：在虚拟网络子网中部署了多个 Azure 服务的实例，例如 HDInsight、应用程序服务环境和虚拟机规模集。 有关可部署到虚拟网络的服务的完整列表，请参阅 [Azure 服务的虚拟网络](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)。 在将网络安全组应用到部署了资源的子网之前，请确保熟悉每个服务的端口要求。 如果拒绝服务所需的端口，服务将无法正常工作。
- **发送出站电子邮件**：Microsoft 建议你利用经过身份验证的 SMTP 中继服务（通常通过 TCP 端口 587 进行连接，但也经常使用其他端口）从 Azure 虚拟机发送电子邮件。 SMTP 中继服务特别重视发件人信誉，尽量降低第三方电子邮件提供商拒绝邮件的可能性。 此类 SMTP 中继服务包括但不限于：Exchange Online Protection 和 SendGrid。 在 Azure 中使用 SMTP 中继服务绝不会受限制，不管订阅类型如何。 

  如果是在 2017 年 11 月 15 日之前创建的 Azure 订阅，则除了能够使用 SMTP 中继服务，还可以直接通过 TCP 端口 25 发送电子邮件。 如果是在 2017 年 11 月 15 日之后创建的订阅，则可能无法直接通过端口 25 发送电子邮件。 经端口 25 的出站通信行为取决于订阅类型，如下所示：

     - **企业协议**：允许端口 25 的出站通信。 可以将出站电子邮件直接从虚拟机发送到外部电子邮件提供商，不受 Azure 平台的限制。 
     - **即用即付：** 阻止所有资源通过端口 25 进行出站通信。 如需将电子邮件从虚拟机直接发送到外部电子邮件提供商（不使用经身份验证的 SMTP 中继），可以请求去除该限制。 Microsoft 会自行审核和批准此类请求，并且只在进行防欺诈检查后授予相关权限。 若要提交请求，请建立一个问题类型为“技术”、“虚拟网络连接”、“无法发送电子邮件（SMTP/端口 25）”的支持案例。   在支持案例中，请详细说明为何你的订阅需要将电子邮件直接发送到邮件提供商，而不经过经身份验证的 SMTP 中继。 如果订阅得到豁免，则只有在豁免日期之后创建的虚拟机能够经端口 25 进行出站通信。
     - **MSDN、Azure Pass、Azure 开放许可、教育、BizSpark 和免费试用版**：阻止所有资源经端口 25 进行出站通信。 不能请求去除该限制，因为不会针对请求授予相关权限。 若需从虚拟机发送电子邮件，则需使用 SMTP 中继服务。
     - **云服务提供商**：如果无法使用安全的 SMTP 中继，通过云服务提供商消耗 Azure 资源的客户可以通过其云服务提供商创建支持案例，并请求提供商代表他们创建取消阻止案例。

  即使 Azure 允许经端口 25 发送电子邮件，Microsoft 也不能保证电子邮件提供商会接受来自你的虚拟机的入站电子邮件。 如果特定的提供商拒绝了来自你的虚拟机的邮件，请直接与该提供商协商解决邮件传送问题或垃圾邮件过滤问题，否则只能使用经身份验证的 SMTP 中继服务。

## <a name="next-steps"></a>后续步骤

* 若要了解哪些 Azure 资源可以部署到虚拟网络中并与网络安全组关联，请参阅 [Azure 服务的虚拟网络集成](virtual-network-for-azure-services.md)
* 若要了解如何通过网络安全组评估流量，请参阅[网络安全组工作原理](network-security-group-how-it-works.md)。
* 如果从未创建过网络安全组，可以先完成一个快速[教程](tutorial-filter-network-traffic.md)，获取一些创建经验。
* 如果已熟悉网络安全组，需要对其进行管理，请参阅[管理网络安全组](manage-network-security-group.md)。 
* 如果有通信问题，需要对网络安全组进行故障排除，请参阅[诊断虚拟机网络流量筛选器问题](diagnose-network-traffic-filter-problem.md)。 
* 了解如何通过[网络安全组流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)来分析出入具有关联网络安全组的资源的网络流量。
