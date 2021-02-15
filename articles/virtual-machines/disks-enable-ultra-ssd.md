---
title: Vm 的 Ultra 磁盘-Azure 托管磁盘
description: 了解 Azure Vm 的 ultra 磁盘
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 12/10/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 9c3c1acbc2606d882ad45744457137be5014bc4c
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093479"
---
# <a name="using-azure-ultra-disks"></a>使用 Azure 超磁盘

本文介绍如何部署和使用超磁盘有关 ultra 磁盘的概念信息，请参阅[Azure 中的可用磁盘类型](disks-types.md#ultra-disk)

Azure ultra 磁盘为 Azure IaaS 虚拟机 (Vm) 提供高吞吐量、高 IOPS 和一致的低延迟磁盘存储。 此新产品提供出类拔萃的性能，其可用性级别与我们的现有磁盘产品相同。 超磁盘的一个主要优点是能够在不重新启动 Vm 的情况下动态更改 SSD 的性能和工作负荷。 超级磁盘适用于 SAP HANA、顶层数据库等数据密集型工作负荷，以及事务密集型工作负荷。

## <a name="ga-scope-and-limitations"></a>GA 范围和限制

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>确定 VM 大小和区域可用性

### <a name="vms-using-availability-zones"></a>使用可用性区域的 Vm

若要利用超磁盘，需要确定你所在的可用性区域。 并非每个区域都支持任何虚拟磁盘的 VM 大小。 若要确定你的区域、区域和 VM 大小是否支持 ultra 磁盘，请运行以下命令之一，确保首先替换 **region**、 **vmSize** 和 **订阅** 值：

#### <a name="cli"></a>CLI

```azurecli
subscription="<yourSubID>"
# example value is southeastasia
region="<yourLocation>"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
$sku = (Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})
if($sku){$sku[0].LocationInfo[0].ZoneDetails} Else {Write-host "$vmSize is not supported with Ultra Disk in $region region"}
```

响应将类似于以下形式，其中 X 是用于在所选区域中进行部署的区域。 X 可能是1、2 或 3。

保留 " **区域** " 值，它表示可用性区域，你需要它来部署超磁盘。

|ResourceType  |名称  |位置  |区域  |限制  |功能  |值  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> 如果命令没有响应，则所选区域中的 ultra 磁盘不支持所选的 VM 大小。

现在，你已了解要部署到哪个区域，请按照本文中的部署步骤，使用附加的 ultra 磁盘部署 VM，或者将超小型磁盘附加到现有 VM。

### <a name="vms-with-no-redundancy-options"></a>无冗余选项的 Vm

现在，必须部署在选择区域中部署的 Ultra 磁盘，无需任何冗余选项。 但是，并不是支持 ultra 磁盘的每个磁盘大小都可以在这些区域中。 若要确定哪些磁盘大小支持超小型磁盘，可以使用以下代码片段之一。 请确保 `vmSize` 首先替换和 `subscription` 值：

```azurecli
subscription="<yourSubID>"
region="westus"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

响应将类似于以下形式， `UltraSSDAvailable   True` 指示 VM 大小是否支持此区域中的超磁盘。

```
Name                                         Value
----                                         -----
MaxResourceVolumeMB                          884736
OSVhdSizeMB                                  1047552
vCPUs                                        64
HyperVGenerations                            V1,V2
MemoryGB                                     432
MaxDataDiskCount                             32
LowPriorityCapable                           True
PremiumIO                                    True
VMDeploymentTypes                            IaaS
vCPUsAvailable                               64
ACUs                                         160
vCPUsPerCore                                 2
CombinedTempDiskAndCachedIOPS                128000
CombinedTempDiskAndCachedReadBytesPerSecond  1073741824
CombinedTempDiskAndCachedWriteBytesPerSecond 1073741824
CachedDiskBytes                              1717986918400
UncachedDiskIOPS                             80000
UncachedDiskBytesPerSecond                   1258291200
EphemeralOSDiskSupported                     True
AcceleratedNetworkingEnabled                 True
RdmaEnabled                                  False
MaxNetworkInterfaces                         8
UltraSSDAvailable                            True
```

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>使用 Azure 资源管理器部署 ultra 磁盘

首先，确定要部署的 VM 大小。 有关受支持的 VM 大小的列表，请参阅 [GA 范围和限制](#ga-scope-and-limitations) 部分。

若要创建具有多个超磁盘的 VM，请参阅示例 [创建包含多个超磁盘的 vm](https://aka.ms/ultradiskArmTemplate)。

如果要使用自己的模板，请确保将 **apiVersion** `Microsoft.Compute/virtualMachines` 设置为 `Microsoft.Compute/Disks` `2018-06-01` (或更高版本) 。

将磁盘 sku 设置为 **UltraSSD_LRS**，然后设置磁盘容量、IOPS、可用性区域和吞吐量（以 MBps 为单位），以创建一个超小型磁盘

在预配 VM 后，可以对数据磁盘进行分区和格式设置并为工作负荷配置这些磁盘。


## <a name="deploy-an-ultra-disk"></a>部署超磁盘

# <a name="portal"></a>[门户](#tab/azure-portal)

本部分介绍如何部署一个虚拟机，并将其作为数据磁盘。 本教程假定你已熟悉如何部署虚拟机，如果不这样做，请参阅 [快速入门：在 Azure 门户中创建 Windows 虚拟机](./windows/quick-create-portal.md)。

- 登录到 [Azure 门户](https://portal.azure.com/) ，导航到 "部署虚拟机" (VM) "。
- 请确保选择支持的 [VM 大小和区域](#ga-scope-and-limitations)。
- 选择 **可用性选项** 中的 **可用性区域**。
- 填写所选选择的其余条目。
- 选择“磁盘”。

![Vm 创建流，基本边栏选项卡的屏幕截图。](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- 在 "磁盘" 边栏选项卡中，选择 **"是"** **启用超高磁盘兼容性**。
- 选择 " **创建并附加新磁盘** "，立即附加一个超磁盘。

![已启用 vm 创建流、"磁盘" 边栏选项卡、"超连接" 和 "创建并附加新磁盘" 的屏幕截图。](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- 在 " **创建新磁盘** " 边栏选项卡上，输入名称，然后选择 " **更改大小**"。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/ultra-disk-create-new-disk-flow.png" alt-text="&quot;创建新磁盘&quot; 边栏选项卡的屏幕截图，突出显示更改大小。":::


- 将 **存储类型** 更改为 " **超小型磁盘**"。
- 将 **自定义磁盘大小的值 (GiB)**、 **磁盘 IOPS** 和 **磁盘吞吐量** 更改为所选的磁盘。
- 在两个边栏选项卡中选择 **"确定"** 。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/ultra-disk-select-new-disk.png" alt-text="&quot;选择磁盘大小&quot; 边栏选项卡的屏幕截图、为存储类型选择的超磁盘、突出显示的其他值。":::

- 继续执行 VM 部署，该部署将与部署任何其他 VM 的部署相同。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

首先，确定要部署的 VM 大小。 有关支持的 VM 大小的列表，请参阅 [GA 范围和限制](#ga-scope-and-limitations) 部分。

必须创建能够使用 ultra 磁盘的 VM，才能附加超磁盘。

将 **$vmname**、 **$rgname**、 **$diskname**、 **$location**、 **$password**、 **$user** 变量替换为你自己的值。 将 **$zone**  设置为从 [本文开头](#determine-vm-size-and-region-availability)获取的可用性区域值。 然后运行以下 CLI 命令，以创建支持 ultra 的 VM：

```azurecli-interactive
az disk create --subscription $subscription -n $diskname -g $rgname --size-gb 1024 --location $location --sku UltraSSD_LRS --disk-iops-read-write 8192 --disk-mbps-read-write 400
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location --attach-data-disks $diskname
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

首先，确定要部署的 VM 大小。 有关支持的 VM 大小的列表，请参阅 [GA 范围和限制](#ga-scope-and-limitations) 部分。

若要使用超磁盘，必须创建能够使用超磁盘的 VM。 将 **$resourcegroup** 和 **$vmName** 变量替换为自己的值。 将 **$zone** 设置为从 [本文开头](#determine-vm-size-and-region-availability)获取的可用性区域值。 然后运行以下 [new-azvm](/powershell/module/az.compute/new-azvm) 命令，以创建支持 HYPER-V 的 VM：

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-and-attach-the-disk"></a>创建并附加磁盘

部署 VM 后，可以创建并将其附加到该虚拟机，请使用以下脚本：

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

---

## <a name="deploy-an-ultra-disk---512-byte-sector-size"></a>部署超高磁盘-512 字节扇区大小

# <a name="portal"></a>[门户](#tab/azure-portal)

Azure 门户当前不支持创建512字节扇区大小的超磁盘。 您可以使用 Azure PowerShell 模块或 Azure CLI 来创建具有512字节扇区大小的超磁盘。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

首先，确定要部署的 VM 大小。 有关支持的 VM 大小的列表，请参阅 [GA 范围和限制](#ga-scope-and-limitations) 部分。

必须创建能够使用 ultra 磁盘的 VM，才能附加超磁盘。

将 **$vmname**、 **$rgname**、 **$diskname**、 **$location**、 **$password**、 **$user** 变量替换为你自己的值。 将 **$zone**  设置为从 [本文开头](#determine-vm-size-and-region-availability)获取的可用性区域值。 然后运行以下 CLI 命令，以创建具有512字节扇区大小的超磁盘的 VM：

```azurecli
#create an ultra disk with 512 sector size
az disk create --subscription $subscription -n $diskname -g $rgname --size-gb 1024 --location $location --sku UltraSSD_LRS --disk-iops-read-write 8192 --disk-mbps-read-write 400 --logical-sector-size 512
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location --attach-data-disks $diskname
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

首先，确定要部署的 VM 大小。 有关支持的 VM 大小的列表，请参阅 [GA 范围和限制](#ga-scope-and-limitations) 部分。

若要使用超磁盘，必须创建能够使用超磁盘的 VM。 将 **$resourcegroup** 和 **$vmName** 变量替换为自己的值。 将 **$zone** 设置为从 [本文开头](#determine-vm-size-and-region-availability)获取的可用性区域值。 然后运行以下 [new-azvm](/powershell/module/az.compute/new-azvm) 命令，以创建支持 HYPER-V 的 VM：

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

若要创建并附加具有512字节扇区大小的 ultra 磁盘，可以使用以下脚本：

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-LogicalSectorSize 512 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
---
## <a name="attach-an-ultra-disk"></a>附加超磁盘

# <a name="portal"></a>[门户](#tab/azure-portal)

或者，如果你的现有 VM 位于能够使用超磁盘的区域/可用性区域中，则可以使用超磁盘，而不必创建新的 VM。 在现有 VM 上启用 ultra 磁盘，并将其附加为数据磁盘。 若要启用超高磁盘兼容性，必须停止 VM。 停止 VM 后，你可以启用兼容性，然后重启 VM。 启用兼容性后，可以附加一个超磁盘：

- 导航到你的 VM 并将其停止，并等待其解除分配。
- 解除分配 VM 后，请选择 " **磁盘**"。
- 选择“编辑”。

![现有 vm 磁盘边栏选项卡的屏幕截图，将突出显示 "编辑"。](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- 选择 **"是"** **启用超高磁盘兼容性**。

![启用超高磁盘兼容性的屏幕截图。](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- 选择“保存”。
- 选择 " **添加数据磁盘** "，然后在 " **名称** " 下拉列表中选择 " **创建磁盘**"。

![磁盘边栏选项卡的屏幕截图，添加新磁盘。](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- 填写新磁盘的名称，并选择 " **更改大小**"。
- 将 **帐户类型** 更改为 " **超小型磁盘**"。
- 将 **自定义磁盘大小的值 (GiB)**、 **磁盘 IOPS** 和 **磁盘吞吐量** 更改为所选的磁盘。

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/ultra-disk-select-new-disk.png" alt-text="&quot;选择磁盘大小&quot; 边栏选项卡的屏幕截图、为存储类型选择的超磁盘、突出显示的其他值。":::

- 选择 **"确定"** ，然后选择 " **创建**"。
- 返回到磁盘的边栏选项卡后，选择 " **保存**"。
- 重新启动 VM。

![Vm 上的 "磁盘" 边栏选项卡。](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

或者，如果你的现有 VM 位于能够使用超磁盘的区域/可用性区域中，则可以使用超磁盘，而不必创建新的 VM。

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm---cli"></a>在现有 VM 上启用超高磁盘兼容性-CLI

如果你的 VM 满足 [GA 范围和限制](#ga-scope-and-limitations) 中概述的要求，并且在 [适用于你的帐户的区域](#determine-vm-size-and-region-availability)中，则可以在 VM 上启用超高磁盘兼容性。

若要启用超高磁盘兼容性，必须停止 VM。 停止 VM 后，你可以启用兼容性，然后重启 VM。 启用兼容性后，可以附加一个超磁盘：

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk---cli"></a>创建 ultra 磁盘-CLI

现在，你有了一个能够附加超磁盘的 VM，你可以创建一个超磁盘并将其附加到该磁盘。

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

### <a name="attach-the-disk---cli"></a>附加磁盘-CLI

```azurecli
rgName="<yourResourceGroupName>"
vmName="<yourVMName>"
diskName="<yourDiskName>"
subscriptionId="<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

或者，如果你的现有 VM 位于能够使用超磁盘的区域/可用性区域中，则可以使用超磁盘，而不必创建新的 VM。

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm---powershell"></a>在现有 VM 上启用超高磁盘兼容性-PowerShell

如果你的 VM 满足 [GA 范围和限制](#ga-scope-and-limitations) 中概述的要求，并且在 [适用于你的帐户的区域](#determine-vm-size-and-region-availability)中，则可以在 VM 上启用超高磁盘兼容性。

若要启用超高磁盘兼容性，必须停止 VM。 停止 VM 后，你可以启用兼容性，然后重启 VM。 启用兼容性后，可以附加一个超磁盘：

```azurepowershell
#Stop the VM
Stop-AzVM -Name $vmName -ResourceGroupName $rgName
#Enable ultra disk compatibility
$vm1 = Get-AzVM -name $vmName -ResourceGroupName $rgName
Update-AzVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled $True
#Start the VM
Start-AzVM -Name $vmName -ResourceGroupName $rgName
```

### <a name="create-and-attach-an-ultra-disk---powershell"></a>创建和附加超小型磁盘-PowerShell

现在，你有了一个能够使用超磁盘的 VM，接下来可以创建一个超磁盘并将其附加到其中：

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

---
## <a name="adjust-the-performance-of-an-ultra-disk"></a>调整超磁盘的性能

# <a name="portal"></a>[门户](#tab/azure-portal)

超磁盘提供了独特的功能，使你能够调整其性能。 你可以在磁盘本身上从 Azure 门户进行这些调整。

- 导航到 VM，然后选择 " **磁盘**"。
- 选择要修改其性能的超磁盘。

![虚拟机上的 "磁盘" 边栏选项卡会突出显示 "超磁盘"。](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- 选择 " **配置** "，然后进行修改。
- 选择“保存”。

![你的超磁盘上的配置边栏选项卡、磁盘大小、iops 和吞吐量会突出显示，并且突出显示 "保存"。](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

超磁盘提供了独特的功能，使你能够调整性能，以下命令描述了如何使用此功能：

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>使用 PowerShell 调整超磁盘的性能

超磁盘具有独特的功能，使你能够调整性能，以下命令是一个示例，用于在不分离磁盘的情况下调整性能：

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```
---

## <a name="next-steps"></a>后续步骤

- [使用 Azure Kubernetes Service 上的 azure ultra 磁盘 (预览) ](../aks/use-ultra-disks.md)。
- [将日志磁盘迁移到超磁盘](../azure-sql/virtual-machines/windows/storage-migrate-to-ultradisk.md)。
