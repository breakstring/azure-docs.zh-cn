---
title: 模板中的子资源
description: 介绍如何在 Azure 资源管理器模板中设置子资源的名称和类型 (ARM 模板) 。
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: a950d72751b829c0a2aa3ba5ca27316a0544d9cc
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2021
ms.locfileid: "97963906"
---
# <a name="set-name-and-type-for-child-resources"></a>设置子资源的名称和类型

子资源是只存在于另一资源的上下文内的资源。 例如，如果没有[虚拟机](/azure/templates/microsoft.compute/virtualmachines)，则[虚拟机扩展](/azure/templates/microsoft.compute/virtualmachines/extensions)不能存在。 扩展资源是虚拟机的子项。

每个父资源仅接受特定的资源类型作为子资源。 子资源的资源类型包括父资源的资源类型。 例如， `Microsoft.Web/sites/config` 和 `Microsoft.Web/sites/extensions` 都是的子资源 `Microsoft.Web/sites` 。 可接受的资源类型在父资源的 [模板架构](https://github.com/Azure/azure-resource-manager-schemas) 中指定。

在 Azure 资源管理器模板 (ARM 模板) ，可以在父资源内或父资源之外指定子资源。 以下示例显示子资源包括在父资源的资源属性中。

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

子资源总共只能定义五级。

下一示例显示子资源位于父资源外部。 如果父资源未部署在同一模板中，或者想要使用 [copy](copy-resources.md) 创建多个子资源，可以使用此方法。

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

为资源名称和类型提供的值因子资源是在父资源内部还是外部定义而异。

## <a name="within-parent-resource"></a>在父资源内部

在父资源类型内部定义时，请将类型和名称值格式化为一个不带斜杠的单词。

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

以下示例显示一个虚拟网络，有一个子网。 请注意，子网包含在虚拟网络的资源数组中。 名称设置为 **Subnet1**，类型设置为 **subnets**。 子资源被标记为依赖父资源，因为在部署子资源之前，必须存在父资源。

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "type": "subnets",
        "apiVersion": "2018-10-01",
        "name": "Subnet1",
        "location": "[parameters('location')]",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

完整的资源类型仍为 `Microsoft.Network/virtualNetworks/subnets` 。 可不提供 `Microsoft.Network/virtualNetworks/`，因为假设它继承父资源类型。

子资源名称设置为 **Subnet1**，但完整名称包含父名称。 可不提供 **VNet1**，因为假设它继承父资源。

## <a name="outside-parent-resource"></a>父资源外部

在父资源外定义时，可以将类型和带斜杠的格式设置为包含父类型和名称。

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

以下示例显示一个虚拟网络和子网，二者都在根级别进行定义。 请注意，子网不包含在虚拟网络的资源数组中。 名称设置为 **VNet1/Subnet1** ，类型设置为 `Microsoft.Network/virtualNetworks/subnets` 。 子资源被标记为依赖父资源，因为在部署子资源之前，必须存在父资源。

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "apiVersion": "2018-10-01",
    "location": "[parameters('location')]",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

## <a name="next-steps"></a>后续步骤

* 若要了解如何创建 ARM 模板，请参阅 [了解 arm 模板的结构和语法](template-syntax.md)。
* 若要了解引用资源时的资源名称格式，请参阅[引用函数](template-functions-resource.md#reference)。
