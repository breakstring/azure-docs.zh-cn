---
title: 必应图像搜索 JavaScript 客户端库快速入门
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 037137cf5a6e4ddd66fc15e8ad9775ea77177ef6
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94625473"
---
在本快速入门中，你将使用必应图像搜索客户端库（它是 API 的包装器并包含相同的功能）进行你的第一次图像搜索。 此简单的 JavaScript 应用程序发送图像搜索查询，分析 JSON 响应，并显示返回的第一个图像的 URL。

[GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) 上提供了此示例的源代码以及附加的错误处理和注释。

## <a name="prerequisites"></a>先决条件

* 最新版本的 [Node.js](https://nodejs.org/en/download/)。
* [适用于 JavaScript 的必应图像搜索 SDK](https://www.npmjs.com/package/@azure/cognitiveservices-imagesearch)
     *  若要安装，请运行 `npm install @azure/cognitiveservices-imagesearch`
* `@azure/ms-rest-azure-js` 包中的 `CognitiveServicesCredentials` 类，用于对客户端进行身份验证。
     * 若要安装，请运行 `npm install @azure/ms-rest-azure-js`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

1. 在你喜欢使用的 IDE 或编辑器中创建一个新的 JavaScript 文件，设置严格性、https 和其他要求。

    ```javascript
    'use strict';
    const ImageSearchAPIClient = require('@azure/cognitiveservices-imagesearch');
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    ```

2. 在项目的主方法中，创建你的有效订阅密钥的变量、必应要返回的图像结果和搜索词。 然后，使用密钥实例化图像搜索客户端。

    ```javascript
    //replace this value with your valid subscription key.
    let serviceKey = "ENTER YOUR KEY HERE";

    //the search term for the request
    let searchTerm = "canadian rockies";

    //instantiate the image search client
    let credentials = new CognitiveServicesCredentials(serviceKey);
    let imageSearchApiClient = new ImageSearchAPIClient(credentials);

    ```

## <a name="create-an-asynchronous-helper-function"></a>创建异步帮助程序函数

1. 创建一个以异步方式调用客户端的函数，并从必应图像搜索服务返回响应。
    ```javascript
    //a helper function to perform an async call to the Bing Image Search API
    const sendQuery = async () => {
        return await imageSearchApiClient.imagesOperations.search(searchTerm);
    };
    ```
   ## <a name="send-a-query-and-handle-the-response"></a>发送查询并处理响应

1. 调用帮助程序函数并处理其 `promise` 来分析响应中返回的图像结果。

    如果响应中包含搜索结果，则存储第一个结果并输出其详细信息，例如缩略图 URL、原始 URL 以及返回的图像的总数。
    ```javascript
    sendQuery().then(imageResults => {
        if (imageResults == null) {
        console.log("No image results were found.");
        }
        else {
            console.log(`Total number of images returned: ${imageResults.value.length}`);
            let firstImageResult = imageResults.value[0];
            //display the details for the first image result. After running the application,
            //you can copy the resulting URLs from the console into your browser to view the image.
            console.log(`Total number of images found: ${imageResults.value.length}`);
            console.log(`Copy these URLs to view the first image returned:`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image content url: ${firstImageResult.contentUrl}`);
        }
      })
      .catch(err => console.error(err))
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [必应图像搜索单页应用教程](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>另请参阅

* [什么是必应图像搜索？](../../overview.md)
* [尝试在线互动演示](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)
* [Azure 认知服务 SDK 的 Node.js 示例](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
* [Azure 认知服务文档](../../../index.yml)
* [必应图像搜索 API 参考](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)