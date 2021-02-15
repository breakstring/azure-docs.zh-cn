---
title: SAP HANA Azure 虚拟机存储配置 | Microsoft Docs
description: 针对已部署 SAP HANA 的 VM 的存储建议。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP，Azure HANA，存储超磁盘，高级存储
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/03/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 48d1c04e59d316ac19000f5b890c0cb1c96fb213
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549433"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure 虚拟机存储配置

对于正在运行 SAP HANA 的 Azure VM，Azure 提供其他适用的存储类型。 下面列出了一些可用于 SAP HANA 部署的 SAP HANA 认证的 Azure 存储类型： 

- Azure 高级 SSD 或高级存储 
- [超级磁盘](../../disks-enable-ultra-ssd.md)
- [Azure NetApp 文件](https://azure.microsoft.com/services/netapp/) 

若要了解这些磁盘类型，请参阅 [Azure 存储类型 SAP 工作负荷](./planning-guide-storage.md) 一文和 [选择磁盘类型](../../disks-types.md)

Azure 为 Azure 标准和高级存储上的 Vhd 提供了两种部署方法。 我们希望你能够利用 azure [托管磁盘](https://azure.microsoft.com/services/managed-disks/) 进行 azure 块存储部署。 

有关存储类型的列表及其 IOPS 和存储吞吐量方面的 SLA，请查看[有关托管磁盘的 Azure 文档](https://azure.microsoft.com/pricing/details/managed-disks/)。

> [!IMPORTANT]
> 无论选择何种 Azure 存储类型，对于特定操作系统和 DBMS，都需要 SAP 支持该存储所使用的文件系统。 [SAP 支持说明 #2972496](https://launchpad.support.sap.com/#/notes/2972496) 列出了不同操作系统和数据库支持的文件系统，包括 SAP HANA。 这适用于 SAP HANA 可能访问的所有卷（以便为任何任务进行读取和写入）。 若为 SAP HANA 专门使用 Azure 上的 NFS，NFS 版本的其他限制将按本文后面的说明适用 


不同存储类型的最低 SAP HANA 认证条件为： 

- Azure 高级存储- **/hana/log** 需要受 azure [写入加速器](../../how-to-enable-write-accelerator.md)支持。 **/Hana/data** 卷可放置在不带 Azure 写入加速器的高级存储或超磁盘上
- Azure Ultra 磁盘至少适用于 **/hana/log** 卷。 **/Hana/data** 卷可放置在无 Azure 写入加速器的高级存储上，或者用于获取超磁盘的重新启动时间
- **NFS** v2.0 上 **/hana/log 和/Hana/data** 的 Azure NetApp 文件的卷。 /Hana/shared 的卷可以使用 NFS v3 或 NFS v2.0 协议

可以组合某些存储类型。 例如，可以将 **/hana/data** 放在高级存储上，而 **/hana/log** 可放置在超小型磁盘存储上，以便达到所需的低延迟。 如果将基于和的卷用于 **/hana/data**，则  **/hana/log** 卷也需要基于和顶层的 NFS。 **不支持** 在和上将 NFS 用于某个卷 (例如/hana/data) 、Azure 高级存储或超磁盘 (如 **/hana/log**) ）。

在本地环境中，很少需要考虑 I/O 子系统及其功能。 原因在于，设备供应商会确保满足 SAP HANA 的最低存储要求。 在自行构建 Azure 基础结构时，你应该注意其中的一些 SAP 颁发的要求。 SAP 所建议的一些最小吞吐量特征包括：

- 每秒 **/hana/log** 250 mb/秒，读取/写入大小为 1 mb 的 i/o 大小
- 对于 **/hana/data** ，至少 400 mb/秒的读取活动，适用于 16 mb 和 64 mb i/o 大小
- 对于具有 16 MB 和 64 MB i/o 大小的 **/hana/data** ，至少 250 mb/秒的写入活动

如果低存储延迟对于 DBMS 系统至关重要（即使对于 SAP HANA 等 DBMS 系统也是如此），请将数据保留在内存中。 存储中的关键路径通常围绕 DBMS 系统的事务日志写入。 同时，在故障恢复之后写入保存点或加载内存中数据的操作可能也很关键。 因此， **必须** 对 **/hana/data** 和 **/hana/log** 卷使用 Azure 高级存储、Ultra 磁盘或和。 


选择 HANA 存储配置时，一些指导原则可以按如下方式列出：

- 根据 [SAP 工作负荷的 Azure 存储类型](./planning-guide-storage.md) 决定存储类型，并 [选择磁盘类型](../../disks-types.md)
- 调整或确定 VM 时的总体 VM i/o 吞吐量和 IOPS 限制。 [内存优化虚拟机大小](../../sizes-memory.md)一文中记录了总体 VM 存储吞吐量
- 在确定存储配置时，请尝试将 VM 的总体吞吐量保持在 **/hana/data** 卷配置的下方。 写入保存点，SAP HANA 可以是主动的、发出的 i/o。 写入保存点时，可以轻松地将 **/hana/data** 卷的吞吐量限制增加到最大值。 如果) 生成 **/hana/data** 卷的 (磁盘的吞吐量比 VM 允许的吞吐量更高，则可能会出现以下情况：保存点写入利用的吞吐量与重做日志写入操作的吞吐量要求产生了干扰。 可能影响应用程序吞吐量的情况


> [!IMPORTANT]
> 存储配置的建议旨在作为开始的指导。 运行工作负荷和分析存储利用率模式，你可能会意识到未利用所有提供的存储带宽或 IOPS。 可以考虑在存储上缩小。 相反，工作负荷可能需要比这些配置所建议的更多的存储吞吐量。 因此，你可能需要部署更多的容量、IOPS 或吞吐量。 在所需的存储容量、需要的存储延迟、所需的存储吞吐量和 IOPS 之间，Azure 提供了足够多的不同存储类型，具有不同的功能和不同的价格，可以为你和 HANA 工作负荷查找并调整到适当的折衷。


## <a name="stripe-sets-versus-sap-hana-data-volume-partitioning"></a>条带集与 SAP HANA 数据卷分区
使用 Azure 高级存储时，可能会在将 **/hana/data** 和/或 **/hana/log** 卷条带化到多个 Azure 磁盘上时达到最佳性价比。 而不是部署更多的磁盘卷，以提供所需的 IOPS 或吞吐量。 到目前为止，此操作是通过 LVM 和 MDADM 卷管理器实现的，这是 Linux 中的一部分。 条带化磁盘的方法是数十年以前，众所周知的。 由于这些条带化卷要达到所需的 IOPS 或吞吐量功能，因此增加了管理这些带区卷的复杂性。 尤其是在卷需要扩展容量的情况下。 至少对于 **/hana/data**，SAP 引入了一种可实现与多个 Azure 磁盘上的条带化相同的目标的替代方法。 由于 SAP HANA 2.0 SPS03，因此 HANA indexserver 可以将其 i/o 活动条带化到位于不同 Azure 磁盘上的多个 HANA 数据文件中。 优点在于，无需负责创建和管理不同 Azure 磁盘上的带区卷。 中详细说明了数据卷分区的 SAP HANA 功能：

- [HANA 管理员指南](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/40b2b2a880ec4df7bac16eae3daef756.html?q=hana%20data%20volume%20partitioning)
- [有关 SAP HANA 的博客–分区数据卷](https://blogs.sap.com/2020/10/07/sap-hana-partitioning-data-volumes/)
- [SAP 说明 #2400005](https://launchpad.support.sap.com/#/notes/2400005)
- [SAP 说明 #2700123](https://launchpad.support.sap.com/#/notes/2700123)

通读详细信息后，利用此功能会使基于卷管理器的带区集变得更加复杂。 你还会认识到，HANA 数据卷分区不仅适用于 Azure 数据块存储，如 Azure 高级存储。 如果这些共享的 IOPS 或吞吐量有限制，则可以使用此功能在 NFS 共享间划分。  


## <a name="linux-io-scheduler-mode"></a>Linux I/O 计划程序模式
Linux 提供多种不同的 I/O 计划模式。 通过 Linux 供应商和 SAP 的常见建议是，将磁盘卷的 i/o 调度器模式从 **mq-截止时间** 或 **kyber** 模式重新配置为 **noop** (非 multiqueue) ，如果 SLES saptune 配置文件尚未执行此操作，则为 ")  (**无** "。 有关详细信息，请参阅： 

- [SAP 说明 #1984787](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP 说明 #2578899](https://launchpad.support.sap.com/#/notes/2578899) 
- [SLES 12 SP4 中的 noop 设置问题](https://www.suse.com/support/kb/doc/?id=000019547)

在 Red Hat 上，将设置保留为不同 SAP 应用程序的特定微调配置文件建立的设置。


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>适用于 Azure M 系列虚拟机的高级存储和 Azure 写入加速器的解决方案
Azure 写入加速器是可用于 Azure M 系列 VM 的一项功能。 作为名称状态，该功能的目的是改善针对 Azure 高级存储的写入操作的 i/o 延迟。 在 SAP HANA 中，预期只能对 **/hana/log** 卷使用写入加速器。 因此，/hana/data 和 /hana/log 是单独的卷，其中 Azure 写入加速器仅支持 /hana/log 卷  。 

> [!IMPORTANT]
> 使用 Azure 高级存储时，对 **/hana/log** 卷使用 azure [写入加速器](../../how-to-enable-write-accelerator.md)是必需的。 写入加速器仅适用于高级存储、M 系列和 Mv2-Series Vm。 写入加速器与其他 Azure VM 系列（如 Esv3 或 Edsv4）不结合工作。

下面列出了适用于 Azure 高级磁盘的缓存建议，这些建议如下所示 SAP HANA：

- 几乎没有任何针对 HANA 数据文件的读取工作负荷。 重启 HANA 实例后或者在将数据载入 HANA 时出现的大型 I/O 例外。 执行 HANA 数据库备份后，也可能出现大量针对数据文件的读取 I/O。 因此，大多数情况下读取缓存没有意义，因为在大多数情况下，需要完全读取所有数据文件卷。 
- HANA 写入点和 HANA 故障恢复会产生大量针对数据文件的写入。 写入保存点是异步进行的，不阻止任何用户事务。 在故障恢复期间写入数据对性能要严格要求，以便系统再次快速响应。 但是，故障恢复更确切地说应该是异常情况
- 几乎不会从 HANA 重做文件进行任何读取。 执行事务日志备份、故障恢复或在 HANA 实例重启阶段出现的大型 I/O 例外。  
- 针对 SAP HANA 重做日志文件的主要负载是写入负载。 根据工作负载的性质，I/O 可以小至 4 KB，也可达到 1 MB 或更大。 针对 SAP HANA 重做文件的写入延迟对性能要很高要求。
- 需要以一种可靠的方式在磁盘上保留所有写入

**建议：通过 SAP HANA 观察到的 i/o 模式，使用 Azure 高级存储的不同卷的缓存应该设置如下：**

- **/hana/data** -无缓存或读缓存
- **/hana/log** -对于应启用 Azure 写入加速器的 M 和 Mv2-Series vm，没有缓存例外 
- **/hana/shared** - 读取缓存
- **OS 磁盘** -不要更改 Azure 在创建 VM 时设置的默认缓存


如果使用 LVM 或 mdadm 跨多个 Azure 高级磁盘构建条带集，则需要定义条带大小。 这些大小在 **/hana/data** 和 **/hana/log** 之间有所不同。 **建议：作为条带大小，建议使用：**

- 256 KB 的 /hana/data
- **/hana/log** 64 KB

> [!NOTE]
> **/Hana/data** 的条带大小已从较早的建议中更改为 64 kb 或 128 kb 256，并基于具有更多最新 Linux 版本的客户体验。 256 KB 的大小是提供略微改善的性能。 我们还将 **/hana/log** 的条带大小建议从 32 kb 改为 64 kb，以获得具有更大 i/o 大小的足够吞吐量。

> [!NOTE]
> 不需要使用 RAID 卷配置任何冗余级别，因为 Azure 块存储会保留 VHD 的三个映像。 使用带 Azure 高级磁盘的带区集纯粹用于配置提供足够的 IOPS 和/或 i/o 吞吐量的卷。

在条带集下堆积大量 Azure Vhd，可从 IOPS 和存储吞吐量端累积总计。 因此，如果你将条带集放在超过 3 x P30 的 Azure 高级存储磁盘上，则它应为你分配三倍的 IOPS 和单个 Azure 高级存储 P30 磁盘的存储吞吐量三倍。

> [!IMPORTANT]
> 如果使用 LVM 或 mdadm 作为卷管理器跨多个 Azure 高级磁盘创建条带集，则不能将这三个 SAP HANA 文件系统/data sources、/log 和/shared 置于默认或根卷组。 强烈建议遵循 Linux 供应商指南，该指南通常用于为 /data、/log 和 /shared 创建各自的卷组。


### <a name="azure-burst-functionality-for-premium-storage"></a>适用于高级存储的 Azure 猝发功能
对于容量较小或等于 512 GiB 的 Azure 高级存储磁盘，提供了突发功能。 磁盘突发如何工作的确切方式在 " [磁盘突发](../../disk-bursting.md)" 一文中进行了介绍。 当您阅读本文时，您将了解在以下情况下，当 i/o 工作负荷低于磁盘的名义 IOPS 和吞吐量时，所产生的的 IOPS 和吞吐量的概念 (有关名义吞吐量的详细信息，请参阅 [托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)) 。 你将在当前使用情况与磁盘的名义值之间累计 IOPS 和吞吐量的增量。 猝发次数限制为最多30分钟。

可以在其中规划此突发功能的理想情况可能是包含不同 DBMS 的数据文件的卷或磁盘。 需要对这些卷执行 i/o 工作负荷，尤其是对于小到中等范围的系统，应如下所示：

- 从低到中等的读取工作负荷，因为数据理想的缓存在内存中，
- 数据库检查点或定期发出的保存点触发的突发写入数
- 在不通过存储快照执行备份的情况下，在连续流中读取的备份工作负荷
- 对于 SAP HANA，在实例重新启动后将数据加载到内存中

尤其是在工作负荷仅每秒处理几百个事务的小型 DBMS 系统上，此类突发功能对于存储事务或重做日志的磁盘或卷也很有用。 针对此类磁盘的预期工作负载如下所示：

- 由于应用程序发出的每个提交都可能触发 i/o 操作，因此，对依赖于工作负荷的磁盘的常规写入以及工作负荷的性质
- 在操作任务的情况下提高吞吐量的工作负荷，如创建或重新生成索引
- 执行事务日志或重做日志备份时，请阅读猝发


### <a name="production-recommended-storage-solution-based-on-azure-premium-storage"></a>基于 Azure 高级存储的生产推荐的存储解决方案

> [!IMPORTANT]
> Azure M 系列虚拟机的 SAP HANA 认证要求中规定，Azure 写入加速器只能用于 **/hana/log** 卷。 因此，在 Azure M 系列虚拟机上的生产场景 SAP HANA 部署中，应该配置 **/hana/log** 卷使用的 Azure 写入加速器。  

> [!NOTE]
> 在涉及 Azure 高级存储的方案中，我们将在配置中实现突发功能。 使用任意形状或窗体的存储测试工具时，请记住 [Azure 高级磁盘突发的工作](../../disk-bursting.md) 方式。 运行通过 SAP HWCCT 或 HCMT 工具传递的存储测试时，我们不希望所有测试都将通过条件，因为某些测试会超出你可以累积的突发富余额度。 尤其是在所有测试都按顺序运行时，无需中断。


> [!NOTE]
> 对于生产场景，请在 [IAAS 的 SAP 文档](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中查看特定的 VM 类型是否受 SAP HANA 的支持。

**建议：用于生产方案的 Azure 高级存储的推荐配置如下所示：**

SAP **/hana/data** 卷的配置：

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/data | 预配的吞吐量 | 最大突发吞吐量 | IOPS | 突发 IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MBps | 4 x P6 | 200 MBps | 680 MBps | 960 | 14,000 |
| M32ls | 256 GiB | 500 MBps | 4 x P6 | 200 MBps | 680 MBps | 960 | 14,000 |
| M64ls | 512 GiB | 1,000 MBps | 4 x P10 | 400 MBps | 680 MBps | 2,000 | 14,000 |
| M64s | 1,000 GiB | 1,000 MBps | 4 x P15 | 500 MBps | 680 MBps | 4400 | 14,000 |
| M64ms | 1,750 GiB | 1,000 MBps | 4 x P20 | 600 MBps | 680 MBps | 9200 | 14,000 |  
| M128s | 2,000 GiB | 2000 MBps | 4 x P20 | 600 MBps | 680 MBps | 9200| 14,000 | 
| M128ms | 3,800 GiB | 2000 MBps | 4 x P30 | 800 MBps | 无突发 | 20,000 | 无突发 | 
| M208s_v2 | 2,850 GiB | 1,000 MBps | 4 x P30 | 800 MBps | 无突发 | 20,000| 无突发 | 
| M208ms_v2 | 5,700 GiB | 1,000 MBps | 4 x P40 | 1,000 MBps | 无突发 | 30,000 | 无突发 |
| M416s_v2 | 5,700 GiB | 2000 MBps | 4 x P40 | 1,000 MBps | 无突发 | 30,000 | 无突发 |
| M416ms_v2 | 11,400 GiB | 2000 MBps | 4 x P50 | 2000 MBps | 无突发 | 30,000 | 无突发 |


对于 **/hana/log** 卷。 此配置如下所示：

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | **/hana/log** 卷 | 预配的吞吐量 | 最大突发吞吐量 | IOPS | 突发 IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192 GiB | 500 MBps | 3 x P10 | 300 MBps | 510 MBps | 1,500 | 10500 | 
| M32ls | 256 GiB | 500 MBps | 3 x P10 | 300 MBps | 510 MBps | 1,500 | 10500 | 
| M64ls | 512 GiB | 1,000 MBps | 3 x P10 | 300 MBps | 510 MBps | 1,500 | 10500 | 
| M64s | 1,000 GiB | 1,000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3300 | 10500 | 
| M64ms | 1,750 GiB | 1,000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3300 | 10500 |  
| M128s | 2,000 GiB | 2000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3300 | 10500|  
| M128ms | 3,800 GiB | 2000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3300 | 10500 | 
| M208s_v2 | 2,850 GiB | 1,000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3300 | 10500 |  
| M208ms_v2 | 5,700 GiB | 1,000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3300 | 10500 |  
| M416s_v2 | 5,700 GiB | 2000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3300 | 10500 |  
| M416ms_v2 | 11,400 GiB | 2000 MBps | 3 x P15 | 375 MBps | 510 MBps | 3300 | 10500 | 


对于其他卷，配置如下所示：

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/shared | /root 卷 | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MBps | 1 x P15 | 1 x P6 | 1 x P6 |
| M32ls | 256 GiB | 500 MBps |  1 x P15 | 1 x P6 | 1 x P6 |
| M64ls | 512 GiB | 1000 MBps | 1 x P20 | 1 x P6 | 1 x P6 |
| M64s | 1,000 GiB | 1,000 MBps | 1 x P30 | 1 x P6 | 1 x P6 |
| M64ms | 1,750 GiB | 1,000 MBps | 1 x P30 | 1 x P6 | 1 x P6 | 
| M128s | 2,000 GiB | 2000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M128ms | 3,800 GiB | 2000 MBps | 1 x P30 | 1 x P10 | 1 x P6 |
| M208s_v2 | 2,850 GiB | 1,000 MBps |  1 x P30 | 1 x P10 | 1 x P6 |
| M208ms_v2 | 5,700 GiB | 1,000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M416s_v2 | 5,700 GiB | 2000 MBps |  1 x P30 | 1 x P10 | 1 x P6 | 
| M416ms_v2 | 11,400 GiB | 2000 MBps | 1 x P30 | 1 x P10 | 1 x P6 | 


检查建议的不同卷的存储吞吐量是否满足所要运行的工作负载。 如果工作负荷需要更高的 **/hana/data** 和 **/hana/log** 卷，则需要增加 Azure 高级存储 vhd 的数量。 对于包含的 VHD 数目比所列数目更多的卷，调整其大小会在 Azure 虚拟机类型的限制范围内增大 IOPS 和 I/O 吞吐量。

只能配合 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)使用 Azure 写入加速器。 至少需要将构成 **/hana/log** 卷的 Azure 高级存储磁盘部署为托管磁盘。 可在 [写入加速器](../../how-to-enable-write-accelerator.md)一文中找到更详细的 Azure 写入加速器说明和限制。

对于 Azure [Esv3](../../ev3-esv3-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#esv3-series) 系列和 [Edsv4](../../edv4-edsv4-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#edsv4-series)的 HANA 认证的 vm，需要对 **/HANA/DATA** 和 **/hana/log** 卷进行和。 或者，需要对 **/hana/log** 卷使用 azure Ultra 磁盘存储而不是 azure 高级存储。 因此，Azure 高级存储上的 **/hana/data** 卷的配置可能如下所示：

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/data | 预配的吞吐量 | 最大突发吞吐量 | IOPS | 突发 IOPS |
| --- | --- | --- | --- | --- | --- | --- |
| E20ds_v4 | 160 GiB | 480 MBps | 3 x P10 | 300 MBps | 510 MBps | 1,500 | 10500 |
| E32ds_v4 | 256 GiB | 768 MBps | 3 x P10 |  300 MBps | 510 MBps | 1,500 | 10500|
| E48ds_v4 | 384 GiB | 1152 MBps | 3 x P15 |  375 MBps |510 MBps | 3300  | 10500 | 
| E64ds_v4 | 504 GiB | 1,200 MBps | 3 x P15 |  375 MBps | 510 MBps | 3300 | 10500 | 
| E64s_v3 | 432 GiB | 1,200 MB/秒 | 3 x P15 |  375 MBps | 510 MBps | 3300 | 10500 | 

对于其他卷（包括超磁盘上的 **/hana/log** ），配置可能如下所示：

| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/log 卷 | /hana/log I/O 吞吐量 | /hana/log IOPS | /hana/shared | /root 卷 | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MBps | 80 GB | 250 MBps | 1,800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E32ds_v4 | 256 GiB | 768 MBps | 128 GB | 250 MBps | 1,800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E48ds_v4 | 384 GiB | 1152 MBps | 192 GB | 250 MBps | 1,800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64ds_v4 | 504 GiB | 1,200 MBps | 256 GB | 250 MBps | 1,800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64s_v3 | 432 GiB | 1,200 MBps | 220 GB | 250 MBps | 1,800 | 1 x P20 | 1 x P6 | 1 x P6 |


## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>适用于 SAP HANA 的 Azure 超级磁盘存储配置
其他 Azure 存储类型称为 [Azure Ultra 磁盘](../../disks-types.md#ultra-disk)。 目前提供的 Azure 存储与超级磁盘之间的显著区别在于，磁盘功能不再受到磁盘大小的约束。 作为客户，你可以为超级磁盘定义以下功能：

- 磁盘大小范围（从 4 GiB 到 65,536 GiB）
- IOPS 范围（从 100 IOPS 到 160K IOPS，最大值也取决于 VM 类型）
- 存储吞吐量（从 300 MB/秒到 2,000 MB/秒）

超级磁盘使你能够定义一个磁盘来满足大小、IOPS 和磁盘吞吐量范围。 不要使用逻辑卷管理器（例如，在 Azure 高级存储上使用 LVM 或 MDADM）来构建满足 IOPS 和存储吞吐量要求的卷。 可以在超磁盘与高级存储之间运行配置混合。 因此，你可以将超磁盘的使用限制为性能关键的 **/hana/data** 和 **/hana/log** 卷，并覆盖包含 Azure 高级存储的其他卷

与高级存储相比，Ultra 磁盘的其他优点可能是更好的读取延迟。 如果希望减少 HANA 启动时间以及向内存中后续加载数据，速度较快的读取延迟可能会有优势。 如果 HANA 在编写保存点，超级磁盘存储的优势也可以体现出来。 

> [!NOTE]
> 超级磁盘并非在所有 Azure 区域都提供，并且还尚不完全支持下面列出的 VM 类型。 有关超级磁盘的可用区域以及受支持的 VM 系列的详细信息，请参阅文章 [Azure 中提供哪些可用磁盘类型？](../../disks-types.md#ultra-disk)。

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>生产推荐的使用纯超级磁盘配置的存储解决方案
在此配置中，你需要单独保留 **/hana/data** 和 **/hana/log** 卷。 建议的值派生自 KPI，即 SAP 必须根据 [SAP TDI 存储白皮书](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)中建议的对 SAP HANA 和存储配置的 VM 类型进行认证。

这些建议通常超出了本文前面所述的 SAP 最低要求。 列出的建议是 SAP 提供的大小建议与不同 VM 类型提供的最大存储吞吐量之间的折衷方案。

> [!NOTE]
> Azure Ultra 磁盘在每 Gb 磁盘上强制实施最少 2 IOPS


| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/data 卷 | /hana/data I/O 吞吐量 | /hana/data IOPS | /hana/log 卷 | /hana/log I/O 吞吐量 | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160 GiB | 480 MB/秒 | 200 GB | 400 MBps | 2,500 | 80 GB | 250 MB | 1,800 |
| E32ds_v4 | 256 GiB | 768 MB/秒 | 300 GB | 400 MBps | 2,500 | 128 GB | 250 MBps | 1,800 |
| E48ds_v4 | 384 GiB | 1152 MB/秒 | 460 GB | 400 MBps | 3,000 | 192 GB | 250 MBps | 1,800 |
| E64ds_v4 | 504 GiB | 1200 MB/秒 | 610 GB | 400 MBps | 3,500 |  256 GB | 250 MBps | 1,800 |
| E64s_v3 | 432 GiB | 1,200 MB/秒 | 610 GB | 400 MBps | 3,500 | 220 GB | 250 MB | 1,800 |
| M32ts | 192 GiB | 500 MB/秒 | 250 GB | 400 MBps | 2,500 | 96 GB | 250 MBps  | 1,800 |
| M32ls | 256 GiB | 500 MB/秒 | 300 GB | 400 MBps | 2,500 | 256 GB | 250 MBps  | 1,800 |
| M64ls | 512 GiB | 1,000 MB/秒 | 620 GB | 400 MBps | 3,500 | 256 GB | 250 MBps  | 1,800 |
| M64s | 1,000 GiB | 1,000 MB/秒 |  1,200 GB | 600 MBps | 5,000 | 512 GB | 250 MBps  | 2,500 |
| M64ms | 1,750 GiB | 1,000 MB/秒 | 2,100 GB | 600 MBps | 5,000 | 512 GB | 250 MBps  | 2,500 |
| M128s | 2,000 GiB | 2,000 MB/秒 |2,400 GB | 750 MBps | 7,000 | 512 GB | 250 MBps  | 2,500 | 
| M128ms | 3,800 GiB | 2,000 MB/秒 | 4,800 GB | 750 MBps |9,600 | 512 GB | 250 MBps  | 2,500 | 
| M208s_v2 | 2,850 GiB | 1,000 MB/秒 | 3,500 GB | 750 MBps | 7,000 | 512 GB | 250 MBps  | 2,500 | 
| M208ms_v2 | 5,700 GiB | 1,000 MB/秒 | 7,200 GB | 750 MBps | 14400 | 512 GB | 250 MBps  | 2,500 | 
| M416s_v2 | 5,700 GiB | 2,000 MB/秒 | 7,200 GB | 1,000 MBps | 14400 | 512 GB | 400 MBps  | 4,000 | 
| M416ms_v2 | 11,400 GiB | 2,000 MB/秒 | 14,400 GB | 1,500 MBps | 28,800 | 512 GB | 400 MBps  | 4,000 |   

**列出的值旨在作为起点，并需要根据实际需求进行评估。** Azure 超级磁盘的优点是可以调整 IOPS 和吞吐量的值，而无需关闭 VM 或停止应用于系统的工作负载。   

> [!NOTE]
> 到目前为止，超级磁盘存储的存储快照不可用。 这会阻止 Azure 备份服务对 VM 快照的使用


## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Azure NetApp 文件上的 NFS v4.1 卷
有关适用于 HANA 的和的详细信息，请阅读[用于 SAP HANA 的 Azure NetApp 文件上](./hana-vm-operations-netapp.md)的文档 NFS v2.0 卷




## <a name="cost-conscious-solution-with-azure-premium-storage"></a>Azure 高级存储的成本意识解决方案
到目前为止，本文档中的 Azure 高级存储解决方案在 [Azure M 系列虚拟机的高级存储和 azure 写入加速器的解决方案](#solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines) 中进行了介绍，用于 SAP HANA 生产支持的方案。 生产可支持性配置的特征之一是将 SAP HANA 数据和重做日志的卷分离成两个不同的卷。 此类隔离的原因是卷上的工作负荷特征不同。 而且，对于推荐的生产配置，可能需要不同类型的缓存或甚至不同类型的 Azure 块存储。 对于非生产方案，为生产系统所做的一些注意事项可能不适用于更低端的非生产系统。 因此，可以合并 HANA 数据和日志卷。 尽管最终有一些原因，但最终不满足生产系统所需的某些吞吐量或延迟 Kpi。 降低此类环境成本的另一个方面就是使用 [Azure 标准 SSD 存储](./planning-guide-storage.md#azure-standard-ssd-storage)。 请记住，选择标准 SSD 或标准 HDD Azure 存储会影响你的单个 VM Sla，如  [虚拟机的 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines)一文中所述。

对于此类配置，更便宜的替代方法如下所示：


| VM SKU | RAM | 最大 VM I/O<br /> 吞吐量 | /hana/data and /hana/log<br /> 使用 LVM 或 MDADM 进行条带化 | /hana/shared | /root 卷 | /usr/sap | comments |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/秒 | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | 不会达到1ms 存储延迟<sup>1</sup> |
| E16v3 | 128 GiB | 384 MB/秒 | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | VM 类型未通过 HANA 认证 <br /> 不会达到1ms 存储延迟<sup>1</sup> |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 5000<sup>2</sup> |
| E20ds_v4 | 160 GiB | 480 MB/秒 | 4 x P6 | 1 x E15 | 1 x E6 | 1 x E6 | 不会达到1ms 存储延迟<sup>1</sup> |
| E32v3 | 256 GiB | 768 MB/秒 | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | VM 类型未通过 HANA 认证 <br /> 不会达到1ms 存储延迟<sup>1</sup> |
| E32ds_v4 | 256 GiB | 768 MBps | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | 不会达到1ms 存储延迟<sup>1</sup> |
| M32ls | 256 GiB | 500 MB/秒 | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 5000<sup>2</sup> |
| E48ds_v4 | 384 GiB | 1152 MBps | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 不会达到1ms 存储延迟<sup>1</sup> |
| E64v3 | 432 GiB | 1,200 MB/秒 | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 不会达到1ms 存储延迟<sup>1</sup> |
| E64ds_v4 | 504 GiB | 1200 MB/秒 |  7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 不会达到1ms 存储延迟<sup>1</sup> |
| M64ls | 512 GiB | 1,000 MB/秒 | 7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 10000<sup>2</sup> |
| M64s | 1,000 GiB | 1,000 MB/秒 | 7 x P15 | 1 x E30 | 1 x E6 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 10000<sup>2</sup> |
| M64ms | 1,750 GiB | 1,000 MB/秒 | 6 x P20 | 1 x E30 | 1 x E6 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 10000<sup>2</sup> |
| M128s | 2,000 GiB | 2,000 MB/秒 |6 x P20 | 1 x E30 | 1 x E10 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 20000<sup>2</sup> |
| M208s_v2 | 2,850 GiB | 1,000 MB/秒 | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 10000<sup>2</sup> |
| M128ms | 3,800 GiB | 2,000 MB/秒 | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 20000<sup>2</sup> |
| M208ms_v2 | 5,700 GiB | 1,000 MB/秒 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 10000<sup>2</sup> |
| M416s_v2 | 5,700 GiB | 2,000 MB/秒 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 20000<sup>2</sup> |
| M416ms_v2 | 11400 GiB | 2,000 MB/秒 | 7 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | 对合并数据和日志卷使用写入加速器会将 IOPS 速率限制为 20000<sup>2</sup> |


<sup>1</sup> [Azure 写入加速器](../../how-to-enable-write-accelerator.md) 不能与 Ev4 和 Ev4 VM 系列一起使用。 由于使用 Azure 高级存储，i/o 延迟不会小于1ms

<sup>2</sup> VM 家族支持 [Azure 写入加速器](../../how-to-enable-write-accelerator.md)，但写入加速器的 IOPS 限制可能会限制磁盘配置 iops 功能

在将数据和日志卷与 SAP HANA 进行组合的情况下，生成条带化卷的磁盘不应启用读缓存或读/写缓存。

列出了未通过 SAP 认证的 VM 类型，因此未在所谓的 " [SAP HANA 硬件目录](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)" 中列出。 客户的反馈是，某些非生产任务已成功使用了这些非列出的 VM 类型。


## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅：

- [Azure 虚拟机的 SAP HANA 高可用性指南](./sap-hana-availability-overview.md)。