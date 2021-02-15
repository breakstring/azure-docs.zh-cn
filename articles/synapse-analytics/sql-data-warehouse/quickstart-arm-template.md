---
title: 使用 Azure 资源管理器模板创建专用 SQL 池（之前称为 SQL DW）
description: 了解如何使用 Azure 资源管理器模板创建 Azure Synapse Analytics SQL 池。
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 06/09/2020
ms.openlocfilehash: 70adb7409c44a79345a192df173a1a073cc9b7dd
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460744"
---
# <a name="quickstart-create-an-azure-synapse-analytics-dedicated-sql-pool-formerly-sql-dw-by-using-an-arm-template"></a>快速入门：使用 ARM 模板创建 Azure Synapse Analytics 专用 SQL 池（之前称为 SQL DW）

使用此 Azure 资源管理器模板（ARM 模板）创建专用 SQL 池（之前称为 SQL DW），并启用透明数据加密。 专用 SQL 池（之前称为 SQL DW）是指 Azure Synapse 中正式发布的企业数据仓库功能。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/201-sql-data-warehouse-transparent-encryption-create/)。

:::code language="json" source="~/quickstart-templates/201-sql-data-warehouse-transparent-encryption-create/azuredeploy.json":::

该模板定义一个资源：

- [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)

## <a name="deploy-the-template"></a>部署模板

1. 选择下图登录到 Azure 并打开模板。 此模板可创建专用 SQL 池（之前称为 SQL DW）。
   
   [![部署到 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

1. 输入或更新以下值：

   * 订阅：选择 Azure 订阅。
   * 资源组： 选择“新建”，输入资源组的唯一名称，然后选择“确定”。 新的资源组会促进资源清理。
   * **区域**：选择区域。  例如“美国中部”。
   * SQL Server 名称：接受默认名称，或为 SQL Server 名称输入新名称。
   * SQL 管理员登录：输入 SQL Server 的管理员用户名。
   * SQL 管理员密码：输入 SQL Server 的管理员密码。
   * 数据仓库名称：输入专用 SQL 池名称。
   * 透明数据加密：接受默认值“已启用”。 
   * 服务级别目标：接受默认值“DW400c”。
   * 位置：接受资源组的默认位置。
   * 审阅并创建：选中。
   * **创建**：选中。

## <a name="review-deployed-resources"></a>查看已部署的资源

可以使用 Azure 门户来检查已部署的资源，也可以使用 Azure CLI 或 Azure PowerShell 脚本列出已部署的资源。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group where your dedicated SQL pool (formerly SQL DW) exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your dedicated SQL pool (formerly SQL DW) account exists"
(Get-AzResource -ResourceType "Microsoft.Sql/servers/databases" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>清理资源

不再需要时，可使用 Azure CLI 或 Azure PowerShell 删除资源组：

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>后续步骤

在本快速入门中，你使用 ARM 模板创建了专用 SQL 池（之前称为 SQL DW），并验证了部署。 若要详细了解 Azure Synapse Analytics 和 Azure 资源管理器，请参阅以下文章。

- 阅读 [Azure Synapse Analytics 概述](sql-data-warehouse-overview-what-is.md)
- 了解有关 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)的详细信息
- [创建并部署第一个 ARM 模板](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)
