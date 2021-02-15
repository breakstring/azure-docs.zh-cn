---
title: 快速入门：适用于 Go 的计算机视觉客户端库
titleSuffix: Azure Cognitive Services
description: 通过本快速入门开始使用适用于 Go 的计算机视觉客户端库。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: df171b74014179c36466a2683a8d0d7c0c3b5e90
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560876"
---
<a name="HOLTop"></a>

使用计算机视觉客户端库可以：

* 分析图像中的标记、文本说明、人脸、成人内容，等等。
* 使用 Read API 读取印刷文本和手写文本。

[参考文档](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision) | [库源代码](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision) | [包](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* 最新版本的 [Go](https://golang.org/dl/)
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="创建计算机视觉资源"  target="_blank">创建计算机视觉资源 <span class="docon docon-navigate-external x-hidden-focus"></span></a>，获取密钥和终结点。 部署后，单击“转到资源”。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到计算机视觉服务。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。
* 为密钥和终结点 URL [创建环境变量](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)，分别将其命名为 `COMPUTER_VISION_SUBSCRIPTION_KEY` 和 `COMPUTER_VISION_ENDPOINT`。

## <a name="setting-up"></a>设置

### <a name="create-a-go-project-directory"></a>创建 Go 项目目录

在控制台窗口（cmd、PowerShell、终端、Bash）中，为 Go 项目创建一个名为 `my-app` 的新工作区并导航到该工作区。

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

工作区包含三个文件夹：

* **src** - 此目录包含源代码和包。 使用 `go get` 命令安装的任何包都将在此目录中。
* **pkg** - 此目录包含编译的 Go 包对象。 这些文件使用 `.a` 扩展名。
* **bin** - 此目录包含运行 `go install` 时创建的二进制可执行文件。

> [!TIP]
> 若要了解有关 Go 工作区结构的详细信息，请参阅 [Go 语言文档](https://golang.org/doc/code.html#Workspaces)。 本指南包含有关设置 `$GOPATH` 和 `$GOROOT` 的信息。

### <a name="install-the-client-library-for-go"></a>安装适用于 Go 的客户端库

接下来，安装适用于 Go 的客户端库：

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

或者，如果使用 dep，则在存储库中运行：

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>创建 Go 应用程序 

接下来，在 src 目录中创建名为 `sample-app.go` 的文件：

```bash
cd src
touch sample-app.go
```

在首选 IDE 或文本编辑器中打开 `sample-app.go`。 然后添加包名称并导入以下库：

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports)]

此外，还可以在脚本的根目录中声明一个上下文。 你将需要此对象来执行大多数计算机视觉函数调用：

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_context)]

接下来，将开始添加代码以执行不同的计算机视觉操作。

> [!div class="nextstepaction"]
> [我设置了客户端](?success=set-up-client#object-model) [我遇到问题](https://www.research.net/r/7QYZKHL?issue=set-up-client)

## <a name="object-model"></a>对象模型

以下类和接口将处理计算机视觉 Go SDK 的某些主要功能。

|名称|说明|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | 所有计算机视觉功能（如图像分析和文本阅读）都需要此类。 可以使用订阅信息实例化此类，然后使用它来执行大多数图像操作。|
|[ImageAnalysis](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ImageAnalysis)| 此类型包含 AnalyzeImage 函数调用的结果。 每个特定于类别的函数都具有相似的类型。|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| 此类型包含批读取操作的结果。 |
|[VisualFeatureTypes](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#VisualFeatureTypes)| 此类型定义了可在标准分析操作中完成的不同类型的图像分析。 请根据需求指定一组 VisualFeatureTypes 值。 |

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Go 的计算机视觉客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [分析图像](#analyze-an-image)
* [读取印刷体文本和手写文本](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>验证客户端

> [!NOTE]
> 此步骤假设已经为计算机视觉密钥和终结点（分别名为 `COMPUTER_VISION_SUBSCRIPTION_KEY` 和 `COMPUTER_VISION_ENDPOINT`）[创建了环境变量](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)。

创建 `main` 函数，并向其添加以下代码，以使用终结点和密钥实例化客户端。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [我执行了客户端身份验证](?success=authenticate-client#analyze-an-image) [我遇到问题](https://www.research.net/r/7QYZKHL?issue=authenticate-client)

## <a name="analyze-an-image"></a>分析图像

以下代码使用客户端对象分析远程映像，并将结果打印到控制台。 可获取文本说明、分类、标记列表、检测到的物体、检测到的品牌、检测到的人脸、成人内容标志、主要颜色和图像类型。

### <a name="set-up-test-image"></a>设置测试图像

首先，保存对要分析的图像的 URL 的引用。 将其放入 `main` 函数。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_url)]

> [!TIP]
> 还可以分析本地图像。 请参阅 [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) 方法，例如 DescribeImageInStream。 或者，请参阅 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) 上的示例代码，了解涉及本地图像的方案。

### <a name="specify-visual-features"></a>指定视觉特性

以下函数调用从示例图像中提取不同的视觉特征。 将在以下各节中定义这些函数。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze)]

