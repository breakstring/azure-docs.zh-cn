---
title: 用于出站连接的 SNAT
description: 描述如何使用 Azure 负载均衡器执行 SNAT 来进行出站 Internet 连接
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: d1632c66791dd5e697b95a2c5aaaddea81629abf
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052816"
---
# <a name="using-snat-for-outbound-connections"></a>使用 SNAT 进行出站连接

可使用 Azure 公共负载均衡器的前端 IP 为后端实例提供到 Internet 的出站连接。 此配置使用源网络地址转换 (SNAT)。 SNAT 将后端的 IP 地址重写为负载均衡器的公共 IP 地址。 

SNAT 启用后端实例的 IP 伪装。 此伪装可以防止外部源直接访问后端实例。 在后端实例之间共享 IP 地址可降低静态公共 IP 的成本，并支持简化带有来自已知公共 IP 的流量的 IP 允许列表等场景。 

>[!Note]
> 对于需要大量出站连接的应用程序或需要在给定虚拟网络中使用一组 Ip 的企业客户，建议使用 [虚拟网络 NAT](../virtual-network/nat-overview.md) 作为建议解决方案。 通过它的动态分配，可进行简单配置，并最有效地使用每个 IP 地址的 SNAT 端口。 该方案还允许虚拟网络中的所有资源共享一组 IP 地址，而无需共享负载均衡器。

>[!Important]
> 即使未配置出站 SNAT，同一区域中的 Azure 存储帐户仍可访问，后端资源仍将有权访问 Microsoft 服务（如 Windows 更新）。

>[!NOTE] 
>本文仅涵盖了 Azure 资源管理器部署。 有关 Azure 中的所有经典部署方案，请查看[出站连接（经典）](/previous-versions/azure/load-balancer/load-balancer-outbound-connections-classic)。

## <a name="sharing-frontend-ip-address-across-backend-resources"></a><a name ="snat"></a> 跨后端资源共享前端 IP 地址

如果负载均衡器的后端资源没有实例级别公共 IP (ILPIP) 地址，则它们会通过公共负载均衡器的前端 IP 建立出站连接。 端口用于生成用于维护不同流的唯一标识符。 Internet 使用五元组来提供这种区别。

5 元组包含：

* 目标 IP
* 目标端口
* 源 IP
* 源端口和协议可以提供这种区别。

如果一个端口用于入站连接，它将有一个用于该端口上入站连接请求的侦听器，且它不能用于出站连接。 若要建立出站连接，必须使用临时端口为目标提供一个端口，在该端口上进行通信并维护不同的通信流。 当这些临时端口用于执行 SNAT 时，它们称为 SNAT 端口 

根据定义，每个 IP 地址具有 65,535 个端口。 每个端口都可以用于 TCP（传输控制协议）和 UDP（用户数据报协议）的入站或出站连接。 将公共 IP 地址作为前端 IP 添加到负载均衡器时，Azure 会提供 64,000 个可用作 SNAT 端口的端口。 

>[!NOTE]
> 用于负载均衡或入站 NAT 规则的每个端口将使用这 64,000 个端口中的 8 个端口，从而减少可用于 SNAT 的端口数量。 如果负载均衡或 NAT 规则与其他规则使用相同的八个端口，则不会使用额外的端口。 

通过[出站规则](./outbound-rules.md)和负载均衡规则，可以将这些 SNAT 端口分发到后端实例，使它们能够共享负载均衡器的公共 IP，实现出站连接。

