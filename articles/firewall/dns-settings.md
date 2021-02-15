---
title: Azure 防火墙 DNS 设置
description: 你可以为 Azure 防火墙配置 DNS 服务器和 DNS 代理设置。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 01/04/2021
ms.author: victorh
ms.openlocfilehash: 855c06b610fb8166f6f2dfcf37af34efb3713ffe
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883209"
---
# <a name="azure-firewall-dns-settings"></a>Azure 防火墙 DNS 设置

可以为 Azure 防火墙配置自定义 DNS 服务器并启用 DNS 代理。 可以在部署防火墙时配置这些设置，或者以后从“DNS 设置”页进行配置。

## <a name="dns-servers"></a>DNS 服务器

DNS 服务器维护域名并将它解析为 IP 地址。 默认情况下，Azure 防火墙将 Azure DNS 用于名称解析。 通过“DNS 服务器”设置，你可以配置自己的 DNS 服务器来用于 Azure 防火墙名称解析。 你可以配置一台服务器或多台服务器。

> [!NOTE]
> 对于使用 Azure 防火墙管理器管理的 Azure 防火墙实例，DNS 设置是在关联的 Azure 防火墙策略中配置的。

### <a name="configure-custom-dns-servers---azure-portal"></a>配置自定义 DNS 服务器 - Azure 门户

1. 在 Azure 防火墙的“设置”下，选择“DNS 设置” 。
2. 在“DNS 服务器”下，可以键入或添加之前在虚拟网络中指定的现有 DNS 服务器。
3. 选择“保存”。

防火墙现在将 DNS 流量定向到指定的 DNS 服务器以进行名称解析。

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="屏幕截图显示了 DNS 服务器的设置。":::

### <a name="configure-custom-dns-servers---azure-cli"></a>配置自定义 DNS 服务器 - Azure CLI

以下示例使用 Azure CLI 更新具有自定义 DNS 服务器的 Azure 防火墙。

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> `az network firewall` 命令要求安装 Azure CLI 扩展 `azure-firewall`。 可以使用 `az extension add --name azure-firewall` 命令来安装它。 

### <a name="configure-custom-dns-servers---azure-powershell"></a>配置自定义 DNS 服务器 - Azure PowerShell

以下示例使用 Azure PowerShell 更新具有自定义 DNS 服务器的 Azure 防火墙。

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy"></a>DNS 代理

可以对 Azure 防火墙进行配置来充当 DNS 代理。 DNS 代理是从客户端虚拟机到 DNS 服务器的 DNS 请求的中介。 如果你配置了自定义 DNS 服务器，则应启用 DNS 代理以避免 DNS 解析不匹配，并在网络规则中启用 FQDN（完全限定的域名）筛选。

:::image type="content" source="media/dns-settings/dns-proxy-2.png" alt-text="使用自定义的 D N S 服务器的 d N S 代理配置。":::


如果未启用 DNS 代理，则来自客户端的 DNS 请求可能会在不同的时间传到 DNS 服务器，或者返回与防火墙不同的响应。 DNS 代理将 Azure 防火墙放置在客户端请求的路径中以避免不一致。

当 Azure 防火墙是 DNS 代理时，可以使用两种缓存函数类型：

- **积极缓存**：DNS 解析成功。 防火墙使用数据包或对象的 TTL（生存时间）。 

- **消极缓存**：DNS 解析不返回响应，或者不进行解析。 防火墙将此信息缓存一小时。

DNS 代理将存储网络规则中的 FQDN 的所有已解析的 IP 地址。 最佳做法是使用可解析为一个 IP 地址的 FQDN。  

### <a name="dns-proxy-configuration"></a>DNS 代理配置

配置 DNS 代理需要三个步骤：
1. 在 Azure 防火墙 DNS 设置中启用 DNS 代理。
2. （可选）配置自定义 DNS 服务器或使用提供的默认设置。
3. 在虚拟网络 DNS 服务器设置中将 Azure 防火墙的专用 IP 地址配置为自定义 DNS 地址。 此设置可确保 DNS 流量定向到 Azure 防火墙。

#### <a name="configure-dns-proxy---azure-portal"></a>配置 DNS 代理 - Azure 门户

若要配置 DNS 代理，必须将你的虚拟网络 DNS 服务器设置配置为使用防火墙专用 IP 地址。 然后在 Azure 防火墙的“DNS 设置”中启用 DNS 代理。

##### <a name="configure-virtual-network-dns-servers"></a>配置虚拟网络 DNS 服务器 

1. 选择 DNS 流量将通过 Azure 防火墙实例路由的虚拟网络。
2. 在“设置”下，选择“DNS 服务器”。 
3. 在“DNS 服务器”下，选择“自定义”。
4. 输入防火墙的专用 IP 地址。
5. 选择“保存” 。
6. 重启已连接到虚拟网络的 VM，以便为其分配新的 DNS 服务器设置。 VM 在重启之前，将继续使用其当前 DNS 设置。

##### <a name="enable-dns-proxy"></a>启用 DNS 代理

1. 选择你的 Azure 防火墙实例。
2. 在“设置”下，选择“DNS 设置” 。
3. 默认情况下，“DNS 代理”已禁用。 启用此设置后，防火墙会在端口 53 上进行侦听，并将 DNS 请求转发到配置的 DNS 服务器。
4. 查看“DNS 服务器”配置以确保设置适用于你的环境。
5. 选择“保存”。

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="屏幕截图显示了 DNS 代理的设置。":::

#### <a name="configure-dns-proxy---azure-cli"></a>配置 DNS 代理 - Azure CLI

你可以使用 Azure CLI 在 Azure 防火墙中配置 DNS 代理设置。 你还可以使用它来更新虚拟网络，以使用 Azure 防火墙作为 DNS 服务器。

##### <a name="configure-virtual-network-dns-servers"></a>配置虚拟网络 DNS 服务器

下面的示例将虚拟网络配置为使用 Azure 防火墙作为 DNS 服务器。
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

##### <a name="enable-dns-proxy"></a>启用 DNS 代理

以下示例在 Azure 防火墙中启用 DNS 代理功能。

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

#### <a name="configure-dns-proxy---azure-powershell"></a>配置 DNS 代理 - Azure PowerShell

你可以使用 Azure PowerShell 在 Azure 防火墙中配置 DNS 代理设置。 你还可以使用它来更新虚拟网络，以使用 Azure 防火墙作为 DNS 服务器。

##### <a name="configure-virtual-network-dns-servers"></a>配置虚拟网络 DNS 服务器

下面的示例将虚拟网络配置为使用 Azure 防火墙作为 DNS 服务器。

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

##### <a name="enable-dns-proxy"></a>启用 DNS 代理

以下示例在 Azure 防火墙中启用 DNS 代理功能。

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>后续步骤

[网络规则中的 FQDN 筛选](fqdn-filtering-network-rules.md)