### <a name="get-image-description"></a>获取图像说明

以下函数获取为图像生成的描述文字列表。 有关图像描述的详细信息，请参阅[描述图像](../../concept-describing-images.md)。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_describe)]

### <a name="get-image-category"></a>获取图像类别

以下函数获取已检测到的映像类别。 有关详细信息，请参阅[对图像进行分类](../../concept-categorizing-images.md)。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_analyze_categorize)]

### <a name="get-image-tags"></a>获取图像标记

以下函数获取映像中检测到的标记集。 有关详细信息，请参阅[内容标记](../../concept-tagging-images.md)。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_tags)]

### <a name="detect-objects"></a>检测物体

以下函数检测图像中的常见物体并将其打印到控制台。 有关详细信息，请参阅[物体检测](../../concept-object-detection.md)。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_objects)]

### <a name="detect-brands"></a>检测品牌

以下代码检测图像中的公司品牌和徽标，并将其输出到控制台。 有关详细信息，请参阅[品牌检测](../../concept-brand-detection.md)。

首先，在 `main` 函数内声明对新图像的引用。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brand_url)]

以下代码定义品牌检测函数。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_brands)]

### <a name="detect-faces"></a>检测人脸

以下函数返回图像中检测到的人脸及其矩形坐标，以及某些人脸属性。 有关详细信息，请参阅[人脸检测](../../concept-detecting-faces.md)。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>检测成人、色情或血腥内容

以下函数打印图像中检测到的成人内容。 有关详细信息，请参阅[成人、色情或血腥内容](../../concept-detecting-adult-content.md)。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>获取图像配色方案

以下函数打印图像中检测到的颜色属性，如主色和主题色。 有关详细信息，请参阅[配色方案](../../concept-detecting-color-schemes.md)。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_color)]

### <a name="get-domain-specific-content"></a>获取特定于域的内容

计算机视觉可以使用专用模型对图像进行进一步分析。 有关详细信息，请参阅[特定于域的内容](../../concept-detecting-domain-content.md)。 

以下代码分析了图像中检测到的名人的相关数据。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_celebs)]

以下代码分析了图像中检测到的地标的相关数据。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>获取图像类型

以下函数将打印有关图像类型的信息，即表明它是剪贴画还是素描。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_type)]

> [!div class="nextstepaction"]
> [我分析了图像](?success=analyze-image#read-printed-and-handwritten-text) [我遇到问题](https://www.research.net/r/7QYZKHL?issue=analyze-image)

## <a name="read-printed-and-handwritten-text"></a>读取印刷体文本和手写文本

计算机视觉可以读取图像中的可见文本，并将其转换为字符流。 本部分的代码定义了函数 `RecognizeTextReadAPIRemoteImage`，该函数使用客户端对象检测并提取图像中的印刷体文本或手写文本。

在 `main` 函数中添加示例图像引用和函数调用。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!TIP]
> 还可以从本地图像提取文本。 请参阅 [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) 方法，例如 BatchReadFileInStream。 或者，请参阅 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) 上的示例代码，了解涉及本地图像的方案。

### <a name="call-the-read-api"></a>调用读取 API

定义用于读取文本的新函数 `RecognizeTextReadAPIRemoteImage`。 添加以下代码，该代码对给定图像调用 BatchReadFile 方法。 此方法返回一个操作 ID 并启动异步进程来读取图像的内容。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>获取读取结果

接下来，获取 BatchReadFile 调用返回的操作 ID，并将其用于 GetReadOperationResult 方法，向服务查询操作结果 。 下面的代码每隔一秒钟检查一次操作，直到返回结果。 然后，它将提取的文本数据输出到控制台。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>显示读取结果

添加以下代码来分析和显示检索到的文本数据，并完成函数定义。

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

> [!div class="nextstepaction"]
> [我阅读了文字](?success=read-printed-handwritten-text#run-the-application) [我遇到问题](https://www.research.net/r/7QYZKHL?issue=read-printed-handwritten-text)

## <a name="run-the-application"></a>运行应用程序

从应用程序目录使用 `go run` 命令运行应用程序。

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [我运行了应用程序](?success=run-the-application#clean-up-resources) [我遇到问题](https://www.research.net/r/7QYZKHL?issue=run-the-application)

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [我清理了资源](?success=clean-up-resources#next-steps) [我遇到问题](https://www.research.net/r/7QYZKHL?issue=clean-up-resources)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [计算机视觉 API 参考 (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)


* [什么是计算机视觉？](../../overview.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) 上找到此示例的源代码。