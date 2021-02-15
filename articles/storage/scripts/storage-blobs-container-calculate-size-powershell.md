---
title: 使用 PowerShell 计算 blob 容器的大小
titleSuffix: Azure Storage
description: 通过计算容器各 blob 的总大小来计算 Azure Blob 存储中容器的大小。
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 87ef18530c549396b7d8fe1ec4ff0e08cb8535e8
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784269"
---
# <a name="calculate-the-size-of-a-blob-container-with-powershell"></a>使用 PowerShell 计算 blob 容器的大小

此脚本通过计算容器中 blob 的总大小来计算 Azure Blob 存储中容器的大小。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> 此 PowerShell 脚本提供容器的估计大小，不应该用于计费计算。 有关为计费目的计算容器大小的脚本，请参阅[为计费目的计算 Blob 存储容器的大小](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)。

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>清理部署

运行以下命令来删除资源组、容器和所有相关资源。

```powershell
Remove-AzResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令来计算 Blob 存储容器的大小。 表中的每一项均链接到命令特定的文档。

| Command | 说明 |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | 获取资源组或订阅中的指定存储帐户或所有存储帐户。 |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | 列出容器中的 Blob。 |

## <a name="next-steps"></a>后续步骤

有关为计费目的计算容器大小的脚本，请参阅[为计费目的计算 Blob 存储容器的大小](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)。

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。

可以在 [Azure 存储的 PowerShell 示例](../blobs/storage-samples-blobs-powershell.md)中找到其他存储 PowerShell 脚本示例。
