---
title: 快速入门：使用必应图像搜索 REST API 和 PHP 来搜索图像
titleSuffix: Azure Cognitive Services
description: 使用本快速入门，通过 PHP 将图像搜索请求发送到必应图像搜索 REST API，并接收 JSON 响应。
services: cognitive-services
documentationcenter: ''
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: cf5478dfb15665047e39d51c71ec9e85a436fe91
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341846"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-php"></a>快速入门：使用必应图像搜索 REST API 和 PHP 来搜索图像

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

使用本快速入门进行你的第一次必应图像搜索 API 调用并接收 JSON 响应。 本文中的简单应用程序会发送一个搜索查询并显示原始结果。

虽然此应用程序是用 PHP 编写的，但 API 是一种 RESTful Web 服务，与任何可以发出 HTTP 请求并分析 JSON 的编程语言兼容。

该示例的源代码可在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/php/Search/BingWebSearchv7.php) 上获得。

## <a name="prerequisites"></a>先决条件

* [PHP 5.6.x 或更高版本](https://php.net/downloads.php)

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

有关详细信息，请参阅[认知服务定价 - 必应搜索 API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。

## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

若要运行此应用程序，请执行以下步骤：

1. 确保在 `php.ini` 文件中启用安全 HTTP 支持。 对于 Windows，此文件位于 C:\windows。
2. 在喜欢使用的 IDE 或编辑器中新建一个 PHP 项目。
3. 定义 API 终结点、订阅密钥和搜索词。 终结点可以是以下代码中的全局终结点，或者是在资源的 Azure 门户中显示的[自定义子域](../../../cognitive-services/cognitive-services-custom-subdomains.md)终结点。

    ```php
    $endpoint = 'https://api.cognitive.microsoft.com/bing/v7.0/images/search';
    // Replace the accessKey string value with your valid access key.
    $accessKey = 'enter key here';
    $term = 'tropical ocean';
    ```

## <a name="construct-and-perform-an-http-request"></a>构造并执行 HTTP 请求

1. 使用上一步中的变量来为图像搜索 API 准备 HTTP 请求。

    ```php
    $headers = "Ocp-Apim-Subscription-Key: $key\r\n";
    $options = array ( 'http' => array (
                            'header' => $headers,
                            'method' => 'GET' ));
    ```

2. 发送 Web 请求并获取 JSON 响应。

    ```php
    $context = stream_context_create($options);
    $result = file_get_contents($url . "?q=" . urlencode($query), false, $context);
    ```

## <a name="process-and-print-the-json"></a>处理并打印 JSON

处理并打印返回的 JSON 响应。

```php
$headers = array();
    foreach ($http_response_header as $k => $v) {
        $h = explode(":", $v, 2);
        if (isset($h[1]))
            if (preg_match("/^BingAPIs-/", $h[0]) || preg_match("/^X-MSEdge-/", $h[0]))
                $headers[trim($h[0])] = trim($h[1]);
    }
    return array($headers, $result);
```

## <a name="example-json-response"></a>示例 JSON 响应

来自必应图像搜索 API 的响应以 JSON 形式返回。 此示例响应已截断，仅显示了单个结果。

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [必应图像搜索单页应用教程](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>另请参阅

* [什么是必应图像搜索 API？](../overview.md)  
* [尝试在线互动演示](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) 
* [必应搜索 API 的定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) 
* [Azure 认知服务文档](../../index.yml)
* [必应图像搜索 API 参考](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)