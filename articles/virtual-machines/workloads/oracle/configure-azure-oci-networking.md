---
title: 通过 Oracle 云基础结构连接 Azure ExpressRoute |Microsoft Docs
description: 连接 Azure ExpressRoute 和 Oracle 云基础结构 (OCI) FastConnect 以启用跨云 Oracle 应用程序解决方案
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 03/16/2020
ms.author: rogardle
ms.reviewer: cynthn
ms.openlocfilehash: 2717ba307cac82465e0c5df996ef3b668af5e7d4
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963702"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>设置 Azure 和 Oracle 云基础结构之间的直接互连  

为了创建 [集成的多云体验](oracle-oci-overview.md)，Microsoft 和 Oracle 通过 [ExpressRoute](../../../expressroute/expressroute-introduction.md) 和 [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm)在 Azure 和 oracle 云基础结构之间提供直接互连 (OCI) 。 通过 ExpressRoute 和 FastConnect 互连，客户可以在两个云之间体验低延迟、高吞吐量、专用直接连接。

> [!IMPORTANT]
> 在 2020 年 5 月之前，Oracle 将认证，在使用 Azure/Oracle 云互连解决方案时，这些应用程序可以在 Azure 中运行。
> * E-Business Suite
> * JD Edwards EnterpriseOne
> * PeopleSoft
> * Oracle Retail 应用程序
> * Oracle Hyperion 财务管理

下图显示了互连的高级概述：

![跨云网络连接](media/configure-azure-oci-networking/azure-oci-connect.png)

## <a name="prerequisites"></a>先决条件

* 若要在 Azure 和 OCI 之间建立连接，必须具有有效的 Azure 订阅和活动的 OCI 租户。

* 仅当 Azure ExpressRoute 对等互连位置与 OCI FastConnect 在同一对等位置中时，才可以进行连接。 请参阅 [区域可用性](oracle-oci-overview.md#region-availability)。

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>配置 ExpressRoute 与 FastConnect 之间的直接连接

1. 在 Azure 订阅下的资源组中创建标准 ExpressRoute 线路。 
    * 创建 ExpressRoute 时，选择 **Oracle Cloud FastConnect** 作为服务提供商。 若要创建 ExpressRoute 线路，请参阅 [分步指南](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)。
    * Azure ExpressRoute 线路提供精细的带宽选项，而 FastConnect 支持1、2、5或 10 Gbps。 因此，建议在 ExpressRoute 下选择其中一个匹配的带宽选项。

    ![创建 ExpressRoute 线路](media/configure-azure-oci-networking/exr-create-new.png)
1. 记下 ExpressRoute **服务密钥**。 需要在配置 FastConnect 线路时提供密钥。

    ![ExpressRoute 服务密钥](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > 预配 ExpressRoute 线路后，将立即为 ExpressRoute 收费计费 (即使 **未**) **提供提供程序状态**。

1. 划分2个专用 IP 地址空间/30，它们不会与 Azure 虚拟网络或 OCI 虚拟云网络 IP 地址空间重叠。 我们会将第一个 IP 地址空间称为主地址空间，并将第二个 IP 地址空间引用为辅助地址空间。 记下配置 FastConnect 线路时所需的地址。
1.  (.DRG) 创建动态路由网关。 创建 FastConnect 线路时需要用到它。 有关详细信息，请参阅 [动态路由网关](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) 文档。
1. 在 Oracle 租户下创建 FastConnect 线路。 有关详细信息，请参阅 [Oracle 文档](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)。
  
    * 在 "FastConnect 配置" 下，选择 " **Microsoft Azure： ExpressRoute** 作为提供程序。
    * 选择上一步中预配的动态路由网关。
    * 选择要设置的带宽。 为了获得最佳性能，带宽必须与创建 ExpressRoute 线路时选择的带宽匹配。
    * 在 " **提供程序服务密钥**" 中，粘贴 ExpressRoute 服务密钥。
    * 使用前一步骤中的第一个/30 个专用 IP 地址空间来划分 **主 BGP Ip 地址** 和 **辅助 bgp ip** 地址的第二/30 个专用 ip 地址空间。
        * 为 Oracle BGP IP 地址分配两个范围的第一个可用地址， (主) 和辅助，以及从 FastConnect 角度)  (的客户 BGP IP 地址的第二个地址。 第一个可用 IP 地址是/30 地址空间中的第二个 IP 地址， (第一个 IP 地址由 Microsoft) 保留。
    * 单击“创建”。
1. 使用路由表通过动态路由网关完成将 FastConnect 链接到 Oracle 租户下的虚拟云网络。
1. 导航到 Azure 并确保 ExpressRoute 线路的 **提供程序状态** 已更改为 "已 **设置** "，并确保已设置 **Azure 专用** 类型的对等互连。 这是以下步骤的先决条件。

    ![ExpressRoute 提供程序状态](media/configure-azure-oci-networking/exr-provider-status.png)
1. 单击 " **Azure 专用** 对等互连"。 你将看到 "对等互连详细信息" 已根据设置 FastConnect 线路时输入的信息自动进行配置。

    ![私有对等设置](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>将虚拟网络连接到 ExpressRoute

1. 创建虚拟网络和虚拟网络网关（如果尚未这样做）。 有关详细信息，请参阅 [分步指南](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)。
1. 通过执行 [Terraform 脚本](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2) 或通过执行 PowerShell 命令来 [配置 expressroute FastPath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)，设置虚拟网络网关与 ExpressRoute 线路之间的连接。

完成网络配置后，可以通过单击 "Azure 门户中的" ExpressRoute 专用对等互连 "边栏选项卡下的" **获取 ARP 记录** 并 **获取路由表** "来验证配置的有效性。

## <a name="automation"></a>自动化

Microsoft 创建了 Terraform 脚本，用于启用网络互连的自动部署。 Terraform 脚本需要在执行前对 Azure 进行身份验证，因为它们需要对 Azure 订阅拥有足够的权限。 可以使用 [Azure Active Directory 服务主体](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) 或使用 Azure CLI 来执行身份验证。 有关详细信息，请参阅 [Terraform 文档](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html)。

可以在此 [GitHub 存储库](https://aka.ms/azureociinterconnecttf)中找到用于部署连接间的 Terraform 脚本和相关文档。

## <a name="monitoring"></a>监视

在这两个云上安装代理，你可以利用 Azure [网络性能监视器 (NPM) ](../../../expressroute/how-to-npm.md) 来监视端到端网络的性能。 NPM 可帮助你轻松识别网络问题，并帮助消除这些问题。

## <a name="delete-the-interconnect-link"></a>删除互连链接

若要删除此互连，必须按照给定的顺序执行以下步骤。 否则，将导致 "失败状态" ExpressRoute 线路。

1. 删除 ExpressRoute 连接。 通过单击连接页面上的 " **删除** " 图标来删除连接。 有关详细信息，请参阅 [ExpressRoute 文档](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#clean-up-resources)。
1. 从 Oracle 云控制台中删除 Oracle FastConnect。
1. 删除 Oracle FastConnect 线路后，可以删除 Azure ExpressRoute 线路。

此时，删除和取消预配过程已完成。

## <a name="next-steps"></a>后续步骤

* 有关 OCI 与 Azure 之间的跨云连接的详细信息，请参阅 [Oracle 文档](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)。
* 使用 [Terraform 脚本](https://aka.ms/azureociinterconnecttf) 通过 Azure 为目标 Oracle 应用程序部署基础结构，并配置网络互连。 
