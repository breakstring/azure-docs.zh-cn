---
title: Azure 资源管理器：创建单一数据库
description: 使用 Azure 资源管理器模板在 Azure SQL 数据库中创建单一数据库。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: subject-armqs sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: sstein
ms.date: 06/24/2020
ms.openlocfilehash: b7883144509760c9670decd50062d2595b8dc495
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187079"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-an-arm-template"></a>快速入门：使用 ARM 模板在 Azure SQL 数据库中创建单一数据库

在 Azure SQL 数据库中创建数据库时，创建[单一数据库](single-database-overview.md)是最快速且最简单的选项。 本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）创建单一数据库。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-database%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，可以[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="review-the-template"></a>查看模板

单一数据库有一组通过两个[购买模型](purchasing-models.md)中的一个定义的计算、内存和存储资源。 创建单一数据库时，还需要定义一个[服务器](logical-servers.md)来管理它并将它放置在指定区域的 [Azure 资源组](../../active-directory-b2c/overview.md)中。

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-sql-database/)。

:::code language="json" source="~/quickstart-templates/101-sql-database/azuredeploy.json":::

该模板中定义了以下资源：

- [**Microsoft.Sql/servers**](/azure/templates/microsoft.sql/servers)
- [Microsoft.Sql/servers/databases](/azure/templates/microsoft.sql/servers/databases)

可以在 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular)中找到更多 Azure SQL 数据库模板示例。

## <a name="deploy-the-template"></a>部署模板

从以下 PowerShell 代码块中选择“试用”以打开 Azure Cloud Shell。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sql-database/azuredeploy.json" -administratorLogin $adminUser -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="validate-the-deployment"></a>验证部署

若要查询数据库，请参阅[查询数据库](single-database-create-quickstart.md#query-the-database)。

## <a name="clean-up-resources"></a>清理资源

如果希望转到[后续步骤](#next-steps)，请保留此资源组、服务器和单一数据库。 后续步骤展示了如何使用各种方法连接和查询数据库。

若要删除资源组，请执行以下操作：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>后续步骤

- 创建服务器级防火墙规则，以便通过本地或远程工具连接到单一数据库。 有关详细信息，请参阅[创建服务器级防火墙规则](firewall-create-server-level-portal-quickstart.md)。
- 创建服务器级防火墙规则后，使用多种不同的工具和语言[连接和查询](connect-query-content-reference-guide.md)数据库。
  - [使用 SQL Server Management Studio 连接和查询](connect-query-ssms.md)
  - [使用 Azure Data Studio 连接和查询](/sql/azure-data-studio/quickstart-sql-database?toc=%2fazure%2fsql-database%2ftoc.json)
- 若要使用 Azure CLI 创建单一数据库，请参阅 [Azure CLI 示例](az-cli-script-samples-content-guide.md)。
- 若要使用 Azure PowerShell 创建单一数据库，请参阅 [Azure PowerShell 示例](powershell-script-content-guide.md)。
- 若要了解如何创建 ARM 模板，请参阅[创建第一个模板](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)。