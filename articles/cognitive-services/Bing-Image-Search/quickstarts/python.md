---
title: 快速入门：使用必应图像搜索 REST API 和 Python 搜索图像
titleSuffix: Azure Cognitive Services
description: 使用本快速入门，通过 Python 将图像搜索请求发送到必应图像搜索 REST API，并接收 JSON 响应。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-python
ms.openlocfilehash: 0d0f3e3578f1fe40794ec5697291d35e3d277419
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341727"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-python"></a>快速入门：使用必应图像搜索 REST API 和 Python 搜索图像

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

使用此快速入门了解如何将搜索请求发送到必应图像搜索 API。 此 Python 应用程序会向 API 发送搜索查询，并在结果中显示第一个图像的 URL。 虽然此应用程序是使用 Python 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。

若要在 [MyBinder](https://mybinder.org) 上将此示例作为 Jupyter 笔记本运行，请选择“启动活页夹”锁屏提醒：

[![活页夹](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py) 上提供了此示例的源代码以及附加的错误处理和注释。


## <a name="prerequisites"></a>先决条件

* [Python 2.x 或 3.x](https://www.python.org/)
* [Python 映像库 (PIL)](https://pillow.readthedocs.io/en/stable/index.html)
* [matplotlib](https://matplotlib.org/) 

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

1. 在你最喜欢使用的 IDE 或编辑器中新建一个 Python 文件，并导入以下模块。 为你的订阅密钥、搜索终结点和搜索词创建变量。 对于 `search_url`，你可以使用以下代码中的全局终结点，或者使用资源的 Azure 门户中显示的[自定义子域](../../../cognitive-services/cognitive-services-custom-subdomains.md)终结点。

    ```python
    import requests
    import matplotlib.pyplot as plt
    from PIL import Image
    from io import BytesIO
    
    subscription_key = "your-subscription-key"
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
    search_term = "puppies"
    ```

2. 将订阅密钥添加到 `Ocp-Apim-Subscription-Key` 标头，方法是：创建一个字典，将密钥作为值添加。 

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="create-and-send-a-search-request"></a>创建并发送搜索请求

1. 为搜索请求的参数创建字典。 向 `q` 参数添加搜索词。 将 `license` 参数设置为 `public` 以便在公共域中搜索图像。 将 `imageType` 设置为 `photo` 以仅搜索照片。

    ```python
    params  = {"q": search_term, "license": "public", "imageType": "photo"}
    ```

2. 使用 `requests` 库调用必应图像搜索 API。 向请求添加标头和参数，并以 JSON 对象形式返回响应。 从响应的 `thumbnailUrl` 字段获取几个缩略图图像的 URL。

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
    ```

## <a name="view-the-response"></a>查看响应

1. 使用 matplotlib 库创建包含四个列和四个行的新图。 

2. 循环访问该图的行和列，并使用 PIL 库的 `Image.open()` 方法将图像的缩略图添加到每个空间。 

3. 使用 `plt.show()` 来绘制该图并显示图像。

    ```python
    f, axes = plt.subplots(4, 4)
    for i in range(4):
        for j in range(4):
            image_data = requests.get(thumbnail_urls[i+4*j])
            image_data.raise_for_status()
            image = Image.open(BytesIO(image_data.content))        
            axes[i][j].imshow(image)
            axes[i][j].axis("off")
    plt.show()
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

* [什么是必应图像搜索 API？](../overview.md)  
* [必应搜索 API 的定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) 
* [Azure 认知服务文档](../../index.yml)
* [必应图像搜索 API 参考](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)