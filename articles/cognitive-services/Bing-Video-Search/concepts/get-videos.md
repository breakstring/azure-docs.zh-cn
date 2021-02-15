---
title: 使用必应视频搜索 API 搜索视频
titleSuffix: Azure Cognitive Services
description: 必应视频搜索 APIfinds 并返回 web 上的相关视频，它提供了多种功能，可用于 web 上的智能和集中视频检索。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 10277efe1f06de3633b2d614e2ee5ec0cc351c76
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351923"
---
# <a name="search-for-videos-with-the-bing-video-search-api"></a>使用必应视频搜索 API 来搜索视频

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

使用必应视频搜索 API，可以轻松将必应的认知新闻搜索功能集成到应用程序中。 虽然该 API 主要用于从网上查找并返回相关的视频，但它还提供了一些在网络上进行智能化、集中化视频检索的功能。

## <a name="getting-videos"></a>获取视频

若要从 Web 获取与用户的搜索词相关的视频，请发送以下 GET 请求：

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

所有请求必须从服务器发出。

如果是首次调用任何必应 API，请勿添加客户端 ID 请求头。 只有在以前调用过必应 API 且必应针对用户和设备组合返回了客户端 ID 的情况下，才包括客户端 ID。

若要从特定的域获取视频，请使用 [site:](/previous-versions/bing/search/ff795613(v=msdn.10)) 查询运算符。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

响应包含一个 [Videos](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) 应答，该应答包含必应认为与查询相关的视频的列表。 该列表中的每个 [Video](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) 对象包含该视频的 URL、持续时间、尺寸和编码格式，以及其他属性。 视频对象还包含视频缩略图的 URL 以及缩略图的尺寸。

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYW...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : 
            {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D569...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y62...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : 
            {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E113CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        ...
    ],
    "queryExpansions" : [...],
    "nextOffsetAddCount" : 0,
    "pivotSuggestions" : [...]
}
```

## <a name="video-thumbnails"></a>视频缩略图

可以显示必应视频搜索 API 返回的所有或部分视频缩略图。 如果显示一部分缩略图，请为用户提供查看剩余视频的选项。 作为必应 API 的[使用和显示要求](../../bing-web-search/use-display-requirements.md)的一部分，你必须按照响应中提供的顺序来显示视频。 若要了解如何重设缩略图的大小，请参阅[重设缩略图的大小并对其进行裁剪](../../bing-web-search/resize-and-crop-thumbnails.md)。 

当用户将鼠标悬停在缩略图上时，你可以使用 [motionThumbnailUrl](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-motionthumbnailurl) 来播放视频的缩略图版本。 显示运动缩略图时，必须显示其属性。

<!-- Removing until the images can be sanitized.
![Motion thumbnail of a video](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

单击缩略图时，有三个用于观看视频的选项：

- 使用 [hostPageUrl](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-hostpageurl) 可在托管网站（例如 YouTube）上观看视频
- 使用 [webSearchUrl](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-websearchurl) 可在必应视频浏览器中观看视频
- 使用 [embdedHtml](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-embedhtml) 可将视频嵌入自己的体验中 

请确保在播放视频时使用发布者和创建者来设置视频的属性。

若要详细了解如何使用 [videoId](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-videoid) 来获取视频的见解，请参阅[视频见解](../video-insights.md)。

## <a name="filtering-videos"></a>筛选视频

默认情况下，视频搜索 API 返回与查询相关的所有视频。 如果只需免费视频或者时长不到五分钟的视频，请使用以下筛选器查询参数：

- [定价](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#pricing) &mdash;按定价 (筛选视频例如，免费或需要为) 付费的视频
- [解决方法](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#resolution) &mdash;按解决方案筛选视频 (例如，使用720p 或更高分辨率的视频) 
- [videoLength](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videolength) &mdash;按视频长度筛选视频 (例如，长度不到五分钟的视频) 
- [新鲜度](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#freshness) &mdash;按 age 筛选视频 (例如，过去一周内必应发现的视频) 

若要从特定域获取视频，请在查询字符串中包含 [site:](/previous-versions/bing/search/ff795613(v=msdn.10)) 查询运算符。

> [!NOTE]
> 如果使用 `site:` 查询运算符，则不管 [safeSearch](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#safesearch) 设置如何，仍有可能出现响应中包含成人内容的情况，具体取决于查询。 只有在知道网站内容且方案允许使用成人内容的情况下，才应使用 `site:`。

以下示例演示了如何从 ContosoSailing.com 获取必应在过去一月发现的分辨率为 720p 或更高的免费视频。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&pricing=free&freshness=month&resolution=720p&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="expanding-the-query"></a>扩展查询

如果必应可以通过扩展查询来缩小原始搜索的范围，则 [Videos](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) 对象会包含 `queryExpansions` 字段。 例如，如果查询为 *Cleaning Gutters*，则扩展的查询可能为：Gutter Cleaning **Tools**、Cleaning Gutters **From the Ground**、Gutter Cleaning **Machine** 以及 **Easy** Gutter Cleaning。

以下示例演示了 *Cleaning Gutters* 的扩展查询。

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC5...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 4,
    "queryExpansions" : [
        {
            "text" : "Gutter Cleaning Tools",
            "displayText" : "Tools",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FB....",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Gutter..."
            }
        },
        ...
    ]
    "pivotSuggestions" : [...],
}
```

`queryExpansions` 字段包含 [Query](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj) 对象的列表。 `text` 字段包含扩展的查询，`displayText` 字段包含扩展词。 可以使用文本和缩略图字段向用户显示扩展的查询字符串（有可能扩展的查询字符串正是用户想要查找的）。 使用 `webSearchUrl` URL 或 `searchLink` URL，使缩略图和文本可供用户单击。 使用 `webSearchUrl` 向用户发送必应搜索结果，或者使用 `searchLink`（如果提供你自己的结果页）。

## <a name="pivoting-the-query"></a>将查询分段

如果必应可以将原始搜索查询分段，则 [Videos](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) 对象会包含 `pivotSuggestions` 字段。 例如，如果原始查询为 *Cleaning Gutters*，则必应可能会将查询分成 *Cleaning* 和 *Gutters* 两段。

以下示例演示了针对 *Cleaning Gutters* 的分段建议。

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 0,
    "queryExpansions" : [...],
    "pivotSuggestions" : [
        {
            "pivot" : "cleaning",
            "suggestions" : [
                {
                    "text" : "Gutter Repair",
                    "displayText" : "Repair",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5\/videos...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=Gutter..."
                    }
                },
                ...
            ]
        },
        {
            "pivot" : "gutters",
            "suggestions" : [
                {
                    "text" : "Window Cleaning",
                    "displayText" : "Window",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC59...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=Window..."
                    }
                },
                ...
            ]
        }
    ]
}
```

每个段的响应包含一个 [Query](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query_obj) 对象的列表，该对象包含建议的查询。 `text` 字段包含建议的查询，`displayText` 字段包含的词替换原始查询中的段。 例如，Window Cleaning。

可以使用 `text` 和 `thumbnail` 字段向用户显示扩展的查询字符串（有可能扩展的查询字符串正是用户想要查找的）。 使用 `webSearchUrl` URL 或 `searchLink` URL，使缩略图和文本可供用户单击。 使用 `webSearchUrl` 向用户发送必应搜索结果，或者使用 `searchLink`（如果提供你自己的结果页）。

## <a name="throttling-requests"></a>限制请求

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]