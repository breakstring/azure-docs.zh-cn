---
title: 必应自定义搜索终结点
titleSuffix: Azure Cognitive Services
description: 为您关注的主题创建定制的搜索体验。 用户会看到针对他们关心的内容量身定制的搜索结果。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 35dd147574eb7820debba55b105deed001be80fb
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338480"
---
# <a name="custom-search"></a>自定义搜索

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。
借助必应自定义搜索，可以为关注的主题创建定制的搜索体验。 用户会看到根据他们关注的内容定制的搜索结果，无需浏览包含无关内容的搜索结果。

## <a name="custom-search-endpoint"></a>自定义搜索终结点
若要使用必应自定义搜索 API 获取结果，请向以下终结点发送 `GET` 请求。 使用标头和 URL 参数来定义更多规范。

终结点：将搜索建议作为 JSON 结果返回，该结果与 `?q=""` 定义的用户输入相关。
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

有关如何设置自定义搜索源的示例，请参阅[教程](./tutorials/custom-search-web-page.md)。 若要详细了解标头、参数、市场代码、响应对象、错误等，请参阅[必应自定义搜索 API v7](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference) 参考。

## <a name="custom-search-response-json"></a>自定义搜索响应 JSON
自定义搜索请求将结果作为 JSON 对象返回，请参阅[响应对象](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#response-objects)。 

## <a name="custom-autosuggest"></a>自定义自动建议
通过自定义自动建议 API，可向必应发送部分搜索查询词，并返回用户可配置的建议查询列表。 借助自定义建议，可添加 API 返回的建议，并选择性地指定是否要包含必应生成的建议。

## <a name="custom-autosuggest-endpoint"></a>自定义自动建议终结点
要请求自定义查询建议，可将 GET 请求发送至：

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

有关定义自定义建议的信息，请参阅[定义自定义搜索建议](define-custom-suggestions.md)。

## <a name="custom-image-search"></a>自定义图像搜索
通过自定义图像搜索 API 可向必应发送搜索查询，并从自定义搜索实例获取相关图像列表。

## <a name="custom-image-search-endpoint"></a>自定义图像搜索终结点
若要从自定义搜索实例请求图像，请将 GET 请求发送到以下 URL：

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

有关配置自定义搜索实例的信息，请参阅[配置自定义搜索体验](./define-your-custom-view.md)。

## <a name="next-steps"></a>后续步骤
必应 API 支持根据其类型返回结果的搜索操作。 所有搜索终结点均将结果作为 JSON 响应对象返回。  所有终结点支持后列查询：按经度、纬度和搜索半径返回特定语言和/或位置的查询。

若要完整了解每个终结点支持的参数，请参阅每种类型对应的参考页面。
有关使用自定义搜索 API 的基本请求的示例，请参阅[自定义搜索快速入门](/azure/cognitive-services/bing-custom-search/)