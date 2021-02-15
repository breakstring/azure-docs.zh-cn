---
title: 使用 Azure Site Recovery/PowerShell 设置到辅助站点的 Hyper-V（使用 VMM）灾难恢复
description: 介绍如何使用 Azure Site Recovery 和 PowerShell 设置 VMM 云中的 Hyper-V VM 到辅助 VMM 站点的灾难恢复。
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 1/10/2020
ms.author: sutalasi
ms.openlocfilehash: a4f7d330db9a4a0d9b435ebe7527e55e37c254e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87086211"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>使用 PowerShell（资源管理器）设置 Hyper-V VM 到辅助站点的灾难恢复

本文介绍如何使用 [Azure Site Recovery](site-recovery-overview.md) 来自动完成相关步骤，以便将 System Center Virtual Machine Manager 云中的 Hyper-V VM 复制到辅助本地站点中的 Virtual Machine Manager 云。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必备条件

- 查看[方案体系结构和组件](hyper-v-vmm-architecture.md)。
- 查看所有组件的[支持要求](./vmware-physical-secondary-support-matrix.md)。
- 确保 Virtual Machine Manager 服务器和 Hyper-V 主机符合[支持要求](./vmware-physical-secondary-support-matrix.md)。
- 确保要复制的 VM 符合[复制计算机支持](./vmware-physical-secondary-support-matrix.md)。

## <a name="prepare-for-network-mapping"></a>准备网络映射

[网络映射](hyper-v-vmm-network-mapping.md)在源和目标云中的本地 Virtual Machine Manager VM 网络之间映射。 映射可执行以下操作：

- 故障转移后，将 VM 连接到适当的目标 VM 网络。
- 最好将副本 VM 放于目标 Hyper-V 主机服务器上。
- 如果不配置网络映射，则故障转移后，VM 副本不会连接到 VM 网络。

如下所述准备 Virtual Machine Manager：

- 确保源服务器和目标 Virtual Machine Manager 服务器上具有 [Virtual Machine Manager 逻辑网络](/system-center/vmm/network-logical)：
  - 源服务器上的逻辑网络应与 Hyper-V 主机所在的源云相关联。
  - 目标服务器上的逻辑网络应与目标云相关联。
- 确保源服务器和目标 Virtual Machine Manager 服务器上具有 [VM 逻辑网络](/system-center/vmm/network-virtual)。 VM 网络应链接到每个位置中的逻辑网络。
- 将源 Hyper-V 主机上的 VM 连接到源 VM 网络。

## <a name="prepare-for-powershell"></a>为 PowerShell 做准备

确保已将 Azure PowerShell 准备就绪：

- 如果已使用 PowerShell，请升级到 0.8.10 或更高版本。 [详细了解](/powershell/azure/)如何设置 PowerShell。
- 设置并配置 PowerShell 后，请参阅[服务 cmdlet](/powershell/azure/)。
- 若要详细了解如何在 PowerShell 中使用参数值、输入和输出，请参阅[入门](/powershell/azure/get-started-azureps)指南。

## <a name="set-up-a-subscription"></a>设置订阅

1. 从 PowerShell 登录到 Azure 帐户。

   ```azurepowershell
   $UserName = "<user@live.com>"
   $Password = "<password>"
   $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
   $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
   Connect-AzAccount #-Credential $Cred
   ```

1. 使用订阅 ID 检索订阅列表。 记下要在其中创建恢复服务保管库的订阅的 ID。

   ```azurepowershell
   Get-AzSubscription
   ```

1. 设置保管库的订阅。

   ```azurepowershell
   Set-AzContext –SubscriptionID <subscriptionId>
   ```

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

1. 如果没有 Azure 资源管理器资源组，则创建一个。

   ```azurepowershell
   New-AzResourceGroup -Name #ResourceGroupName -Location #location
   ```

1. 创建新的恢复服务保管库。 将保管库对象保存在后面会用到的变量中。

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   ```

   可以在创建保管库对象后使用 `Get-AzRecoveryServicesVault` cmdlet 来检索它。

## <a name="set-the-vault-context"></a>设置保管库上下文

1. 检索现有保管库。

   ```azurepowershell
   $vault = Get-AzRecoveryServicesVault -Name #vaultname
   ```

1. 设置保管库上下文。

   ```azurepowershell
   Set-AzRecoveryServicesAsrVaultContext -Vault $vault
   ```

## <a name="install-the-site-recovery-provider"></a>安装 Site Recovery 提供程序

1. 在 Virtual Machine Manager 计算机上，运行以下命令创建一个目录：

   ```azurepowershell
   New-Item -Path C:\ASR -ItemType Directory
   ```

1. 使用下载的提供程序安装文件提取这些文件。

   ```console
   pushd C:\ASR\
   .\AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. 安装该提供程序，等待安装完成。

   ```console
   .\SetupDr.exe /i
   $installationRegPath = "HKLM:\Software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
   do
   {
     $isNotInstalled = $true;
     if(Test-Path $installationRegPath)
     {
       $isNotInstalled = $false;
     }
   }While($isNotInstalled)
   ```

1. 在保管库中注册服务器。

   ```console
   $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
   pushd $BinPath
   $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
   ```

## <a name="create-and-associate-a-replication-policy"></a>创建和关联复制策略

