---
title: 快速入门：创建 Azure DB for PostgresSQL 灵活服务器 - ARM 模板
description: 本快速入门介绍如何使用 ARM 模板创建 Azure Database for PostgresSQL 灵活服务器。
author: mksuni
ms.service: postgresql
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: sumuth
ms.date: 10/23/2020
ms.openlocfilehash: 542528bb0a3f76705e61f28338ccf1460159871d
ms.sourcegitcommit: 003ac3b45abcdb05dc4406661aca067ece84389f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96749073"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-postgresql---flexible-server"></a>快速入门：使用 ARM 模板创建 Azure Database for PostgreSQL 灵活服务器

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

灵活服务器是一种托管服务，可用于在云中运行、管理和缩放具有高可用性的 PostgreSQL 数据库。 可使用 Azure 资源管理器模板（ARM 模板）来预配 PostgreSQL 灵活服务器，以便部署多个服务器或在一个服务器上部署多个数据库。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Azure 资源管理器是 Azure 的部署和管理服务。 它提供了一个管理层，用于在 Azure 帐户中创建、更新和删除资源。 部署后，可以使用访问控制、锁和标记等管理功能来保护和组织资源。 若要了解 Azure 资源管理器模板，请参阅[模板部署概述](../../azure-resource-manager/templates/overview.md)。

## <a name="prerequisites"></a>先决条件

具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/)。

## <a name="review-the-template"></a>查看模板

Azure Database for PostgresSQL 服务器是某区域中一个或多个数据库的父资源。 它提供应用于其数据库的管理策略范围：登录名、防火墙、用户、角色和配置。

创建一个 postgres-flexible-server-template.json 文件，并将以下 JSON 脚本复制到该文件中。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "administratorLogin": {
      "type": "String"
    },
    "administratorLoginPassword": {
      "type": "SecureString"
    },
    "location": {
      "type": "String"
    },
    "serverName": {
      "type": "String"
    },
    "serverEdition": {
      "type": "String"
    },
    "storageSizeMB": {
      "type": "Int"
    },
    "haEnabled": {
      "type": "string"
    },
    "availabilityZone": {
      "type": "String"
    },
    "version": {
      "type": "String"
    },
    "tags": {
      "defaultValue": {},
      "type": "Object"
    },
    "firewallRules": {
      "defaultValue": {},
      "type": "Object"
    },
    "vnetData": {
      "defaultValue": {},
      "type": "Object"
    },
    "backupRetentionDays": {
      "type": "Int"
    }
  },
  "variables": {
    "api": "2020-02-14-privatepreview",
    "firewallRules": "[parameters('firewallRules').rules]",
    "publicNetworkAccess": "[if(empty(parameters('vnetData')), 'Enabled', 'Disabled')]",
    "vnetDataSet": "[if(empty(parameters('vnetData')), json('{ \"subnetArmResourceId\": \"\" }'), parameters('vnetData'))]",
    "finalVnetData": "[json(concat('{ \"subnetArmResourceId\": \"', variables('vnetDataSet').subnetArmResourceId, '\"}'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/flexibleServers",
      "apiVersion": "[variables('api')]",
      "name": "[parameters('serverName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_D4ds_v4",
        "tier": "[parameters('serverEdition')]"        
      },
      "tags": "[parameters('tags')]",
      "properties": {
        "version": "[parameters('version')]",
        "administratorLogin": "[parameters('administratorLogin')]",
        "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
        "publicNetworkAccess": "[variables('publicNetworkAccess')]",
        "DelegatedSubnetArguments": "[if(empty(parameters('vnetData')), json('null'), variables('finalVnetData'))]",
        "haEnabled": "[parameters('haEnabled')]",
        "storageProfile": {
          "storageMB": "[parameters('storageSizeMB')]",
          "backupRetentionDays": "[parameters('backupRetentionDays')]"
        },
        "availabilityZone": "[parameters('availabilityZone')]"
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-08-01",
      "name": "[concat('firewallRules-', copyIndex())]",
      "dependsOn": [
        "[concat('Microsoft.DBforPostgreSQL/flexibleServers/', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.DBforPostgreSQL/flexibleServers/firewallRules",
              "name": "[concat(parameters('serverName'),'/',variables('firewallRules')[copyIndex()].name)]",
              "apiVersion": "[variables('api')]",
              "properties": {
                "StartIpAddress": "[variables('firewallRules')[copyIndex()].startIPAddress]",
                "EndIpAddress": "[variables('firewallRules')[copyIndex()].endIPAddress]"
              }
            }
          ]
        }
      },
      "copy": {
        "name": "firewallRulesIterator",
        "count": "[if(greater(length(variables('firewallRules')), 0), length(variables('firewallRules')), 1)]",
        "mode": "Serial"
      },
      "condition": "[greater(length(variables('firewallRules')), 0)]"
    }
  ]
}
```

该模板中定义了以下资源：

- Microsoft.DBforPostgreSQL/flexibleServers

## <a name="deploy-the-template"></a>部署模板

从以下 PowerShell 代码块中选择“试用”以打开 Azure Cloud Shell。

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for PostgreSQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for PostgreSQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateFile "D:\Azure\Templates\EngineeringSite.json
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```
---

## <a name="review-deployed-resources"></a>查看已部署的资源

按照以下步骤验证服务器是否已在 Azure 中创建。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

1. 在 [Azure 门户](https://portal.azure.com)中，搜索并选择“Azure Database for PostgreSQL 灵活服务器(预览版)”。
1. 在数据库列表中，选择新服务器来查看其“概述”页，从而管理该服务器。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

必须输入新服务器的名称才能查看 Azure Database for PostgreSQL 灵活服务器的详细信息。

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for PostgreSQL server"
Get-AzResource -ResourceType "Microsoft.DBforPostgreSQL/flexibleServers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[CLI](#tab/CLI)

必须输入新服务器的名称和资源组才能查看 Azure Database for PostgreSQL 灵活服务器的详细信息。

```azurecli-interactive
echo "Enter your Azure Database for PostgreSQL Flexible Server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for PostgreSQL Flexible Server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DBforPostgreSQL/flexibleServers"
```

---

## <a name="clean-up-resources"></a>清理资源

如果希望转到[后续步骤](#next-steps)，请保留此资源组、服务器和单一数据库。 后续步骤展示了如何使用各种方法连接和查询数据库。

若要删除资源组，请执行以下操作：

# <a name="portal"></a>[Portal](#tab/azure-portal)

在[门户](https://portal.azure.com)中，选择要删除的资源组。

1. 选择“删除资源组”。
1. 若要确认删除，请键入资源组的名称

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```
----

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用转储和还原迁移数据库](../howto-migrate-using-dump-and-restore.md)
