---
title: '将虚拟机移动到另一个区域 (Azure Site Recovery) '
description: 了解如何在 Azure 中将 SQL Server 虚拟机从一个区域迁移到另一个区域。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: migration
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 789554121af1c83d9077e6153ca9db01477bde25
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2020
ms.locfileid: "97360146"
---
# <a name="move-a-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery"></a>使用 Azure Site Recovery 将 SQL Server VM 移到 Azure 中的另一个区域
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍如何使用 Azure Site Recovery 在 Azure 中将 SQL Server 虚拟机 (VM) 从一个区域迁移到另一个区域。 

将 SQL Server VM 移到另一个区域需要执行以下操作：
1. [准备](#prepare-to-move)：确认源 SQL Server VM 和目标区域都已做好充分的迁移准备。 
1. [配置](#configure-azure-site-recovery-vault)：如果要移动 SQL Server VM，该 VM 必须是 Azure Site Recovery 保管库中的复制对象。 需要将 SQL Server VM 添加到 Azure Site Recovery 保管库中。 
1. [测试](#test-move-process)：如果要迁移 SQL Server VM，必须将它从源区域故障转移到复制的目标区域。 若要确保移动过程成功，需要先测试 SQL Server VM 是否可以成功故障转移到目标区域。 这将有助于发现任何问题，并在执行实际移动时避免这些问题。 
1. [移动](#move-the-sql-server-vm)：如果测试故障转移通过，并且你知道可以安全地迁移 SQL Server VM，就可以将 VM 移动到目标区域。 
1. [清理](#clean-up-source-resources)：若要避免计费，请从保管库中删除 SQL Server VM，并删除资源组中留下的所有不必要的资源。 

## <a name="verify-prerequisites"></a>验证先决条件 

- 确认[支持](../../../site-recovery/azure-to-azure-support-matrix.md#region-support)从源区域移动到目标区域。  
- 查看[方案体系结构和组件](../../../site-recovery/azure-to-azure-architecture.md)以及[支持限制和要求](../../../site-recovery/azure-to-azure-support-matrix.md)。 
- 验证帐户权限。 如果你创建了免费 Azure 帐户，那么你就是订阅的管理员。 如果你不是订阅管理员，请要求管理员分配你所需的权限。 若要为 VM 启用复制，并使用 Azure Site Recovery 复制数据，必须： 
    - 创建 VM 的权限。 “虚拟机参与者”内置角色具有这些权限，这包括： 
        - 在所选资源组中创建 VM 的权限。 
        - 在所选虚拟网络中创建 VM 的权限。 
        - 向所选存储帐户进行写入的权限。 
      - 管理 Azure Site Recovery 操作的权限。 “Site Recovery 参与者”角色拥有管理恢复服务保管库中 Site Recovery 操作所需的全部权限。  

## <a name="prepare-to-move"></a>准备移动
为移动准备源 SQL Server VM 和目标区域。 

### <a name="prepare-the-source-sql-server-vm"></a>准备源 SQL Server VM

- 请确保要移动的 SQL Server VM 上存在所有最新的根证书。 如果不存在最新的根证书，则安全性约束将阻止数据复制到目标区域。 
- 对于 Windows VM，请在 VM 上安装所有最新的 Windows 更新，使所有受信任的根证书位于该计算机上。 在断开连接的环境中，按照组织的标准 Windows 更新和证书更新过程进行操作。 
- 对于 Linux VM，请遵循 Linux 分销商提供的指导，在 VM 上获取最新的受信任根证书和证书吊销列表。 
- 确保未使用身份验证代理来控制要移动的 VM 的网络连接。 
- 如果尝试移动的 VM 无法访问 Internet，或使用防火墙代理来控制出站访问，请检查要求。 
- 确定源网络布局和当前正在使用的所有资源。 这包括但不限于负载均衡器、网络安全组 (NSG) 和公共 IP。 

### <a name="prepare-the-target-region"></a>准备目标区域

- 验证 Azure 订阅是否允许在用于灾难恢复的目标区域中创建 VM。 请联系支持部门，启用所需配额。 
- 请确保订阅中有足够的资源，能够支持大小与源 VM 匹配的 VM。 如果使用 Site Recovery 将数据复制到目标区域，Site Recovery 会为目标 VM 选择相同的大小或尽可能接近的大小。 
- 请确保为源网络布局中标识的每个组件创建目标资源。 此步骤对于确保目标区域中具有与源区域中相同的所有功能很重要。 
    - 为源 VM 启用复制时，Azure Site Recovery 会自动发现并创建虚拟网络。 此外可以预先创建网络，并将其分配到用户流中的 VM 以启用复制。 需要手动创建目标区域中的其他任何资源。
- 若要根据源 VM 配置创建最常用的相关网络资源，请参阅以下文档： 
    - [网络安全组](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [负载均衡器](../../../load-balancer/quickstart-load-balancer-standard-internal-portal.md)
    - [公共 IP 地址](../../../virtual-network/virtual-network-public-ip-address.md)
    - 对于其他任何网络组件，请参阅[网络文档](../../../virtual-network/virtual-networks-overview.md)。
- 若要在最终移动到目标区域之前测试配置，请在目标区域中手动创建非生产网络。 我们建议执行此步骤，因为这可以确保尽量减少对生产网络造成的干扰。 

## <a name="configure-azure-site-recovery-vault"></a>配置 Azure Site Recovery 保管库

以下步骤演示如何使用 Azure Site Recovery 将数据复制到目标区域。 在源区域以外的任何区域中创建恢复服务保管库。 

1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 从导航窗格的左上角选择“创建资源”。 
1. 选择“IT 和管理工具”，然后选择“备份和 Site Recovery” 。 
1. 在“基本”选项卡上的“项目详细信息”下，在目标区域中创建新的资源组或在目标区域中选择现有的资源组 。 
1. 在“实例详细信息”下，为保管库指定名称，然后从下拉列表中选择目标区域 。 
1. 选择“查看 + 创建”，创建恢复服务保管库。 
1. 从导航窗格的左上角选择“所有服务”，然后在搜索框中键入 `recovery services`。 
1. （可选）选择“恢复服务保管库”旁边的星号，将其添加到快速导航栏中。 
1. 选择“恢复服务保管库”，然后选择已创建的恢复服务保管库。 
1. 在“概述”窗格上，选择“复制” 。 

   ![配置复制](./media/move-sql-vm-different-region/configure-replication.png)

1. 选择“源”，然后选择“Azure”作为源 。 为其他下拉字段选择适当的值，例如源 VM 的位置。 仅位于“源位置”区域中的资源组才会显示在“源资源组”字段中 。 
1. 选择“虚拟机”，然后选择要迁移的虚拟机。 选择“确定”以保存 VM 选择。 
1. 选择“设置”，然后从下拉列表中选择“目标位置” 。 这应该是你之前准备的资源组。 
1. 自定义复制后，选择“创建目标资源”，在新位置创建资源。 
1. 资源创建完成后，选择“启用复制”，开始将 SQL Server VM 从源复制到目标区域。
1. 你可以通过导航到恢复保管库，选择“复制的项”，并查看 SQL Server VM 的状态来查看复制的状态 。 “受保护”状态指示复制已完成。 

   ![验证复制状态](./media/move-sql-vm-different-region/check-replication-status.png)

## <a name="test-move-process"></a>测试移动过程
以下步骤演示如何使用 Azure Site Recovery 测试移动过程。 

1. 导航到 [Azure 门户](https://portal.azure.com)的恢复服务保管库，选择“复制的项” 。 
1. 选择要移动的SQL Server VM，确认“复制运行状况”显示为“正常”，然后选择“测试故障转移”  。 

   ![为 VM 测试故障转移](./media/move-sql-vm-different-region/test-failover-of-replicated-vm.png)

1. 在“测试故障转移”页上，选择要用于故障转移的“最新的应用一致”恢复点，因为这是可以保证 SQL Server 数据一致性的唯一快照类型 。 
1. 选择“Azure 虚拟网络”下的虚拟网络，然后选择“确定”以测试故障转移 。 
   
   >[!IMPORTANT]
   > 我们建议使用单独的 Azure VM 网络进行故障转移测试。 不要使用在启用复制时设置的生产网络和最终要将 VM 移动到其中的生产网络。 

1. 要监视进度，请导航到保管库，在“监视”下选择“Site Recovery 作业”，然后选择正在进行的“测试故障转移”作业  。

   ![监视故障转移测试进度](./media/move-sql-vm-different-region/monitor-failover-test-job.png)

1. 测试完成后，导航至门户中的“虚拟机”并查看新创建的虚拟机。 请确保 SQL Server VM 正在运行、大小适当并已连接到适当的网络。 
1. 删除在测试中创建的 VM，因为在清理故障转移测试资源之前，“故障转移”选项将显示为灰色。 导航回保管库，依次选择“复制的项”、“SQL Server VM”和“清理测试故障转移” 。 在“备注”部分中记录并保存与测试相关的所有观察结果，然后选择“测试已完成”旁边的复选框。 删除测试故障转移虚拟机。 选择“确定”以在测试后清理资源。 

   ![故障转移测试后清理项](./media/move-sql-vm-different-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>移动 SQL Server VM 
以下步骤演示如何将 SQL Server VM 从源区域移动到目标区域。 

1. 导航到“恢复服务”保管库，依次选择“复制的项”、VM、和“故障转移”  。 

   ![启动故障转移](./media/move-sql-vm-different-region/initiate-failover.png)

1. 在“恢复点”下选择“最新的应用一致”恢复点 。 
1. 选中 " **在开始故障转移之前关闭计算机**" 旁边的复选框。 Site Recovery 在触发故障转移之前会尝试关闭源 VM。 即使关闭失败，故障转移也仍会继续。 
1. 选择“确定”以启动故障转移。
1. 可以从上一节中监视故障转移测试时查看的同一“Site Recovery 作业”页监视故障转移过程。 
1. 该作业完成后，检查 SQL Server VM 是否按预期显示在目标区域中。 
1. 导航回保管库，依次选择“复制的项”、“SQL Server VM”和“提交”以完成到目标区域的移动过程 。 请等待提交作业完成。 
1. 向 SQL IaaS 代理扩展注册 SQL Server VM，以在 Azure 门户中启用 **SQL 虚拟机** 可管理性以及与该扩展相关联的功能。 有关详细信息，请参阅 [向 SQL IaaS 代理扩展注册 SQL Server VM](sql-agent-extension-manually-register-single-vm.md)。 

  > [!WARNING]
  > 只有应用一致的快照才能保证 SQL Server 数据一致性。 最新处理的快照不能用于 SQL Server 故障转移，因为故障恢复快照不能保证 SQL Server 数据一致性。 

## <a name="clean-up-source-resources"></a>清理源资源
若要避免计费，请从保管库中删除 SQL Server VM，并删除任何不必要的关联资源。 

1. 导航回“Site Recovery”保管库，选择“复制的项”，然后选择“SQL Server VM” 。 
1. 选择“禁用复制”。 选择禁用保护的原因，然后选择“确定”以禁用复制。 

   >[!IMPORTANT]
   > 请务必执行此步骤，避免 Azure Site Recovery 复制产生费用。 

1. 如果没有计划重复使用源区域中的任何资源，请删除所有相关的网络资源和相应的存储帐户。 


## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上的 SQL Server 常见问题解答](frequently-asked-questions-faq.md)
* [Windows VM 上的 SQL Server 定价指南](pricing-guidance.md)
* [Windows VM 上的 SQL Server 发行说明](doc-changes-updates-release-notes.md)