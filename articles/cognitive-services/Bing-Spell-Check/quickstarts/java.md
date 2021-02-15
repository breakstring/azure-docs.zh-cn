---
title: 快速入门：使用 REST API 和 Java 检查拼写 - 必应拼写检查
titleSuffix: Azure Cognitive Services
description: 开始使用必应拼写检查 REST API 和 Java 检查拼写和语法。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: cc6ad88b70a1addbbf7df6a53218810de683e5db
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352705"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>快速入门：使用必应拼写检查 REST API 和 Java 进行拼写检查

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

根据此快速入门中的说明对必应拼写检查 REST API 进行第一次调用。 此简单的 Java 应用程序将向 API 发送请求并返回一系列建议的更正。 

虽然此应用程序是使用 Java 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheck.java) 上提供了此应用程序的源代码。

## <a name="prerequisites"></a>先决条件

* Java 开发工具包 (JDK) 7 或更高版本。

* 导入 [gson-2.8.5.jar](https://libraries.io/maven/com.google.code.gson%3Agson) 或最新的 [Gson](https://github.com/google/gson) 版本。 对于命令行执行，请将 `.jar` 添加到具有主类的 Java 文件夹中。

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>创建并初始化应用程序

1. 在常用的 IDE 或编辑器中使用所选类名新建一个 Java 项目，然后导入以下包：

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. 为 API 终结点主机、路径和订阅密钥创建变量。 然后，创建市场变量、要进行拼写检查的文本和拼写检查模式的字符串。 你可以使用以下代码中的全局终结点，或者使用资源的 Azure 门户中显示的[自定义子域](../../../cognitive-services/cognitive-services-custom-subdomains.md)终结点。

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>创建和发送 API 请求

1. 创建名为 `check()` 的函数，用于创建和发送 API 请求。 在此函数中，添加在后续步骤中指定的代码。 创建请求参数的字符串：

   1. 用 `=` 运算符将市场代码分配到 `mkt` 参数。 

   1. 使用 `&` 运算符添加 `mode` 参数，然后分配拼写检查模式。 

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. 通过组合终结点主机、路径和参数字符串，创建 URL。 创建新 `HttpsURLConnection` 对象。

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. 与 URL 建立连接。 将请求方法设置为 `POST` 并添加请求参数。 请务必将订阅密钥添加到 `Ocp-Apim-Subscription-Key` 标头中。

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. 创建新的 `DataOutputStream` 对象并向 API 发送请求。

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>设置格式并读取 API 响应

1. 将 `prettify()` 方法添加到类，该方法将 JSON 格式设置为可读性更强的输出。

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

1. 创建 `BufferedReader` 并读取来自该 API 的响应。 打印到控制台。
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(prettify(line));
    }
    in.close();
    ```

## <a name="call-the-api"></a>调用 API

在应用程序的主函数中，调用之前创建的 `check()` 方法。
```java
        public static void main(String[] args) {
            try {
                check();
            }
            catch (Exception e) {
                System.out.println (e);
            }
        }
```

## <a name="run-the-application"></a>运行应用程序

生成并运行项目。 如果使用命令行，则使用以下命令生成并运行应用程序：

1. 构建应用程序：

   ```bash
   javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
   ```

2. 运行应用程序：

   ```bash
   java -cp .;gson-2.2.2.jar\* <CLASS_NAME>
   ```

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