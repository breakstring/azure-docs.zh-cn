---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 5c0341087cdd348e973da5faaa1f90081780c9c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88602350"
---
<!--Create a media services account -->

以下 Azure CLI 命令创建新的媒体服务帐户。 可以替换以下值：`amsaccount``storageaccountforams`（必须与提供给存储帐户的值相符）和 `amsResourceGroup`（必须与提供给资源组的值相符）。

```azurecli
az ams account create --name amsaccount -g amsResourceGroup --storage-account storageaccountforams -l westus2
```