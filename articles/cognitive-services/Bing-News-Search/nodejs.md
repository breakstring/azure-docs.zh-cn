---
title: 快速入门：使用 Node.js 执行新闻搜索 - 必应新闻搜索 REST API
titleSuffix: Azure Cognitive Services
description: 使用本快速入门，通过 Node.js 将请求发送到必应新闻搜索 REST API，并接收 JSON 响应。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-js
ms.openlocfilehash: a3241fc72fc1add8381f7681b4df6df9173683ac
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351310"
---
# <a name="quickstart-perform-a-news-search-using-nodejs-and-the-bing-news-search-rest-api"></a>快速入门：使用 Node.js 和必应新闻搜索 REST API 执行新闻搜索

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

根据此快速入门中的说明对必应资讯搜索 API 进行第一次调用。 这个简单的 JavaScript 应用程序会向 API 发送一个搜索查询并显示 JSON 响应。

虽然此应用程序采用 JavaScript 编写且以 Node.js 运行，但 API 是一种 RESTful Web 服务，可与大多数编程语言兼容。

该示例的源代码可在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingNewsSearchv7.js) 上获得。

## <a name="prerequisites"></a>先决条件

* 最新版本的 [Node.js](https://nodejs.org/en/download/)。
* [JavaScript 请求库](https://github.com/request/request)。

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

1. 在最喜爱的 IDE 或编辑器中创建新的 JavaScript 文件，并设置严格性和 HTTPS 要求。

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. 为 API 终结点、新闻 API 搜索路径、订阅密钥和搜索词创建变量。 你可以使用以下代码中的全局终结点，或者使用资源的 Azure 门户中显示的[自定义子域](../../cognitive-services/cognitive-services-custom-subdomains.md)终结点。 

    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/news/search';
    let term = 'Microsoft';
    ```

## <a name="handle-and-parse-the-response"></a>处理和分析响应

1. 定义一个名为 `response_handler` 的函数，该函数使用 HTTP 调用 `response` 作为参数。 

   在接下来的步骤中，将代码添加到此函数。

2. 定义一个包含 JSON 响应的正文的变量。  

    ```javascript
    let response_handler = function (response) {
        let body = '';
    };
    ```

3. 调用 `data` 标志时，存储响应的正文。

    ```javascript
    response.on('data', function (d) {
        body += d;
    });
    ```

3. 当通过信号发出了 `end` 标志时，可以查看 JSON 和标头。

    ```javascript
    response.on('end', function () {
        console.log('\nRelevant Headers:\n');
        for (var header in response.headers)
            // header keys are lower-cased by Node.js
            if (header.startsWith("bingapis-") || header.startsWith("x-msedge-"))
                 console.log(header + ": " + response.headers[header]);
        body = JSON.stringify(JSON.parse(body), null, '  ');
        console.log('\nJSON Response:\n');
        console.log(body);
     });
    ```

## <a name="example-json-response"></a>示例 JSON 响应

在 JSON 中返回成功的响应，如以下示例所示： 

```json
{
   "_type": "News",
   "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft",
   "totalEstimatedMatches": 36,
   "sort": [
      {
         "name": "Best match",
         "id": "relevance",
         "isSelected": true,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft"
      },
      {
         "name": "Most recent",
         "id": "date",
         "isSelected": false,
         "url": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/news\/search?q=Microsoft&sortby=date"
      }
   ],
   "value": [
      {
         "name": "Microsoft to open flagship London brick-and-mortar retail store",
         "url": "http:\/\/www.contoso.com\/article\/microsoft-to-open-flagshi...",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.F9E4A49EC010417...",
               "width": 220,
               "height": 146
            }
         },
         "description": "After years of rumors about Microsoft opening a brick-and-mortar...", 
         "about": [
           {
             "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entiti...", 
             "name": "Microsoft"
           }, 
           {
             "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entit...", 
             "name": "London"
           }
         ], 
         "provider": [
           {
             "_type": "Organization", 
             "name": "Contoso"
           }
         ], 
          "datePublished": "2017-09-21T21:16:00.0000000Z", 
          "category": "ScienceAndTechnology"
      }, 

      . . .
      
      {
         "name": "Microsoft adds Availability Zones to its Azure cloud platform",
         "url": "https:\/\/contoso.com\/2017\/09\/21\/microsoft-adds-availability...",
         "image": {
            "thumbnail": {
               "contentUrl": "https:\/\/www.bing.com\/th?id=ON.0AE7595B9720...",
               "width": 700,
               "height": 466
            }
         },
         "description": "Microsoft has begun adding Availability Zones to its...",
         "about": [
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/a093e9b...",
               "name": "Microsoft"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/cf3abf7d-e379-...",
               "name": "Windows Azure"
            },
            {
               "readLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/entities\/9cdd061c-1fae-d0...",
               "name": "Cloud"
            }
         ],
         "provider": [
            {
               "_type": "Organization",
               "name": "Contoso"
            }
         ],
         "datePublished": "2017-09-21T09:01:00.0000000Z",
         "category": "ScienceAndTechnology"
      }
   ]
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建单页 Web 应用](tutorial-bing-news-search-single-page-app.md)