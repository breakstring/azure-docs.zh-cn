---
title: 在 Azure Bastion 中使用 VM 和 NSG
description: 可以将网络安全组与 Azure Bastion 配合使用。 了解此配置所需的子网。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 12/09/2020
ms.author: cherylmc
ms.openlocfilehash: 4fe22e0dae73df7af4fc24ba508ecbecf72dfd05
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2020
ms.locfileid: "97795356"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>使用 NSG 访问和 Azure Bastion

使用 Azure Bastion 时，可以使用网络安全组 (NSG)。 有关详细信息，请参阅[安全组](../virtual-network/network-security-groups-overview.md)。

:::image type="content" source="./media/bastion-nsg/figure-1.png" alt-text="NSG":::

在此图中：

* Bastion 主机部署到虚拟网络中。
* 用户使用任何 HTML5 浏览器连接到 Azure 门户。
* 用户导航到虚拟机，以便进行 RDP/SSH 操作。
* 连接集成 - 在浏览器中单击“RDP/SSH 会话”
* Azure VM 无需公共 IP。

## <a name="network-security-groups"></a><a name="nsg"></a>网络安全组

本部分介绍用户与 Azure Bastion 之间的网络流量，以及到虚拟网络中的目标 VM 的网络流量：

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet

Azure Bastion 将专门部署到 *AzureBastionSubnet_。

_ **入口流量：**

   * **来自公共 Internet 的入口流量：** Azure Bastion 将创建一个公共 IP，需要在该公共 IP 上启用端口 443，用于入口流量。 不需要在 AzureBastionSubnet 上打开端口 3389/22。
   * **来自 Azure Bastion 控制平面的入口流量：** 对于控制平面连接，请启用从 GatewayManager 服务标记进行的端口 443 入站。 这使控制平面（即网关管理器）能够与 Azure Bastion 通信。
   * **从 Azure 堡垒数据平面传输流量：** 对于 Azure 堡垒基础组件之间的数据平面通信，请从 **VirtualNetwork** service 标记中启用端口8080、5701 Inbound 入 **VirtualNetwork** service 标记。 这使得 Azure 堡垒的组件能够彼此通信。
   * **来自 Azure 负载均衡器的入口流量：** 对于运行状况探测，请从 AzureLoadBalancer 服务标记启用端口 443 入站。 这使得 Azure 负载均衡器能够检测连接性


   :::image type="content" source="./media/bastion-nsg/inbound.png" alt-text="屏幕截图显示 Azure Bastion 连接的入站安全规则。":::

* **出口流量：**

   * **流向目标 VM 的出口流量：** Azure Bastion 将通过专用 IP 到达目标 VM。 NSG 需要允许端口 3389 和 22 的出口流量流向其他目标 VM 子网。
   * **到 Azure 堡垒数据平面的出口流量：** 对于 Azure 堡垒基础组件之间的数据平面通信，启用端口8080，将 **VirtualNetwork** service 标记中的5701出站发送到 **VirtualNetwork** 服务标记。 这使得 Azure 堡垒的组件能够彼此通信。
   * **流向 Azure 中其他公共终结点的出口流量：** Azure Bastion 需要能够连接到 Azure 中的各种公共终结点，以便执行相应操作（例如，存储诊断日志和计量日志）。 因此，Azure Bastion 需要出站到 443，再到 AzureCloud 服务标记。
   * 流向 **Internet 的出口流量：** 若要进行会话和证书验证，Azure 堡垒需要能够与 Internet 通信。 出于此原因，我们建议启用到 Internet 的出站端口 80 **。**


   :::image type="content" source="./media/bastion-nsg/outbound.png" alt-text="屏幕截图显示 Azure Bastion 连接的出站安全规则。":::

### <a name="target-vm-subnet"></a>目标 VM 子网
此子网包含要通过 RDP/SSH 连接到的目标虚拟机。

   * **来自 Azure Bastion 的入口流量：** Azure Bastion 将通过专用 IP 到达目标 VM。 需要在目标 VM 端通过专用 IP 打开 RDP/SSH 端口（即端口 3389/22）。 最佳做法是，可以在此规则中添加 Azure Bastion 子网 IP 地址范围，仅允许 Bastion 在目标 VM 子网中的目标 VM 上打开这些端口。


## <a name="next-steps"></a>后续步骤

有关 Azure Bastion 的详细信息，请参阅[常见问题解答](bastion-faq.md)。
