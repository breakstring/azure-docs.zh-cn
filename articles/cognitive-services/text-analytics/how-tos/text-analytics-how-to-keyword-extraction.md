---
title: 使用文本分析 REST API 提取关键短语
titleSuffix: Azure Cognitive Services
description: 如何通过 Azure 认知服务使用文本分析 REST API 提取关键短语。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 12/17/2020
ms.author: aahi
ms.openlocfilehash: 91e10c25d2c3bef9c1ca20e3e5737a326d45997c
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654772"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>示例：如何使用文本分析提取关键短语

[关键短语提取 API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) 用于计算非结构化的文本，并针对每个 JSON 文档返回关键短语列表。

如果需要快速确定文档集中的要点，此功能十分有用。 例如，给定输入文本“The food was delicious and there were wonderful staff”，服务会返回谈话要点：“food”和“wonderful staff”。

有关详细信息，请参阅[支持的语言](../language-support.md)。

> [!TIP]
> * 文本分析还提供一个基于 Linux 的 Docker 容器映像，用于提取关键短语，因此可以在靠近数据的位置[安装并运行文本分析容器](text-analytics-how-to-install-containers.md)。
> * 你还可以使用端点 [异步](text-analytics-how-to-call-api.md) 使用此功能 `/analyze` 。

## <a name="preparation"></a>准备工作

提供的要处理的文本量越大，关键短语提取效果越好。 这恰好与情绪分析（文本量越小，效果越好）相反。 要从两个操作获取最佳结果，请考虑相应地重建输入。

必须拥有以下格式的 JSON 文档：ID、文本、语言

每个文档的大小必须为 5,120 个或更少的字符，每个集合最多可包含 1,000 个项目 (ID)。 集合在请求正文中提交。 以下示例例举了可能提交以进行关键短语提取的内容。 

有关请求和响应对象的详细信息，请参阅 [如何调用文本分析 API](text-analytics-how-to-call-api.md) 。  

### <a name="example-synchronous-request-object"></a>示例同步请求对象


```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

### <a name="example-asynchronous-request-object"></a>示例异步请求对象

从开始 `v3.1-preview.3` ，你可以使用终结点异步发送 NER 请求 `/analyze` 。


```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
    }
}
```

## <a name="step-1-structure-the-request"></a>步骤 1：构造请求

有关请求定义的信息，请参阅[如何调用文本分析 API](text-analytics-how-to-call-api.md)。 为方便起见，特重申以下几点：

+ 创建 POST 请求  。 查看此请求的 API 文档：[关键短语 API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases)。

+ 使用 Azure 上的文本分析资源或实例化的[文本分析容器](text-analytics-how-to-install-containers.md)设置 HTTP 终结点，以便提取关键短语。 如果你以同步方式使用 API，则必须 `/text/analytics/v3.0/keyPhrases` 在 URL 中包含。 例如：`https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`。

+ 设置请求头以包含文本分析操作的[访问密钥](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。

+ 在请求正文中，提供为此分析准备的 JSON 文档集合。

> [!Tip]
> 使用 [Postman](text-analytics-how-to-call-api.md) 或打开[文档](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases)中的“API 测试控制台”来构造请求并将其 POST 到该服务  。

## <a name="step-2-post-the-request"></a>步骤 2：发布请求

在收到请求时执行分析。 有关每分钟或每秒可以发送的请求的大小和数量的信息，请参阅概述中的 " [数据限制](../overview.md#data-limits) " 部分。

记住，该服务是无状态服务。 帐户中未存储任何数据。 结果会立即在响应中返回。

## <a name="step-3-view-results"></a>步骤 3：查看结果

所有 POST 请求都将返回 JSON 格式的响应，其中包含 ID 和检测到的属性。 返回的关键短语的顺序由模型在内部确定。

系统会立即返回输出。 可将结果流式传输到接受 JSON 的应用程序，或者将输出保存到本地系统上的文件中，然后将其导入到允许对数据进行排序、搜索和操作的应用程序。

下面显示的是从 v3.1-preview.2 终结点提取关键短语的输出示例：

### <a name="synchronous-result"></a>同步结果

```json
    {
       "documents":[
          {
             "id":"1",
             "keyPhrases":[
                "year",
                "trail",
                "trip",
                "views",
                "hike"
             ],
             "warnings":[]
          },
          {
             "id":"2",
             "keyPhrases":[
                "marked trails",
                "Worst hike",
                "goners"
             ],
             "warnings":[]
          },
          {
             "id":"3",
             "keyPhrases":[
                "trail",
                "small children",
                "family"
             ],
             "warnings":[]
          },
          {
             "id":"4",
             "keyPhrases":[
                "spectacular views",
                "trail",
                "Worth",
                "area"
             ],
             "warnings":[]
          },
          {
             "id":"5",
             "keyPhrases":[
                "places",
                "beautiful views",
                "favorite trail",
                "rest"
             ],
             "warnings":[]
          }
       ],
       "errors":[],
       "modelVersion":"2020-07-01"
    }

```
如上所述，分析器查找和放弃不重要的字词，并保留似乎是句子主语或宾语的字词或短语。

### <a name="asynchronous-result"></a>异步结果

如果将 `/analyze` 终结点用于异步操作，将收到一个包含发送到 API 的任务的响应。

```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```


## <a name="summary"></a>摘要

在本文中，你已了解使用认知服务中的文本分析进行关键短语提取的概念和工作流。 综上所述：

+ [关键短语提取 API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) 适用于所选语言。
+ 请求正文中的 JSON 文档包括 ID、文本和语言代码。
+ POST 请求发送到 `/keyphrases` 或 `/analyze` 终结点，使用个性化 [访问密钥和](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) 对订阅有效的终结点。
+ 响应输出包含每个文档 ID 的关键单词和短语，可以流式传输到接受 JSON 的任何应用，包括 Microsoft Office Excel 和 Power BI（仅举几例）。

## <a name="see-also"></a>另请参阅

 [文本分析概述](../overview.md)[常见问题解答 (FAQ)](../text-analytics-resource-faq.md)</br>
 [文本分析产品页](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>后续步骤

* [文本分析概述](../overview.md)
* [使用文本分析客户端库](../quickstarts/client-libraries-rest-api.md)
* [新增功能](../whats-new.md)
