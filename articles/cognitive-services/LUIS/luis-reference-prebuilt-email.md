---
title: LUIS 预生成实体电子邮件参考
titleSuffix: Azure Cognitive Services
description: 本文包含了语言理解 (LUIS) 中的电子邮件预构建实体信息。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 806357670bead54658f0b501ca20473293275d58
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91533360"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>LUIS 应用的电子邮件预生成实体
电子邮件提取包括陈述中的整个电子邮件地址。 此实体已定型，因此不需要将包含电子邮件的陈述示例添加到应用程序意向中。 只有 `en-us` 语言区域中支持电子邮件实体。

## <a name="resolution-for-prebuilt-email"></a>预构建电子邮件解析

查询返回以下实体对象：

`please send the information to patti@contoso.com`

#### <a name="v3-response"></a>[V3 响应](#tab/V3)

以下 JSON 的 `verbose` 参数设置为 `false`：

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 详细响应](#tab/V3-verbose)

以下 JSON 的 `verbose` 参数设置为 `true`：

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ],
    "$instance": {
        "email": [
            {
                "type": "builtin.email",
                "text": "patti@contoso.com",
                "startIndex": 31,
                "length": 17,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2 响应](#tab/V2)

以下示例显示了 **builtin.email** 实体的解析。

```json
"entities": [
    {
        "entity": "patti@contoso.com",
        "type": "builtin.email",
        "startIndex": 31,
        "endIndex": 55,
        "resolution": {
        "value": "patti@contoso.com"
        }
    }
]
```
* * *

## <a name="next-steps"></a>后续步骤

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。

了解[数字](luis-reference-prebuilt-number.md)、[序号](luis-reference-prebuilt-ordinal.md)和[百分比](luis-reference-prebuilt-percentage.md)。
