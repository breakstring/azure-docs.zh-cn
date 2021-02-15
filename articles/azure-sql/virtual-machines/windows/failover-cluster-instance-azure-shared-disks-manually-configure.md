---
title: 使用 Azure 共享磁盘创建 FCI
description: 使用 Azure 共享磁盘为 Azure 虚拟机上的 SQL Server 创建故障转移群集实例 (FCI)。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2020
ms.author: mathoma
ms.openlocfilehash: 70f4ac69721db57aa06c0d8fda12189f43e79686
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537824"
---
# <a name="create-an-fci-with-azure-shared-disks-sql-server-on-azure-vms"></a>使用 Azure 共享磁盘创建 FCI（Azure VM 上的 SQL Server）
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍了如何使用 Azure 共享磁盘为 Azure 虚拟机 (VM) 上的 SQL Server 创建故障转移群集实例 (FCI)。 

若要了解详细信息，请参阅对 [Azure VM 上的 SQL Server 的 FCI](failover-cluster-instance-overview.md) 和[群集最佳做法](hadr-cluster-best-practices.md)的概述。 

## <a name="prerequisites"></a>先决条件 

在按本文中的说明操作之前，你应已具备以下条件：

- Azure 订阅。 [免费试用](https://azure.microsoft.com/free/)。 
- [两个或多个 Windows Azure 虚拟机](failover-cluster-instance-prepare-vm.md)。 对于超磁盘，支持高级 SSD 和[可用性区域](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address) (PPGs) 支持的[可用性集](../../../virtual-machines/windows/tutorial-availability-sets.md)和[邻近位置组](../../../virtual-machines/co-location.md#proximity-placement-groups)。 所有节点必须位于同一 [邻近位置组](../../../virtual-machines/co-location.md#proximity-placement-groups)中。
- 有权限在 Azure 虚拟机和 Active Directory 中创建对象的帐户。
- 最新版本的 [PowerShell](/powershell/azure/install-az-ps)。 

## <a name="add-azure-shared-disk"></a>添加 Azure 共享磁盘
在启用共享磁盘功能的情况下部署托管型高级 SSD 磁盘。 将 `maxShares` 设置为与群集节点的数目一致，以使磁盘可在所有 FCI 节点之间共享。 

通过执行以下操作添加一个 Azure 共享磁盘： 

1. 将以下脚本保存为 SharedDiskConfig.json： 

   ```JSON
   { 
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "dataDiskName": {
         "type": "string",
         "defaultValue": "mySharedDisk"
       },
       "dataDiskSizeGB": {
         "type": "int",
         "defaultValue": 1024
       },
       "maxShares": {
         "type": "int",
         "defaultValue": 2
       }
     },
     "resources": [
       {
         "type": "Microsoft.Compute/disks",
         "name": "[parameters('dataDiskName')]",
         "location": "[resourceGroup().location]",
         "apiVersion": "2019-07-01",
         "sku": {
           "name": "Premium_LRS"
         },
         "properties": {
           "creationData": {
             "createOption": "Empty"
           },
           "diskSizeGB": "[parameters('dataDiskSizeGB')]",
           "maxShares": "[parameters('maxShares')]"
         }
       }
     ] 
   }
   ```

2. 使用 PowerShell 运行 SharedDiskConfig.json： 

   ```powershell
   $rgName = < specify your resource group name>
       $location = 'westcentralus'
       New-AzResourceGroupDeployment -ResourceGroupName $rgName `
   -TemplateFile "SharedDiskConfig.json"
   ```

3. 对于每个 VM，请通过运行以下命令将附加的共享磁盘初始化为 GUID 分区表 (GPT)，并将其格式化为新技术文件系统 (NTFS)： 

    ```powershell
    $resourceGroup = "<your resource group name>"
    $location = "<region of your shared disk>"
    $ppgName = "<your proximity placement groups name>"
    $vm = Get-AzVM -ResourceGroupName "<your resource group name>" `
        -Name "<your VM node name>"
    $dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup `
        -DiskName "<your shared disk name>"
    $vm = Add-AzVMDataDisk -VM $vm -Name "<your shared disk name>" `
        -CreateOption Attach -ManagedDiskId $dataDisk.Id `
        -Lun <available LUN - check disk setting of the VM>
    Update-AzVm -VM $vm -ResourceGroupName $resourceGroup
    ```

## <a name="create-failover-cluster"></a>创建故障转移群集

若要创建故障转移群集，需要：

- 将成为群集节点的虚拟机的名称。
- 故障转移群集的名称。
- 故障转移群集的 IP 地址。 可以使用群集节点所在的同一 Azure 虚拟网络和子网中未使用的 IP 地址。

# <a name="windows-server-2012-2016"></a>[Windows Server 2012-2016](#tab/windows2012)

以下 PowerShell 脚本创建一个故障转移群集。 使用节点的名称（虚拟机名称）以及 Azure 虚拟网络中可用的 IP 地址更新脚本。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

以下 PowerShell 脚本创建一个故障转移群集。 使用节点的名称（虚拟机名称）以及 Azure 虚拟网络中可用的 IP 地址更新脚本。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

有关详细信息，请参阅[故障转移群集：群集网络对象](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)。

---

## <a name="configure-quorum"></a>配置仲裁

配置最符合业务需求的仲裁解决方案。 你可以配置[磁盘见证](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)、[云见证](/windows-server/failover-clustering/deploy-cloud-witness)或[文件共享见证](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)。 有关详细信息，请参阅 [SQL Server VM 上的仲裁](hadr-cluster-best-practices.md#quorum)。 

## <a name="validate-cluster"></a>验证群集
使用 UI 或 PowerShell 验证群集。

若要使用 UI 来验证群集，请在某个虚拟机中执行以下步骤：

1. 在“服务器管理器”下，依次选择“工具”、“故障转移群集管理器”。  
1. 在“故障转移群集管理器”下，依次选择“操作”、“验证配置”。  
1. 选择“**下一页**”。
1. 在“选择服务器或群集”下，输入两个虚拟机的名称。
1. 在“测试选项”下，选择“仅运行选择的测试”。  
1. 选择“**下一页**”。
1. 在“测试选择”中，选择除“存储”以外的所有测试。

## <a name="test-cluster-failover"></a>测试群集故障转移

测试你的群集的故障转移。 在“故障转移群集管理器”中，右键单击你的群集并选择“更多操作” > “移动核心群集资源” > “选择节点”，然后选择群集的其他节点。    将核心群集资源移到群集的每个节点，再将它移回主节点。 如果可以成功将群集移到每个节点，则表示你已为安装 SQL Server 做好了准备。  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="通过将核心资源移到其他节点来测试群集故障转移":::

## <a name="create-sql-server-fci"></a>创建 SQL Server FCI

配置故障转移群集和所有群集组件（包括存储）后，可以创建 SQL Server FCI。

1. 使用远程桌面协议 (RDP) 连接到第一个虚拟机。

1. 在“故障转移群集管理器”中，确保所有核心群集资源都位于第一个虚拟机上。 如有必要，请将所有资源移到该虚拟机。

1. 找到安装媒体。 如果虚拟机使用某个 Azure 市场映像，该媒体将位于 `C:\SQLServer_<version number>_Full`。 

1. 选择“设置”。

1. 在“SQL Server 安装中心”中选择“安装”。 

1. 选择“新建 SQL Server 故障转移群集安装”。 遵照向导中的说明安装 SQL Server FCI。

FCI 数据目录需位于 Azure 共享磁盘上。 

1. 根据向导中的说明完成操作后，安装程序会在第一个节点上安装 SQL Server FCI。

1. 安装程序在第一个节点上安装 FCI 后，请使用 RDP 连接到第二个节点。

1. 打开“SQL Server 安装中心”，然后选择“安装”。 

1. 选择“将节点添加到 SQL Server 故障转移群集”。 按照向导中的说明安装 SQL Server 并将此服务器添加到 FCI。

   >[!NOTE]
   >如果使用了包含 SQL Server 的 Azure 市场库映像，该映像已随附 SQL Server 工具。 如果未使用其中的某个映像，请单独安装 SQL Server 工具。 有关详细信息，请参阅 [下载 SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)。
   >

## <a name="register-with-the-sql-vm-rp"></a>向 SQL VM RP 注册

若要从门户管理你的 SQL Server VM，请在 [轻型管理模式](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode)下将其注册到 SQL IaaS 代理扩展 (RP) ，目前，Azure vm 上的 FCI 和 SQL Server 仅支持唯一模式。 

使用 PowerShell 在轻型模式下注册 SQL Server VM：  

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>配置连接 

若要将流量正确路由到当前主节点，请配置适用于你的环境的连接选项。 你可以创建 [Azure 负载均衡器](failover-cluster-instance-vnn-azure-load-balancer-configure.md)，也可以在使用 SQL Server 2019 CU2（或更高版本）和 Windows Server 2016（或更高版本）的情况下改用[分布式网络名称](failover-cluster-instance-distributed-network-name-dnn-configure.md)功能。 

## <a name="limitations"></a>限制

- 仅支持在 [轻型管理模式下](sql-server-iaas-agent-extension-automate-management.md#management-modes) 注册 SQL IaaS 代理扩展。

## <a name="next-steps"></a>后续步骤

使用[虚拟网络名称和 Azure 负载均衡器](failover-cluster-instance-vnn-azure-load-balancer-configure.md)或[分布式网络名称 (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md) 配置到 FCI 的连接（如果尚未执行此操作）。 

如果 Azure 共享磁盘不是适合你的 FCI 存储解决方案，请考虑改用[高级文件共享](failover-cluster-instance-premium-file-share-manually-configure.md)或[存储空间直通](failover-cluster-instance-storage-spaces-direct-manually-configure.md)来创建 FCI。 

若要了解详细信息，请参阅对 [Azure VM 上的 SQL Server 的 FCI](failover-cluster-instance-overview.md) 和[群集配置最佳做法](hadr-cluster-best-practices.md)的概述。

有关详细信息，请参阅： 
- [Windows 群集技术](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server 故障转移群集实例](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
