---
title: 使用 Azure Site Recovery 复制运行存储空间直通的 Azure VM
description: 了解如何使用 Azure Site Recovery 复制运行存储空间直通的 Azure VM。
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: f438fadb73f7e3bd25cd7ab9aef0bc46285e30e2
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424831"
---
# <a name="replicate-azure-vms-running-storage-spaces-direct-to-another-region"></a>将运行存储空间直通的 Azure VM 复制到另一区域

本文介绍了如何为运行存储空间直通的 Azure VM 启用灾难恢复。

>[!NOTE]
>存储空间直通群集仅支持故障一致恢复点。
>

[存储空间直通 (S2D)](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) 是软件定义的存储，可便于在 Azure 上创建[来宾群集](https://techcommunity.microsoft.com/t5/failover-clustering/bg-p/FailoverClustering)。  Microsoft Azure 中的来宾群集是由 IaaS Vm 组成的故障转移群集。 这样一来，托管的 VM 工作负载可以跨来宾群集进行故障转移，从而实现更高的应用程序可用性 SLA（与单一 Azure VM 相比）。 它适用于 VM 托管关键应用程序（如 SQL 或横向扩展文件服务器）的方案。

## <a name="disaster-recovery-with-storage-spaces-direct"></a>利用存储空间直通进行灾难恢复

在典型方案中，为了提高横向扩展文件服务器等应用程序的复原能力，可能会在 Azure 上使用虚拟机来宾群集。 虽然这样可以提高应用程序可用性，但仍希望使用 Site Recovery 保护这些应用程序免受任何区域级别故障。 发生故障转移时，Site Recovery 将数据从一个 Azure 区域复制到另一个 Azure 区域，并调出灾难恢复区域中的群集。

下图展示了一个使用存储空间直通的双节点 Azure VM 故障转移群集。

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)


- Windows 故障转移群集中有两个 Azure 虚拟机，每个虚拟机都有两个或多个数据磁盘。
- S2D 同步数据磁盘上的数据，并以存储池的形式提供同步的存储。
- 存储池作为群集共享卷 (CSV) 提供给故障转移群集。
- 故障转移群集对数据驱动器使用 CSV。

**灾难恢复注意事项**

1. 为群集设置[云见证](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp)时，请让见证一直位于灾难恢复区域中。
2. 若要将虚拟机故障转移到与源区域不同的 DR 区域上的子网，那么需要在执行故障转移后更改群集 IP 地址。  必须使用 Site Recovery [恢复计划脚本](./site-recovery-runbook-automation.md)，才能更改群集 IP。</br>
[示例脚本](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1)使用自定义脚本扩展在 VM 中执行命令 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>为 S2D 群集启用 Site Recovery：

1. 在恢复服务保管库中，单击“+复制”
1. 选择群集中的所有节点，并将它们归入[多 VM 一致性组](./azure-to-azure-common-questions.md#multi-vm-consistency)
1. 选择复制策略，同时禁用应用程序一致性*（仅支持故障一致性）
1. 启用复制

   ![显示在何处配置复制设置的屏幕截图。](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. 转到已复制的项，可以看到两个虚拟机的状态。
3. 两个虚拟机均受到保护，并显示为属于多 VM 一致性组。

   ![显示虚拟机受保护并且属于多 VM 一致性组的屏幕截图。](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>创建恢复计划
恢复计划支持在故障转移期间将多层应用程序中的各个层排序。 排序有助于保持应用程序一致性。 为多层 Web 应用程序创建恢复计划时，请完成[使用 Site Recovery 创建恢复计划](site-recovery-create-recovery-plans.md)中所述的步骤。

### <a name="adding-virtual-machines-to-failover-groups"></a>将虚拟机添加到故障转移组

1.  通过添加虚拟机来创建恢复计划。
2.  单击“自定义”将 VM 分组。 默认情况下，所有 VM 属于“组 1”。


### <a name="add-scripts-to-the-recovery-plan"></a>将脚本添加到恢复计划
在故障转移后或测试故障转移期间，可能需要在 Azure 虚拟机上执行一些操作才能让应用程序正常工作。 可将某些故障转移后的操作自动化。 例如，此时将附加负载均衡器，并更改群集 IP。


### <a name="failover-of-the-virtual-machines"></a>虚拟机故障转移 
VM 的两个节点都需要使用 [Site Recovery 恢复计划](./site-recovery-create-recovery-plans.md)进行故障转移 

![storagespacesdirect 保护](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>运行测试故障转移
1.  在 Azure 门户中，选择恢复服务保管库。
2.  选择已创建的恢复计划。
3.  选择“测试故障转移”  。
4.  若要启动测试故障转移过程，请选择恢复点和 Azure 虚拟网络。
5.  当辅助环境启动时，执行验证。
6.  完成验证后，选择“清理测试故障转移”清理测试故障转移环境。 

有关详细信息，请参阅[在 Site Recovery 中执行到 Azure 的测试故障转移](site-recovery-test-failover-to-azure.md)。

## <a name="run-a-failover"></a>运行故障转移

1.  在 Azure 门户中，选择恢复服务保管库。
2.  选择针对 SAP 应用程序创建的恢复计划。
3.  选择“故障转移”。 
4.  若要启动故障转移过程，请选择恢复点。

有关详细信息，请参阅 [Site Recovery 中的故障转移](site-recovery-failover.md)。
## <a name="next-steps"></a>后续步骤

[详细了解](./azure-to-azure-tutorial-failover-failback.md)如何运行故障回复。
