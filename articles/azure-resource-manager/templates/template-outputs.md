---
title: 模板中的输出
description: 介绍如何在 Azure 资源管理器模板 (ARM 模板) 中定义输出值。
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: f8f13b6caf063cea79dc71775fb936f406a3ee6c
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964008"
---
# <a name="outputs-in-arm-templates"></a>ARM 模板中的输出

本文介绍如何在 Azure 资源管理器模板 (ARM 模板) 中定义输出值。 `outputs`需要从部署的资源返回值时，可使用。

每个输出值的格式必须与[数据类型](template-syntax.md#data-types)中的一种匹配。

## <a name="define-output-values"></a>定义输出值

以下示例演示如何返回公共 IP 地址的资源 ID：

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>条件输出

在 `outputs` 部分中，您可以有条件地返回值。 通常，在有 `condition` 条件地 `outputs` [部署](conditional-resource-deployment.md) 资源时，在中使用。 以下示例展示了如何根据是否部署了新的公共 IP 地址，有条件地返回公共 IP 地址的资源 ID：

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

有关条件输出的简单示例，请参阅[条件输出模板](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json)。

## <a name="dynamic-number-of-outputs"></a>动态输出数量

在某些情况下，创建模板时你不知道需要返回的值的实例数量。 您可以使用元素返回值的可变数量 `copy` 。

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

有关详细信息，请参阅 [ARM 模板中的输出迭代](copy-outputs.md)。

## <a name="linked-templates"></a>链接模板

若要从链接模板中检索输出值，请在父模板中使用 [reference](template-functions-resource.md#reference) 函数。 父模板中的语法为：

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

从链接模板获取输出属性时，属性名称不能包含短划线。

以下示例演示如何通过从链接模板检索值，在负载均衡器上设置 IP 地址。

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

不能在[嵌套模板](linked-templates.md#nested-template)的 outputs 节中使用 `reference` 函数。 若要返回嵌套模板中部署的资源的值，请将嵌套模板转换为链接模板。

## <a name="get-output-values"></a>获取输出值

部署成功时，将在部署结果中自动返回输出值。

若要从部署历史记录中获取输出值，可以使用脚本。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>示例模板

以下示例演示了使用输出的方案。

|模板  |说明  |
|---------|---------|
|[复制变量](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | 创建复杂变量，并输出这些值。 不部署任何资源。 |
|[公共 IP 地址](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | 创建公共 IP 地址并输出资源 ID。 |
|[负载均衡器](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | 链接到前面的模板。 创建负载均衡器时，请使用输出中的资源 ID。 |

## <a name="next-steps"></a>后续步骤

* 若要了解有关输出的可用属性，请参阅 [了解 ARM 模板的结构和语法](template-syntax.md)。
