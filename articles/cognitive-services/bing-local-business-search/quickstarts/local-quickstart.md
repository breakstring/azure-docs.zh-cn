---
title: 快速入门 - 使用必应当地企业搜索通过 C# 将查询发送到 API
titleSuffix: Azure Cognitive Services
description: 使用此快速入门开始使用 C# 向必应当地企业搜索 API 发送请求，该 API 是一项 Azure 认知服务。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 10/22/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 02fc67862a332ef51e164c17ef973bb610cf4fc7
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499112"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-c"></a>快速入门：使用 C# 将查询发送到必应当地企业搜索 API

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

使用此快速入门了解如何向必应当地企业搜索 API 发送请求，该 API 是一项 Azure 认知服务。 虽然这个简单的应用程序是用 C# 编写的，但 API 是一种 RESTful Web 服务，可以与任何能够发出 HTTP 请求并解析 JSON 的编程语言兼容。

此示例应用程序从 API 获取搜索查询的本地响应数据。

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* 任何版本的 [Visual Studio 2019](https://www.visualstudio.com/downloads/)。
* 如果使用的是 Linux/MacOS，则可使用 [Mono](https://www.mono-project.com/) 运行此应用程序。
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="创建必应搜索资源"  target="_blank">创建必应搜索资源<span class="docon docon-navigate-external x-hidden-focus"></span></a>来获取密钥和终结点。 部署后，单击“转到资源”。

## <a name="create-the-request"></a>创建请求 

下面的代码创建 `WebRequest`、设置访问密钥标头，并添加针对“restaurant in Bellevue”的查询字符串。  然后发送请求，并将响应分配到包含 JSON 文本的字符串。

```csharp
    // Replace the accessKey string value with your valid access key.
    const string accessKey = "enter key here";

    const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search";   

    const string searchTerm = "restaurant in Bellevue";
    // Construct the URI of the search request
    var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery) + mkt=en-us;

    // Run the Web request and get response.
    WebRequest request = HttpWebRequest.Create(uriQuery);
    request.Headers["Ocp-Apim-Subscription-Key"] = accessKey; 

    HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
    string json = new StreamReader(response.GetResponseStream()).ReadToEnd();
```

## <a name="run-the-complete-application"></a>运行完整应用程序

以下代码使用必应当地企业搜索 API 从必应搜索引擎返回本地化的搜索结果。 可按照以下步骤使用此代码：
1. 在 Visual Studio 中（用社区版就足够）创建一个新的控制台解决方案。
2. 将 Program.cs 替换为下面提供的代码。
3. 使用对订阅有效的访问密钥替换 `accessKey` 值。
4. 运行该程序。

```csharp
using System;
using System.Collections.Generic;
using System.Text;
using System.Net;
using System.IO;

namespace localSearch
{
    class Program
    {
        // **********************************************
        // **_ Update or verify the following values. _*_
        // _*********************************************

        // Replace the accessKey string value with your valid access key.
        const string accessKey = "enter key here";

        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search";   

        const string searchTerm = "restaurant in Bellevue";

        // Returns search results including relevant headers
        struct SearchResult
        {
            public String jsonResult;
            public Dictionary<String, String> relevantHeaders;
        }

        static void Main()
        {
            Console.OutputEncoding = System.Text.Encoding.UTF8;
            Console.WriteLine("Searching locally for: " + searchTerm);

            SearchResult result = BingLocalSearch(searchTerm, accessKey);

            Console.WriteLine("\nRelevant HTTP Headers:\n");
            foreach (var header in result.relevantHeaders)
                Console.WriteLine(header.Key + ": " + header.Value);

            Console.WriteLine("\nJSON Response:\n");
            Console.WriteLine(JsonPrettyPrint(result.jsonResult));

            Console.Write("\nPress Enter to exit ");
            Console.ReadLine();
        }

        /// <summary>
        /// Performs a Bing Local business search and return the results as a SearchResult.
        /// </summary>
        static SearchResult BingLocalSearch(string searchQuery, string key)
        {
            // Construct the URI of the search request
            var uriQuery = uriBase + "?q=" + Uri.EscapeDataString(searchQuery) + "&mkt=en-us";

            // Perform the Web request and get the response
            WebRequest request = HttpWebRequest.Create(uriQuery);
            request.Headers["Ocp-Apim-Subscription-Key"] = key; 

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            // Create result object for return
            var searchResult = new SearchResult();
            searchResult.jsonResult = json;
            searchResult.relevantHeaders = new Dictionary<String, String>();

            // Extract Bing HTTP headers
            foreach (String header in response.Headers)
            {
                if (header.StartsWith("BingAPIs-") || header.StartsWith("X-MSEdge-"))
                    searchResult.relevantHeaders[header] = response.Headers[header];
            }

            return searchResult;
        }

        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            int offset = 0;
            int indentLength = 3;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\'':
                        if (quote) ignore = !ignore;
                        break;
                }

                if (quote)
                    sb.Append(ch);
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (ch != ' ') sb.Append(ch);
                            break;
                    }
                }
            }

            return sb.ToString().Trim();
        }
    }
}

```

## <a name="next-steps"></a>后续步骤
- [当地企业搜索 Java 快速入门](local-search-java-quickstart.md)
- [当地企业搜索 Node.js 快速入门](local-search-node-quickstart.md)
- [当地企业搜索 Python 快速入门](local-search-python-quickstart.md)