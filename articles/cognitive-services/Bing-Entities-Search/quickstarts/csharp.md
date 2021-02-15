---
title: 快速入门：使用 C# 向 REST API 发送搜索请求 - 必应实体搜索
titleSuffix: Azure Cognitive Services
description: 使用本快速入门，通过 C# 向必应实体搜索 REST API 发送请求，并接收 JSON 响应。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 10/19/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: a0a77faba971f328c6ae1c5a03f8faf7ccfcf60d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351480"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-c"></a>快速入门：使用 C# 向必应实体搜索 REST API 发送搜索请求

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

使用本快速入门，对必应实体搜索 API 进行第一次调用并查看 JSON 响应。 这个简单的 C# 应用程序会向该 API 发送一个新闻搜索查询并显示响应。 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingEntitySearchv7.cs) 上提供了此应用程序的源代码。

虽然此应用程序是使用 C# 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。


## <a name="prerequisites"></a>先决条件

- 任何版本的 [Visual Studio 2017 或更高版本](https://www.visualstudio.com/downloads/)。
- 或者，如果使用的是 Linux 或 MacOS，则可以使用 [Visual Studio Code](https://code.visualstudio.com/) 和 [.NET Core](/dotnet/core/install/macos) 遵循此快速入门操作
- [免费的 Azure 帐户](https://azure.microsoft.com/free/dotnet)


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>创建并初始化项目

1. 在 Visual Studio 中创建一个新的 C# 控制台解决方案。 
1. 添加 [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) NuGet 包。
    1. 在“解决方案资源管理器”中右键单击项目。
    2. 选择“管理 NuGet 包”。
    3. 搜索并选择 Newtonsoft.Json，然后安装该包。
1. 然后将以下命名空间添加到主代码文件：
    
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    ```

2. 创建一个新类，并为 API 终结点、订阅密钥和你要搜索的查询添加变量。 你可以使用以下代码中的全局终结点，或者使用资源的 Azure 门户中显示的[自定义子域](../../../cognitive-services/cognitive-services-custom-subdomains.md)终结点。

    ```csharp
    namespace EntitySearchSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/entities";
    
            static string market = "en-US";
    
            // NOTE: Replace this example key with a valid subscription key.
            static string key = "ENTER YOUR KEY HERE";
    
            static string query = "italian restaurant near me";
        //...
        }
    }
    ```

## <a name="send-a-request-and-get-the-api-response"></a>发送请求并获取 API 响应

1. 在该类中创建一个名为 `Search()` 的函数。 在此函数中，创建一个新的 `HttpClient` 对象，并将你的订阅密钥添加到 `Ocp-Apim-Subscription-Key` 标头。

2. 通过将主机和路径进行组合来构造你的请求的 URI。 然后，添加你的市场，并对你的查询进行 URL 编码。

3. 等待 `client.GetAsync()` 获得 HTTP 响应，然后通过等待 `ReadAsStringAsync()` 来存储 JSON 响应。

4. 使用 `JsonConvert.DeserializeObject()` 设置 JSON 字符串的格式并将其输出到控制台。

      ```csharp
      async static void Search()
      {
       //...
       HttpClient client = new HttpClient();
       client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

       string uri = host + path + "?mkt=" + market + "&q=" + System.Net.WebUtility.UrlEncode(query);

       HttpResponseMessage response = await client.GetAsync(uri);

       string contentString = await response.Content.ReadAsStringAsync();
       dynamic parsedJson = JsonConvert.DeserializeObject(contentString);
       Console.WriteLine(parsedJson);
      }
      ```

5. 在应用程序的 `Main()` 方法中调用 `Search()` 函数。
    
    ```csharp
    static void Main(string[] args)
    {
        Search();
        Console.ReadLine();
    }
    ```


## <a name="example-json-response"></a>示例 JSON 响应

在 JSON 中返回成功的响应，如以下示例所示： 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [构建单页 Web 应用](../tutorial-bing-entities-search-single-page-app.md)

* [什么是必应实体搜索 API？](../overview.md )
* [必应实体搜索 API 参考](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)。