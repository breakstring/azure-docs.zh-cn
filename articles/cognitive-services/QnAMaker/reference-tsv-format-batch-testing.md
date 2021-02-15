---
title: Batch 测试 TSV 格式-QnA Maker
titleSuffix: Azure Cognitive Services
description: 了解用于批处理测试的 TSV 格式
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.openlocfilehash: 0ed948b4bf69fc672a59a7825279a12868d10521
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132119"
---
# <a name="batch-testing-tsv-format"></a>批处理测试 TSV 格式

批处理测试在 [源代码](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting) 中提供，或作为 [可下载的可执行文件压缩](https://aka.ms/qna_btzip)。 用于运行批处理测试的命令的格式为：

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|预期值|
|--|--|
|1|用[tsv 输入字段](#tsv-input-fields)进行格式设置的 tsv 文件的名称|
|2|终结点的 URI，通过 QnA Maker 门户的 "发布" 页中的-HOST。|
|3|终结点-密钥，位于 QnA Maker 门户的 "发布" 页上。|
|4|批处理测试结果创建的 tsv 文件的名称。|

使用以下信息来了解和实现用于批处理测试的 TSV 格式。 

## <a name="tsv-input-fields"></a>TSV 输入字段

|TSV 输入文件字段|说明|
|--|--|
|KBID|在 "发布" 页上找到你的 KB ID。|
|问题|用户要输入的问题。|
|元数据标记|可选|
|Top 参数|可选| 
|预期的答案 ID|可选|

![用于批处理测试的 TSV 文件的输入格式。](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>TSV 输出字段 

|TSV 输出文件参数|注意|
|--|--|
|KBID|在 "发布" 页上找到你的 KB ID。|
|问题|输入文件中输入的问题。|
|答案|知识库的最佳答案。|
|应答 ID|应答 ID|
|分数|答案的预测分数。 |
|元数据标记|与返回的答卷关联|
|预期的答案 ID|可选 (仅当给定了预期的应答 ID 时) |
|判断标签|可选，值可以是：正确或不正确的 (仅当给定了预期的答案时) |
