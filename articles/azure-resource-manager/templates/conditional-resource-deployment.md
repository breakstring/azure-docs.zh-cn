---
title: 使用模板进行条件部署
description: 介绍如何在 Azure 资源管理器模板 (ARM 模板) 中有条件地部署资源。
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 5650f7fb9f1483f2dc7059607732ecc68cbb7b9d
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934775"
---
# <a name="conditional-deployment-in-arm-templates"></a>使用 ARM 模板进行条件部署

有时，你需要根据需要在 Azure 资源管理器模板 (ARM 模板) 中部署资源。 使用 `condition` 元素指定是否部署资源。 此元素的值将解析为 true 或 false。 如果值为 true，则创建了该资源。 如果值为 false，则未创建该资源。 值只能应用到整个资源。

> [!NOTE]
> 条件部署不会级联到[子资源](child-resource-name-type.md)。 如果要有条件地部署资源及其子资源，需要对每种资源类型应用相同的条件。

## <a name="new-or-existing-resource"></a>新资源或现有资源

可以使用条件部署来创建新资源或使用现有资源。 下面的示例演示如何使用 `condition` 来部署新的存储帐户或使用现有的存储帐户。

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "2017-06-01",
  "name": "[variables('storageAccountName')]",
  "location": "[parameters('location')]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

如果将参数 `newOrExisting` 设置为 **new**，则条件的计算结果为 true。 将部署存储帐户。 但是，当 `newOrExisting` 设置为 " **现有**" 时，条件的计算结果为 false，并且不部署存储帐户。

有关使用 `condition` 元素的完整示例模板，请参阅[具有新的或现有虚拟网络、存储和公共 IP 的 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions)。

## <a name="allow-condition"></a>允许条件

可以传入指示是否允许条件的参数值。 以下示例部署 SQL Server，并可选择允许 Azure IP。

```json
{
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2015-05-01-preview",
  "name": "[parameters('serverName')]",
  "location": "[parameters('location')]",
  "properties": {
    "administratorLogin": "[parameters('administratorLogin')]",
    "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
    "version": "12.0"
  },
  "resources": [
    {
      "condition": "[parameters('allowAzureIPs')]",
      "type": "firewallRules",
      "apiVersion": "2015-05-01-preview",
      "name": "AllowAllWindowsAzureIps",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Sql/servers/', parameters('serverName'))]"
      ],
      "properties": {
        "endIpAddress": "0.0.0.0",
        "startIpAddress": "0.0.0.0"
      }
    }
  ]
}
```

有关完整的模板，请参阅 [Azure SQL 逻辑服务器](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server)。

## <a name="runtime-functions"></a>运行时函数

如果对条件性部署的资源使用 [reference](template-functions-resource.md#reference) 或 [list](template-functions-resource.md#list) 函数，则会对该函数进行评估，即使资源尚未部署。 如果该函数引用某个不存在的资源，则会出现错误。

请使用 [if](template-functions-logical.md#if) 函数，以确保仅当资源已部署时，才根据条件评估函数。 有关使用和的示例模板的示例模板，请参阅 [if 函数](template-functions-logical.md#if) `if` `reference` 。

[将资源设置为依赖于](define-resource-dependency.md)条件资源，这与设置任何其他资源完全一样。 条件资源未部署时，Azure 资源管理器会自动将其从所需依赖项中删除。

## <a name="complete-mode"></a>完整模式

如果部署的模板具有 [完整模式](deployment-modes.md) 并且资源未部署，因为 `condition` 计算结果为 false，则结果取决于用于部署模板的 REST API 版本。 如果使用 2019-05-10 之前的版本，则 **不会删除** 该资源。 如果使用 2019-05-10 或更高版本，则 **会删除** 该资源。 最新版本的 Azure PowerShell 和 Azure CLI 在条件为 false 时会删除该资源。

## <a name="next-steps"></a>后续步骤

* 有关涵盖条件部署的 Microsoft Learn 模块，请参阅 [使用高级 ARM 模板功能管理复杂的云部署](/learn/modules/manage-deployments-advanced-arm-template-features/)。
* 有关创建模板的建议，请参阅 [ARM 模板的最佳做法](template-best-practices.md)。
* 要创建资源的多个实例，请参阅 [ARM 模板中的资源迭代](copy-resources.md)。
