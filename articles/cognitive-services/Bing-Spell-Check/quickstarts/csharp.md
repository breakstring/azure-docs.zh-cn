---
title: 快速入门：使用 REST API 和 C# 检查拼写 - 必应拼写检查
titleSuffix: Azure Cognitive Services
description: 开始使用必应拼写检查 REST API 和 C# 检查拼写和语法。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: ace7a0ccaba533c9e72961536159d32af5ab8d98
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352722"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-c"></a>快速入门：使用必应拼写检查 REST API 和 C# 检查拼写

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

根据此快速入门中的说明对必应拼写检查 REST API 进行第一次调用。 此简单的 C# 应用程序将向 API 发送请求并返回一系列建议的更正。 

虽然此应用程序是使用 C# 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingAutosuggestv7.cs) 上提供了此应用程序的源代码。

## <a name="prerequisites"></a>先决条件

* 任何版本的 [Visual Studio 2017 或更高版本](https://www.visualstudio.com/downloads/)。
* Newtonsoft.Json NuGet 包。 
     
   若要在 Visual Studio 中安装此包，请执行以下操作：

     1. 在 **解决方案资源管理器** 中，右键单击解决方案文件。
     1. 选择“管理解决方案的 NuGet 包”。
     1. 搜索 *Newtonsoft.Json* 并安装该包。

* 如果使用的是 Linux/MacOS，则可以通过使用 [Mono](https://www.mono-project.com/) 运行此应用程序。

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>创建并初始化项目

1. 在 Visual Studio 中创建一个名为 SpellCheckSample 的新控制台解决方案。 然后将以下命名空间添加到主代码文件：
    
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Newtonsoft.Json;
    ```

2. 为 API 终结点、订阅密钥和要进行拼写检查的文本创建变量。 你可以使用以下代码中的全局终结点，或者使用资源的 Azure 门户中显示的[自定义子域](../../../cognitive-services/cognitive-services-custom-subdomains.md)终结点。

    ```csharp
    namespace SpellCheckSample
    {
        class Program
        {
            static string host = "https://api.cognitive.microsoft.com";
            static string path = "/bing/v7.0/spellcheck?";
            static string key = "<ENTER-KEY-HERE>";
            //text to be spell-checked
            static string text = "Hollo, wrld!";
        }
    }
    ```

3. 为搜索参数创建字符串： 

   1. 用 `=` 运算符将市场代码分配到 `mkt` 参数。 市场代码指示发出请求的国家/地区的代码。 

   1. 使用 `&` 运算符添加 `mode` 参数，然后分配拼写检查模式。 模式可以是 `proof`（捕获大部分拼写/语法错误）或 `spell`（捕获大部分拼写错误，但是捕获的语法错误较少）。
    
    ```csharp
    static string params_ = "mkt=en-US&mode=proof";
    ```

## <a name="create-and-send-a-spell-check-request"></a>创建和发送拼写检查请求

1. 创建名为 `SpellCheck()` 的异步函数，以便向 API 发送请求。 创建 `HttpClient`，并将订阅密钥添加到 `Ocp-Apim-Subscription-Key` 标头。 在函数中，按后续步骤执行操作。

    ```csharp
    async static void SpellCheck()
    {
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

        HttpResponseMessage response = null;
        // add the rest of the code snippets here (except for main())...
    }
    ```

2. 通过追加你的主机、路径和参数为你的请求创建 URI。
    
    ```csharp
    string uri = host + path + params_;
    ```

3. 创建含有 `KeyValuePair` 对象（其中包含文本）的列表，并将其用于创建 `FormUrlEncodedContent` 对象。 设置标头信息，并使用 `PostAsync()` 发送请求。

    ```csharp
    var values = new Dictionary<string, string>();
    values.Add("text", text);
    var content = new FormUrlEncodedContent(values);
    content.Headers.ContentType = new MediaTypeHeaderValue("application/x-www-form-urlencoded");
    response = await client.PostAsync(uri, new FormUrlEncodedContent(values));
    ```

## <a name="get-and-print-the-api-response"></a>获取和打印 API 响应

### <a name="get-the-client-id-header"></a>获取客户端 ID 标头

如果响应包含 `X-MSEdge-ClientID` 标头，获取该值并将其打印。

``` csharp
string client_id;
if (response.Headers.TryGetValues("X-MSEdge-ClientID", out IEnumerable<string> header_values))
{
    client_id = header_values.First();
    Console.WriteLine("Client ID: " + client_id);
}
```

### <a name="get-the-response"></a>获取响应

从 API 获取响应。 反序列化 JSON 对象，并将其打印到控制台。

```csharp
string contentString = await response.Content.ReadAsStringAsync();

dynamic jsonObj = JsonConvert.DeserializeObject(contentString);
Console.WriteLine(jsonObj);
```

## <a name="call-the-spell-check-function"></a>调用拼写检查函数

在项目的 `Main()` 函数中，调用 `SpellCheck()`。

```csharp
static void Main(string[] args)
{
    SpellCheck();
    Console.ReadLine();
}
```

## <a name="run-the-application"></a>运行应用程序

生成并运行项目。 如果使用的是 Visual Studio，请按 **F5** 调试该文件。

## <a name="example-json-response"></a>示例 JSON 响应

在 JSON 中返回成功的响应，如以下示例所示： 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建单页 Web 应用](../tutorials/spellcheck.md)

- [什么是必应拼写检查 API？](../overview.md)
- [必应拼写检查 API v7 参考](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)