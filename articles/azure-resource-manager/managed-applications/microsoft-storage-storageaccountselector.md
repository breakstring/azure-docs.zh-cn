---
title: StorageAccountSelector UI 元素
description: 介绍了 Azure 门户的 Microsoft.Storage.StorageAccountSelector UI 元素。
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: fe70c42387e9dedea8943b5dcce04a1f9ded5190
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87033271"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft.Storage.StorageAccountSelector UI 元素

一个用于选择新的或现有存储帐户的控件。

## <a name="ui-sample"></a>UI 示例

此控件显示默认值。

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft-storage-storageaccountselector.png)

此控件允许用户创建一个新的存储帐户或选择一个现有存储帐户。

![Microsoft.Storage.StorageAccountSelector 新](./media/managed-application-elements/microsoft-storage-storageaccountselector-new.png)

## <a name="schema"></a>架构

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.StorageAccountSelector",
  "label": "Storage account",
  "toolTip": "",
  "defaultValue": {
    "name": "storageaccount01",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "options": {
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>示例输出

```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="remarks"></a>备注

- 如果指定，则会自动验证 `defaultValue.name` 的唯一性。 如果存储帐户名称不唯一，则用户必须指定一个不同的名称或选择现有存储帐户。
- `defaultValue.type` 的默认值为 **Premium_LRS**。
- 将隐藏未在 `constraints.allowedTypes` 中指定的任何类型，会显示未在 `constraints.excludedTypes` 中指定的任何类型。 `constraints.allowedTypes` 和 `constraints.excludedTypes` 都是可选的，并且不能同时使用。
- 如果 `options.hideExisting` 为 **true**，则用户无法选择现有存储帐户。 默认值是 **false**秒。

## <a name="next-steps"></a>后续步骤
* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
