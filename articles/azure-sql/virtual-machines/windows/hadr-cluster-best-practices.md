---
title: 群集配置最佳实践
description: 了解在为 Azure 虚拟机上的 SQL Server 配置高可用性和灾难恢复 (HADR) 时支持的群集配置，例如支持的仲裁或连接路由选项。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 5a2540aeb36cfcb2048ec994bbb486badc8a68d1
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358803"
---
# <a name="cluster-configuration-best-practices-sql-server-on-azure-vms"></a>群集配置最佳做法（Azure VM 上的 SQL Server）
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

群集用于实现高可用性和灾难恢复 (HADR) ，并在 Azure 虚拟机 (Vm) 中 SQL Server。 

本文提供了在 Azure Vm 上将故障转移群集实例与 SQL Server 结合使用时， (Fci) 和[可用性组](availability-group-overview.md)的[故障转移群集实例](failover-cluster-instance-overview.md)的群集配置最佳实践。 


## <a name="networking"></a>网络

每台服务器 (群集节点上使用单个 NIC) 和单个子网。 Azure 网络具有物理冗余，这使得 Azure 虚拟机来宾群集上不需要其他 Nic 和子网。 群集验证报告将发出警告，指出节点只能在单个网络上访问。 可以在 Azure 虚拟机来宾故障转移群集上忽略此警告。

### <a name="tuning-failover-cluster-network-thresholds"></a>优化故障转移群集网络阈值

使用 SQL Server AlwaysOn 在 Azure Vm 中运行 Windows 故障转移群集节点时，建议将群集设置更改为更宽松的监视状态。  这会使群集更稳定、更可靠。  有关此功能的详细信息，请参阅 [IaaS WITH SQL AlwaysOn-优化故障转移群集网络阈值](/windows-server/troubleshoot/iaas-sql-failover-cluster)。

## <a name="quorum"></a>Quorum

尽管双节点群集在没有 [仲裁资源](/windows-server/storage/storage-spaces/understand-quorum)的情况下运行，但客户必须严格使用仲裁资源才能获得生产支持。 群集验证不会传递任何没有仲裁资源的群集。 

从技术上说，一个三节点群集可经受单个节点丢失 (到) 无仲裁资源的两个节点。 但在群集关闭到两个节点后，在发生节点丢失或通信失败以防出现裂脑情况时，群集资源会处于脱机状态。

配置仲裁资源将允许群集只在一个节点联机时继续联机。

下表按建议用于 Azure VM 的顺序列出了可用的仲裁选项，磁盘见证是首选选项： 


