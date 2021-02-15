---
title: 管理资源组 - Azure CLI
description: 使用 Azure CLI 通过 Azure 资源管理器管理资源组。 说明如何创建、列出和删除资源组。
author: mumian
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: db4a938d2f773ed24d4c7a48d747dd5cc22c0bd2
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900274"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>使用 Azure CLI 管理 Azure 资源管理器资源组

了解如何将 Azure CLI 与 [Azure 资源管理器](overview.md)配合使用来管理 Azure 资源组。 若要管理 Azure 资源，请参阅[使用 Azure CLI 管理 Azure 资源](manage-resources-cli.md)。

有关资源组管理的其他文章：

- [使用 Azure 门户管理 Azure 资源组](manage-resources-portal.md)
- [使用 Azure PowerShell 管理 Azure 资源组](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>什么是资源组

资源组是用于保存 Azure 解决方案相关资源的容器。 资源组可以包含解决方案的所有资源，也可以只包含以组的形式进行管理的资源。 根据对组织有利的原则，决定如何将资源分配到资源组。 通常可将共享相同生命周期的资源添加到同一资源组，以便将其作为一个组轻松部署、更新和删除。

” 资源组存储有关资源的元数据。 因此，当指定资源组的位置时，也就指定了元数据的存储位置。 出于合规性原因，可能需要确保数据存储在某一特定区域。

” 资源组存储有关资源的元数据。 当指定资源组的位置时，也就指定了元数据的存储位置。

## <a name="create-resource-groups"></a>创建资源组

以下 CLI 命令将创建一个资源组。

```azurecli-interactive
az group create --name demoResourceGroup --location westus
```

## <a name="list-resource-groups"></a>列出资源组

以下 CLI 脚本将列出订阅下的资源组。

```azurecli-interactive
az group list
```

若要获取某个资源组，请执行以下操作：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>删除资源组

以下 CLI 脚本删除某个资源组：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

若要详细了解 Azure 资源管理器如何管理资源的删除，请参阅 [Azure 资源管理器资源组的删除](delete-resource-group.md)。

## <a name="deploy-resources-to-an-existing-resource-group"></a>将资源部署到现有的资源组

请参阅[将资源部署到现有的资源组](manage-resources-cli.md#deploy-resources-to-an-existing-resource-group)。

## <a name="deploy-a-resource-group-and-resources"></a>部署资源组和资源

可以使用资源管理器模板创建一个资源组并将资源部署到该组。 有关详细信息，请参阅[创建资源组并部署资源](../templates/deploy-to-subscription.md#resource-groups)。

## <a name="redeploy-when-deployment-fails"></a>部署失败时，重新部署

此功能也称为“出错时回滚”  。 有关详细信息，请参阅[在部署失败时重新部署](../templates/rollback-on-error.md)。

## <a name="move-to-another-resource-group-or-subscription"></a>移到另一个资源组或订阅

可将组中的资源移到另一个资源组。 有关详细信息，请参阅[移动资源](manage-resources-cli.md#move-resources)。

## <a name="lock-resource-groups"></a>锁定资源组

锁定可以防止组织中的其他用户意外删除或修改关键资源，例如 Azure 订阅、资源组或资源。

以下脚本锁定了一个资源组，因此无法删除该资源组。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName
```

以下脚本获取资源组的所有锁：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName
```

以下脚本将删除锁：

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

有关详细信息，请参阅 [使用 Azure 资源管理器锁定资源](lock-resources.md)。

## <a name="tag-resource-groups"></a>标记资源组

可以将标记应用到资源组和资源，以按照逻辑组织资产。 有关信息，请参阅[使用标记组织 Azure 资源](tag-resources.md#azure-cli)。

## <a name="export-resource-groups-to-templates"></a>将资源组导出到模板

成功设置资源组后，可能需要查看资源组的资源管理器模板。 导出模板有两个好处：

- 由于模板包含整个基础结构，因此将来可以自动完成解决方案的部署。
- 通过查看代表解决方案的 JavaScript 对象表示法 (JSON)，了解模板语法。

若要导出资源组中的所有资源，请使用 [az group export](/cli/azure/group?view=azure-cli-latest#az_group_export&preserve-view=true) ，并提供资源组名称。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName
```

该脚本在控制台上显示模板。 复制 JSON，并将其另存为文件。

可以选择要导出的资源，而不是导出资源组中的所有资源。

若要导出一个资源，请传递该资源 ID。

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $resourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az group export --resource-group $resourceGroupName --resource-ids $storageAccount
```

若要导出多个资源，请传递以空格分隔的资源 Id。 若要导出所有资源，请不要指定此参数或提供 "*"。

```azurecli-interactive
az group export --resource-group <resource-group-name> --resource-ids $storageAccount1 $storageAccount2
```

导出模板时，可以指定是否在模板中使用参数。 默认情况下，包含资源名称的参数，但它们没有默认值。 在部署过程中必须传递该参数值。

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "type": "String"
  }
}
```

在资源中，参数用于名称。

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

如果在导出模板时使用 `--include-parameter-default-value` 参数，则模板参数包括设置为当前值的默认值。 可以使用该默认值，也可以通过传入不同的值来覆盖默认值。

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

如果在导出模板时使用 `--skip-resource-name-params` 参数，则模板中不包括资源名称的参数。 而是，资源名称直接在资源上设置为其当前值。 无法在部署过程中自定义该名称。

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

导出模板功能不支持导出 Azure 数据工厂资源。 若要了解如何导出数据工厂资源，请参阅[在 Azure 数据工厂中复制或克隆数据工厂](../../data-factory/copy-clone-data-factory.md)。

若要导出通过经典部署模型创建的资源，必须[将其迁移到资源管理器部署模型](../../virtual-machines/migration-classic-resource-manager-overview.md)。

有关详细信息，请参阅[将 Azure 门户中的单资源和多资源导出到模板](../templates/export-template-portal.md)。

## <a name="manage-access-to-resource-groups"></a>管理对资源组的访问

可以通过 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 管理对 Azure 中资源的访问权限。 有关详细信息，请参阅[使用 Azure CLI 添加或删除 Azure 角色分配](../../role-based-access-control/role-assignments-cli.md)。

## <a name="next-steps"></a>后续步骤

- 若要了解 Azure 资源管理器，请参阅 [Azure 资源管理器概述](overview.md)。
- 若要了解资源管理器模板语法，请参阅[了解 Azure 资源管理器模板的结构和语法](../templates/template-syntax.md)。
- 若要了解如何开发模板，请参阅[分步教程](../index.yml)。
- 若要查看 Azure 资源管理器模板架构，请参阅[模板参考](/azure/templates/)。