---
title: 将 SQL Server 数据库迁移到虚拟机上的 SQL Server | Microsoft Docs
description: 了解如何将本地用户数据库迁移到 Azure 虚拟机中的 SQL Server。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.subservice: migration
ms.topic: how-to
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f6e9009040d2d02702f8a71c352716491d07d1f7
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704298"
---
# <a name="migrate-a-sql-server-database-to-sql-server-on-an-azure-virtual-machine"></a>将 SQL Server 数据库迁移到 Azure 虚拟机中的 SQL Server

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

将本地 SQL Server 用户数据库迁移到 Azure 虚拟机 (VM) 中的 SQL Server 的方法有很多。 本文简要讨论各种方法，并针对各种场景建议最佳方法。


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > 对于本地实例，SQL Server 2008 和 SQL Server 2008 R2 的[支持生命周期即将结束](https://www.microsoft.com/sql-server/sql-server-2008)。 若要延长支持，可以将 SQL Server 实例迁移到 Azure VM，也可以购买扩展安全更新以将其保留在本地。 有关详细信息，请参阅[使用 Azure 延长对 SQL Server 2008 和 2008 R2 的支持](sql-server-2008-extend-end-of-support.md)

## <a name="what-are-the-primary-migration-methods"></a>主要迁移方法有哪些？

主要迁移方法包括：

* 使用压缩功能执行本地备份并将备份文件手动复制到 Azure VM 中。
* 执行“备份到 URL”并从该 URL 还原到 Azure VM。
* 拆离数据和日志文件后，将其复制到 Azure blob 存储，然后从 URL 附加到 Azure VM 中的 SQL Server。
* 将本地物理计算机转换为 Hyper-V VHD，上传到 Azure Blob 存储，然后使用上传的 VHD 部署为新 VM。
* 使用 Windows 导入/导出服务运送硬盘驱动器。
* 如果在本地有 AlwaysOn 可用性组部署，请使用[添加 Azure 副本向导](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability)在 Azure 中创建副本，然后进行故障转移，并将用户指向 Azure 数据库实例。
* 使用 SQL Server [事务复制](/sql/relational-databases/replication/transactional/transactional-replication)将 Azure SQL Server 实例配置为订阅服务器，然后禁用复制，并将用户指向 Azure 数据库实例。

> [!TIP]
> 也可使用这些相同的技术在 Azure 的 SQL Server VM 之间移动数据库。 例如，无法使用支持的方法将 SQL Server 库映像 VM 从一个版本升级到另一个版本。 在这种情况下，应使用新的版本创建新的 SQL Server VM，并使用本文中的一项迁移技术移动数据库。 

## <a name="choose-a-migration-method"></a>选择迁移方法

要获得最出色的数据传输性能，请使用压缩后的备份文件将数据库文件迁移到 Azure VM。

若要在数据库迁移过程中最大限度地减少停机时间，请使用 AlwaysOn 选项或事务复制选项。

如果不能使用上述方法，请手动迁移数据库。 通常先进行数据库备份，接下来将数据库备份复制到 Azure，然后还原数据库。 用户还可以将数据库文件本身复制到 Azure，并附加这些文件。 可以通过多种方法完成将数据库迁移到 Azure VM 的这一手动流程。

> [!NOTE]
> 从较旧版本的 SQL Server 升级到 SQL Server 2014 或 SQL Server 2016 时，应考虑是否需要做一些更改。 建议在迁移项目时处理好不受新版 SQL Server 支持的功能上的所有依赖项。 有关受支持的版本和方案的详细信息，请参阅[升级到 SQL Server](/sql/database-engine/install-windows/upgrade-sql-server)。

下表列出了各种主要迁移方法，并讨论了最适合使用该方法的场合。

| 方法 | 源数据库版本 | 目标数据库版本 | 源数据库备份大小限制 | 注释 |
| --- | --- | --- | --- | --- |
| [使用压缩功能执行本地备份并将备份文件手动复制到 Azure 虚拟机](#back-up-and-restore) |SQL Server 2005 或更高版本 |SQL Server 2005 或更高版本 |[Azure VM 存储限制](../../../index.yml) | 这是一项很简单且经过严格测试的技术，适用于跨计算机移动数据库。 |
| [执行“备份到 URL”并从该 URL 还原到 Azure 虚拟机](#backup-to-url-and-restore-from-url) |SQL Server 2012 SP1 CU2 或更高版本 | SQL Server 2012 SP1 CU2 或更高版本 | 小于 12.8 TB 用于 SQL Server 2016，否则大于 1 TB | 此方法是通过另一种方式使用 Azure 存储将备份文件移至 VM。 |
| [分离后，将数据和日志文件复制到 Azure Blob 存储，然后从 URL 附加到 Azure 虚拟机中的 SQL Server](#detach-and-attach-from-a-url) | SQL Server 2005 或更高版本 |SQL Server 2014 或更高版本 | [Azure VM 存储限制](../../../index.yml) | 如果计划[使用 Azure Blob 存储服务存储这些文件](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure)并将它们附加到 Azure VM 中运行的 SQL Server，尤其是对于非常大的数据库，可以使用此方法。 |
| [将本地计算机转换为 Hyper-V VHD，上传到 Azure Blob 存储，并使用上传的 VHD 部署一个新虚拟机](#convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm) |SQL Server 2005 或更高版本 |SQL Server 2005 或更高版本 |[Azure VM 存储限制](../../../index.yml) |在以下场合使用：[使用自己的 SQL Server 许可证时](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md)；迁移的数据库会在较旧版本的 SQL Server 上运行时；或者将系统数据库和用户数据库一起作为依赖于其他用户数据库和/或系统数据库的数据库的一部分进行迁移时。 |
| [使用 Windows 导入/导出服务运送硬盘驱动器](#ship-a-hard-drive) |SQL Server 2005 或更高版本 |SQL Server 2005 或更高版本 |[Azure VM 存储限制](../../../index.yml) |当手动复制方法速度太慢时使用 [Windows 导入/导出服务](../../../import-export/storage-import-export-service.md)，比如复制非常大的数据库 |
| [使用“添加 Azure 副本”向导](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability) |SQL Server 2012 或更高版本 |SQL Server 2012 或更高版本 |[Azure VM 存储限制](../../../index.yml) |最大程度减少故障时间，在具有 AlwaysOn 本地部署时使用 |
| [使用 SQL Server 事务复制](/sql/relational-databases/replication/transactional/transactional-replication) |SQL Server 2005 或更高版本 |SQL Server 2005 或更高版本 |[Azure VM 存储限制](../../../index.yml) |在需要尽量减少故障时间且不具备 Always On 本地部署时使用 |

## <a name="back-up-and-restore"></a>备份和还原

通过压缩方式备份数据库，将备份复制到 VM，并还原数据库。 如果备份文件大于 1 TB，则必须创建条带集，因为 VM 磁盘的最大大小是 1 TB。 使用此手动方法按照下列常规步骤迁移用户数据库：

1. 执行到本地位置的完整数据库备份。
2. 创建或上传具有所需 SQL Server 版本的虚拟机。
3. 根据要求设置连接。 请参阅[连接到 Azure 上的 SQL Server 虚拟机 (Resource Manager)](ways-to-connect-to-sql.md)。
4. 使用远程桌面、Windows 资源管理器或命令提示符处的复制命令将备份文件复制到 VM。

## <a name="backup-to-url-and-restore-from-url"></a>备份到 URL 并从 URL 还原

可以使用[备份到 URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) 的方式，并从 URL 还原到 VM，而不必备份到本地文件。 SQL Server 2016 支持条带化备份集。 建议将它们用于性能，并在超过每个 blob 的大小限制时是必需的。 对于非常大的数据库，建议使用 [Windows 导入/导出服务](../../../import-export/storage-import-export-service.md)。

## <a name="detach-and-attach-from-a-url"></a>拆离和从 URL 附加

拆离数据库和日志文件，并将其传输到 [Azure Blob 存储](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure)。 然后在 Azure VM 上从 URL 附加数据库。 如果希望物理数据库文件驻留在 Blob 存储中，可使用此方法，这可能对非常大的数据库很有用。 使用此手动方法按照下列常规步骤迁移用户数据库：

1. 从本地数据库实例拆离数据库文件。
2. 使用 [AZCopy 命令行实用工具](../../../storage/common/storage-use-azcopy-v10.md)将分离的数据库文件复制到 Azure Blob 存储。
3. 从 Azure URL 将数据库文件附加到 Azure VM 中的 SQL Server 实例。

## <a name="convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm"></a>转换为 VM、上传到 URL 并部署为新的 VM

使用此方法可将本地 SQL Server 实例中的所有系统数据库和用户数据库迁移到 Azure 虚拟机。 请使用此手动方法并按照下列常规步骤迁移整个 SQL Server 实例：

1. 将物理机或虚拟机转换为 Hyper-V VHD。
2. 使用 [Add-AzureVHD cmdlet](/previous-versions/azure/dn495173(v=azure.100)) 将 VHD 文件上传到 Azure 存储。
3. 使用上传的 VHD 部署新的虚拟机。

> [!NOTE]
> 若要迁移整个应用程序，请考虑使用 [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)。

## <a name="ship-a-hard-drive"></a>运送硬盘驱动器

在通过网络上传成本过高或不可行时，可以使用 [Windows 导入/导出服务方法](../../../import-export/storage-import-export-service.md)将大量文件数据传输到 Azure Blob 存储中。 借助此服务，可以将包含这些数据的一个或多个硬盘驱动器运送到 Azure 数据中心，在那里，数据将上传到存储帐户中。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Azure 虚拟机上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)。

> [!TIP]
> 如果对 SQL Server 虚拟机有任何疑问，请参阅[常见问题解答](frequently-asked-questions-faq.md)。

有关从捕获的映像在 Azure 虚拟机上创建 SQL Server 虚拟机的说明，请参阅 CSS SQL Server 工程师博客上的 [Tips & Tricks on 'cloning' Azure SQL virtual machines from captured images](/archive/blogs/psssql/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images)（有关从捕获的映像“克隆”Azure SQL 虚拟机的提示和技巧）。