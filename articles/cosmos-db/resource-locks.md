---
title: 防止删除或更改 Azure Cosmos DB 资源
description: 使用 Azure 资源锁定来防止删除或更改 Azure Cosmos DB 资源。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/06/2020
ms.author: mjbrown
ms.openlocfilehash: 1c8c766208132aec115e1fbeb15af3a057c3de3e
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636684"
---
# <a name="prevent-azure-cosmos-db-resources-from-being-deleted-or-changed"></a>防止删除或更改 Azure Cosmos DB 资源
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

作为管理员，可能需要锁定 Azure Cosmos 帐户、数据库和容器，以防止组织中的其他用户意外删除或修改关键资源。 可以将锁定级别设置为 CanNotDelete 或 ReadOnly。

- **CanNotDelete** 味着经授权的用户仍可读取和修改资源，但不能删除资源。
- **ReadOnly** 意味着经授权的用户可以读取资源，但不能删除或更新资源。 应用此锁类似于将所有经授权的用户限制于“读者”角色授予的权限。

## <a name="how-locks-are-applied"></a>锁的应用方式

在父范围应用锁时，该范围内所有资源都会继承相同的锁。 即使是之后添加的资源也会从父作用域继承该锁。 继承中限制性最强的锁优先执行。

与 Azure 基于角色的访问控制不同，可以使用管理锁来对所有用户和角色应用限制。 若要了解有关的 Azure RBAC Azure Cosmos DB 参阅 [Azure Cosmos DB 中的 azure 基于角色的访问控制](role-based-access-control.md)。

Resource Manager 锁仅适用于管理平面内发生的操作，包括发送到 https://management.azure.com的操作。 这类锁不会限制资源如何执行各自的函数。 资源更改将受到限制，但资源操作不受限制。 例如，Azure Cosmos 容器上的 ReadOnly 锁定会阻止你删除或修改容器。 它不会阻止你在容器中创建、更新或删除数据。 会允许数据事务，因为这些操作不会发送到 https://management.azure.com。

## <a name="manage-locks"></a>管理锁定

> [!WARNING]
> 资源锁定对于使用帐户密钥访问 Azure Cosmos DB 的用户所做的更改不起作用，除非首先通过启用 disableKeyBasedMetadataWriteAccess 属性锁定 Azure Cosmos 帐户。 在启用此属性之前，应注意确保它不会中断使用任何 SDK、Azure 门户或通过帐户密钥连接并修改资源（如更改吞吐量、更新索引策略等）的第三方工具对资源进行更改的现有应用程序。若要了解详细信息并检查清单以确保应用程序继续运行，请参阅[阻止 Azure Cosmos DB SDK 的更改](role-based-access-control.md#prevent-sdk-changes)

### <a name="powershell"></a>PowerShell

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "my-cosmos-account"
$lockName = "$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName -DisableKeyBasedMetadataWriteAccess true

# Create a Delete Lock on an Azure Cosmos account resource and all child resources
New-AzResourceLock `
    -ApiVersion "2020-04-01" `
    -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
    -ResourceGroupName $resourceGroupName `
    -ResourceName $accountName `
    -LockName $lockName `
    -LockLevel "CanNotDelete" # CanNotDelete or ReadOnly
```

### <a name="azure-cli"></a>Azure CLI

```bash
resourceGroupName='myResourceGroup'
accountName='my-cosmos-account'
$lockName="$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
az cosmosdb update  --name $accountName --resource-group $resourceGroupName  --disable-key-based-metadata-write-access true

# Create a Delete Lock on an Azure Cosmos account resource
az lock create --name $lockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/databaseAccount \
    --lock-type 'CanNotDelete' # CanNotDelete or ReadOnly \
    --resource $accountName
```

### <a name="template"></a>模板

将锁定应用于 Azure Cosmos DB 资源时，请使用以下格式：

- name - `{resourceName}/Microsoft.Authorization/{lockName}`
- type - `{resourceProviderNamespace}/{resourceType}/providers/locks`

> [!IMPORTANT]
> 修改现有 Azure Cosmos 帐户时，请确保在使用此属性重新部署时包含帐户和子资源的其他属性。 请勿按原样部署此模板，否则它将重置所有的帐户属性。

```json
"resources": [
    {
        "type": "Microsoft.DocumentDB/databaseAccounts",
        "name": "[variables('accountName')]",
        "apiVersion": "2020-04-01",
        "kind": "GlobalDocumentDB",
        "location": "[parameters('location')]",
        "properties": {
            "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
            "locations": "[variables('locations')]",
            "databaseAccountOfferType": "Standard",
            "enableAutomaticFailover": "[parameters('automaticFailover')]",
            "disableKeyBasedMetadataWriteAccess": true
        }
    },
    {
        "type": "Microsoft.DocumentDB/databaseAccounts/providers/locks",
        "apiVersion": "2020-04-01",
        "name": "[concat(variables('accountName'), '/Microsoft.Authorization/siteLock')]",
        "dependsOn": [
        "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]"
        ],
        "properties": {
        "level": "CanNotDelete",
        "notes": "Cosmos account should not be deleted."
        }
    }
]
```

## <a name="next-steps"></a>后续步骤

- [Azure 资源管理器锁定概述](../azure-resource-manager/management/lock-resources.md)
