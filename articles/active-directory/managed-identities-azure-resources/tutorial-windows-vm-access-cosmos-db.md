---
title: 教程：使用托管标识访问 Azure Cosmos DB - Windows - Azure AD
description: 本教程介绍了使用 Windows VM 上系统分配的托管标识访问 Azure Cosmos DB 的过程。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc3417284137cdbc9f93ac02f825820bfe744843
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97107492"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-cosmos-db"></a>教程：使用 Windows VM 系统分配的托管标识访问 Azure Cosmos DB

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

本教程介绍了如何使用 Windows 虚拟机 (VM) 的系统分配托管标识来访问 Cosmos DB。 学习如何：

> [!div class="checklist"]
> * 创建 Cosmos DB 帐户
> * 向 Windows VM 系统分配的托管标识授予对 Cosmos DB 帐户访问密钥的访问权限
> * 使用 Windows VM 系统分配的托管标识获取访问令牌来调用 Azure 资源管理器
> * 从 Azure 资源管理器中获取访问密钥，以便进行 Cosmos DB 调用

## <a name="prerequisites"></a>先决条件

- 如果不熟悉 Azure 资源功能的托管标识，请参阅此[概述](overview.md)。 
- 如果没有 Azure 帐户，请在继续操作前[注册免费帐户](https://azure.microsoft.com/free/)。
- 要执行所需的资源创建和角色管理，帐户在相应的范围（订阅或资源组）需要“所有者”权限。 如果需要有关角色分配的帮助，请参阅[使用基于角色的访问控制管理对 Azure 订阅资源的访问权限](../../role-based-access-control/role-assignments-portal.md)。
- 安装最新版本的 [Azure PowerShell](/powershell/azure/install-az-ps)
- 还需要启用了系统分配的托管标识的 Windows 虚拟机。
  - 如需为本教程创建虚拟机，则可以按照标题为[创建启用了系统分配的标识的虚拟机](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)的文章进行操作

## <a name="create-a-cosmos-db-account"></a>创建 Cosmos DB 帐户 

如果还没有 Cosmos DB 帐户，请创建一个。 可以跳过此步骤，使用现有的 Cosmos DB 帐户。 

1. 单击 Azure 门户左上角的“+ 创建资源”按钮。
2. 单击“数据库”，然后单击“Azure Cosmos DB”，新的“新建帐户”面板便会显示。  
3. 输入 Cosmos DB 帐户的 **ID**，供以后使用。  
4. **API** 应设置为“SQL”。 本教程中介绍的方法可以与其他可用的 API 类型配合使用，但本教程中的步骤是针对 SQL API 的。
5. 确保“订阅”和“资源组”与上一步中创建 VM 时指定的名称匹配。    选择提供 Cosmos DB 的“位置”。 
6. 单击“创建”。 

### <a name="create-a-collection"></a>创建集合 

接下来，在 Cosmos DB 帐户中添加数据集合，以便在后续步骤中进行查询。

1. 导航到新创建的 Cosmos DB 帐户。
2. 在“概览”选项卡中单击“+/添加集合”按钮，此时“添加集合”面板就会滑出。  
3. 为集合提供数据库 ID、集合 ID，选择存储容量，输入分区键，输入吞吐量值，然后单击“确定”。   就本教程来说，使用“测试”作为数据库 ID 和集合 ID，选择固定的存储容量和最低吞吐量（400 RU/秒）就可以了。  


## <a name="grant-access"></a>授予访问权限

本部分介绍如何授予 Windows VM 系统分配的托管标识访问 Cosmos DB 帐户访问密钥的权限。 Cosmos DB 原本不支持 Azure AD 身份验证。 但是，可以使用系统分配的托管标识从资源管理器检索 Cosmos DB 访问密钥，然后使用该密钥访问 Cosmos DB。 在此步骤中，将向 Windows VM 系统分配的托管标识授予对 Cosmos DB 帐户密钥的访问权限。

若要向 Windows VM 系统分配的托管标识授予在 Azure 资源管理器中使用 PowerShell 访问 Cosmos DB 帐户的权限，请更新以下值：

- `<SUBSCRIPTION ID>`
- `<RESOURCE GROUP>`
- `<COSMOS DB ACCOUNT NAME>`

Cosmos DB 在使用访问密钥时支持两种级别的粒度：对帐户的读/写访问权限，以及对帐户的只读访问权限。  如果需要获取帐户的读/写密钥，请分配 `DocumentDB Account Contributor` 角色；如果需要获取帐户的只读密钥，请分配 `Cosmos DB Account Reader Role` 角色。  对于本教程，请分配 `Cosmos DB Account Reader Role`：

```azurepowershell
$spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Cosmos DB Account Reader Role" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>"
```

>[!NOTE]
> 请记住，如果无法执行操作，则可能没有相应的权限。 若要对密钥进行写入访问，需要使用 Azure 角色（如 DocumentDB 帐户参与者）或创建自定义角色。 有关详细信息，请查看 [Azure Cosmos DB 中基于角色的访问控制](../../cosmos-db/role-based-access-control.md)

## <a name="access-data"></a>访问数据

本部分介绍如何使用 Windows VM 系统分配的托管标识的访问令牌调用 Azure 资源管理器。 在本教程的剩余部分中，我们从先前创建的 VM 入手。 

需在 Windows VM 上安装最新版本的 [Azure CLI](/cli/azure/install-azure-cli)。

### <a name="get-an-access-token"></a>获取访问令牌

1. 在 Azure 门户中，导航到“虚拟机”，转到 Windows 虚拟机，然后在“概述”页中单击顶部的“连接”。 
2. 输入创建 Windows VM 时添加的用户名和密码。 
3. 现在，已经创建了与虚拟机的远程桌面连接，请在远程会话中打开 PowerShell。
4. 使用 Powershell 的 Invoke-WebRequest，向 Azure 资源终结点的本地托管标识发出请求以获取 Azure 资源管理器的访问令牌。

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
   ```

   > [!NOTE]
   > “资源”参数的值必须完全匹配 Azure AD 预期的值。 如果使用 Azure 资源管理器资源 ID，必须在 URI 的结尾添加斜线。
    
   接下来，提取“内容”元素，该元素以 JavaScript 对象表示法 (JSON) 格式字符串的形式存储在 $response 对象中。 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```
   接下来，从响应中提取访问令牌。
    
   ```powershell
   $ArmToken = $content.access_token
   ```

### <a name="get-access-keys"></a>获取访问密钥 

本部分介绍如何从 Azure 资源管理器获取访问密钥以进行 Cosmos DB 调用。 我们使用之前获取的访问令牌通过 PowerShell 调用资源管理器，以检索 Cosmos DB 帐户访问密钥。 有了访问密钥以后，即可查询 Cosmos DB。 使用你自己的值替换以下条目：

- `<SUBSCRIPTION ID>`
- `<RESOURCE GROUP>`
- `<COSMOS DB ACCOUNT NAME>` 
- 将 `<ACCESS TOKEN>` 值替换为前面检索到的访问令牌。 

>[!NOTE]
>若要检索读/写密钥，请使用密钥操作类型 `listKeys`。  若要检索只读密钥，请使用密钥操作类型 `readonlykeys`。 如果无法使用“listkeys”，请验证是否已向托管标识分配[相应的角色](../../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)。

```powershell
Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>/readonlykeys/?api-version=2016-03-31' -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
响应会提供一个密钥列表。  例如，如果获取只读密钥：

```powershell
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```
有了 Cosmos DB 帐户的访问密钥以后，即可将其传递给 Cosmos DB SDK 并通过调用来访问该帐户。  如需快速示例，可将该访问密钥传递给 Azure CLI。  在 Azure 门户中，可以从 Cosmos DB 帐户边栏选项卡上的“概览”选项卡获取 `<COSMOS DB CONNECTION URL>`。  将 `<ACCESS KEY>` 替换为在上面获取的值：

```azurecli
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

此 CLI 命令返回有关集合的详细信息：

```output
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```


## <a name="disable"></a>禁用

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>后续步骤

在本教程中，你学习了如何使用 Windows VM 系统分配的标识来访问 Cosmos DB。  若要详细了解 Cosmos DB，请参阅：

> [!div class="nextstepaction"]
>[Azure Cosmos DB 概述](../../cosmos-db/introduction.md)