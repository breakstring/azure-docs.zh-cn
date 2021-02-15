---
title: 将数据渗透限制为 Azure 存储-Azure CLI
description: 本文介绍如何使用 Azure CLI 的虚拟网络服务终结点策略，将虚拟网络数据渗透限制和限制为 Azure 存储资源。
services: virtual-network
documentationcenter: virtual-network
author: rdhillon
manager: ''
editor: ''
tags: azure-resource-manager
Customer intent: I want only specific Azure Storage account to be allowed access from a virtual network subnet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: 9ce1e320a93a834a938ce95f3931d885d2214faa
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216851"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-the-azure-cli"></a>使用 Azure CLI 的虚拟网络服务终结点策略管理数据渗透到 Azure 存储帐户

利用虚拟网络服务终结点策略，你可以通过服务终结点在虚拟网络中应用对 Azure 存储帐户的访问控制。 这是保护工作负荷的关键，管理允许的存储帐户和允许数据渗透的位置。
在本文中，学习如何：

* 创建一个虚拟网络并添加一个子网。
* 启用 Azure 存储的服务终结点。
* 创建两个 Azure 存储帐户，并在上面创建的子网中允许对其进行网络访问。
* 创建服务终结点策略，只允许访问其中一个存储帐户。
* 将 (VM) 的虚拟机部署到子网。
* 从子网确认对允许的存储帐户的访问权限。
* 确认拒绝从子网到不允许的存储帐户的访问。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 2.0.28 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-virtual-network"></a>创建虚拟网络

创建虚拟网络之前，必须为虚拟网络创建资源组以及本文中创建的所有其他资源。 使用 [az group create](/cli/azure/group) 创建资源组。 以下示例在“eastus”  位置创建名为“myResourceGroup”  的资源组。

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

使用 [az network vnet create](/cli/azure/network/vnet) 创建包含一个子网的虚拟网络。

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Private \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>启用服务终结点 

在此示例中，为子网 "*专用*" 创建了 *Microsoft* 的服务终结点： 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.0.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>限制子网的网络访问

使用 [az network nsg create](/cli/azure/network/nsg) 创建网络安全组。 以下示例创建名为 *myNsgPrivate* 的网络安全组。

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet) 将该网络安全组关联到 *Private* 子网。 以下示例将 *myNsgPrivate* 网络安全组关联到 *Private* 子网：

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

使用 [az network nsg rule create](/cli/azure/network/nsg/rule) 创建安全规则。 下面的规则允许对分配给 Azure 存储服务的公共 IP 地址进行出站访问： 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"
```

每个网络安全组包含多个[默认安全规则](./network-security-groups-overview.md#default-security-rules)。 以下规则将替代允许对所有公共 IP 地址进行出站访问的默认安全规则。 `destination-address-prefix "Internet"` 选项拒绝对所有公共 IP 地址进行出站访问。 上一个规则将替代此规则，因为它的优先级更高，上一个规则允许对 Azure 存储的公共 IP 地址进行访问。

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"
```

以下规则允许 SSH 流量从任何位置入站到子网。 该规则将替代拒绝来自 Internet 的所有入站流量的默认安全规则。 允许通过 SSH 访问子网，以便在稍后的步骤中测试连接。

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>限制对 Azure 存储帐户的网络访问

本部分列出了通过服务终结点限制虚拟网络中给定子网的 Azure 存储帐户的网络访问的步骤。

### <a name="create-a-storage-account"></a>创建存储帐户

用 [az storage account create](/cli/azure/storage/account)创建两个 Azure 存储帐户。

```azurecli-interactive
storageAcctName1="allowedstorageacc"

az storage account create \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2

storageAcctName2="notallowedstorageacc"

az storage account create \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

创建存储帐户后，使用 [az storage account show-string](/cli/azure/storage/account)将存储帐户的连接字符串检索到变量中。 在后面的步骤中将使用此连接字符串来创建文件共享。

```azurecli-interactive
saConnectionString1=$(az storage account show-connection-string \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)

