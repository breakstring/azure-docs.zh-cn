---
title: 词对齐 - 翻译器
titleSuffix: Azure Cognitive Services
description: 若要接收对齐信息，请使用 Translate 方法，并包含可选的 includeAlignment 参数。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 8368ca0ca4c3f2f0ab3cb88a5d54295d71451636
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898013"
---
# <a name="how-to-receive-word-alignment-information"></a>如何接收词对齐信息

## <a name="receiving-word-alignment-information"></a>接收词对齐信息
若要接收对齐信息，请使用 Translate 方法，并包含可选的 includeAlignment 参数。

## <a name="alignment-information-format"></a>对齐信息格式
对齐将作为以下格式的字符串值返回给源的每个词。 每个词的信息由一个空格分隔，其中包括非空格分隔的语言（脚本），比如中文：

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

对齐字符串示例：“0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21”。

换而言之，冒号分隔开始和结束索引，连字符分隔语言，空格分隔词。 一个单词可能与另一种语言中的 0 个、1 个或多个单词比对，而比对的词可能是不连续的。 当没有可用的对齐信息时，Alignment 元素将为空。 在这种情况下，该方法不会返回任何错误。

## <a name="restrictions"></a>限制
目前，仅针对语言对的子集返回对齐：
* 从英语到任何其他语言；
* 从任何其他语言到英语，简体中文、繁体中文和拉脱维亚语到英语除外
* 从日语到韩语或从韩语到日语；如果句子是预录翻译，则不会收到对齐信息。 预录翻译示例有“This is a test”、“I love you”，以及其他高频率句子。

## <a name="example"></a>示例

示例 JSON

```json
[
  {
    "translations": [
      {
        "text": "Kann ich morgen Ihr Auto fahren?",
        "to": "de",
        "alignment": {
          "proj": "0:2-0:3 4:4-5:7 6:10-25:30 12:15-16:18 17:19-20:23 21:28-9:14 29:29-31:31"
        }
      }
    ]
  }
]
```
