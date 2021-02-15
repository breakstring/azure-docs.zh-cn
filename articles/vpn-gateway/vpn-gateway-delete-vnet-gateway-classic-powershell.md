---
title: 删除虚拟网络网关：Azure 经典
description: 在经典部署模型中使用 PowerShell 删除虚拟网络网关。
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 39585a68c5cddc50cd04e82caca71209270f7b68
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91874112"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>使用 PowerShell 删除虚拟网络网关（经典）

> [!div class="op_single_selector"]
> * [Resource Manager - Azure 门户](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [经典 - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>

本文可帮助在经典部署模型中使用 PowerShell 删除 VPN 网关。 删除虚拟网络网关后，修改网络配置文件以删除不再使用的元素。

## <a name="step-1-connect-to-azure"></a><a name="connect"></a>步骤 1：连接到 Azure

### <a name="1-install-the-latest-powershell-cmdlets"></a>1.安装最新的 PowerShell cmdlet。

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="2-connect-to-your-azure-account"></a>2.连接到 Azure 帐户。

使用提升的权限打开 PowerShell 控制台，并连接到帐户。 使用下面的示例来帮助连接：

1. 使用提升的权限打开 PowerShell 控制台。
2. 连接到帐户。 使用下面的示例来帮助连接：

   ```powershell
   Add-AzureAccount
   ```

## <a name="step-2-export-and-view-the-network-configuration-file"></a><a name="export"></a>步骤 2：导出并查看网络配置文件

在计算机上创建一个目录，然后将网络配置文件导出到该目录。 使用此文件查看当前配置信息并修改网络配置。

在此示例中，网络配置文件导出到 C:\AzureNet。

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

使用文本编辑器打开文件，并查看经典 VNet 的名称。 在 Azure 门户中创建 VNet 时，Azure 使用的全名在门户中不可见。 例如，在 Azure 门户中命名为“ClassicVNet1”的 VNet 可能在网络配置文件中具有更长的名称。 名称可能如下所示：“Group ClassicRG1 ClassicVNet1”。 虚拟网络名称以 **"VirtualNetworkSite name ="** 的形式列出。 运行 PowerShell cmdlet 时，请使用网络配置文件中的名称。

## <a name="step-3-delete-the-virtual-network-gateway"></a><a name="delete"></a>步骤 3：删除虚拟网络网关

删除虚拟网络网关时，通过该网关的所有 VNet 连接都将断开。 如果 P2S 客户端连接到 VNet，它们将断开连接且不发出警告。

此示例将删除虚拟网络网关。 确保使用网络配置文件中虚拟网络的全名。

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

如果成功，则返回显示：

```
Status : Successful
```

## <a name="step-4-modify-the-network-configuration-file"></a><a name="modify"></a>步骤 4：修改网络配置文件

删除虚拟网络网关时，cmdlet 不会修改网络配置文件。 需修改文件才可删除不再使用的元素。 以下部分可帮助你修改下载的网络配置文件。

### <a name="local-network-site-references"></a><a name="lnsref"></a>本地网络站点引用

若要删除站点引用信息，请更改 **ConnectionsToLocalNetwork/LocalNetworkSiteRef** 的配置。 删除本地站点引用会触发 Azure 删除隧道。 根据你创建的配置，可能没有列出 **LocalNetworkSiteRef** 。

```
<Gateway>
   <ConnectionsToLocalNetwork>
     <LocalNetworkSiteRef name="D1BFC9CB_Site2">
       <Connection type="IPsec" />
     </LocalNetworkSiteRef>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

例如：

```
<Gateway>
   <ConnectionsToLocalNetwork>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

### <a name="local-network-sites"></a><a name="lns"></a>本地网络站点

删除不再使用的所有本地站点。 根据已创建的配置，可能没有列出 **LocalNetworkSite**。

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
  <LocalNetworkSite name="Site3">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>57.179.18.164</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

本例中将仅删除 Site3。

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

### <a name="client-addresspool"></a><a name="clientaddresss"></a>客户地址池

如果 P2S 连接到 VNet，将有一个 **VPNClientAddressPool**。 删除与所删除的虚拟网络网关对应的客户地址池。

```
<Gateway>
    <VPNClientAddressPool>
      <AddressPrefix>10.1.0.0/24</AddressPrefix>
    </VPNClientAddressPool>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

例如：

```
<Gateway>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

### <a name="gatewaysubnet"></a><a name="gwsub"></a>GatewaySubnet

删除与 VNet 对应的 **GatewaySubnet** 。

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
   <Subnet name="GatewaySubnet">
     <AddressPrefix>10.11.1.0/29</AddressPrefix>
   </Subnet>
 </Subnets>
```

例如：

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
 </Subnets>
```

## <a name="step-5-upload-the-network-configuration-file"></a><a name="upload"></a>步骤 5：上传网络配置文件

保存所做的更改，并将网络配置文件上传到 Azure。 确保根据环境需要更改文件路径。

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

如果成功，则返回显示类似于下例的内容：

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
```
