---
title: 如何使用 Azure VMware 解决方案中的公共 IP 功能
description: 本文介绍如何使用 Azure 虚拟 WAN 中的公共 IP 功能。
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 640f359ed8934048bffd7f58b36e8a3eef71c2f1
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581374"
---
# <a name="how-to-use-the-public-ip-functionality-in-azure-vmware-solution"></a>如何使用 Azure VMware 解决方案中的公共 IP 功能

公共 IP 是 Azure VMware 解决方案连接的一项新功能。 它使资源（如 web 服务器）、虚拟机 (Vm) ，以及可通过公共网络访问的主机。 

可以通过两种方式实现公共 internet 访问。 

- 应用程序可以在 HTTP/HTTPS 流量的应用程序网关负载均衡器中托管和发布。
- 通过 Azure 虚拟 WAN 中的公共 IP 功能发布。

作为 Azure VMware 解决方案私有云部署的一部分，启用公共 IP 功能时，自动创建和启用了自动化的必需组件：

-  虚拟 WAN

-  具有 ExpressRoute 连接的虚拟 WAN 集线器

-  带有公共 IP 的 Azure 防火墙服务

本文详细介绍如何使用虚拟 WAN 中的公共 IP 功能。

## <a name="prerequisites"></a>先决条件

- Azure VMware 解决方案环境
- 在 Azure VMware 解决方案环境中运行的 web 服务器。
- 虚拟 WAN 中心部署的新的非重叠 IP 范围，通常为 `/24` 。

## <a name="reference-architecture"></a>参考体系结构

:::image type="content" source="media/public-ip-usage/public-ip-architecture-diagram.png" alt-text="公共 IP 体系结构图" border="false" lightbox="media/public-ip-usage/public-ip-architecture-diagram.png":::

体系结构关系图显示了在 Azure VMware 解决方案环境中托管的 web 服务器，并配置了 RFC1918 专用 IP 地址。  Web 服务通过虚拟 WAN 公共 IP 功能提供给 internet。  公共 IP 通常是在 Azure 防火墙中转换的目标 NAT。 使用 DNAT 规则时，防火墙策略使用端口将公共 IP 地址请求转换为 (web 服务器) 的专用地址。

用户请求访问公共 IP 上的防火墙，而该 IP 又使用 Azure 防火墙中的 DNAT 规则转换为专用 IP。 防火墙检查 NAT 表，如果请求与某个条目匹配，则会将流量转发到 Azure VMware 解决方案环境中的已转换地址和端口。

Web 服务器接收请求，并将请求的信息或页面回复到防火墙，然后防火墙将信息转发给公共 IP 地址的用户。

