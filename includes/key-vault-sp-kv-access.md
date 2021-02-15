---
author: msmbaldwin
ms.service: key-vault
ms.subservice: B2C
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 826d5c2183ecc70908192695927890dcf06137b1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "87013080"
---
通过将 `clientId` 传递给 [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 命令，为密钥保管库创建授予服务主体权限的访问策略。 授予服务主体对密钥和机密的 get、list 和 set 权限。

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```
