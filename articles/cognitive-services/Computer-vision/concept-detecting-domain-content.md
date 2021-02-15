---
title: 特定于域的内容-计算机视觉
titleSuffix: Azure Cognitive Services
description: 了解如何指定图像分类域以返回有关图像的更多详细信息。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5cd872d66088e165bfc8356ab6d96a0a6135a0e0
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94538302"
---
# <a name="detect-domain-specific-content"></a>检测特定于域的内容

除标记和高级分类外，计算机视觉还支持使用已经过专门数据训练的模型执行进一步特定于域的分析。

可通过两种方法使用特定于域的模型：使用模型本身（作用域分析）或用作分类功能的增强。

### <a name="scoped-analysis"></a>作用域分析

可以通过调用 [模型/ \<model\> /analyze](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API，仅使用所选的特定于域的模型来分析映像。

以下是 **models/celebrities/analyze** API 为给定图像返回的示例 JSON 响应：

![Satya Nadella 的站立微笑图](./images/satya.jpeg)

```json
{
  "result": {
    "celebrities": [{
      "faceRectangle": {
        "top": 391,
        "left": 318,
        "width": 184,
        "height": 184
      },
      "name": "Satya Nadella",
      "confidence": 0.99999856948852539
    }]
  },
  "requestId": "8217262a-1a90-4498-a242-68376a4b956b",
  "metadata": {
    "width": 800,
    "height": 1200,
    "format": "Jpeg"
  }
}
```

### <a name="enhanced-categorization-analysis"></a>增强版分类分析

特定于域的模型还可用于对常规图像分析进行补充。 可通过在 [Analyze](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API 调用的 *details* 参数中指定特定于域的模型，作为 [高级分类](concept-categorizing-images.md)的一部分执行此操作。

在这种情况下，首先会调用 86 类别分类分类器。 如果检测到的任何类别具有匹配的特定于域的模型，图像也会通过该模型并会添加结果。

以下 JSON 响应展示可如何在更广泛的分类分析中以 `detail` 节点的形式包含特定于域的分析。

```json
"categories":[
  {
    "name":"abstract_",
    "score":0.00390625
  },
  {
    "name":"people_",
    "score":0.83984375,
    "detail":{
      "celebrities":[
        {
          "name":"Satya Nadella",
          "faceRectangle":{
            "left":597,
            "top":162,
            "width":248,
            "height":248
          },
          "confidence":0.999028444
        }
      ],
      "landmarks":[
        {
          "name":"Forbidden City",
          "confidence":0.9978346
        }
      ]
    }
  }
]
```

## <a name="list-the-domain-specific-models"></a>列出特定于域的模型

目前，计算机视觉支持以下特定于域的模型：

| 名称 | 说明 |
|------|-------------|
| 名人 | 名人识别，支持属于 `people_` 类别的图像 |
| 地标 | 地标识别，支持属于 `outdoor_` 或 `building_` 类别的图像 |

调用 [Models](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20e) API 将返回此信息，以及每个模型可应用于的类别：

```json
{
  "models":[
    {
      "name":"celebrities",
      "categories":[
        "people_",
        "人_",
        "pessoas_",
        "gente_"
      ]
    },
    {
      "name":"landmarks",
      "categories":[
        "outdoor_",
        "户外_",
        "屋外_",
        "aoarlivre_",
        "alairelibre_",
        "building_",
        "建筑_",
        "建物_",
        "edifício_"
      ]
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

了解有关[对图像进行分类](concept-categorizing-images.md)的概念。
