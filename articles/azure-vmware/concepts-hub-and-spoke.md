---
title: 概念-在中心辐射型体系结构中集成 Azure VMware 解决方案部署
description: 了解如何在 Azure 上的中心和辐射型体系结构中集成 Azure VMware 解决方案部署。
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: bfc442e569572349b1323500fbd0b2f912ebbc62
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062739"
---
# <a name="integrate-azure-vmware-solution-in-a-hub-and-spoke-architecture"></a>在中心和辐射型体系结构中集成 Azure VMware 解决方案

本文介绍了如何在 Azure 上的现有或新的 [中心和辐射型体系结构](/azure/architecture/reference-architectures/hybrid-networking/#hub-spoke-network-topology) 中集成 Azure VMware 解决方案部署。 


中心辐射型方案假设混合云环境中的工作负荷已开启：

* 使用 IaaS 或 PaaS 服务的本机 Azure
* Azure VMware 解决方案 
* 本地 vSphere

## <a name="architecture"></a>体系结构

*中心* 是一种 Azure 虚拟网络，它充当本地和 Azure VMware 解决方案私有云的中心连接点。 *轮辐* 是与中心对等互连的虚拟网络，用于启用跨虚拟网络通信。

本地数据中心、Azure VMware 解决方案私有云和中心之间的流量通过 Azure ExpressRoute 连接。 轮辐虚拟网络通常包含基于 IaaS 的工作负载，但可以具有 PaaS 服务（如 [应用服务环境](../app-service/environment/intro.md)）、直接与虚拟网络集成或启用了 [Azure 专用链接](../private-link/index.yml) 的其他 PaaS 服务。

>[!IMPORTANT]
>你可以使用现有的 ExpressRoute 网关连接到 Azure VMware 解决方案，只要它不超过每个虚拟网络四条 ExpressRoute 线路的限制即可。  但是，若要通过 ExpressRoute 从本地访问 Azure VMware 解决方案，必须具有 ExpressRoute Global Reach，因为 ExpressRoute 网关不提供其连接线路之间的传递路由。

此图显示了通过 ExpressRoute Global Reach 连接到本地和 Azure VMware 解决方案的 Azure 中的中心和分支部署的示例。

:::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png" alt-text="Azure VMware 解决方案中心和分支集成部署" border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png":::

该体系结构包括以下主要组件：

- **本地站点：** 客户本地数据中心 (s) 通过 ExpressRoute 连接连接到 Azure。

- **Azure VMware 解决方案私有云：** 由一个或多个 vSphere 群集构成的 Azure VMware 解决方案 SDDC，其中每个群集最多包含16个主机。

- **ExpressRoute 网关：** 启用 Azure VMware 解决方案私有云之间的通信、中心虚拟网络上的共享服务和在辐射虚拟网络上运行的工作负荷。

- **ExpressRoute Global Reach：** 启用本地与 Azure VMware 解决方案私有云之间的连接。 Azure VMware 解决方案与 Azure fabric 之间的连接仅通过 ExpressRoute Global Reach。 不能选择 ExpressRoute 快速路径以外的任何选项。  不支持 ExpressRoute 直通。


- **S2S VPN 注意事项：** 对于 Azure VMware 解决方案生产部署，不支持 Azure S2S VPN，因为 VMware HCX 的网络要求。 不过，你可以将其用于 PoC 部署。


- **中心虚拟网络：** 作为与本地网络和 Azure VMware 解决方案私有云的连接的中心点。

- **辐射虚拟网络**

    - **IaaS 分支：** IaaS 辐射托管基于 Azure IaaS 的工作负载，包括 VM 可用性集和虚拟机规模集，以及相应的网络组件。

    - **PaaS 分支：** PaaS 分支使用专用寻址来托管 Azure PaaS 服务，感谢 [专用终结点](../private-link/private-endpoint-overview.md) 和 [专用链接](../private-link/private-link-overview.md)。

- **Azure 防火墙：** 充当辐射和 Azure VMware 解决方案之间的流量的中心部分。

- **应用程序网关：** 公开和保护在 Azure IaaS/PaaS 或 Azure VMware 解决方案虚拟机 (Vm) 上运行的 web 应用。 它与其他服务（例如 API 管理）集成。

## <a name="network-and-security-considerations"></a>网络和安全注意事项

ExpressRoute 连接使流量能够在本地、Azure VMware 解决方案和 Azure 网络结构之间流动。 Azure VMware 解决方案使用 [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) 来实现这种连接。

由于 ExpressRoute 网关不提供其连接线路之间的可传递路由，本地连接也必须使用 ExpressRoute Global Reach 在本地 vSphere 环境与 Azure VMware 解决方案之间进行通信。 

* **本地到 Azure VMware 解决方案流量流**

  :::image type="content" source="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png" alt-text="本地到 Azure VMware 解决方案流量流" border="false" lightbox="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png":::


* **Azure VMware 解决方案到中心 VNET 的通信流**

  :::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png" alt-text="Azure VMware 解决方案到中心虚拟网络流量流" border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png":::


有关 Azure VMware 解决方案网络和连接性概念的详细信息，请参阅 [Azure Vmware 解决方案产品文档](./concepts-networking.md)。

### <a name="traffic-segmentation"></a>流量分段

[Azure 防火墙](../firewall/index.yml) 是中心辐射型拓扑中心，部署在中心虚拟网络上。 使用 Azure 防火墙或其他受 Azure 支持的网络虚拟设备来建立流量规则，并分段不同辐射型和 Azure VMware 解决方案工作负载之间的通信。

创建路由表以将流量定向到 Azure 防火墙。  对于辐射虚拟网络，请创建将默认路由设置为 Azure 防火墙内部接口的路由。 这样一来，当虚拟网络中的工作负荷需要访问 Azure VMware 解决方案地址空间时，防火墙就可以对其进行评估，并应用相应的流量规则来允许或拒绝它。  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="创建路由表以将流量定向到 Azure 防火墙" lightbox="media/hub-spoke/create-route-table-to-direct-traffic.png":::


> [!IMPORTANT]
> 不支持 **GatewaySubnet** 设置上地址前缀为 0.0.0.0/0 的路由。

针对相应的路由表设置特定网络的路由。 例如，路由从辐射工作负荷和其他方法访问 Azure VMware 解决方案管理和工作负荷 IP 前缀。

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="针对相应的路由表设置特定网络的路由" lightbox="media/hub-spoke/specify-gateway-subnet-for-route-table.png":::

使用轮辐和中心内的网络安全组创建更精细的流量分段的二级流量分段。

> [!NOTE]
> **从本地到 Azure VMware 解决方案的流量：** 本地工作负载之间的流量（基于 vSphere 或其他工作负荷）由 Global Reach 启用，但流量不会通过集线器上的 Azure 防火墙。 在这种情况下，你必须在本地或 Azure VMware 解决方案中实现流量分段机制。

### <a name="application-gateway"></a>应用程序网关

已使用在作为后端池的 Azure VMware 解决方案虚拟机上运行的 web 应用来测试 Azure 应用程序网关 V1 和 V2。 应用程序网关目前是唯一受支持的方法，可将 Azure VMware 解决方案 Vm 上运行的 web 应用公开到 internet。 它也可以安全地向内部用户公开应用程序。

有关详细信息，请参阅 [应用程序网关上](./protect-azure-vmware-solution-with-application-gateway.md)的特定于 Azure VMware 解决方案的文章。

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="使用网络安全组的第二级流量分段" border="false":::


### <a name="jump-box-and-azure-bastion"></a>跳转框和 Azure 堡垒

使用 "跳转盒" 访问 Azure VMware 解决方案环境，它是在中心虚拟网络中的共享服务子网中部署的 Windows 10 或 Windows Server VM。

>[!IMPORTANT]
>Azure 堡垒是推荐用于连接到跳转盒的服务，以防止向 internet 公开 Azure VMware 解决方案。 不能使用 Azure 堡垒连接到 Azure VMware 解决方案 Vm，因为它们不是 Azure IaaS 对象。  

作为最佳安全方案，请在中心虚拟网络中部署 [Microsoft Azure 堡垒](../bastion/index.yml) 服务。 Azure 堡垒提供对 Azure 中部署的 Vm 的无缝 RDP 和 SSH 访问，无需为这些资源预配公共 IP 地址。 预配 Azure 堡垒服务后，可以从 Azure 门户访问所选 VM。 建立连接后，会打开一个新的选项卡，显示跳转框桌面，并从该桌面访问 Azure VMware 解决方案私有云管理平面。

> [!IMPORTANT]
> 不要向跳转盒 VM 提供公共 IP 地址，也不要向公共 internet 公开 3389/TCP 端口。 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Azure 堡垒中心虚拟网络" border="false":::


## <a name="azure-dns-resolution-considerations"></a>Azure DNS 解析注意事项

对于 Azure DNS 解析，有两个可用的选项：

-   使用在 " [标识注意事项](#identity-considerations)) 为名称服务器" 中所述的 (集线器上部署的域控制器。

-   部署和配置 Azure DNS 专用区域。

最佳方法是将两者结合起来，为 Azure VMware 解决方案、本地和 Azure 提供可靠的名称解析。

作为一般的设计建议，在这种情况下，请使用现有的 Azure DNS 基础结构 (，将 Active Directory 集成的) DNS 部署到至少部署在中心虚拟网络中的两个 Azure Vm，并在辐射虚拟网络中进行配置，以在 DNS 设置中使用这些 Azure DNS 服务器。

可以使用 Azure 专用 DNS，其中 Azure 专用 DNS 区域链接到虚拟网络。  DNS 服务器使用客户 Azure 专用 DNS 基础结构通过条件转发到本地或 Azure VMware 解决方案，将 DNS 服务器用作混合解析器。 

若要为在辐射虚拟网络中部署的 Vm 自动管理 DNS 记录的生命周期，请启用自动注册。 启用后，最大专用 DNS 区域数仅为1。 如果禁用，则最大数目为1000。

本地和 Azure VMware 解决方案服务器可以配置有条件转发器，以便在 azure 中为 Azure 专用 DNS 区域解析 Vm。

## <a name="identity-considerations"></a>标识注意事项

出于标识目的，最佳方法是在中心部署至少一个域控制器。 使用区域分布式方式或 VM 可用性集的两个共享服务子网。 有关将本地 Active Directory (AD) 域扩展到 Azure 的详细信息，请参阅 [Azure 体系结构中心](/azure/architecture/reference-architectures/identity/adds-extend-domain)。

此外，在 Azure VMware 解决方案端部署另一个域控制器，在 vSphere 环境中充当标识和 DNS 源。

作为推荐的最佳做法，将 [AD 域与 Azure Active Directory](/azure/architecture/reference-architectures/identity/azure-ad)集成。

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->
