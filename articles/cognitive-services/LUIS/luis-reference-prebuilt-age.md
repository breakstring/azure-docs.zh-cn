---
title: 存在时间预生成实体 - LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含了语言理解 (LUIS) 中的存在时间预构建实体信息。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.openlocfilehash: 4e1a6b1b6fb99a2786de06e89960e8480a5e1338
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91532918"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>LUIS 应用的存在时间预生成实体
预构建存在时间实体同时采用数字和天、周、月以及年捕获存在时间值。 此实体已定型，因此不需要将包含存在时间的陈述示例添加到应用程序意向中。 [许多语言区域](luis-reference-prebuilt-entities.md)都支持存在时间实体。

## <a name="types-of-age"></a>存在时间类型
存在时间托管在 [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) Github 存储库中

## <a name="resolution-for-prebuilt-age-entity"></a>预构建存在时间实体的解析



#### <a name="v3-response"></a>[V3 响应](#tab/V3)

以下 JSON 的 `verbose` 参数设置为 `false`：

```json
"entities": {
    "age": [
        {
            "number": 90,
            "unit": "Day"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 详细响应](#tab/V3-verbose)
以下 JSON 的 `verbose` 参数设置为 `true`：

```json
"entities": {
    "age": [
        {
            "number": 90,
            "unit": "Day"
        }
    ],
    "$instance": {
        "age": [
            {
                "type": "builtin.age",
                "text": "90 day old",
                "startIndex": 2,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor"
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[V2 响应](#tab/V2)

以下示例显示了 **builtin.age** 实体的解析。

```json
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
```
* * *

## <a name="next-steps"></a>后续步骤

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。

了解[货币](luis-reference-prebuilt-currency.md)、[datetimeV2](luis-reference-prebuilt-datetimev2.md) 和[维度](luis-reference-prebuilt-dimension.md)实体。