||[磁盘见证](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |[云见证](/windows-server/failover-clustering/deploy-cloud-witness)  |[文件共享见证](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**支持的 OS**| 全部 |Windows Server 2016 及更高版本| 全部|


### <a name="disk-witness"></a>磁盘见证

磁盘见证是群集可用存储组中的小型群集磁盘。 此磁盘高度可用，可以在节点之间进行故障转移。 它包含群集数据库的副本，其默认大小通常小于 1 GB。 对于使用 Azure 共享磁盘 (或任何共享磁盘解决方案（例如，共享 SCSI、iSCSI 或光纤通道 SAN) ）的任何群集，磁盘见证都是首选的仲裁选项。  群集共享卷不能用作磁盘见证。

将 Azure 共享磁盘配置为磁盘见证。 

若要开始，请参阅 [配置磁盘见证](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)。


**支持的操作系统**：All   


### <a name="cloud-witness"></a>云见证

云见证是一种故障转移群集仲裁见证，使用 Microsoft Azure 在群集仲裁上提供投票。 默认大小约为 1 MB，只包含时间戳。 云见证非常适合多个站点、多个区域和多个区域中的部署。

若要开始，请参阅 [配置云见证](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp)。


**支持的操作系统**：Windows Server 2016 及更高版本   


### <a name="file-share-witness"></a>文件共享见证

文件共享见证是通常在运行 Windows Server 的文件服务器上配置的 SMB 文件共享。 它在 witness.log 文件中维护群集信息，但不存储群集数据库的副本。 在 Azure 中，可以将 [azure 文件共享](../../../storage/files/storage-how-to-create-file-share.md) 配置为用作文件共享见证，也可以在单独的虚拟机上使用文件共享。

如果要使用 Azure 文件共享，可以使用用于 [装载高级文件共享](failover-cluster-instance-premium-file-share-manually-configure.md#mount-premium-file-share)的相同过程进行装载。 

若要开始，请参阅 [配置文件共享见证](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)。


**支持的操作系统**：Windows Server 2012 和更高版本   

## <a name="connectivity"></a>连接

在传统的本地网络环境中，SQL Server 故障转移群集实例似乎是在一台计算机上运行 SQL Server 的单个实例。 由于故障转移群集实例从节点故障转移到节点，因此，实例的虚拟网络名称 (VNN) 提供统一的连接点，并允许应用程序连接到 SQL Server 实例，而无需知道哪个节点当前处于活动状态。 发生故障转移时，虚拟网络名称会在新的活动节点启动后注册到该节点。 此过程对于连接到 SQL Server 的客户端或应用程序是透明的，这会最大程度地减少客户端或应用程序在故障期间遇到的停机时间。 同样，可用性组侦听器使用 VNN 将流量路由到相应的副本。 

将 VNN 与 Azure 负载均衡器或分布式网络名称一起使用 (DNN) 将流量路由到使用 Azure Vm 上的 SQL Server 的故障转移群集实例的 VNN，或替换可用性组中的现有 VNN 侦听器。 


下表比较了 HADR 连接的可支持性： 

| |**虚拟网络名称 (VNN)**  |**分布式网络名称 (DNN)**  |
|---------|---------|---------|
|**最低操作系统版本**| All | Windows Server 2016 |
|**最低 SQL Server 版本** |All |适用于 FCI 的 SQL Server 2019 CU2 () <br/> AG ) 的 SQL Server 2019 CU8 (|
|**支持的 HADR 解决方案** | 故障转移群集实例 <br/> 可用性组 | 故障转移群集实例 <br/> 可用性组|


### <a name="virtual-network-name-vnn"></a>虚拟网络名称 (VNN)

由于虚拟 IP 访问点在 Azure 中的工作方式有所不同，因此需要配置 [Azure 负载均衡器](../../../load-balancer/index.yml) 以将流量路由到 FCI 节点或可用性组侦听器的 IP 地址。 在 Azure 虚拟机中，负载均衡器保留群集 SQL Server 资源所依赖的 VNN 的 IP 地址。 负载均衡器分配到达前端的入站流，然后将该流量路由到后端池定义的实例。 可以使用负载均衡规则和运行状况探测来配置流量流。 通过 SQL Server FCI，后端池实例是运行 SQL Server 的 Azure 虚拟机。 

使用负载均衡器时存在轻微的故障转移延迟，因为默认情况下，运行状况探测每10秒执行一次活动检查。 

若要开始操作，请了解如何为[故障转移群集实例](failover-cluster-instance-vnn-azure-load-balancer-configure.md)或[可用性组](availability-group-vnn-azure-load-balancer-configure.md)配置 Azure 负载均衡器

**支持的操作系统**：All   
**支持的 SQL 版本**：All   
**支持的 HADR 解决方案**：故障转移群集实例和可用性组   


### <a name="distributed-network-name-dnn"></a>分布式网络名称 (DNN)

分布式网络名称是用于 SQL Server 2019 的新 Azure 功能。 DNN 提供了一种替代方法，SQL Server 客户端连接到 SQL Server 故障转移群集实例或可用性组，而无需使用负载均衡器。 

创建 DNN 资源后，该群集会将 DNS 名称与群集中所有节点的 IP 地址绑定在一起。 SQL 客户端将尝试连接到此列表中的每个 IP 地址，以查找要连接到的资源。  可以通过 `MultiSubnetFailover=True` 在连接字符串中指定来加速此过程。 此设置将告知提供程序并行尝试所有 IP 地址，以便客户端可以即时连接到 FCI 或侦听器。 

建议在可能的情况上通过负载均衡器使用分布式网络名称，因为： 
- 端到端解决方案更可靠，因为你不再需要维护负载均衡器资源。 
- 消除负载均衡器探测会将故障转移时间降至最低。 
- DNN 简化了对 Azure Vm 上 SQL Server 的故障转移群集实例或可用性组侦听器的预配和管理。 

当使用 DNN 时，大多数 SQL Server 功能都与 FCI 和可用性组透明地合作，但某些功能可能需要特别注意。 有关详细信息，请参阅 [FCI 和 DNN 互操作性](failover-cluster-instance-dnn-interoperability.md) 和 [AG 和 DNN 互操作性](availability-group-dnn-interoperability.md) 。 

若要开始，请参阅为[故障转移群集实例](failover-cluster-instance-distributed-network-name-dnn-configure.md)或[可用性组](availability-group-distributed-network-name-dnn-listener-configure.md)配置分布式网络名称资源

**支持的操作系统**：Windows Server 2016 及更高版本   
**支持的 SQL 版本**： SQL SERVER 2019 CU2 (FCI) 和 SQL SERVER 2019 CU8 (AG)    
**支持的 HADR 解决方案**：故障转移群集实例和可用性组   


## <a name="limitations"></a>限制

在 Azure 虚拟机上使用 FCI 或可用性组以及 SQL Server 时，请注意以下限制。 

### <a name="msdtc"></a>MSDTC 

Azure 虚拟机支持 Windows Server 2019 上的 Microsoft 分布式事务处理协调器 (MSDTC)，其中存储位于群集共享卷 (CSV) 和[标准负载均衡器](../../../load-balancer/load-balancer-overview.md)上，或者位于正在使用 Azure 共享磁盘的 SQL Server VM 上。 

在 Azure 虚拟机上，具有群集共享卷的 Windows Server 2016 或更早版本不支持 MSDTC，因为：

- 无法将群集 MSDTC 资源配置为使用共享存储。 在 Windows Server 2016 上，如果创建 MSDTC 资源，即使存储可用，它也不会显示任何可用的共享存储。 Windows Server 2019 中已修复此问题。
- 基本负载均衡器不处理 RPC 端口。


## <a name="next-steps"></a>后续步骤

确定适用于解决方案的最佳做法后，请通过使用[Azure 门户](availability-group-azure-portal-configure.md)、 [Azure CLI/PowerShell](./availability-group-az-commandline-configure.md)或[AZURE 快速入门模板](availability-group-quickstart-template-configure.md)[为 FCI 准备 SQL Server VM](failover-cluster-instance-prepare-vm.md)或创建可用性组来开始操作。