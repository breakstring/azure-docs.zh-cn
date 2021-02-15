---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: a87fbbb276fd8813492cc293bc08b958ee3d6b7b
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070241"
---
使用 Azure CLI [az keyvault create](/cli/azure/keyvault#az_keyvault_create) 命令在上一步骤的资源组中创建 Key Vault。 需要提供某些信息：

- Key Vault 名称：由 3 到 24 个字符构成的字符串，只能包含数字 (0-9)、字母（a-z、A-Z）和连字符 (-)

  > [!Important]
  > 每个密钥保管库必须具有唯一的名称。 在以下示例中，将 <your-unique-keyvault-name> 替换为密钥保管库的名称。

- 资源组名称：myResourceGroup。
- 位置：EastUS。

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
```

此命令的输出会显示新建的 Key Vault 的属性。 请记下下面列出的两个属性：

- **保管库名称**：为上面的 --name 参数提供的名称。
- **保管库 URI**：在本示例中为 https://&lt;your-unique-keyvault-name&gt;.vault.azure.net/。 通过其 REST API 使用保管库的应用程序必须使用此 URI。

目前，只有你的 Azure 帐户才有权对这个新保管库执行任何操作。
