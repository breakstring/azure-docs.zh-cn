---
title: 快速入门 - 使用 Java 将查询发送到 API - 必应当地企业搜索
titleSuffix: Azure Cognitive Services
description: 通过此快速入门开始使用 Java 向必应当地企业搜索 API 发送请求 - 该 API 是一项 Azure 认知服务。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 8bd2c174456c35da872a0e995333503a72ce7c7a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493298"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-java"></a>快速入门：使用 Java 将查询发送到必应当地企业搜索 API

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

使用此快速入门了解如何向必应当地企业搜索 API 发送请求，该 API 是一项 Azure 认知服务。 尽管这个简单的应用程序是用 Java 编写的，但 API 是一种 RESTful Web 服务，可以与任何能够发出 HTTP 请求并解析 JSON 的编程语言兼容。

此示例应用程序从 API 获取搜索查询的本地响应数据。

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* [Java 开发工具包 (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)。
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="创建必应搜索资源"  target="_blank">创建必应搜索资源<span class="docon docon-navigate-external x-hidden-focus"></span></a>来获取密钥和终结点。 部署后，单击“转到资源”。

## <a name="create-the-request"></a>创建请求 

下面的代码创建 `WebRequest`、设置访问密钥标头，并为“hotel in Bellevue”添加查询字符串。  然后发送请求，并将响应分配到包含 JSON 文本的字符串。

```java
    // construct URL of search request (endpoint + query string)
     URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + &mkt=en-us");
    HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

    // receive JSON body
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();

    // construct result object for return
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);
```

## <a name="run-the-complete-application"></a>运行完整应用程序

以下代码使用必应当地企业搜索 API 从必应搜索引擎返回搜索结果。 按照以下步骤运行此代码：
1. 下载或安装 gson 库。
2. 在最喜爱的 IDE 或编辑器中新建一个 Java 项目。
3. 添加以下提供的代码。
4. 使用对订阅有效的访问密钥替换 `subscriptionKey` 值。
5. 运行该程序。

```java
package localSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (localSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac localSearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar localSearch
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

public class LocalSearchCls {

    // ***********************************************
    // **_ Update or verify the following values. _*_
    // _*********************************************

        // Replace the subscriptionKey string value with your valid subscription key.
        static String subscriptionKey = "YOUR-ACCESS-KEY";

        static String host = "https://api.cognitive.microsoft.com/bing";
        static String path = "/v7.0/localbusinesses/search";

        static String searchTerm = "Hotel in Bellevue";

        public static SearchResults SearchLocal (String searchQuery) throws Exception {
            // construct URL of search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&mkt=en-us");
            HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
            connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

            // receive JSON body
            InputStream stream = connection.getInputStream();
            String response = new Scanner(stream).useDelimiter("\\A").next();

            // construct result object for return
            SearchResults results = new SearchResults(new HashMap<String, String>(), response);

            // extract Bing-related HTTP headers
            Map<String, List<String>> headers = connection.getHeaderFields();
            for (String header : headers.keySet()) {
                if (header == null) continue;      // may have null key
                if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
                    results.relevantHeaders.put(header, headers.get(header).get(0));
                }
            }

            stream.close();
            return results;
        }

        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }

        public static void main (String[] args) {
            try {
                System.out.println("Searching the Web for: " + searchTerm);

                SearchResults result = SearchLocal(searchTerm);

                System.out.println("\nRelevant HTTP Headers:\n");
                for (String header : result.relevantHeaders.keySet())
                    System.out.println(header + ": " + result.relevantHeaders.get(header));

                System.out.println("\nJSON Response:\n");
                System.out.println(prettify(result.jsonResponse));
            }
            catch (Exception e) {
                e.printStackTrace(System.out);
                System.exit(1);
            }
        }
    }

    // Container class for search results encapsulates relevant headers and JSON data
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }

```

## <a name="next-steps"></a>后续步骤
- [当地企业搜索 C# 快速入门](local-quickstart.md)
- [当地企业搜索 Node.js 快速入门](local-search-node-quickstart.md)
- [当地企业搜索 Python 快速入门](local-search-python-quickstart.md)