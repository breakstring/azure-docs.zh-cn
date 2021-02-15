---
title: 使用排名显示答案 - 必应实体搜索
titleSuffix: Azure Cognitive Services
description: 了解如何使用排名显示必应实体搜索 API 返回的答案。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: b63c2e53f34ac86f8ddf5ad300c2465ee2d9f032
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365629"
---
# <a name="using-ranking-to-display-entity-search-results"></a>使用排名显示实体搜索结果  

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 **2020 年10月 30** 日起，需要按照 [此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程设置必应搜索的任何新实例。
> 在接下来的三年中，将支持使用认知服务进行预配的必应搜索 API，或者在企业协议结束后（以先发生者为准）。
> 有关迁移说明，请参阅 [必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

每个实体搜索响应包括一个 [RankingResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse) 答案，它指定必应实体搜索 API 返回的搜索结果的显示方式。 排名响应会将结果分组到两极、主线和边栏内容。 两极结果是最重要或突出的结果，应首先显示。 如果不以传统主线和边栏格式显示剩余结果，必须以比边栏内容更高的可见度提供主线内容。 
  
在每个组中，[项](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items)数组标识了内容必须显示的顺序。 每个项提供两种方法来标识答案中的结果。  
 

|字段 | 说明  |
|---------|---------|
|`answerType` 和 `resultIndex` | `answerType` 标识答案（实体或位置），`resultIndex` 标识该答案中的结果（例如某个实体）。 索引从 0 开始。|
|`value`    | `value` 包含一个 ID，它与答案或答案中结果的 ID 匹配。 答案或结果包含 ID，而不是同时包含。 |
  
使用 `answerType` 和 `resultIndex` 的过程分为两步。 首先，使用 `answerType` 来标识包含要显示的结果的答案。 然后，使用 `resultIndex` 对答案的结果进行索引，以获取要显示的结果。  (`answerType` 值是 [SearchResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) 对象中的字段的名称。 ) 如果您应该将所有答案的结果显示在一起，则排名响应项目不包含该 `resultIndex` 字段。

使用 ID 必须使排名 ID 与答案或其结果之一的 ID 匹配。 如果答案对象包含 `id` 字段，则同时显示答案的所有结果。 例如，如果 `Entities` 对象包含 `id` 字段，则同时显示所有实体项目。 如果 `Entities` 对象不包含 `id` 字段，则每个实体包含 `id` 字段，排名响应将实体与位置结果混合。  
  
## <a name="ranking-response-example"></a>排名响应示例

下面展示了一个示例 [RankingResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse)。
  
```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Jimi Hendrix"
  },
  "entities": { ... },
  "rankingResponse": {
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        },
        {
          "answerType": "Entities",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.1"
          }
        }
      ]
    }
  }
}
```

根据此排名响应，侧栏应显示与 Jimi Hendrix 相关的两个实体结果。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建单页 Web 应用](tutorial-bing-entities-search-single-page-app.md)