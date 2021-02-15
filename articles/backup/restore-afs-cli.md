---
title: 还原包含 Azure CLI 的 Azure 文件共享
description: 了解如何使用 Azure CLI 在恢复服务保管库中还原已备份的 Azure 文件共享
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: a025de7bfb9db037b2008d69be7782feabb482f3
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94562315"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>还原包含 Azure CLI 的 Azure 文件共享

Azure CLI 提供了用于管理 Azure 资源的命令行体验。 这是一个很好的工具，用于构建自定义自动化以使用 Azure 资源。 本文介绍如何使用 Azure CLI 从 [Azure 备份](./backup-overview.md) 创建的还原点还原整个文件共享或特定文件。 也可以使用 [Azure PowerShell](./backup-azure-afs-automation.md) 或 [Azure 门户](backup-afs.md)执行这些步骤。

本文结束时，你将学习如何使用 Azure CLI 执行以下操作：

* 查看备份的 Azure 文件共享的还原点。
* 还原完整的 Azure 文件共享。
* 还原单个文件或文件夹。

>[!NOTE]
> Azure 备份现在支持使用 Azure CLI 将多个文件或文件夹还原到原始位置或备用位置。 有关详细信息，请参阅本文档的将 [多个文件或文件夹还原到原始或备用位置](#restore-multiple-files-or-folders-to-original-or-alternate-location) 部分。

## <a name="prerequisites"></a>先决条件

本文假设已有一个 azure 文件共享由 Azure 备份进行备份。 如果没有，请参阅 [使用 CLI 备份 Azure 文件共享](backup-afs-cli.md) ，为文件共享配置备份。 本文将使用以下资源：

| 文件共享 | 存储帐户 | 区域 | 详细信息 |
|---|---|---|---|
| *azurefiles* | *afsaccount* | EastUS | 使用 Azure 备份备份的原始源 |
| *azurefiles1* | *afaccount1* | EastUS | 用于备用位置恢复的目标源 |

你可以使用类似的文件共享结构来尝试本文中所述的不同类型的还原。

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

 - 本教程需要 Azure CLI 版本的2.0.18 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>提取 Azure 文件共享的恢复点

使用 [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list) cmdlet 列出已备份文件共享的所有恢复点。

以下示例在 *afsaccount* 存储帐户中获取 *azurefiles* 文件共享的恢复点列表。

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --backup-management-type azurestorage --item-name "AzureFileShare;azurefiles" --workload-type azurefileshare --out table
```

还可以通过提供以下两个附加参数，使用容器和项的友好名称运行以前的 cmdlet：

* **--备份管理-类型** ： *azurestorage*
* **--工作负荷类型** ： *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

结果集是具有每个还原点的时间和一致性详细信息的恢复点列表。

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

输出中的 **name** 属性对应于可用作恢复操作中的 **--rp name** 参数的值的恢复点名称。

## <a name="full-share-recovery-by-using-the-azure-cli"></a>使用 Azure CLI 完全共享恢复

您可以使用此还原选项在原始或备用位置还原完整的文件共享。

定义以下参数以执行还原操作：

* **--container-name** ：托管已备份原始文件共享的存储帐户的名称。 若要检索容器的名称或友好名称，请使用 [az backup container list](/cli/azure/backup/container#az-backup-container-list) 命令。
* **--项-name** ：要用于还原操作的已备份原始文件共享的名称。 若要检索已备份项的名称或友好名称，请使用 [az backup item list](/cli/azure/backup/item#az-backup-item-list) 命令。

### <a name="restore-a-full-share-to-the-original-location"></a>将完全共享还原到原始位置

还原到原始位置时，无需指定与目标相关的参数。 必须仅提供 " **解决冲突** "。

下面的示例使用了 [az backup restore azurefileshare](/cli/azure/backup/restore#az-backup-restore-restore-azurefileshare) cmdlet，并将还原模式设置为 *originallocation* ，以将 *azurefiles* 文件共享还原到原始位置。 你使用在 [Azure 文件共享的提取恢复点](#fetch-recovery-points-for-the-azure-file-share)中获取的恢复点932883129628959823：

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

输出中的 **name** 属性对应于备份服务为还原操作创建的作业的名称。 若要跟踪作业的状态，请使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet。

### <a name="restore-a-full-share-to-an-alternate-location"></a>将完全共享还原到备用位置

可以使用此选项将文件共享还原到备用位置，并使原始文件共享保持原样。 为备用位置恢复指定以下参数：

* **--目标存储帐户** ：备份的内容要还原到的存储帐户。 目标存储帐户应与保管库位于同一位置。
* **--文件共享** ：要将备份内容还原到的目标存储帐户中的文件共享。
* **--folder** ：要将数据还原到的文件共享下的文件夹。 如果要将备份内容还原到根文件夹，请将目标文件夹值指定为空字符串。
* **--resolve-冲突** ：如果与还原的数据发生冲突，则为指令。 接受“覆盖”或“跳过”。

下面的示例使用 [az backup restore azurefileshare](/cli/azure/backup/restore#az-backup-restore-restore-azurefileshare) with restore mode as *alternatelocation* 将 *afsaccount* 存储帐户中的 *azurefiles* 文件共享还原到 *azurefiles1* 存储帐户中的 *afaccount1 "* 文件共享。

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

输出中的 **name** 属性对应于备份服务为还原操作创建的作业的名称。 若要跟踪作业的状态，请使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet。

## <a name="item-level-recovery"></a>项目级恢复

您可以使用此还原选项还原原始或备用位置中的单个文件或文件夹。

定义以下参数以执行还原操作：

* **--container-name** ：托管已备份原始文件共享的存储帐户的名称。 若要检索容器的名称或友好名称，请使用 [az backup container list](/cli/azure/backup/container#az-backup-container-list) 命令。
* **--项-name** ：要用于还原操作的已备份原始文件共享的名称。 若要检索已备份项的名称或友好名称，请使用 [az backup item list](/cli/azure/backup/item#az-backup-item-list) 命令。

为要恢复的项目指定以下参数：

* **SourceFilePath** ：文件共享中要还原的文件的绝对路径（以字符串形式）。 此路径与 [az storage file 下载](/cli/azure/storage/file#az-storage-file-download) 或 [az storage file show](/cli/azure/storage/file#az-storage-file-show) CLI 命令中使用的路径相同。
* **SourceFileType** ：选择是否选择目录或文件。 接受“目录”或“文件”。
* **ResolveConflict** ：如果与还原的数据发生冲突，则为指令。 接受“覆盖”或“跳过”。

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>将单个文件或文件夹还原到原始位置

使用带有还原模式的 [az backup restore azurefiles](/cli/azure/backup/restore#az-backup-restore-restore-azurefiles) cmdlet 将特定文件或文件夹 *还原到其* 原始位置。

下面的示例将 *RestoreTest.txt* 文件还原到其原始位置： *azurefiles* 文件共享。

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

输出中的 **name** 属性对应于备份服务为还原操作创建的作业的名称。 若要跟踪作业的状态，请使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet。

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>将单个文件或文件夹还原到备用位置

若要将特定文件或文件夹还原到备用位置，请使用 [az backup restore azurefiles](/cli/azure/backup/restore#az-backup-restore-restore-azurefiles) cmdlet，并将还原模式设置为 *alternatelocation* ，并指定下列与目标相关的参数：

* **--目标存储帐户** ：备份的内容要还原到的存储帐户。 目标存储帐户应与保管库位于同一位置。
* **--文件共享** ：要将备份内容还原到的目标存储帐户中的文件共享。
* **--folder** ：要将数据还原到的文件共享下的文件夹。 如果将备份的内容还原到根文件夹，则将目标文件夹的值指定为空字符串。

下面的示例将 *azurefiles* 文件共享中最初显示的 *RestoreTest.txt* 文件还原到另一个位置： *afaccount1* 存储帐户中托管的 *azurefiles1* 文件共享中的 *restoredata* 文件夹。

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

输出中的 **name** 属性对应于备份服务为还原操作创建的作业的名称。 若要跟踪作业的状态，请使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet。

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>将多个文件或文件夹还原到原始或备用位置

若要为多个项目执行还原，请将 **源文件路径** 参数的值作为要还原的所有文件或文件夹的 **空格分隔** 路径传递。

下面的示例将 *Restore.txt* 和 *AFS 测试* 还原到其原始位置 Report.docx文件中。

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932889937058317910 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore Test.txt" "AFS Testing Report.docx" --resolve-conflict overwrite  --out table
```

输出将如下所示：

```output
Name                                          ResourceGroup
------------------------------------          ---------------
649b0c14-4a94-4945-995a-19e2aace0305          azurefiles
```

输出中的 **name** 属性对应于备份服务为还原操作创建的作业的名称。 若要跟踪作业的状态，请使用 [az backup job show](/cli/azure/backup/job#az-backup-job-show) cmdlet。

如果要将多个项还原到备用位置，请使用上面的命令，方法是指定与目标相关的参数，如将 [单个文件或文件夹还原到备用位置](#restore-individual-files-or-folders-to-an-alternate-location) 部分所述。

## <a name="next-steps"></a>后续步骤

了解如何 [通过 Azure CLI 管理 Azure 文件共享备份](manage-afs-backup-cli.md)。
