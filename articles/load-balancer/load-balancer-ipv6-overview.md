---
title: IPv6 概述 - Azure 负载均衡器
description: 通过此学习路径，可以开始了解 Azure 负载均衡器和负载均衡 VM 的 IPv6 支持。
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, azure 负载均衡器, 双堆栈, 公共 ip, 本机 ipv6, 移动, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: allensu
ms.openlocfilehash: 4061a3dbf4dc92d6d412528115d46edc36d20d5e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700668"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Azure 负载均衡器的 IPv6 概述


>[!NOTE] 
>此内容已由[适用于 Azure VNet 的 IPv6 概述](../virtual-network/ipv6-overview.md)取代。 Azure 建议新的 IPv6 部署使用适用于 Azure 虚拟网络的新 IPv6 功能。

>[!NOTE]
>Azure 负载均衡器支持两种不同的类型：“基本”和“标准”。 本文介绍基本负载均衡器。 有关标准负载均衡器的详细信息，请参阅[标准负载均衡器概述](./load-balancer-overview.md)。

面向 Internet 的基本 SKU 负载均衡器可以使用 IPv6 地址进行部署。 除了 IPv4 连接以外，这还可以实现以下功能：

* 通过负载均衡器在公共 Internet 客户端与 Azure 虚拟机 (VM) 之间建立本机端到端 IPv6 连接。
* 在 VM 与公共 Internet 上已启用 IPv6 的客户端之间建立本机端到端 IPv6 输出连接。

下图演示了 Azure 负载均衡器的 IPv6 功能。

![使用 IPv6 的 Azure 负载均衡器](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

部署后，已启用 IPv4 或 IPv6 的 Internet 客户端可与面向 Internet 的 Azure 负载均衡器公共 IPv4 或 IPv6 地址（或主机名）通信。 负载均衡器使用网络地址转换 (NAT) 将 IPv6 数据包路由到 VM 的专用 IPv6 地址。 IPv6 Internet 客户端无法与 VM 的 IPv6 地址直接通信。

## <a name="features"></a>功能

通过 Azure Resource Manager 部署的本机 IPv6 支持提供以下功能：

1. 为 Internet 上的 IPv6 客户端提供负载均衡的 IPv6 服务
2. 在 VM上提供本机 IPv6 和 IPv4 终结点（“双堆栈”）
3. 提供入站和出站流量发起的本机 IPv6 连接
4. 支持的协议（如 TCP、UDP 和 HTTP(S)）可以启用各种服务体系结构

## <a name="benefits"></a>优点

此功能具有以下主要优点：

* 要求新应用程序只能由使用 IPv6 的客户端访问，符合政府法规
* 可让移动和物联网 (IOT) 开发人员使用双堆栈 (IPv4+IPv6) Azure 虚拟机解决不断发展的移动和 IOT 市场需求

## <a name="details-and-limitations"></a>详细信息和限制

详细信息

* Azure DNS 服务包含 IPv4 A 和 IPv6 AAAA 名称记录，可对负载均衡器的两种记录做出响应。 客户端选择要与哪个地址（IPv4 或 IPv6）通信。
* 当 VM 与已连接到公共 Internet IPv6 的设备发起连接时，VM 的源 IPv6 地址是经网络地址转换 (NAT) 后的负载均衡器公共 IPv6 地址。
* 运行 Linux 操作系统的 VM 必须配置为通过 DHCP 接收 IPv6 IP 地址。 Azure 库中的许多 Linux 映像已配置为支持 IPv6，不需要修改。 有关详细信息，请参阅 [Configuring DHCPv6 for Linux VMs](load-balancer-ipv6-for-linux.md)
* 如果选择在负载均衡器中使用运行状况探测，请创建 IPv4 探测，并将它同时用于 IPv4 和 IPv6 终结点。 如果 VM 上的服务中断，IPv4 和 IPv6 终结点将脱离轮换。

限制

* 无法在 Azure 门户中添加 IPv6 负载均衡规则。 只能通过模板、CLI 或 PowerShell 创建规则。
* 无法将现有 VM 升级为使用 IPv6 地址。 为此必须部署新 VM。
* 可将单个 IPv6 地址分配给每个 VM 中的单个网络接口。
* 无法将公共 IPv6 地址分配给 VM， 只能分配给负载均衡器。
* 无法为公共 IPv6 地址配置反向 DNS 查找。
* 使用 IPv6 地址的 VM 不能是 Azure 云服务的成员。 这些 VM 不能连接到 Azure 虚拟网络 (VNet)，也不能通过其 IPv4 地址相互通信。
* 可将专用 IPv6 地址部署到资源组中的单个 VM，但无法通过规模集部署到资源组。
* Azure VM 无法通过 IPv6 连接到其他 VM、其他 Azure 服务或本地设备， 只能通过 IPv6 来与 Azure 负载均衡器通信。 但是，它们可以使用 IPv4 来与其他这些资源通信。
* 双堆栈 (IPv4+IPv6) 部署支持 IPv4 的网络安全组 (NSG) 保护。 NSG 不适用于 IPv6 终结点。
* VM 上的 IPv6 终结点不会直接在 Internet 上公开， 而是位于负载均衡器的后面。 通过 IPv6 只能访问负载均衡器规则中指定的端口。
* **当前不支持** 更改 IPv6 的 IdleTimeout 参数。 该参数默认为 4 分钟。
* **当前不支持** 更改 IPv6 的 loadDistributionMethod 参数。
* 基本负载均衡器的 IPv6 已锁定为动态 SKU。  标准负载均衡器的 IPv6 已锁定为静态 SKU。
* 不支持 NAT64（将 IPv6 转换为 IPv4）。
* 当前不支持将引用 IPv6 子网的辅助 NIC 附加到后端池  。

## <a name="next-steps"></a>后续步骤

了解如何部署使用 IPv6 的负载均衡器。

* [IPv6 在各区域的可用性](https://go.microsoft.com/fwlink/?linkid=828357)
* [使用模板部署具有 IPv6 的负载均衡器](load-balancer-ipv6-internet-template.md)
* [使用 Azure PowerShell 部署具有 IPv6 的负载均衡器](load-balancer-ipv6-internet-ps.md)
* [使用 Azure CLI 部署具有 IPv6 的负载均衡器](load-balancer-ipv6-internet-cli.md)