saConnectionString2=$(az storage account show-connection-string \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>查看变量的内容并记下在输出中返回的 **AccountKey** 的值，因为后面的步骤中将使用该值。

```azurecli-interactive
echo $saConnectionString1

echo $saConnectionString2
```

### <a name="create-a-file-share-in-the-storage-account"></a>在存储帐户中创建文件共享

使用 [az storage share create](/cli/azure/storage/share) 在存储帐户中创建一个文件共享。 在后面的步骤中，将装载此文件共享来确认对它的网络访问。

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString1 > /dev/null

az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString2 > /dev/null
```

### <a name="deny-all-network-access-to-the-storage-account"></a>拒绝对存储帐户的所有网络访问

默认情况下，存储帐户接受来自任何网络中的客户端的网络连接。 若要仅允许所选的网络进行访问，请使用 [az storage account update](/cli/azure/storage/account) 将默认操作更改为 *Deny*。 在拒绝网络访问后，将无法从任何网络访问存储帐户。

```azurecli-interactive
az storage account update \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --default-action Deny

az storage account update \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-virtual-network-subnet"></a>从虚拟网络子网启用网络访问

使用 [az storage account network-rule add](/cli/azure/storage/account/network-rule) 允许从 *Private* 子网对存储帐户进行网络访问。

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName1 \
  --vnet-name myVirtualNetwork \
  --subnet Private

az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName2 \
  --vnet-name myVirtualNetwork \
  --subnet Private
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>应用策略以允许访问有效的存储帐户

Azure 服务终结点策略仅适用于 Azure 存储。 因此，在此示例设置中，我们将为此子网启用服务终结点 *。*

服务终结点策略应用于服务终结点。 首先，我们将创建服务终结点策略。 然后，我们将在此策略下为要为此子网批准的 Azure 存储帐户创建策略定义

创建服务终结点策略

```azurecli-interactive
az network service-endpoint policy create \
  --resource-group myResourceGroup \
  --name mysepolicy \
  --location eastus
```

在变量中保存允许的存储帐户的资源 URI。 在执行下面的命令之前，请 *\<your-subscription-id>* 将替换为你的订阅 ID 的实际值。

```azurecli-interactive
$serviceResourceId="/subscriptions/<your-subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/allowedstorageacc"
```

创建 & 为允许将上述 Azure 存储帐户添加到服务终结点策略的策略定义

```azurecli-interactive
az network service-endpoint policy-definition create \
  --resource-group myResourceGroup \
  --policy-name mysepolicy \
  --name mypolicydefinition \
  --service "Microsoft.Storage" \
  --service-resources $serviceResourceId
```

并更新虚拟网络子网，使其与上一步中创建的服务终结点策略关联

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --service-endpoints Microsoft.Storage \
  --service-endpoint-policy mysepolicy
```

## <a name="validate-access-restriction-to-azure-storage-accounts"></a>验证对 Azure 存储帐户的访问限制

### <a name="create-the-virtual-machine"></a>创建虚拟机

若要测试对存储帐户的网络访问，请将 VM 部署到子网。

使用 [az VM create](/cli/azure/vm)在 *专用* 子网中创建 VM。 如果默认密钥位置中尚不存在 SSH 密钥，该命令会创建它们。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

创建 VM 需要几分钟时间。 在创建后，记下返回的输出中的 **publicIpAddress**。 在后面的步骤中会使用此地址通过 Internet 访问 VM。

### <a name="confirm-access-to-storage-account"></a>确认对存储帐户的访问

通过 SSH 登录到 *myVmPrivate* VM。 将 *\<publicIpAddress>* 替换为 *myVmPrivate* VM 的公共 IP 地址。

```bash 
ssh <publicIpAddress>
```

为装入点创建一个文件夹：

```bash
sudo mkdir /mnt/MyAzureFileShare1
```

将 Azure 文件共享装载到你创建的目录中。 执行下面的命令之前，请将替换 *\<storage-account-key>* **$saConnectionString 1** 中 *AccountKey* 的值。

```bash
sudo mount --types cifs //allowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare1 --options vers=3.0,username=allowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

你将收到 `user@myVmPrivate:~$` 提示。 Azure 文件共享已成功装载到 */mnt/MyAzureFileShare*。

### <a name="confirm-access-is-denied-to-storage-account"></a>确认已拒绝对存储帐户的访问

在同一 VM *myVmPrivate* 中，为装入点创建一个目录：

```bash
sudo mkdir /mnt/MyAzureFileShare2
```

尝试将 Azure 文件共享从存储帐户 *notallowedstorageacc* 装载到你创建的目录中。 本文假定你已部署了 Ubuntu 的最新版本。 如果使用的是 Ubuntu 的早期版本，请参阅[在 Linux 上装载](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json)来了解有关装载文件共享的其他说明。 

在执行下面的命令之前，请将替换 *\<storage-account-key>* 为 **$saConnectionString 2** 中的 *AccountKey* 值。

```bash
sudo mount --types cifs //notallowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare2 --options vers=3.0,username=notallowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

拒绝访问，并收到 `mount error(13): Permission denied` 错误，因为此存储帐户不在应用于子网的服务终结点策略的允许列表中。 

退出与 *myVmPublic* VM 建立的 SSH 会话。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，可以使用 [az group delete](/cli/azure) 将其删除。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>后续步骤

本文介绍如何通过 Azure 虚拟网络服务终结点将服务终结点策略应用于 Azure 存储。 已创建 Azure 存储帐户，并且仅限 (特定存储帐户的网络访问权限，因此从虚拟网络子网中拒绝其他) 。 若要详细了解服务终结点策略，请参阅 [服务终结点策略概述](virtual-network-service-endpoint-policies-overview.md)。