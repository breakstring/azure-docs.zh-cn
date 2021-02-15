---
title: 使用 PowerShell 轮换存储帐户访问密钥
titleSuffix: Azure Storage
description: 创建 Azure 存储帐户，然后检索并轮换其中的一个帐户访问密钥。
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7d8585b5d05012ab2aff2580d41fecf6423b509c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89070422"
---
# <a name="rotate-storage-account-access-keys-with-powershell"></a>使用 PowerShell 轮换存储帐户访问密钥

此脚本会创建一个 Azure 存储帐户，显示新存储帐户的主访问密钥，然后续订（轮换）密钥。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.ps1 "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>清理部署

运行以下命令来删除资源组、存储帐户和所有相关资源。

```powershell
Remove-AzResourceGroup -Name rotatekeystestrg
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建存储帐户并检索和轮换其中的一个访问密钥。 表中的每一项均链接到命令特定的文档。

| Command | 说明 |
|---|---|
| [Get-AzLocation](/powershell/module/az.resources/get-azlocation) | 获取所有位置以及每个位置支持的资源提供程序。 |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建 Azure 资源组。 |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | 创建存储帐户。 |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | 获取 Azure 存储帐户的访问密钥。 |
| [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) | 重新生成 Azure 存储帐户的访问密钥。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。

有关其他存储 PowerShell 脚本示例，可参阅 [Azure Blob 存储的 PowerShell 示例](../blobs/storage-samples-blobs-powershell.md)。
