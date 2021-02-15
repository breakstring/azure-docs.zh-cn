---
title: 必应视频搜索 C# 客户端库快速入门
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 6d50a8e2c9d0263616b25e25958be6a6f0fb7fe1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "88929257"
---
通过本快速入门开始使用适用于 C# 的必应视频搜索客户端库来搜索新闻。 虽然必应视频搜索具有与大多数编程语言兼容的 REST API，但该客户端库提供了一种简单方法来将服务集成到应用程序中。 此示例的源代码可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) 上找到，其中包含其他注释和功能。

## <a name="prerequisites"></a>先决条件

* 任何版本的 [Visual Studio 2017 或更高版本](https://visualstudio.microsoft.com/downloads/)。
* Json.NET 框架，以 [NuGet 程序包](https://www.nuget.org/packages/Newtonsoft.Json/)的形式提供。

若要向项目中添加必应视频搜索客户端库，请在 Visual Studio 中的**解决方案资源管理器**中选择“管理 NuGet 包”  选项。 添加 `Microsoft.Azure.CognitiveServices.Search.VideoSearch` 程序包。

安装 [[NuGet 视频搜索 SDK 程序包]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0)还将安装以下依赖项：

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>创建并初始化项目

1. 在 Visual Studio 中创建一个新的 C# 控制台解决方案。 然后将以下内容添加到主代码文件。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. 通过使用你的订阅密钥创建新的 `ApiKeyServiceClientCredentials` 对象并调用构造函数来实例化客户端。

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>发送搜索请求并处理结果

1. 使用客户端发送搜索请求。 在搜索查询中使用 "SwiftKey"。

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. 如果返回了任何结果，则通过 `videoResults.Value[0]` 获取第一个结果。 然后输出该视频的 ID、标题和 url。

    ```csharp
    if (videoResults.Value.Count > 0)
    {
        var firstVideoResult = videoResults.Value[0];

        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
        Console.WriteLine($"First video name: {firstVideoResult.Name}");
        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
    }
    else
    {
        Console.WriteLine("Couldn't find video results!");
    }
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建单页 Web 应用](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>另请参阅 

* [什么是必应视频搜索 API？](../../overview.md)
* [认知服务 .NET SDK 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
