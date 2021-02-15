---
title: 管理 Azure 存储帐户中 blob 的访问层
description: 了解如何更改 GPv2 或 Blob 存储帐户中 blob 的层
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: 183593022c934eaf52ffe18649c23e8deced34d8
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2021
ms.locfileid: "98166478"
---
# <a name="manage-the-access-tier-of-a-blob-in-an-azure-storage-account"></a>管理 Azure 存储帐户中 blob 的访问层

每个 blob 都有一个默认访问层： "热"、"冷" 或 "存档"。 Blob 在创建该 blob 的 Azure 存储帐户的默认访问层上使用。 Blob 存储和 GPv2 帐户在帐户级别公开“访问层”属性。 此属性指定在对象级别未显式设置的任何 blob 的默认访问层。 对于已在对象级别设置该层的对象，不会应用帐户层。 存档层仅适用于对象级别。 可以通过执行以下步骤，随时在访问层之间切换。

## <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>更改 GPv2 或 Blob 存储帐户中 Blob 的层

以下方案使用 Azure 门户或 PowerShell：

# <a name="portal"></a>[门户](#tab/portal)

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户中，搜索并选择“所有资源”。

1. 选择存储帐户。

1. 选择容器，然后选择 Blob。

1. 在“Blob 属性”中选择“更改层”。 

1. 选择“热”、“冷”或“存档”访问层。   如果 Blob 目前位于存档中，而你想要解冻至联机层，则还可以选择“标准”或“高”作为“解冻优先级”。 

1. 选择底部的“保存”。

![在 Azure 门户中更改 Blob 层](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

以下 PowerShell 脚本可用于更改 blob 层。 必须使用资源组名称初始化 `$rgName` 变量。 必须使用你的存储帐户名初始化 `$accountName` 变量。 必须使用容器名称初始化 `$containerName` 变量。 必须使用 blob 名称初始化 `$blobName` 变量。

```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```

---

## <a name="next-steps"></a>后续步骤

- [如何管理 Azure 存储帐户的默认帐户访问层](../common/manage-account-default-access-tier.md)
- [了解如何从存档层解冻 Blob 数据](storage-blob-rehydration.md)
- [按区域查看 Blob 存储帐户和 GPv2 帐户中的热层、冷层和存档层定价](https://azure.microsoft.com/pricing/details/storage/)
