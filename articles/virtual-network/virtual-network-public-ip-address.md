---
title: 管理公共 IP 地址 |Microsoft Docs
titleSuffix: Azure Virtual Network
description: 管理公共 IP 地址。  还将了解公共 IP 地址如何成为自带可配置设置的资源。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: d52430c87d99f8837c78fcff89d8b214e45350ff
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98934937"
---
# <a name="manage-public-ip-addresses"></a>管理公共 IP 地址

了解公共 IP 地址，以及如何创建、更改和删除此类地址。 公共 IP 地址是一种自带可配置设置的资源。 将公共 IP 地址分配给支持公共 IP 地址的 Azure 资源以启用：
- 从 Internet 到资源的入站通信，如 Azure 虚拟机 (VM)、Azure 应用程序网关、Azure 负载均衡器、Azure VPN 网关等。 如果 VM 没有分配有公共 IP 地址，则仍可通过 Internet 与某些资源（如 VM）进行通信，前提是 VM 是负载均衡器后端池的一部分且负载均衡器分配有公共 IP 地址。 若要确定是否可向特定 Azure 服务的资源分配公共 IP 地址，或是否可通过其他 Azure 资源的公共 IP 地址与之通信，请参阅该服务的文档。
- 使用可预测的 IP 地址与 Internet 建立出站连接。 例如，如果某虚拟机未分配有公共 IP 地址，但其地址由 Azure 网络地址转换为可预测的公共地址，则默认情况下，该虚拟机可与 Internet 建立出站通信。 通过将公共 IP 地址分配给资源，可了解哪个 IP 地址用于出站连接。 尽管可预测，但地址可根据所选分配方法进行更改。 有关详细信息，请参阅[创建公共 IP 地址](#create-a-public-ip-address)。 有关从 Azure 资源建立出站连接的详细信息，请参阅[了解出站连接](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="before-you-begin"></a>准备阶段

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在完成本文任何部分中的步骤之前，请完成以下任务：

- 如果还没有 Azure 帐户，请注册[免费试用帐户](https://azure.microsoft.com/free)。
- 如果使用门户，请打开 https://portal.azure.com ，并使用 Azure 帐户登录。
- 如果使用 PowerShell 命令来完成本文中的任务，请运行 [Azure Cloud Shell](https://shell.azure.com/powershell) 中的命令，或从计算机运行 PowerShell。 Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 本教程需要 Azure PowerShell 模块 1.0.0 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。
- 如果使用 Azure 命令行接口 (CLI) 命令来完成本文中的任务，请运行 [Azure Cloud Shell](https://shell.azure.com/bash) 中的命令，或从计算机运行 CLI。 本教程需要 Azure CLI 2.0.31 或更高版本。 运行 `az --version` 查找已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 如果在本地运行 Azure CLI，则还需运行 `az login` 以创建与 Azure 的连接。

登录或连接到 Azure 所用的帐户必须分配有[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或者分配有可执行[权限](#permissions)中列出的适当操作的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

公共 IP 地址会产生少许费用。 若要查看定价，请参阅 [IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses)页。

## <a name="create-a-public-ip-address"></a>创建公共 IP 地址

有关如何使用门户、PowerShell 或 CLI 来创建公共 IP 地址的说明，请参阅以下页面：

 * [创建公共 IP 地址 - 门户](./create-public-ip-portal.md?tabs=option-create-public-ip-standard-zones)
 * [创建公共 IP 地址 - PowerShell](./create-public-ip-powershell.md?tabs=option-create-public-ip-standard-zones)
 * [创建公共 IP 地址 - Azure CLI](./create-public-ip-cli.md?tabs=option-create-public-ip-standard-zones)

>[!NOTE]
>虽然门户提供了用于创建两个公共 IP 地址资源（一个 IPv4 和一个 IPv6）的选项，但 PowerShell 和 CLI 命令可使用任一 IP 版本的地址创建一个资源。 如果需要两个公共 IP 地址资源（每个 IP 版本一个），必须运行此命令两次，为公共 IP 地址资源指定不同的名称和 IP 版本。

有关创建过程中公共 IP 地址的特定属性的更多详细信息，请参阅下表。

   |设置|必需？|详细信息|
   |---|---|---|
   |IP 版本|是| 选择 IPv4 或 IPv6 或“两者”。 选择“两者”将导致创建 2 个公共 IP 地址，1 个 IPv4 地址和 1个 IPv6 地址。 详细了解 [Azure VNET 中的 IPv6](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。|
   |SKU|是|引入 SKU 之前创建的所有公共 IP 地址均为基本 SKU 公共 IP 地址。 创建公共 IP 地址后，无法更改此 SKU。 独立虚拟机、可用性集内的虚拟机或虚拟机规模集可使用基本 SKU 或标准 SKU。 不允许在可用性集或规模集或独立 VM 内的虚拟机之间混用 SKU。 基本 SKU：如果要在支持可用性区域的区域内创建公共 IP 地址，“可用性区域”设置默认设为“无”。 基本公共 Ip 不支持可用性区域。 **标准** SKU：标准 SKU 公共 IP 可关联到虚拟机或负载均衡器前端。 如果要在支持可用性区域的区域内创建公共 IP 地址，“可用性区域”设置默认设为“区域冗余”。 有关可用性区域的详细信息，请参阅“可用性区域”设置。 将地址关联到标准负载均衡器时需使用标准 SKU。 若要了解标准 负载均衡器的详细信息，请参阅 [Azure 负载均衡器标准 SKU](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 将标准 SKU 公共 IP 地址分配到虚拟机的网络接口时，必须使用 [网络安全组](./network-security-groups-overview.md#network-security-groups)显式允许预期流量。 创建并关联网络安全组且显式允许所需流量之后，才可与资源通信。|
   |层|是|指示 IP 地址是与区域 (区域) ，还是从多 **个区域 (** **全局**) 进行 "随机广播"。 *请注意，"全局层" IP 是标准 ip 的预览功能，目前仅用于跨区域负载均衡器*。|
   |名称|是|名称在所选资源组中必须唯一。|
   |IP 地址分配|是|**动态：** 只有在将公共 IP 地址与 Azure 资源相关联并首次启动该资源时，才分配动态地址。 如果将动态地址分配给某个资源，例如虚拟机，并且虚拟机停止（解除分配）后又重启，则动态地址可能会更改。 如果虚拟机重启或停止（但未解除分配），该地址将保持不变。 当公共 IP 地址资源从它关联到的资源取消关联时，会释放动态地址。 **静态：** 静态地址是在创建公共 IP 地址时分配的。 删除公共 IP 地址资源之前，不会释放静态地址。 如果地址没有关联到资源，则在创建地址后可以更改分配方法。 如果地址已关联到资源，则无法更改分配方法。 如果选择 IPv6 作为“IP 版本”，则对于 Basic SKU，分配方法必须为“动态”。  对于 IPv4 和 IPv6，标准 SKU 地址均为“静态”。 |
   |空闲超时(分钟)|否|在不依赖客户端发送保持连接消息的情况下，TCP 或 HTTP 连接持续打开的分钟数。 如果选择 IPv6 作为“IP 版本”，则不能更改此值。 |
   |DNS 名称标签|否|必须在创建名称的 Azure 位置中（在所有订阅和所有客户中）保持唯一。 Azure 会在其 DNS 中自动注册该名称和 IP 地址，使你能够连接到使用该名称的资源。 Azure 会将“location.cloudapp.azure.com”（其中 location 是所选的位置）此类默认子网追加到提供的名称后面，以创建完全限定的 DNS 名称。 如果选择同时创建这两个地址版本，则会将相同的 DNS 名称分配给 IPv4 和 IPv6 地址。 Azure 的默认 DNS 服务包含 IPv4 A 和 IPv6 AAAA 名称记录，并在查找 DNS 名称时响应这两个记录。 客户端选择要与哪个地址（IPv4 或 IPv6）通信。 除了使用带有默认后缀的 DNS 名称标签，还可以改用 Azure DNS 服务来配置带有自定义后缀（可解析为公共 IP 地址）的 DNS 名称。 有关详细信息，请参阅[将 Azure DNS 与 Azure 公共 IP 地址配合使用](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)。|
   |名称（仅选择“两者”的 IP 版本时才可见）|是，如果选择“两者”的 IP 版本|该名称必须不同于在此列表中的第一个“名称”中输入的名称。 如果选择同时创建 IPv4 和 IPv6 地址，门户将创建两个单独的公共 IP 地址资源，每个资源中分配有一个 IP 地址版本。|
   |IP 地址分配（仅选择“两者”的 IP 版本时才可见）|是，如果选择“两者”的 IP 版本|与上面的 IP 地址分配有相同的限制|
   |订阅|是|必须与要将公共 IP 地址关联到的资源位于同一[订阅](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)中。|
   |资源组|是|可与要将公共 IP 地址关联到的资源位于相同或不同的[资源组](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)中。|
   |位置|是|必须与要将公共 IP 地址关联到的资源位于同一[位置](https://azure.microsoft.com/regions)（也称为“区域”）。|
   |可用性区域| 否 | 选择受支持的位置时才会显示此设置。 有关支持位置的列表，请参阅[可用性区域概述](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 如果选择“标准”SKU，则自动选择“无”。 如果倾向于保证特定区域，可以选择一个特定区域。 任一选择皆不是区域冗余。 如果选择“标准”SKU，则自动选中区域冗余，让数据路径在区域故障时具有恢复能力。 如果倾向于保证特定区域（区域故障时不具有恢复能力），可以选择一个特定区域。

## <a name="view-modify-settings-for-or-delete-a-public-ip-address"></a>查看、修改公共 IP 地址的设置或删除公共 IP 地址

   - 查看/列出：用于检查公共 IP 的设置，包括 SKU、地址、任何适用的关联（例如虚拟机 NIC、负载均衡器前端）。
   - **修改**：用于使用 [创建公共 IP 地址](#create-a-public-ip-address)的第 4 步中的信息来修改设置，如空闲超时、DNS 名称标签或分配方法。  （有关将公共 IP SKU 从“基本”升级到“标准”的完整过程，请参阅[升级 Azure 公共 IP 地址](./virtual-network-public-ip-address-upgrade.md)。）
   >[!WARNING]
   >若要将公共 IP 地址的分配方法从静态更改为动态，必须先将该地址与任何适用的 IP 配置取消关联（请参阅“删除”部分）。  另外请注意，在将分配方法从静态更改为动态时，已分配给公共 IP 地址的 IP 地址会丢失。 尽管 Azure 公共 DNS 服务器会保留静态或动态地址与任何 DNS 名称标签（若已定义）之间的映射，但如果虚拟机在处于停止（解除分配）状态之后启动，动态 IP 地址可能更改。 为防止地址变化，请分配静态 IP 地址。
   
|操作|Azure 门户|Azure PowerShell|Azure CLI|
|---|---|---|---|
|查看 | 在公共 IP 的“概述”部分中 |使用 [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 来检索公共 IP 地址对象并查看其设置| 使用 [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) 来显示设置|
|列出 | 在“公共 IP 地址”类别下 |使用 [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 来检索一个或多个公共 IP 地址对象并查看其设置|使用 [az network public-ip list](/cli/azure/network/public-ip#az-network-public-ip-list) 来列出公共 IP 地址|
|修改 | 对于取消关联的 IP，请选择“配置”来修改空闲超时、DNS 名称标签，或将基本 IP 的分配方式从静态更改为动态  |使用 [Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) 来更新设置 |使用 [az network public-ip update](/cli/azure/network/public-ip#az-network-public-ip-update) 进行更新 |

   - **删除**：若要删除公共 IP，需要该公共 IP 对象不与任何 IP 配置或虚拟机 NIC 关联。 有关详细信息，请参阅下表。

|资源|Azure 门户|Azure PowerShell|Azure CLI|
|---|---|---|---|
|[虚拟机](./remove-public-ip-address-vm.md)|选择“取消关联”，以将该 IP 地址与 NIC 配置取消关联，然后选择“删除” 。|使用 [Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) 来将该 IP 地址与 NIC 配置取消关联；使用 [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) 来删除|使用 [az network public-ip update --remove](/cli/azure/network/public-ip#az-network-public-ip-update) 来将该 IP 地址与 NIC 配置取消关联；使用 [az network public-ip delete](/cli/azure/network/public-ip#az-network-public-ip-delete) 来删除 |
|负载均衡器前端 | 导航到未使用的公共 IP 地址并选择“关联”，然后选择具有相关的前端 IP 配置的负载均衡器来替换该地址（然后可使用与用于 VM 的同一方法来删除旧 IP）  | 使用 [Set-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig) 将新的前端 IP 配置关联到公共负载均衡器；使用 [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) 来删除；也可以使用 [Remove-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/remove-azloadbalancerfrontendipconfig) 来删除前端 IP 配置（如果有多个） |使用 [az network lb frontend-ip update](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_update) 将新的前端 IP 配置关联到公共负载均衡器；使用 [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) 来删除，也可以使用 [az network lb frontend-ip delete](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_delete) 来删除前端 IP 配置（如果有多个）|
|防火墙|空值| 使用 [Deallocate()](../firewall/firewall-faq.yml#how-can-i-stop-and-start-azure-firewall) 来解除分配防火墙并删除所有 IP 配置 | 使用 [az network firewall ip-config delete](/cli/azure/ext/azure-firewall/network/firewall/ip-config#ext_azure_firewall_az_network_firewall_ip_config_delete) 来删除 IP（但必须先使用 PowerShell 来解除分配）|

## <a name="virtual-machine-scale-sets"></a>虚拟机规模集

在使用具有公共 IP 的虚拟机规模集时，不存在与单个虚拟机实例关联的独立公共 IP 对象。 但是，公共 IP 前缀对象[可用于生成实例 IP](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/)。

若要列出虚拟机规模集上的公共 IP，可以使用 PowerShell ([Get-AzPublicIpAddress -VirtualMachineScaleSetName](/powershell/module/az.network/get-azpublicipaddress)) 或 CLI ([az vmss list-instance-public-ips](/cli/azure/vmss#az_vmss_list_instance_public_ips))。

有关详细信息，请参阅 [Azure 虚拟机规模集的网络](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine)。

## <a name="assign-a-public-ip-address"></a>分配公共 IP 地址

了解如何将公共 IP 地址分配给以下资源：

- [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机（在创建时），或分配给[现有的虚拟机](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [公共负载均衡器](../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [应用程序网关](../application-gateway/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [使用 VPN 网关的站点到站点连接](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [虚拟机规模集](../virtual-machine-scale-sets/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>权限

若要在公共 IP 地址上执行任务，必须将你的帐户分配给[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或分配有下表中所列适当操作的[自定义](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)角色：

| 操作                                                             | 名称                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | 读取公共 IP 地址                                          |
| Microsoft.Network/publicIPAddresses/write                          | 创建或更新公共 IP 地址                           |
| Microsoft.Network/publicIPAddresses/delete                         | 删除公共 IP 地址                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | 将公共 IP 地址关联到资源                    |

## <a name="next-steps"></a>后续步骤

- 使用 [PowerShell](powershell-samples.md) 或 [Azure CLI](cli-samples.md) 示例脚本或使用 Azure [资源管理器模板](template-samples.md)创建公共 IP 地址
- 为公共 IP 地址创建和分配 [Azure Policy 定义](./policy-reference.md)
