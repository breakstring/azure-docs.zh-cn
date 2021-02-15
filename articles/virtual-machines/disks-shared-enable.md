---
title: 为 Azure 托管磁盘启用共享磁盘
description: 为 Azure 托管磁盘配置共享磁盘，以便可以跨多个 VM 共享它
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/30/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: e8bb97196fcceea0c86f58fe4a63146e681c427e
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500727"
---
# <a name="enable-shared-disk"></a>启用共享磁盘

本文介绍了如何为 Azure 托管磁盘启用共享磁盘功能。 Azure 共享磁盘是 Azure 托管磁盘的一项新功能，可同时将托管磁盘附加到多个虚拟机 (VM)。 通过将托管磁盘附加到多个 VM，可以向 Azure 部署新的群集应用程序或迁移现有的群集应用程序。 

如果正在查找有关已启用共享磁盘的托管磁盘的概念信息，请参阅 [Azure 共享磁盘](disks-shared.md)。

## <a name="limitations"></a>限制

[!INCLUDE [virtual-machines-disks-shared-limitations](../../includes/virtual-machines-disks-shared-limitations.md)]

## <a name="supported-operating-systems"></a>支持的操作系统

共享磁盘支持多个操作系统。 有关支持的操作系统，请参阅概念文章的 [Windows](./disks-shared.md#windows) 和 [Linux](./disks-shared.md#linux) 部分。

## <a name="disk-sizes"></a>磁盘大小

[!INCLUDE [virtual-machines-disks-shared-sizes](../../includes/virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>部署共享磁盘

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>将高级 SSD 部署为共享磁盘

若要部署启用了共享磁盘功能的托管磁盘，请使用新属性 `maxShares` 并定义大于 1 的值。 这会使该磁盘可在多个 VM 之间共享。

> [!IMPORTANT]
> 仅当从所有 VM 中卸载了某个磁盘时，才能设置或更改 `maxShares` 的值。 有关 `maxShares` 的允许值，请参阅[磁盘大小](#disk-sizes)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku Premium_LRS --max-shares 2
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType Premium_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig
```

# <a name="resource-manager-template"></a>[资源管理器模板](#tab/azure-resource-manager)

使用以下模板之前，请使用你自己的值替换 `[parameters('dataDiskName')]`、`[resourceGroup().location]`、`[parameters('dataDiskSizeGB')]` 和 `[parameters('maxShares')]`。

[高级 SSD 共享磁盘模板](https://aka.ms/SharedPremiumDiskARMtemplate)

---

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>将超磁盘部署为共享磁盘

若要部署启用了共享磁盘功能的托管磁盘，请将 `maxShares` 参数更改为大于1的值。 这会使该磁盘可在多个 VM 之间共享。

> [!IMPORTANT]
> 仅当从所有 VM 中卸载了某个磁盘时，才能设置或更改 `maxShares` 的值。 有关 `maxShares` 的允许值，请参阅[磁盘大小](#disk-sizes)。


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

##### <a name="regional-disk-example"></a>地区性磁盘示例

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

##### <a name="zonal-disk-example"></a>区域性磁盘示例

此示例与上一个示例基本相同，只不过它在可用性区域1中创建了一个磁盘。

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1 --zone 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

##### <a name="regional-disk-example"></a>地区性磁盘示例

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

##### <a name="zonal-disk-example"></a>区域性磁盘示例

此示例与上一个示例基本相同，只不过它在可用性区域1中创建了一个磁盘。

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5 -Zone 1

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

# <a name="resource-manager-template"></a>[资源管理器模板](#tab/azure-resource-manager)

##### <a name="regional-disk-example"></a>地区性磁盘示例

使用以下模板之前，请将、、、、、、 `[parameters('dataDiskName')]` `[resourceGroup().location]` 和替换 `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` `[parameters('diskMBpsReadOnly')]` 为自己的值。

[区域共享的 ultra 磁盘模板](https://aka.ms/SharedUltraDiskARMtemplateRegional)

##### <a name="zonal-disk-example"></a>区域性磁盘示例

使用以下模板之前，请将、、、、、、 `[parameters('dataDiskName')]` `[resourceGroup().location]` 和替换 `[parameters('dataDiskSizeGB')]` `[parameters('maxShares')]` `[parameters('diskIOPSReadWrite')]` `[parameters('diskMBpsReadWrite')]` `[parameters('diskIOPSReadOnly')]` `[parameters('diskMBpsReadOnly')]` 为自己的值。

[区域性共享的 ultra 磁盘模板](https://aka.ms/SharedUltraDiskARMtemplateZonal)

---

## <a name="using-azure-shared-disks-with-your-vms"></a>将 Azure 共享磁盘与 VM 配合使用

使用 `maxShares>1` 部署共享磁盘后，可以将该磁盘装载到一个或多个 VM。

> [!NOTE]
> 如果要部署的是 ultra 磁盘，请确保它符合必要的要求。 有关详细信息，请参阅 [使用 Azure ultra 磁盘](disks-enable-ultra-ssd.md) 。

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress"

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>支持的 SCSI PR 命令

将共享磁盘装载到群集中的 VM 后，可以使用 SCSI PR 建立仲裁以及在磁盘中进行读取/写入操作。 使用 Azure 共享磁盘时，可以使用以下 PR 命令：

若要与磁盘进行交互，请从 persistent-reservation-action 列表开始：

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

使用 PR_RESERVE、PR_PREEMPT_RESERVATION 或 PR_RELEASE_RESERVATION 时，请提供下列 persistent-reservation-type 之一：

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

使用 PR_RESERVE、PR_REGISTER_AND_IGNORE、PR_REGISTER_KEY、PR_PREEMPT_RESERVATION、PR_CLEAR_RESERVATION 或 PR_RELEASE-RESERVATION 时，还需要提供 persistent-reservation-key。


## <a name="next-steps"></a>后续步骤

如果希望使用 Azure 资源管理器模板来部署磁盘，可使用以下示例模板：
- [高级·SSD](https://aka.ms/SharedPremiumDiskARMtemplate)
- [区域超磁盘](https://aka.ms/SharedUltraDiskARMtemplateRegional)
- [区域性超磁盘](https://aka.ms/SharedUltraDiskARMtemplateZonal)