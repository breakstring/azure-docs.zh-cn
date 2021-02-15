---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 872164dfee9f35881fca97b0339bd95c64a2f5ff
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070173"
---
资源组是在其中部署和管理 Azure 资源的逻辑容器。 使用 Azure PowerShell [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 在 eastus 位置创建一个名为 myResourceGroup 的资源组 。 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```