1. 创建复制策略（在本例中，复制策略是针对 Hyper-V 2012 R2 的），如下所示：

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $RepProvider = HyperVReplica2012R2
   $Recoverypoints = 24                    #specify the number of hours to retain recovery points
   $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
   $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
   $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
   $InitialRepMethod = "Online" #options are "Online" or "Offline"

   $policyresult = New-AzRecoveryServicesAsrPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -NumberOfRecoveryPointsToRetain $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod
   ```

   > [!NOTE]
   > Virtual Machine Manager 云包含的 Hyper-V 主机可能运行不同版本的 Windows Server，但复制策略是针对特定版本的操作系统的。 如果不同的主机运行在不同的操作系统上，请为每个系统创建不同的复制策略。 例如，如果有 5 个主机运行在 Windows Server 2012 上，3 个主机运行在 Windows Server 2012 R2 上，请创建两个复制策略。 为每种类型的操作系统各创建一个复制策略。

1. 检索主保护容器（主 Virtual Machine Manager 云）和恢复保护容器（恢复 Virtual Machine Manager 云）。

   ```azurepowershell
   $PrimaryCloud = "testprimarycloud"
   $primaryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloud;

   $RecoveryCloud = "testrecoverycloud"
   $recoveryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $RecoveryCloud;
   ```

1. 使用友好名称检索所创建的复制策略。

   ```azurepowershell
   $policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $policyname
   ```

1. 开始将保护容器（Virtual Machine Manager 云）与复制策略相关联。

   ```azurepowershell
   $associationJob  = New-AzRecoveryServicesAsrProtectionContainerMapping -Policy $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
   ```

1. 等待策略关联作业完成。 若要检查作业是否已完成，请使用以下 PowerShell 代码片段：

   ```azurepowershell
   $job = Get-AzRecoveryServicesAsrJob -Job $associationJob

   if($job -eq $null -or $job.StateDescription -ne "Completed")
   {
     $isJobLeftForProcessing = $true;
   }
   ```

1. 在作业完成处理后，运行以下命令：

   ```azurepowershell
   if($isJobLeftForProcessing)
   {
     Start-Sleep -Seconds 60
   }
   While($isJobLeftForProcessing)
   ```

若要检查作业是否完成，请遵循[监视活动](#monitor-activity)中的步骤。

##  <a name="configure-network-mapping"></a>配置网络映射

1. 使用此命令检索当前保管库的服务器。 此命令将 Site Recovery 服务器存储在 `$Servers` 数组变量中。

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesAsrFabric
   ```

1. 运行以下命令，检索源 Virtual Machine Manager 服务器和目标 Virtual Machine Manager 服务器的网络。

   ```azurepowershell
   $PrimaryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[0]

   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[1]
   ```

   > [!NOTE]
   > 在服务器数组中，源 Virtual Machine Manager 服务器可以是一个，也可以是第二个。 检查 Virtual Machine Manager 服务器名称，并相应地检索网络。

1. 此 cmdlet 在主网络与恢复网络之间创建映射。 它将主网络指定为 `$PrimaryNetworks` 的第一个元素。 它将恢复网络指定为 `$RecoveryNetworks` 的第一个元素。

   ```azurepowershell
   New-AzRecoveryServicesAsrNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]
   ```

## <a name="enable-protection-for-vms"></a>为 VM 启用保护

正确配置服务器、云和网络后，请在云中为 VM 启用保护。

1. 若要启用保护，请运行以下命令以检索保护容器：

   ```azurepowershell
   $PrimaryProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloudName
   ```

1. 获取保护实体 (VM)，如下所示：

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
   ```

1. 为 VM 启用复制。

   ```azurepowershell
   $jobResult = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $protectionentity -ProtectionContainerMapping $policy -VmmToVmm
   ```

> [!NOTE]
> 如果希望复制到 Azure 中启用了 CMK 的托管磁盘，请使用 Az PowerShell 3.3.0 及更高版本执行以下步骤：
>
> 1. 通过更新 VM 属性启用到托管磁盘的故障转移
> 1. 使用 `Get-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet 获取受保护项的每个磁盘的磁盘 ID
> 1. 使用 `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` cmdlet 创建包含磁盘 ID 到磁盘加密集映射的字典对象。 这些磁盘加密集将由你在目标区域中预先创建。
> 1. 通过在 **DiskIdToDiskEncryptionSetMap** 参数中传递字典对象，使用 `Set-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet 更新 VM 属性。

## <a name="run-a-test-failover"></a>运行测试故障转移

若要测试部署，请针对单个虚拟机运行测试故障转移。 也可以创建包含多个 VM 的恢复计划，并针对该计划运行测试故障转移。 测试故障转移在隔离的网络中模拟故障转移和恢复机制。

1. 检索需要将 VM 故障转移到其中的 VM。

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesASRFabric
   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Name $Servers[1]
   ```

1. 执行测试故障转移。

   对于单个 VM：

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity -VMNetwork $RecoveryNetworks[1]
   ```

   对于恢复计划：

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan -VMNetwork $RecoveryNetworks[1]
   ```

若要检查作业是否完成，请遵循[监视活动](#monitor-activity)中的步骤。

## <a name="run-planned-and-unplanned-failovers"></a>运行计划内和计划外故障转移

1. 执行计划内故障转移。

   对于单个 VM：

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   对于恢复计划：

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

1. 执行计划外故障转移。

   对于单个 VM：

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   对于恢复计划：

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

## <a name="monitor-activity"></a>监视活动

使用以下命令来监视故障转移活动。 在执行不同的作业之前，请等待处理完成。

```azurepowershell
Do
{
    $job = Get-AzRecoveryServicesAsrJob -TargetObjectId $associationJob.JobId;
    Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
    if($job -eq $null -or $job.StateDescription -ne "Completed")
    {
        $isJobLeftForProcessing = $true;
    }

if($isJobLeftForProcessing)
    {
        Start-Sleep -Seconds 60
    }
}While($isJobLeftForProcessing)
```

## <a name="next-steps"></a>后续步骤

[详细了解](/powershell/module/az.recoveryservices)如何将 Site Recovery 和资源管理器 PowerShell cmdlet 配合使用。
