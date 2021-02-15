---
title: VNet 对等互连和 Azure Bastion 体系结构
description: 本文介绍了如何将 VNet 对等互连和 Azure Bastion 配合使用来连接到 VM。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 12/09/2020
ms.author: cherylmc
ms.openlocfilehash: 472261666c86b666efd09c7217d12e5a795a50d9
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97094875"
---
# <a name="vnet-peering-and-azure-bastion-preview"></a>VNet 对等互连和 Azure Bastion（预览版）

Azure Bastion 和 VNet 对等互连可以一起使用。 配置 VNet 对等互连后，无需在每个对等互连的 VNet 中部署 Azure Bastion。 这就意味着，如果在一个虚拟网络 (VNet) 中配置了 Azure Bastion 主机，则可使用该主机连接到在对等互连的 VNet 中部署的 VM，而无需部署其他 Bastion 主机。 有关 VNet 对等互连的详细信息，请参阅[关于虚拟网络对等互连](../virtual-network/virtual-network-peering-overview.md)。

Azure Bastion 使用以下类型的对等互连：

* **虚拟网络对等互连：** 连接同一 Azure 区域中的虚拟网络。
* **全局虚拟网络对等互连：** 跨 Azure 区域连接虚拟网络。

## <a name="architecture"></a>体系结构

配置 VNet 对等互连后，可以在中心辐射型拓扑或全网格型拓扑中部署 Azure Bastion。 Azure Bastion 部署是按虚拟网络进行的，而不是按订阅/帐户或虚拟机进行的。

在虚拟网络中预配 Azure Bastion 服务后，同一 VNet（以及对等互连的 VNet）中所有的 VM 均可获得 RDP/SSH 体验。 这就意味着，可以将 Bastion 部署合并到一个 VNet，而仍然可以访问对等互连的 VNet 中部署的 VM，集中进行总体部署。

:::image type="content" source="./media/vnet-peering/design.png" alt-text="设计和体系结构图":::

此图显示了中心辐射型模型中 Azure Bastion 部署的体系结构。 在此图中，可以看到以下配置：

* Bastion 主机部署在集中式中心虚拟网络中。
* 已部署集中式网络安全组 (NSG)。
* Azure VM 无需公共 IP。

**步骤：**

1. 使用任何 HTML5 浏览器连接到 Azure 门户。
1. 选择要连接到的虚拟机。
1. 在对等互连的 VNet 中会顺利检测到 Azure Bastion。
1. 单击一下，在浏览器中打开 RDP/SSH 会话。 有关 RDP 和 SSH 并发会话的限制，请参阅 [RDP 和 SSH 会话](bastion-faq.md#limits)。

  :::image type="content" source="../../includes/media/bastion-vm-rdp/connect-vm.png" alt-text="“连接”":::

   若要详细了解如何通过 Azure Bastion 连接到 VM，请参阅：

   * [连接到 VM - RDP](bastion-connect-vm-rdp.md)。
   * [连接到 VM - SSH](bastion-connect-vm-ssh.md)。

## <a name="faq"></a>常见问题解答

[!INCLUDE [FAQ for VNet peering](../../includes/bastion-faq-peering-include.md)]

## <a name="next-steps"></a>后续步骤

阅读 [Bastion 常见问题解答](bastion-faq.md)。