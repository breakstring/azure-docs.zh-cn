---
title: 定义变量的多个实例
description: 使用 Azure 资源管理器模板中的复制操作 (ARM 模板) 在创建变量时循环访问多次。
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: b8acd85659b843cb482e1ccc61e28da03431db1b
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905887"
---
# <a name="variable-iteration-in-arm-templates"></a>ARM 模板中的变量迭代

本文介绍如何在 Azure 资源管理器模板 (ARM 模板) 中为变量创建多个值。 通过将 `copy` 元素添加到模板的 variables 节，你可以在部署期间动态设置变量的项数。 还可以避免重复模板语法。

还可以将 copy 用于[资源](copy-resources.md)、[资源中的属性](copy-properties.md)，以及[输出](copy-outputs.md)。

## <a name="syntax"></a>语法

copy 元素采用以下常规格式：

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

`name`属性是标识循环的任何值。 `count`属性指定该变量所需的迭代数。

`input`属性指定要重复的属性。 可以创建一个由 `input` 属性中的值构造的元素数组。 它可以是单个属性（例如字符串），也可以是具有多个属性的对象。

## <a name="copy-limits"></a>复制限制

count 不能超过 800。

count 不能为负数。 如果使用最新版本的 Azure CLI、PowerShell 或 REST API 部署模板，则它可以为零。 具体而言，必须使用：

* Azure PowerShell 2.6 或更高版本
* Azure CLI 2.0.74 或更高版本
* REST API 版本 2019-05-10 或更高版本
* [链接的部署](linked-templates.md)必须将 API 版本 2019-05-10 或更高版本用于部署资源类型

更早版本的 PowerShell、CLI 和 REST API 不支持将 count 设为零。

## <a name="variable-iteration"></a>变量迭代

以下示例展示了如何创建字符串值的数组：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
     },
    "variables": {
        "copy": [
            {
                "name": "stringArray",
                "count": "[parameters('itemCount')]",
                "input": "[concat('item', copyIndex('stringArray', 1))]"
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('stringArray')]"
        }
    }
}
```

前面的模板返回包含以下值的数组：

```json
[
    "item1",
    "item2",
    "item3",
    "item4",
    "item5"
]
```

下一个示例演示如何创建包含三个属性（ `name` 、和）的对象数组 `diskSizeGB` `diskIndex` 。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "copy": [
            {
                "name": "objectArray",
                "count": "[parameters('itemCount')]",
                "input": {
                    "name": "[concat('myDataDisk', copyIndex('objectArray', 1))]",
                    "diskSizeGB": "1",
                    "diskIndex": "[copyIndex('objectArray')]"
                }
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('objectArray')]"
        }
    }
}
```

前面的示例返回包含以下值的数组：

```json
[
    {
        "name": "myDataDisk1",
        "diskSizeGB": "1",
        "diskIndex": 0
    },
    {
        "name": "myDataDisk2",
        "diskSizeGB": "1",
        "diskIndex": 1
    },
    {
        "name": "myDataDisk3",
        "diskSizeGB": "1",
        "diskIndex": 2
    },
    {
        "name": "myDataDisk4",
        "diskSizeGB": "1",
        "diskIndex": 3
    },
    {
        "name": "myDataDisk5",
        "diskSizeGB": "1",
        "diskIndex": 4
    }
]
```

> [!NOTE]
> 变量迭代支持偏移量参数。 偏移量必须晚于迭代的名称，如 `copyIndex('diskNames', 1)` 。 如果未提供偏移量值，对于第一个实例，它将默认为 0。
>

还可以在 `copy` 变量中使用元素。 以下示例创建一个使用数组作为其值之一的对象。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "topLevelObject": {
            "sampleProperty": "sampleValue",
            "copy": [
                {
                    "name": "disks",
                    "count": "[parameters('itemCount')]",
                    "input": {
                        "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
                        "diskSizeGB": "1",
                        "diskIndex": "[copyIndex('disks')]"
                    }
                }
            ]
        }
    },
    "resources": [],
    "outputs": {
        "objectResult": {
            "type": "object",
            "value": "[variables('topLevelObject')]"
        }
    }
}
```

前面的示例返回包含以下值的对象：

```json
{
    "sampleProperty": "sampleValue",
    "disks": [
        {
            "name": "myDataDisk1",
            "diskSizeGB": "1",
            "diskIndex": 0
        },
        {
            "name": "myDataDisk2",
            "diskSizeGB": "1",
            "diskIndex": 1
        },
        {
            "name": "myDataDisk3",
            "diskSizeGB": "1",
            "diskIndex": 2
        },
        {
            "name": "myDataDisk4",
            "diskSizeGB": "1",
            "diskIndex": 3
        },
        {
            "name": "myDataDisk5",
            "diskSizeGB": "1",
            "diskIndex": 4
        }
    ]
}
```

下一个示例显示了可与变量一起使用的不同方法 `copy` 。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="example-templates"></a>示例模板

以下示例显示了为一个变量创建多个值的常见方案。

|模板  |说明  |
|---------|---------|
|[复制变量](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |演示循环访问变量的不同方式。 |
|[多项安全规则](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |将多个安全规则部署到网络安全组。 它从参数构造安全规则。 有关参数，请参阅[多个 NSG 参数文件](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)。 |

## <a name="next-steps"></a>后续步骤

* 若要学习教程，请参阅 [教程：使用 ARM 模板创建多个资源实例](template-tutorial-create-multiple-instances.md)。
* 有关 copy 元素的其他用法，请参阅：
  * [ARM 模板中的资源迭代](copy-resources.md)
  * [ARM 模板中的属性迭代](copy-properties.md)
  * [ARM 模板中的输出迭代](copy-outputs.md)
* 如果要了解有关模板的各个部分的信息，请参阅 [了解 ARM 模板的结构和语法](template-syntax.md)。
* 若要了解如何部署模板，请参阅 [利用 ARM 模板部署资源和 Azure PowerShell](deploy-powershell.md)。