## <a name="test-case"></a>测试用例
在此方案中，你要将 IIS web 服务器发布到 internet。 使用 Azure VMware 解决方案中的公共 IP 功能将网站发布到公共 IP 地址上。  你还将在防火墙上配置 NAT 规则，并使用具有公共 IP 的 web 服务器) 访问 Azure VMware 解决方案资源 (Vm。

## <a name="deploy-virtual-wan"></a>部署虚拟 WAN

1. 登录到 Azure 门户，然后搜索并选择 " **Azure VMware 解决方案**"。

1. 选择 "Azure VMware 解决方案私有云"。

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-resource.png" alt-text="Azure VMware 解决方案私有云的屏幕截图。" border="true" lightbox="media/public-ip-usage/avs-private-cloud-resource.png":::

1. 在 " **管理**" 下，选择 " **连接**"。

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-manage-menu.png" alt-text="连接部分的屏幕截图。" border="true" lightbox="media/public-ip-usage/avs-private-cloud-manage-menu.png":::

1. 选择 " **公共 IP** " 选项卡，然后选择 " **配置**"。

   :::image type="content" source="media/public-ip-usage/connectivity-public-ip-tab.png" alt-text="显示开始配置公共 IP 的位置的屏幕截图" border="true" lightbox="media/public-ip-usage/connectivity-public-ip-tab.png":::

1. 接受或更改默认值，然后选择 " **创建**"。

   - 虚拟 WAN 资源组

   - 虚拟 WAN 名称

   - 使用新的非重叠 IP 范围 (虚拟中心地址块) 

   - 公共 Ip (1-100) 的数目

完成所有组件的部署大约需要一小时。 此部署只需要发生一次即可支持此 Azure VMware 解决方案环境的所有未来公共 Ip。  

>[!TIP]
>你可以从 **通知** 区域监视状态。 

## <a name="view-and-add-public-ip-addresses"></a>查看和添加公共 IP 地址

可以按照以下步骤检查并添加更多公共 IP 地址。

1. 在 Azure 门户中，搜索并选择 " **防火墙**"。

1. 选择已部署的防火墙，然后选择 " **访问 Azure 防火墙管理器" 以配置和管理此防火墙**。

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="显示用于配置和管理防火墙的选项的屏幕截图" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. 选择 " **受保护的虚拟中心** "，然后从列表中选择一个虚拟中心。

   :::image type="content" source="media/public-ip-usage/select-virtual-hub.png" alt-text="防火墙管理器的屏幕截图" lightbox="media/public-ip-usage/select-virtual-hub.png":::

1. 在 "虚拟中心" 页上，选择 " **公共 ip 配置**" 并添加更多公共 ip 地址，然后选择 " **添加**"。 

   :::image type="content" source="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png" alt-text="如何在防火墙管理器中添加公共 IP 配置的屏幕截图" border="true" lightbox="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png":::

1. 提供所需的 Ip 数量，然后选择 " **添加**"。

   :::image type="content" source="media/public-ip-usage/add-number-of-ip-addresses-required.png" alt-text="添加指定数量的公共 IP 配置的屏幕截图" border="true":::


## <a name="create-firewall-policies"></a>创建防火墙策略

部署所有组件后，即可在添加的资源组中看到它们。 下一步是添加防火墙策略。

1. 在 Azure 门户中，搜索并选择 " **防火墙**"。

1. 选择已部署的防火墙，然后选择 " **访问 Azure 防火墙管理器" 以配置和管理此防火墙**。

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="显示用于配置和管理防火墙的选项的屏幕截图" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. 选择 " **Azure 防火墙策略** "，然后选择 " **创建 azure 防火墙策略**"。

   :::image type="content" source="media/public-ip-usage/create-firewall-policy.png" alt-text="如何在防火墙管理器中创建防火墙策略的屏幕截图" border="true" lightbox="media/public-ip-usage/create-firewall-policy.png":::

1. 在 " **基本** 信息" 选项卡下提供所需的详细信息并选择 " **下一步： DNS 设置**"。 

1. 在 " **DNS** " 选项卡下，选择 " **禁用**"，然后选择 " **下一步：规则**"。

1. 选择 " **添加规则集合**"，提供以下详细信息，选择 " **添加** "，然后选择 " **下一步：威胁情报**"。

   -  名称
   -  规则集合类型-DNAT
   -  优先级
   -  规则收集操作-允许
   -  规则名称
   -  源类型- **IPaddress**
   -  源程序 **\***
   -  协议– **TCP**
   -  目标端口– **80**
   -  目标类型– **IP 地址**
   -  目标– **公共 IP 地址**
   -  翻译地址- **Azure VMware 解决方案 Web 服务器专用 IP 地址**
   -  已转换端口- **Azure VMware 解决方案 Web 服务器端口**

1. 保留默认值，然后选择 " **下一步：集线器**"。

1. 选择 " **关联虚拟中心**"。

1. 从列表中选择一个集线器，然后选择 " **添加**"。

   :::image type="content" source="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png" alt-text="显示将转换为受保护虚拟中心的选定集线器的屏幕截图。" border="true" lightbox="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png":::

1. 在完成时选择“下一步:  标记”。 

1.  (可选) 创建用于对资源进行分类的名称和值对。 

1. 选择 " **下一步"：检查 + 创建** ，然后选择 " **创建**"。

## <a name="limitations"></a>限制

每个 SDDCs 可以有100个公共 Ip。

## <a name="next-steps"></a>后续步骤

现在，你已介绍了如何使用 Azure VMware 解决方案中的公共 IP 功能，你可能想要了解：

- 将公共 IP 地址用于 [Azure 虚拟 WAN](../virtual-wan/virtual-wan-about.md)。
- [在 Azure VMware 解决方案中创建 IPSec 隧道](create-ipsec-tunnel.md)。
