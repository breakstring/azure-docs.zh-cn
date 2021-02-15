---
title: 从 Web 获取图像 - 必应图像搜索 API
titleSuffix: Azure Cognitive Services
description: 使用必应图像搜索 API 在 Web 中搜索并获取相关图像。
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 988a1332d03bf2c9563ab0576f7a20ee6b0615aa
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96342050"
---
# <a name="get-images-from-the-web-with-the-bing-image-search-api"></a>使用必应图像搜索 API 从 Web 获取图像

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

使用必应图像搜索 REST API 时，可以通过发送以下 GET 请求从 Web 获取与你的搜索词相关的图像：

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

使用用于 url 编码搜索词的 [q](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) 查询参数。 例如，如果输入 *sailing dinghies*，系统会将 `q` 设置为 `sailing+dinghies` 或 `sailing%20dinghies`。

> [!IMPORTANT]
> * 所有请求必须从服务器发出，不得从客户端发出。
> * 如果是首次调用任何必应搜索 API，请勿包括客户端 ID 标头。 只有在以前调用过必应 API 且该 API 针对用户和设备组合返回了客户端 ID 的情况下，才包括客户端 ID。

## <a name="get-images-from-a-specific-web-domain"></a>从特定 Web 域获取图像

若要从特定的域获取图像，请使用 [site:](/previous-versions/bing/search/ff795613(v=msdn.10)) 查询运算符。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> 当查询使用 `site:` 运算符时，不管 [safeSearch](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch) 设置如何，对查询的响应都可能包含成人内容。 只有在知道域内容的情况下，才使用 `site:`。

## <a name="filter-images"></a>筛选图像

 默认情况下，图像搜索 API 返回与查询相关的所有图像。 若要筛选必应返回的图像（例如，只返回背景透明的图像或特定大小的图像），请使用以下查询参数：

* [方位](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#aspect)—按纵横比筛选图像 (例如，) 的标准或宽屏幕图像。
* [颜色](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#color)—按基准颜色或黑色和白色筛选图像。
* [新鲜度](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#freshness)-按 age 筛选图像 (例如，过去一周内 Bing 发现的图像) 。
* [高度](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#height)、 [宽度](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#width)-按宽度和高度筛选图像。
* [imageContent](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagecontent)—按内容筛选图像 (例如，仅显示人脸) 的图像。
* [imageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype)-按类型 (例如，按类型对图像进行筛选) 。
* [许可证](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license)-按与站点关联的许可证类型筛选映像。
* [大小](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#size)—按大小（如小图像，最大为200x200 大小像素）对图像进行筛选。

若要从特定的域获取图像，请使用 [site:](/previous-versions/bing/search/ff795613(v=msdn.10)) 查询运算符。

以下示例演示了如何从 ContosoSailing.com 获取必应在过去一周发现的小图像。  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>必应图像搜索响应格式

必应提供的响应消息包含一个 [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) 应答，该应答包含认知服务确定与查询相关的图像的列表。 列表中的每个 [Image](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) 对象包含图像的以下信息：URL、大小、尺寸、编码格式、缩略图的 URL，以及缩略图的尺寸。

> [!NOTE]
> * 必须按响应中提供的顺序显示图像。
> * 由于 URL 格式和参数可能会在未另行通知的情况下有所更改，请按现状使用所有 URL。 不应依赖于 URL 格式或参数，除非另有说明。

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

调用必应图像搜索 API 时，必应会返回结果的列表。 此列表是与查询相关的所有结果的一部分。 响应的 `totalEstimatedMatches` 字段包含一个估计数，是对可以查看的图像数的估计。 有关如何逐页查看剩余图像的详细信息，请参阅[对图像进行分页](../../bing-web-search/paging-search-results.md)。

## <a name="next-steps"></a>后续步骤

如果从未尝试过必应图像搜索 API，请尝试[快速入门](../quickstarts/csharp.md)。 如果需要更复杂的内容，请尝试关于如何创建[单页 Web 应用](../tutorial-bing-image-search-single-page-app.md)的教程。