---
title: 必应视觉搜索 Java 客户端库快速入门
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.custom: devx-track-java, devx-track-csharp
ms.author: aahi
ms.openlocfilehash: d1c9589265726bae01b86c152853c40f79825ceb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948472"
---
按照本快速入门，开始使用 Java 客户端库通过必应视觉搜索服务获取图像见解。 虽然必应视觉搜索具有与大多数编程语言兼容的 REST API，但该客户端库提供了一种简单方法来将服务集成到应用程序中。 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVisualSearch) 上找到此快速入门的源代码。

使用适用于 Java 的必应视觉搜索客户端库，可以执行以下操作：

* 上传图像以发送视觉搜索请求。
* 获取图像见解标记和视觉搜索标记。

[参考文档](/java/api/overview/azure/cognitiveservices/client/bingvisualsearch) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch) | [项目 (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-visualsearch/) | [示例](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* 最新版本的 [Java 开发工具包 (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle 生成工具](https://gradle.org/install/)，或其他依赖项管理器

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]

从资源获取密钥后，为密钥[创建一个环境变量](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)（名为 `BING_SEARCH_V7_SUBSCRIPTION_KEY`）。

### <a name="create-a-new-gradle-project"></a>创建新的 Gradle 项目

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。 

```console
mkdir myapp && cd myapp
```

从工作目录运行 `gradle init` 命令。 此命令将创建 Gradle 的基本生成文件，包括 *build.gradle.kts*，在运行时将使用该文件创建并配置应用程序。

```console
gradle init --type basic
```

当提示你选择一个 **DSL** 时，选择 **Kotlin**。

找到 *build.gradle.kts*，并使用喜好的 IDE 或文本编辑器将其打开。 然后将以下生成配置复制到其中：

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingVisualSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

为示例应用创建一个文件夹。 在工作目录中运行以下命令：

```console
mkdir -p src/main/java
```

为要上传到 API 的图像创建一个文件夹。 将图像放入 **resources** 文件夹。

```console
mkdir -p src/main/resources
``` 

导航到新文件夹，创建名为 *BingVisualSearchSample.java* 的文件。 在喜好的编辑器或 IDE 中打开该文件并添加以下 `import` 语句：

[!code-java[Import statements](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=imports)]

然后创建一个新类。

```java
public class BingVisualSearchSample {
}
```

在应用程序的 `main` 方法中，为该资源的 Azure 终结点和密钥创建变量。 如果在启动应用程序后创建了环境变量，则需要关闭再重新打开运行该应用程序的编辑器、IDE 或 shell 才能访问该变量。 然后为要上传的图像创建 `byte[]`。 为稍后要定义的方法创建 `try` 块，加载该图像并使用 `toByteArray()` 将其转换为字节。

[!code-java[Main method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=main)]


### <a name="install-the-client-library"></a>安装客户端库

本快速入门使用 Gradle 依赖项管理器。 可以在 [Maven 中央存储库](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/)中找到客户端库以及其他依赖项管理器的信息。

在项目的 *build.gradle.kts* 文件中，请务必以 `implementation` 语句的形式包含客户端库。 

```kotlin
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用必应视觉搜索客户端库和 Java 执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [发送视觉搜索请求](#send-a-visual-search-request)
* [输出图像见解标记和视觉搜索标记](#print-the-image-insight-token-and-visual-search-tags)

## <a name="authenticate-the-client"></a>验证客户端

> [!NOTE]
> 本快速入门假设你已为必应视觉搜索密钥创建了名为 `BING_SEARCH_V7_SUBSCRIPTION_KEY` 的[环境变量](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)。


在 main 方法中，请务必使用订阅密钥实例化 [BingVisualSearchAPI](/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingvisualsearchapi) 对象。

```csharp
BingVisualSearchAPI client = BingVisualSearchManager.authenticate(subscriptionKey);
```

## <a name="send-a-visual-search-request"></a>发送视觉搜索请求

在新方法中，使用客户端的 [bingImages().visualSearch()](/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingimages.visualsearch#com_microsoft_azure_cognitiveservices_search_visualsearch_BingImages_visualSearch__) 方法发送图像字节数组（在 `main()` 方法中创建）。 

[!code-java[visualSearch() method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=visualSearch)]

## <a name="print-the-image-insight-token-and-visual-search-tags"></a>输出图像见解标记和视觉搜索标记

检查 [ImageKnowledge](/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.models.imageknowledge) 对象是否为 null。 如果不是，则输出图像见解标记、标记数量、操作数量和第一个操作类型。

[!code-java[Print token and tags](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=printVisualSearchResults)]

## <a name="run-the-application"></a>运行应用程序

可使用以下命令生成应用：

```console
gradle build
```

使用 `run` 目标运行应用程序：

```console
gradle run
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [构建单页 Web 应用](../../tutorial-bing-visual-search-single-page-app.md)
