---
title: 必应视频搜索 Java 客户端库快速入门
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 2b3d4993406f150b2983d4d820f7d070b5de1e96
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "87374865"
---
通过本快速入门开始使用适用于 Java 的必应视频搜索客户端库来搜索新闻。 虽然必应视频搜索具有与大多数编程语言兼容的 REST API，但该客户端库提供了一种简单方法来将服务集成到应用程序中。 此示例的源代码可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVideoSearch) 上找到，其中包含其他注释和功能。

## <a name="prerequisites"></a>先决条件

* [Java 开发工具包 (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

* [Gson 库](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

通过使用 Maven、Gradle 或其他依赖项管理系统安装必应视频搜索客户端库依赖项。 Maven POM 文件需要以下声明：

```xml
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-videosearch</artifactId>
      <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
  </dependencies> 
```

## <a name="create-and-initialize-a-project"></a>创建并初始化项目


在你最喜欢的 IDE 或编辑器中新建一个 Java 项目，并导入以下库。

```java
    import com.microsoft.azure.cognitiveservices.videosearch.*;
    import com.microsoft.azure.cognitiveservices.videosearch.VideoObject;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List; 
```

## <a name="create-a-search-client"></a>创建搜索客户端

1. 实现 `VideoSearchAPIImpl` 客户端，这需要使用你的 API 终结点以及 `ServiceClientCredentials` 类的实例。

    ```java
    public static VideoSearchAPIImpl getClient(final String subscriptionKey) {
        return new VideoSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    若要实现 `ServiceClientCredentials`，请执行以下步骤：

    1. 重写 `applyCredentialsFilter()` 函数，使用 `OkHttpClient.Builder` 对象作为参数。 
        
        ```java
        //...
        new ServiceClientCredentials() {
                @Override
                public void applyCredentialsFilter(OkHttpClient.Builder builder) {
                //...
                }
        //...
        ```
    
    2. 在 `applyCredentialsFilter()` 中，调用 `builder.addNetworkInterceptor()`。 创建一个新的 `Interceptor` 对象，并重写其 `intercept()` 方法来获取 `Chain` 侦听器对象。

        ```java
        //...
        builder.addNetworkInterceptor(
            new Interceptor() {
                @Override
                public Response intercept(Chain chain) throws IOException {
                //...    
                }
            });
        ///...
        ```

    3. 在 `intercept` 函数中，为请求创建变量。 使用 `Request.Builder()` 生成请求。 将订阅密钥添加到 `Ocp-Apim-Subscription-Key` 标头，并返回请求对象中的 `chain.proceed()`。
            
        ```java
        //...
        public Response intercept(Chain chain) throws IOException {
            Request request = null;
            Request original = chain.request();
            Request.Builder requestBuilder = original.newBuilder()
                    .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            request = requestBuilder.build();
            return chain.proceed(request);
        }
        //...
        ```

## <a name="send-a-search-request-and-receive-the-response"></a>发送搜索请求并接收响应 

1. 创建名为 `VideoSearch()` 的函数，该函数采用字符串形式的订阅密钥。 实例化前面创建的搜索客户端。
    
    ```java
    public static void VideoSearch(String subscriptionKey){
        VideoSearchAPIImpl client = VideoSDK.getClient(subscriptionKey);
        //...
    }
    ```
2. 在 `VideoSearch()` 中，使用 `SwiftKey` 作为搜索词，通过客户端发送视频搜索请求。 如果视频搜索 API 返回了结果，请获取第一个结果并列显其 ID、名称和 URL，以及返回的视频总数。 
    
    ```java
    VideosInner videoResults = client.searchs().list("SwiftKey");

    if (videoResults == null){
        System.out.println("Didn't see any video result data..");
    }
    else{
        if (videoResults.value().size() > 0){
            VideoObject firstVideoResult = videoResults.value().get(0);

            System.out.println(String.format("Video result count: %d", videoResults.value().size()));
            System.out.println(String.format("First video id: %s", firstVideoResult.videoId()));
            System.out.println(String.format("First video name: %s", firstVideoResult.name()));
            System.out.println(String.format("First video url: %s", firstVideoResult.contentUrl()));
        }
        else{
            System.out.println("Couldn't find video results!");
        }
    }
    ```

3. 从 main 方法调用 search 方法。

    ```java
    public static void main(String[] args) {
        VideoSDK.VideoSearch("YOUR-SUBSCRIPTION-KEY");
    }
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建单页 Web 应用](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>另请参阅 

* [什么是必应视频搜索 API？](../../overview.md)
* [认知服务 .NET SDK 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
