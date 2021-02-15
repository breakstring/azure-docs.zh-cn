---
title: 通过 Azure Data Box 将数据迁移到 Azure 文件同步
description: 脱机迁移与 Azure 文件同步兼容的大容量数据。避免文件冲突，并在启用同步后保留文件和文件夹的 Acl 和时间戳。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 02/12/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 94abb33d39765a19306a013576d43fb2602d1c37
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017621"
---
# <a name="migrate-bulk-data-to-azure-file-sync-with-azure-databox"></a>使用 Azure DataBox 将数据批量迁移到 Azure 文件同步
可以通过两种方式将大容量数据迁移到 Azure 文件同步：

* **使用 Azure 文件同步上传文件。** 这是最简单的方法。 将文件本地移动到 Windows Server 2012 R2 或更高版本，然后安装 Azure 文件同步代理。 设置同步后，文件将从服务器上传。  (我们的客户当前在每两天的时间内获得1个 TiB 的平均上传速度。 ) 若要确保你的服务器不会对你的数据中心使用太多的带宽，你可能需要设置一个 [带宽限制计划](storage-sync-files-server-registration.md#ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter)。
* **脱机传输文件。** 如果没有足够的带宽，可能无法在合理的时间内将文件上传到 Azure。 质询是整个文件集的初始同步。 若要解决此问题，请使用如 [Azure Data Box 系列](https://azure.microsoft.com/services/storage/databox)的脱机大容量迁移工具。 

本文介绍如何以与 Azure 文件同步兼容的方式脱机迁移文件。按照这些说明操作，以避免文件冲突，并在启用同步后保留文件和文件夹的访问控制列表 (Acl) 和时间戳。

## <a name="migration-tools"></a>迁移工具
本文介绍的过程不仅适用于 Data Box，还适用于其他脱机迁移工具。 它还适用于 AzCopy、Robocopy 或合作伙伴工具以及直接在 internet 上使用的服务等工具。 但是，若要解决最初的上传难题，请按照本文中的步骤操作，以与 Azure 文件同步兼容的方式使用这些工具。

在某些情况下，你需要在采用 Azure 文件同步之前从一台 Windows Server 迁移到另一台 Windows Server。 [存储迁移服务](/windows-server/storage/storage-migration-service/overview) (SMS) 可为此提供帮助。 无论你是需要迁移到 Azure 文件同步 (Windows Server 2012R2 和更) 高版本所支持的服务器操作系统版本，还是只需迁移，因为你要为 Azure 文件同步购买新系统，但 SMS 具有许多功能和优势，可帮助你顺利完成迁移。

## <a name="benefits-of-using-a-tool-to-transfer-data-offline"></a>使用工具脱机传输数据的优点
下面是使用传输工具（例如 Data Box 进行脱机迁移）的主要优点：

- 无需通过网络上传所有文件。 对于大型命名空间，此工具可以节省大量的网络带宽和时间。
- 当你使用 Azure 文件同步时，无论你使用哪种传输工具 (Data Box、Azure 导入/导出服务，) 等等，你的实时服务器将仅上传将数据移至 Azure 后更改的文件。
- Azure 文件同步同步文件和文件夹 Acl，即使脱机大容量迁移工具不传输 Acl 也是如此。
- Data Box 和 Azure 文件同步不需要停机。 使用 Data Box 将数据传输到 Azure 中时，可以有效地使用网络带宽并保持文件保真度。 还应仅上载在将数据移动到 Azure 后发生更改的文件，从而使命名空间保持最新。

## <a name="prerequisites-for-the-offline-data-transfer"></a>脱机数据传输的先决条件
在完成脱机数据传输之前，不应在要迁移的服务器上启用同步。 开始之前要考虑的其他事项如下：

- 如果你计划使用 Data Box 进行大容量迁移：请查看 [Data Box 的部署先决条件](../../databox/data-box-deploy-ordered.md#prerequisites)。
- 规划最终 Azure 文件同步拓扑： [规划 Azure 文件同步部署](storage-sync-files-planning.md)
- 选择将保存要与其同步的文件共享的 Azure 存储帐户。 确保批量迁移发生在同一存储帐户中的临时暂存共享中。 批量迁移只能通过使用驻留在同一存储帐户中的最终共享和暂存共享启用。
- 仅在创建与服务器位置的新同步关系时，才能使用批量迁移。 无法使用现有同步关系启用批量迁移。


## <a name="process-for-offline-data-transfer"></a>脱机数据传输过程
下面介绍如何以与大容量迁移工具（如 Azure Data Box）兼容的方式设置 Azure 文件同步：

![显示如何设置 Azure 文件同步的关系图](media/storage-sync-files-offline-data-transfer/data-box-integration-1-600.png)

| 步骤 | 详细信息 |
|---|---------------------------------------------------------------------------------------|
| ![步骤 1](media/storage-sync-files-offline-data-transfer/bullet_1.png) | [订购 Data Box](../../databox/data-box-deploy-ordered.md)。 Data Box 系列提供 [多种产品](https://azure.microsoft.com/services/storage/databox/data) 来满足你的需求。 收到 Data Box 后，请按照其文档，将 [数据复制](../../databox/data-box-deploy-copy-data.md#copy-data-to-data-box)到 Data Box 上的此 UNC 路径： *\\<DeviceIPAddres \> \<StorageAccountName_AzFile\> \<ShareName\>*。 此处， *共享* 名是暂存共享的名称。 将 Data Box 发送回 Azure。 |
| ![步骤 2](media/storage-sync-files-offline-data-transfer/bullet_2.png) | 等待文件显示在你选择作为临时暂存共享的 Azure 文件共享中。 *不要启用到这些共享的同步。* |
| ![步骤 3](media/storage-sync-files-offline-data-transfer/bullet_3.png) | <ul><li>为 Data Box 为你创建的每个文件共享创建一个新的空共享。 此新共享应该与 Data Box 共享位于同一存储帐户中。 [如何创建新的 Azure 文件共享](storage-how-to-create-file-share.md)。</li><li>在存储同步服务中[创建同步组](storage-sync-files-deployment-guide.md#create-a-sync-group-and-a-cloud-endpoint)。 将空共享作为云终结点引用。 对每个 Data Box 文件共享重复此步骤。 [设置 Azure 文件同步](storage-sync-files-deployment-guide.md)。</li></ul> |
| ![步骤 4](media/storage-sync-files-offline-data-transfer/bullet_4.png) | [将实时服务器目录添加为服务器终结点](storage-sync-files-deployment-guide.md#create-a-server-endpoint)。 在此过程中，指定将文件移动到 Azure，并引用暂存共享。 你可以根据需要启用或禁用云分层。 在您的活动服务器上创建服务器终结点时，请引用过渡共享。 在 " **添加服务器终结点** " 边栏选项卡上的 " **脱机数据传输** 下，选择" **已启用**"，然后选择与云终结点必须位于同一存储帐户中的暂存共享。 此处的可用共享列表按存储帐户和尚未同步的共享进行筛选。 此表后面的屏幕截图显示了如何在 Azure 门户中创建服务器终结点期间引用 DataBox 共享。 |
| ![步骤 5](media/storage-sync-files-offline-data-transfer/bullet_5.png) | 在上一步中添加服务器终结点后，数据将自动从正确的源流动。 [同步共享](#syncing-the-share)部分说明了从 DataBox 共享或从 Windows Server 流动数据的时间 |
| |

![Azure 门户用户界面的屏幕截图，显示如何在创建新服务器终结点时启用脱机数据传输](media/storage-sync-files-offline-data-transfer/data-box-integration-2-600.png)

## <a name="syncing-the-share"></a>同步共享
创建服务器终结点后，将开始同步。 同步过程确定服务器上的每个文件是否也存在于 Data Box 将文件存放到的暂存共享中。 如果文件存在于该位置，同步过程将从暂存共享复制文件，而不是从服务器上传该文件。 如果该文件不存在于暂存共享中，或者如果本地服务器上提供了较新版本，则同步过程将从本地服务器上传该文件。

同步共享时，同步将从本地服务器上的文件变体中合并任何缺失的文件属性、权限或时间戳，并将其与 DataBox 共享中的相应文件进行组合。 这可确保每个文件和文件夹在 Azure 文件共享中具有所有可能的文件保真。

> [!IMPORTANT]
> 仅当创建服务器终结点时，才可以启用大容量迁移模式。 建立服务器终结点后，不能将已同步服务器中的大容量迁移数据集成到命名空间中。

## <a name="acls-and-timestamps-on-files-and-folders"></a>文件和文件夹上的 ACL 和时间戳
Azure 文件同步确保文件和文件夹 Acl 与实时服务器同步，即使你使用的大容量迁移工具最初未传输 Acl 也是如此。 因此，过渡共享不需要包含任何文件和文件夹的 Acl。 当你在创建新服务器终结点时启用脱机数据迁移功能时，所有文件 Acl 都将在服务器上同步。 新创建和修改的时间戳也将同步。

## <a name="shape-of-the-namespace"></a>命名空间的形状
启用同步时，服务器的内容将确定命名空间的形状。 如果在 Data Box 快照和迁移完成后从本地服务器中删除文件，则这些文件不会移动到实时同步命名空间。 它们保留在暂存共享中，但不会被复制。 这是必需的，因为同步会根据活动服务器保留命名空间。 Data Box *快照* 只是一种用于高效文件复制的过渡方式。 它不是实时命名空间形状的授权。

## <a name="cleaning-up-after-bulk-migration"></a>大容量迁移后进行清理 
当服务器完成命名空间的初始同步时，大容量迁移的文件 Data Box 使用暂存文件共享。 在 Azure 门户的 " **服务器终结点属性** " 边栏选项卡中，在 " **脱机数据传输** " 部分中，状态从 " **正在进行** " 更改为 " **已完成**"。 

![服务器终结点属性边栏选项卡的屏幕截图，可在其中找到脱机数据传输的状态和禁用控件](media/storage-sync-files-offline-data-transfer/data-box-integration-3-444.png)

现在，可以清理临时共享来节省成本：

1. 在 " **服务器终结点属性** " 边栏选项卡上，当状态为 " **已完成**" 时，选择 " **禁用脱机数据传输**"
2. 请考虑删除暂存共享以节省成本。 暂存共享可能不包含文件和文件夹 Acl，因此不太可能有用。 对于备份时点，请创建 [同步 Azure 文件共享](storage-snapshots-files.md)的实际快照。 可以 [设置 Azure 备份，按计划拍摄快照]( ../../backup/backup-afs.md) 。

仅当状态为 " **已完成** " 或由于配置错误而要取消时，才禁用脱机数据传输模式。 如果在部署过程中禁用该模式，则即使过渡共享仍可用，文件也会开始从服务器上传。

> [!IMPORTANT]
> 禁用脱机数据传输模式之后，就不能再次启用它，即使大容量迁移中的暂存共享仍可用。

## <a name="azure-file-sync-and-pre-seeded-files-in-the-cloud"></a>云中的 Azure 文件同步和预植入文件

如果在 Azure 文件共享中，通过其他方式（例如 via AzCopy、从云备份或任何其他方法执行 RoboCopy）在 Azure 文件共享中创建了种子文件，则仍应按照本文中所述的 [脱机数据传输过程](#process-for-offline-data-transfer) 进行操作。 你只需忽略 DataBox 作为文件移动到云的方法。 但是，确保仍遵循将文件播种到 *暂存共享* 而不是最后 Azure 文件同步连接的共享的过程，这一点非常重要。

> [!WARNING]
> **按照将文件播种到暂存共享而不是最后** Azure 文件同步连接的共享的过程进行操作。 如果不这样做，可能会发生文件冲突 (这两个文件版本都将存储) 并且在实时服务器上删除的文件可能会返回，前提是这些文件仍然存在于较旧的、种子的文件集。 此外，文件夹更改会相互合并，这使得在发生此类错误后，很难分隔命名空间。

## <a name="next-steps"></a>后续步骤
- [规划 Azure 文件同步部署](storage-sync-files-planning.md)
- [部署 Azure 文件同步](storage-sync-files-deployment-guide.md)