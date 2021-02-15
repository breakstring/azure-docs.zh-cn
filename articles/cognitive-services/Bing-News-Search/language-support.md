---
title: 语言支持 - 必应新闻搜索 API
titleSuffix: Azure Cognitive Services
description: 必应新闻搜索 API 支持的自然语言、国家/地区和区域列表。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 9c7fd03c2239cea05dc79ad4dd1965fe253a2ce9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341591"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>必应新闻搜索 API 的语言和区域支持

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

必应新闻搜索 API 支持许多个国家/地区，其中很多具有多种语言。 使用查询指定国家/地区主要用于根据对该国家/地区的兴趣来优化搜索结果。 此外，结果可能包含指向必应的链接，这些链接可能会根据指定的国家/地区或语言本地化必应用户体验。

可以使用 `cc` 查询参数指定国家/地区。 如果指定了国家/地区，还必须使用 `Accept-Language` HTTP 标头指定一个或多个语言代码。 支持的语言因国家/地区而异；“市场”表中提供了每个国家/地区适用的语言。

或者，可以使用 `mkt` 查询参数和市场表中的代码指定市场。 指定市场的同时指定国家/地区和首选语言。 在这种情况下，`setLang` 查询参数可能设置为一个语言代码，通常这与 `mkt` 所指定的语言相同，除非用户希望以另一种语言查看必应。

## <a name="supported-markets-for-news-search-endpoint"></a>新闻搜索终结点支持的市场

对于 `/news/search` 终结点，下表列出了可用于指定 `mkt` 查询参数的市场代码值。 必应仅为这些市场返回内容。 列表可能随时变动。  

有关可使用 `cc` 查询参数指定的国家/地区代码列表，请参阅[国家/地区代码](#countrycodes)。  

|国家/地区|语言|市场代码|  
|---------------------|--------------|-----------------|
|丹麦|丹麦语|da-DK|
|奥地利|德语|de-AT|
|瑞士|德语|de-CH|
|德国|德语|de-DE|
|澳大利亚|英语|en-AU|
|加拿大|英语|en-CA|
|英国|英语|en-GB|
|印度尼西亚|英语|en-ID|
|爱尔兰|英语|en-IE|
|印度|英语|en-IN|
|马来西亚|英语|en-MY|
|新西兰|英语|en-NZ|
|菲律宾共和国|英语|en-PH|
|新加坡|英语|en-SG|
|美国|英语|en-US|
|英语|常规|en-WW|
|英语|常规|en-XA|
|南非|英语|en-ZA|
|阿根廷|西班牙语|es-AR|
|智利|西班牙语|es-CL|
|西班牙|西班牙语|es-ES|
|墨西哥|西班牙语|es-MX|
|美国|西班牙语|es-US|
|西班牙语|常规|es-XL|
|芬兰|芬兰语|fi-FI|  
|法国|法语|fr-BE|
|加拿大|法语|fr-CA|
|比利时|荷兰语|nl-BE|
|瑞士|法语|fr-CH|
|法国|法语|fr-FR|  
|意大利|意大利语|it-IT|
|香港特别行政区|繁体中文|zh-HK|  
|台湾|繁体中文|zh-TW|
|日本|日语|ja-JP|  
|韩国|朝鲜语|ko-KR|  
|荷兰|荷兰语|nl-NL|  
|中华人民共和国|中文|zh-CN|  
|巴西|葡萄牙语|pt-BR|
|俄罗斯|俄语|ru-RU|  
|瑞典|瑞典语|sv-SE|  
|土耳其|土耳其语|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>新闻终结点支持的市场
对于 `/news` 终结点，下表列出了可用于指定 `mkt` 查询参数的市场代码值。 必应仅为这些市场返回内容。 列表可能随时变动。  

有关可使用 `cc` 查询参数指定的国家/地区代码列表，请参阅[国家/地区代码](#countrycodes)。  

|国家/地区|语言|市场代码|  
|---------------------|--------------|-----------------|
|丹麦|丹麦语|da-DK|
|德国|德语|de-DE|
|澳大利亚|英语|en-AU|
|英国|英语|en-GB|
|美国|英语|en-US|
|英语|常规|en-WW|
|智利|西班牙语|es-CL|
|墨西哥|西班牙语|es-MX|
|美国|西班牙语|es-US|
|芬兰|芬兰语|fi-FI|  
|加拿大|法语|fr-CA|
|法国|法语|fr-FR|  
|意大利|意大利语|it-IT|
|巴西|葡萄牙语|pt-BR|
|中华人民共和国|中文|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>新闻热门终结点支持的市场
对于 `/news/trendingtopics` 终结点，下表列出了可用于指定 `mkt` 查询参数的市场代码值。 必应仅为这些市场返回内容。 列表可能随时变动。  

有关可使用 `cc` 查询参数指定的国家/地区代码列表，请参阅[国家/地区代码](#countrycodes)。  

|国家/地区|语言|市场代码|  
|---------------------|--------------|-----------------|
|德国|德语|de-DE|
|澳大利亚|英语|en-AU|
|英国|英语|en-GB|
|美国|英语|en-US|
|加拿大|英语|en-CA|
|印度|英语|en-IN|
|法国|法语|fr-FR|
|加拿大|法语|fr-CA|
|巴西|葡萄牙语|pt-BR|
|中华人民共和国|中文|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>国家/地区代码  

下面是可使用 `cc` 查询参数指定的国家/地区代码。 列表可能随时变动。  

|国家/地区|国家/地区代码|  
|---------------------|------------------|  
|阿根廷|AR|  
|澳大利亚|AU|  
|奥地利|AT|  
|比利时|BE|  
|巴西|BR|  
|Canada|CA|  
|智利|CL|  
|丹麦|DK|  
|芬兰|FI|  
|法国|FR|  
|德国|DE|  
|香港特别行政区|HK|  
|印度|IN|  
|印度尼西亚|ID|  
|意大利|IT|  
|日本|JP|  
|韩国|KR|  
|马来西亚|MY|  
|墨西哥|MX|  
|荷兰|NL|  
|新西兰|NZ|  
|挪威|是|  
|中华人民共和国|CN|  
|波兰|PL|  
|葡萄牙|PT|  
|菲律宾共和国|PH|  
|俄罗斯|RU|  
|沙特阿拉伯|SA|  
|南非|ZA|  
|西班牙|ES|  
|瑞典|SE|  
|瑞士|CH|  
|中国台湾|TW|  
|土耳其|TR|  
|United Kingdom|GB|  
|United States|美国|

## <a name="next-steps"></a>后续步骤
有关必应新闻搜索终结点的详细信息，请参阅[新闻搜索 API v7 参考](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)。