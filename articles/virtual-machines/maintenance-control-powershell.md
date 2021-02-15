---
title: 使用 PowerShell 对 Azure 虚拟机进行维护控制
description: 了解如何使用维护控制和 PowerShell 来控制对 Azure VM 应用维护的时间。
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/19/2020
ms.author: cynthn
ms.openlocfilehash: 2cc935e81e867609159b5c150b6ee7c346bb9f8e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026142"
---
# <a name="control-updates-with-maintenance-control-and-azure-powershell"></a>使用维护控制和 Azure PowerShell 来控制更新

维护控制允许您决定何时将平台更新应用于隔离 Vm 和 Azure 专用主机的主机基础结构。 本主题介绍维护控制的 Azure PowerShell 选项。 有关使用维护控制的好处、其限制和其他管理选项的详细信息，请参阅[使用维护控制管理平台更新](maintenance-control.md)。
 
## <a name="enable-the-powershell-module"></a>启用 PowerShell 模块

确保 `PowerShellGet` 为最新版本。    

```azurepowershell-interactive  
Install-Module -Name PowerShellGet -Repository PSGallery -Force 
``` 

安装 `Az.Maintenance` PowerShell 模块。     

```azurepowershell-interactive  
Install-Module -Name Az.Maintenance
``` 

如果在本地安装，请确保以管理员身份打开 PowerShell 提示符。

系统还可能会要求你确认是否要从不受信任的存储库进行安装。 键入 `Y` 或选择“全是”以安装该模块。


## <a name="create-a-maintenance-configuration"></a>创建维护配置

创建一个资源组作为适用于配置的容器。 在此示例中，在 *eastus* 中创建名为 *myMaintenanceRG* 的资源组。 如果已有一个可供使用的资源组，则可跳过此部分，并在其余示例中将资源组名称替换为你自己的名称。

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

使用 [New-AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) 创建维护配置。 此示例创建名为 myConfig 的维护配置，该配置的范围限定为主机。 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

使用 `-MaintenanceScope host` 确保将维护配置用于控制对主机的更新。

如果尝试创建同名的但位于不同位置的配置，则会收到错误。 配置名称在资源组中必须是唯一的。

可以使用 [Get-AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration) 来查询可用的维护配置。

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>使用计划的窗口创建维护配置

你还可以在 Azure 将对资源应用更新时声明计划的窗口。 此示例将创建一个名为 Myconfig.xml 的维护配置，其中每个月的第四个星期一的计划窗口为5小时。 创建计划的窗口后，不再需要手动应用更新。

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope Host `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Month Fourth Monday"
```
> [!IMPORTANT]
> 维护 **持续时间** 必须是 *2 小时* 或更长时间。 在35天内，必须至少将维护 **重复** 设置为一次。

可以按每日、每周或每月来表示维护 **定期** 。 一些示例如下：
 - **每日**-RecurEvery "Day" **或** "3Days" 
 - **每周**-RecurEvery "3Weeks" **或** "Week 周六，星期日" 
 - **每月**-RecurEvery "month day23、day24" **或** "month 上月" **或** "month 第四个月"  
      

## <a name="assign-the-configuration"></a>分配此配置

使用 [New-AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment) 将配置分配到隔离的 VM 或 Azure 专用主机。

### <a name="isolated-vm"></a>隔离的 VM

使用此配置的 ID 将配置应用到 VM。 指定 `-ResourceType VirtualMachines`，为 `-ResourceName` 提供 VM 的名称，为 `-ResourceGroupName` 提供 VM 的资源组。 

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

### <a name="dedicated-host"></a>专用主机

若要将配置应用到专用主机，还需要包含 `-ResourceType hosts`、带主机组名称的 `-ResourceParentName`，以及 `-ResourceParentType hostGroups`。 


```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

## <a name="check-for-pending-updates"></a>检查是否有挂起的更新

使用 [Get-AzMaintenanceUpdate](/powershell/module/az.maintenance/get-azmaintenanceupdate) 查看是否存在挂起的更新。 使用 `-subscription` 指定 VM 的 Azure 订阅（如果其订阅不同于你登录到的订阅）。

如果没有可显示的更新，此命令不会返回任何内容。 否则，它会返回一个 PSApplyUpdate 对象：

```json
{
   "maintenanceScope": "Host",
   "impactType": "Freeze",
   "status": "Pending",
   "impactDurationInSec": 9,
   "notBefore": "2020-02-21T16:47:44.8728029Z",
   "properties": {
      "resourceId": "/subscriptions/39c6cced-4d6c-4dd5-af86-57499cd3f846/resourcegroups/Ignite2019/providers/Microsoft.Compute/virtualMachines/MCDemo3"
} 
```

### <a name="isolated-vm"></a>隔离的 VM

检查隔离的 VM 的挂起更新。 在此示例中，输出的格式为表格，方便你阅读。

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>专用主机

检查专用主机的挂起更新。 在此示例中，输出的格式为表格，方便你阅读。 将资源的值替换为你自己的值。

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```


## <a name="apply-updates"></a>应用更新

使用 [New-AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate) 应用挂起的更新。

### <a name="isolated-vm"></a>隔离的 VM

创建向隔离的 VM 应用更新的请求。

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

成功时，此命令会返回一个 `PSApplyUpdate` 对象。 可以在 `Get-AzApplyUpdate` 命令中使用 Name 特性来检查更新状态。 请参阅[检查更新状态](#check-update-status)。

### <a name="dedicated-host"></a>专用主机

将更新应用到专用主机。

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>检查更新状态
使用 [Get-AzApplyUpdate](/powershell/module/az.maintenance/get-azapplyupdate) 检查更新状态。 对 `-ApplyUpdateName` 参数使用 `default` 时，以下命令会显示最新更新的状态。 可以使用更新的名称（由 [New-AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate) 命令返回）来获取特定更新的状态。

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime 是完成更新的时间，此更新是你发起的，或由平台在你未使用自行维护时段时发起的。 如果从未通过维护控制来应用更新，则会显示默认值。

### <a name="isolated-vm"></a>隔离的 VM

检查对特定虚拟机的更新。

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>专用主机

检查对专用主机的更新。

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName myUpdateName
```

## <a name="remove-a-maintenance-configuration"></a>删除维护配置

使用 [Remove-AzMaintenanceConfiguration](/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) 删除维护配置。

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>后续步骤
若要了解详细信息，请参阅[维护和更新](maintenance-and-updates.md)。
