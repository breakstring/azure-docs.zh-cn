---
title: 快速入门：使用 Java 调用必应自定义搜索终结点 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 参考本快速入门开始从必应自定义搜索实例请求 Java 搜索结果。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 9090bf01c25c85158781160626f0d1bb72ab8ff2
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352059"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-java"></a>快速入门：使用 Java 调用必应自定义搜索终结点

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

使用此快速入门了解如何从必应自定义搜索实例请求搜索结果。 虽然此应用程序是以 Java 编写的，但必应自定义搜索 API 是一种 RESTful Web 服务，与大多数编程语言兼容。 该示例的源代码可在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingCustomSearchv7.java) 上获得。

## <a name="prerequisites"></a>先决条件

- 必应自定义搜索实例。 有关详细信息，请参阅[快速入门：创建首个必应自定义搜索实例](quick-start.md)。

- 最新的 [Java 开发工具包](https://www.oracle.com/technetwork/java/javase/downloads/index.html)。

- [Gson 库](https://github.com/google/gson)。

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

1. 在你最喜欢的 IDE 或编辑器中新建一个 Java 项目，并导入以下库：

    ```java
    import java.io.InputStream;
    import java.net.URL;
    import java.net.URLEncoder;
    import java.util.HashMap;
    import java.util.List;
    import java.util.Map;
    import java.util.Scanner;
    import javax.net.ssl.HttpsURLConnection;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    ```

2. 创建名为 `CustomSrchJava` 的类，然后为订阅密钥、自定义搜索终结点和搜索实例的自定义配置 ID 创建变量。 你可以使用以下代码中的全局终结点，或者使用资源的 Azure 门户中显示的[自定义子域](../../cognitive-services/cognitive-services-custom-subdomains.md)终结点。
    ```java
    public class CustomSrchJava {
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";
        static String searchTerm = "Microsoft";  
    ...
    ```

3. 创建另一个名为 `SearchResults` 的类，用于存储来自必应自定义搜索实例的响应。

    ```java
    class SearchResults {
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }
    ```

4. 创建名为 `prettify()` 的函数，用于设置来自必应自定义搜索 API 的 JSON 响应的格式。

    ```java
        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="send-and-receive-a-search-request"></a>发送和接收搜索请求 

1. 创建一个名为 `SearchWeb()` 的函数，用于发送请求并返回 `SearchResults` 对象。 通过合并自定义配置 ID、查询和终结点信息，创建请求 URL。 将订阅密钥添加到 `Ocp-Apim-Subscription-Key` 标头。

    ```java
    public class CustomSrchJava {
    ...
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            // construct the URL for your search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchTerm, "UTF-8") + "&CustomConfig=" + customConfigId);
            HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
            connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    ...
    ```

2. 创建流并存储 `SearchResults` 对象中的 JSON 响应。

    ```java
    public class CustomSrchJava {
    ...
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            ...
            // receive the JSON body
            InputStream stream = connection.getInputStream();
            String response = new Scanner(stream).useDelimiter("\\A").next();
        
            // construct result object for return
            SearchResults results = new SearchResults(new HashMap<String, String>(), response);
            
            stream.close();
            return results;
        }
    ```

3. 输出 JSON 响应。

    ```java
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(result.jsonResponse));
    ```

4. 运行该程序。
    
## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [生成自定义搜索 Web 应用](./tutorials/custom-search-web-page.md)