如果配置了下面的[方案 2](#scenario2)，则每个后端实例的主机都将对出站连接中的数据包执行 SNAT。 当对来自后端实例的出站连接执行 SNAT 时，主机会将源 IP 重写为其中一个前端 IP。 为维护唯一流，主机将每个出站数据包的源端口重写为分配给后端实例的 SNAT 端口之一。

## <a name="outbound-connection-behavior-for-different-scenarios"></a>不同方案的出站连接行为
  * 具有公共 IP 的虚拟机。
  * 没有公共 IP 的虚拟机。
  * 没有公共 IP 且没有标准负载均衡器的虚拟机。
        

 ### <a name="scenario-1-virtual-machine-with-public-ip"></a><a name="scenario1"></a>方案 1：具有公共 IP 的虚拟机


 | 关联 | 方法 | IP 协议 |
 | ---------- | ------ | ------------ |
 | 公共负载均衡器或独立 | [SNAT（源网络地址转换）](#snat) </br> 未使用。 | TCP（传输控制协议） </br> UDP（用户数据报协议） </br> ICMP（Internet 控制消息协议） </br> ESP（封装安全有效负载） |


 #### <a name="description"></a>描述


 Azure 将分配给实例 NIC 的 IP 配置的公共 IP 用于所有出站流。 此实例具有所有可用的临时端口。 VM 是否负载均衡无关紧要。 此方案优先于其他方案。 


 分配到 VM 的公共 IP 属于 1 对 1 关系（而不是 1 对多关系），并实现为无状态的 1 对 1 NAT。


 ### <a name="scenario-2-virtual-machine-without-public-ip-and-behind-standard-public-load-balancer"></a><a name="scenario2"></a>场景 2：没有公共 IP 且在标准公共负载均衡器之后的虚拟机


 | 关联 | 方法 | IP 协议 |
 | ------------ | ------ | ------------ |
 | 标准公共负载均衡器 | 将负载均衡器前端 IP 用于 [SNAT](#snat)。| TCP </br> UDP |


 #### <a name="description"></a>描述


 负载均衡器资源配置有出站规则或启用默认 SNAT 的负载均衡规则。 此规则用于在公共 IP 前端与后端池之间创建链接。 


 如果没有完成此规则配置，则行为将如方案 3 所述。 


 不需要使用包含侦听器的规则即可成功进行运行状况探测。


 当 VM 创建出站流时，Azure 会将源 IP 地址转换为公共负载均衡器前端的公共 IP 地址。 此转换通过 [SNAT](#snat) 完成。 


 负载均衡器的前端公共 IP 地址的临时端口用于区分源自 VM 的各个流。 创建出站流后，SNAT 动态使用[预先分配的临时端口](#preallocatedports)。 


 在此情况下，用于 SNAT 的临时端口被称为 SNAT 端口。 强烈建议显式配置[出站规则](./outbound-rules.md)。 如果通过负载均衡规则使用默认 SNAT，则按照[默认 SNAT 端口分配表](#snatporttable)中所述预先分配 SNAT 端口。

 ### <a name="scenario-3-virtual-machine-without-public-ip-and-behind-standard-internal-load-balancer"></a><a name="scenario3"></a>方案3：没有公共 IP 和标准内部负载均衡器的虚拟机


 | 关联 | 方法 | IP 协议 |
 | ------------ | ------ | ------------ |
 | 标准内部负载均衡器 | 无 internet 连接。| 无 |

 #### <a name="description"></a>说明
 
使用标准内部负载均衡器时，不会为 SNAT 使用临时 IP 地址。 这是为了在默认情况下支持安全性，并确保资源使用的所有 IP 地址都可配置并可保留。 若要在使用标准内部负载均衡器时实现到 internet 的出站连接，请将实例级别的公共 IP 地址配置为遵循 (方案 1) [#scenario1] 中的行为，或将后端实例添加到标准的公共负载均衡器，并将另外中配置的出站规则添加到内部负载均衡器，以遵循 (方案 2) [#scenario2] 中 

 ### <a name="scenario-4-virtual-machine-without-public-ip-and-behind-basic-load-balancer"></a><a name="scenario4"></a>方案4：没有公共 IP 和基本负载均衡器的虚拟机


 | 关联 | 方法 | IP 协议 |
 | ------------ | ------ | ------------ |
 |无 </br> 基本负载均衡器 | 具有实例级动态 IP 地址的 [SNAT](#snat)| TCP </br> UDP | 

 #### <a name="description"></a>描述


 当 VM 创建出站流时，Azure 将源 IP 地址转换为动态分配的公共源 IP 地址。 此公共 IP 地址不可配置且无法保留。 针对订阅的公共 IP 资源限制进行计数时，不会计入此地址。 


 如果你重新部署以下项，系统会释放此公共 IP 地址并请求新的公共 IP： 


 * 虚拟机
 * 可用性集
 * 虚拟机规模集 


 不要将此方案用于向允许列表添加 IP。 请使用方案 1 或 2，你可以在其中显式声明出站行为。 [SNAT](#snat) 端口是预先分配的，如[默认 SNAT 端口分配表](#snatporttable)所述。

## <a name="exhausting-ports"></a><a name="scenarios"></a> 大量消耗端口

每个连接到同一目标 IP 和目标端口的连接都将使用 SNAT 端口。 此连接维护从后端实例或从客户端到服务器的不同流量  。 这个过程为服务器提供了一个不同的端口来处理流量。 如果没有此过程，客户端计算机将无法知道数据包属于哪个流。

假设有多个浏览器将访问 https://www.microsoft.com ，即：

* 目标 IP = 23.53.254.142
* 目标端口 = 443
* 协议 = TCP

如果返回流量的目标端口（用于建立连接的 SNAT 端口）都相同，客户端将无法将一个查询结果与另一个查询结果分开。

出站连接可能会突发。 后端实例可能无法分配到足够的端口。 如果未启用连接重用，则会增加 SNAT 端口耗尽的风险 。

当端口耗尽时，与目标 IP 的新出站连接将失败。 当端口变为可用时，连接将成功。 当来自 IP 地址的 64,000 个端口在许多后端实例上分散分布时，就会发生这种耗尽。 有关缓解 SNAT 端口耗尽的指导，请参阅[故障排除指南](./troubleshoot-outbound-connection.md)。  

对于 TCP 连接，负载均衡器将为每个目标 IP 和端口使用一个 SNAT 端口。 这种多用途允许使用相同 SNAT 端口建立与相同目标 IP 的多个连接。 如果连接不是指向不同的目标端口，那么这种多用途是有限的。

对于 UDP 连接，负载均衡器使用端口受限的 cone NAT 算法，无论目标端口是什么，每个目标 IP 都会消耗一个 SNAT 端口。 

可以在无限数量的连接中重复使用端口。 仅当目标 IP 或端口不同时，才可重复使用端口。

## <a name="default-port-allocation"></a><a name="preallocatedports"></a> 默认端口分配

作为负载均衡器的前端 IP 分配的每个公共 IP 都会为其后端池成员分配 64,000 个 SNAT 端口。 这些端口无法与后端池成员共享。 一系列的 SNAT 端口只能由单个后端实例使用，这样才可确保正确路由返回包。 

建议使用显式出站规则来配置 SNAT 端口分配。 此规则将使每个后端实例可用于出站连接的 SNAT 端口数最多。 

如果选择通过负载均衡规则自动分配出站 SNAT，则分配表将定义端口分配。

下表<a name="snatporttable"></a>显示了针对后端池大小的层级的 SNAT 端口预分配情况：

| 池大小（VM 实例） | 每个 IP 配置的预先分配 SNAT 端口 |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 | 

>[!NOTE]
> 如果你有一个最大大小为 10 的后端池，且定义了一个显式出站规则，则每个实例可以有 64,000/10 = 6,400 个端口。 根据上表，如果选择自动分配，则每个实例只有 1,024 个端口。

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> 出站规则和虚拟网络 NAT

Azure 负载均衡器出站规则和虚拟网络 NAT 是用于虚拟网络流出量的选项。

有关出站规则的详细信息，请参阅[出站规则](outbound-rules.md)。

有关 Azure 虚拟网络 NAT 的详细信息，请参阅[什么是 Azure 虚拟网络 NAT](../virtual-network/nat-overview.md)。

## <a name="constraints"></a>约束

*   如果连接处于闲置状态且没有发送新的数据包，则将在 4 - 120 分钟后释放端口。
  * 可以通过出站规则配置此阈值。
*   每个 IP 地址提供 64,000 个端口，这些端口可用于 SNAT。
*   每个端口都可以用于到目标 IP 地址的 TCP 和 UDP 连接
  * 无论目标端口是否唯一，都需要 UDP SNAT 端口。 对于每个到目标 IP 的 UDP 连接，将使用一个 UDP SNAT 端口。
  * 如果目标端口不同，则可以将一个 TCP SNAT 端口用于到同一目标 IP 的多个连接。
*   当后端实例用完给定的 SNAT 端口时，会发生 SNAT 耗尽。 负载均衡器仍然可以有未使用的 SNAT 端口。 如果后端实例的已使用 SNAT 端口超过其给定的 SNAT 端口，则无法建立新的出站连接。

## <a name="next-steps"></a>后续步骤

*   [SNAT 耗尽造成的出站连接失败故障排除](./troubleshoot-outbound-connection.md)
*   [查看 SNAT 指标](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation)并熟悉筛选、拆分和查看它们的正确方法。
