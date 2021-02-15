---
title: 使用必应自动建议 API 来建议搜索词
titleSuffix: Azure Cognitive Services
description: 本文介绍使用必应自动推荐 API 建议查询术语的概念，以及查询长度对相关性的影响。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: be7686c4d8a676d2a1d85516d2e4aa6abe3f3bfd
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353402"
---
# <a name="suggesting-query-terms"></a>建议查询词

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

通常，每次用户在应用程序的搜索框中键入新字符时，你都会调用必应自动建议 API。 查询字符串的完整性会影响 API 返回的建议查询词的相关性。 查询字符串越完整，建议的查询词列表就越相关。 例如，API 可能会为 `s` 返回的建议可能不如它为 `sailing dinghies` 返回的查询相关。

## <a name="example-request"></a>示例请求

以下示例显示了一个请求，该请求针对 *sail* 返回建议的查询字符串。 在设置 [q](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#query) 查询参数时，请记得对用户的部分查询词进行 URL 编码。 例如，如果用户输入了 *sailing les*，请将 `q` 设置为 `sailing+les` 或 `sailing%20les`。

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

以下响应列出了包含建议查询词的 [SearchAction](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#searchaction) 对象。

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

## <a name="using-suggested-query-terms"></a>使用建议查询词

每个建议都包括 `displayText`、`query` 和 `url` 字段。 `displayText` 字段包含用于填充搜索框下拉列表的建议查询。 必须按给定的顺序显示响应包含的所有建议。

下面的示例显示了一个下拉搜索框，其中包含 Bing 自动建议 API 提供的建议查询词。

![自动建议下拉搜索框列表](../media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

如果用户从下拉列表中选择了一个建议的查询，则你将使用 `query` 字段中的查询词来调用[必应 Web 搜索 API](../../bing-web-search/overview.md) 并自行显示结果。 或者，可以使用 `url` 字段中的 URL 将用户发送到必应搜索结果页。

## <a name="next-steps"></a>后续步骤

* [什么是必应自动建议 API？](../get-suggested-search-terms.md)