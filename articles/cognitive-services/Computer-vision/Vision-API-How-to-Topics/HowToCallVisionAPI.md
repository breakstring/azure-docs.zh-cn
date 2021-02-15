---
title: 调用计算机视觉 API
titleSuffix: Azure Cognitive Services
description: 了解如何使用 Azure 认知服务中的 REST API 调用计算机视觉 API。
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: e5e404e4cc0e4131ba7ade5ec8ec6115e26268a2
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91965890"
---
# <a name="call-the-computer-vision-api"></a>调用计算机视觉 API

本文演示如何使用 REST API 调用计算机视觉 API。 示例是使用计算机视觉 API 客户端库以 C# 语言作为 HTTP POST/GET 调用编写的。 本文重点介绍：

- 获取标记、说明和类别
- 获取特定于域的信息（或“名人”）

本文中的示例演示以下功能：

* 分析图像以返回标记数组和说明
* 使用特定于域的模型（具体而言，是“名人”模型）分析图像并以 JSON 格式返回相应结果

这些功能提供以下选项：

- **选项 1**：范围分析 - 仅分析指定的模型
- **选项 2**：增强分析 - 使用 [86 类别分类法](../Category-Taxonomy.md)进行分析，以提供更多详细信息

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="创建计算机视觉资源"  target="_blank">创建计算机视觉资源 <span class="docon docon-navigate-external x-hidden-focus"></span></a>，获取密钥和终结点。 部署后，单击“转到资源”。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到计算机视觉服务。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。
* 本地存储的图像的图像 URL 或路径
* 支持的输入方法：原始图像二进制，采用应用程序/八位字节流或图像 URL 的形式
* 支持的图像文件格式：JPEG、PNG、GIF 和 BMP
* 图像文件大小：4 MB 或更小
* 图像尺寸：50 &times; 50 像素或以上
  
## <a name="authorize-the-api-call"></a>授权 API 调用

每次调用计算机视觉 API 都需要订阅密钥。 此密钥必须通过查询字符串参数传递，或在请求标头中指定。

可通过以下任一操作传递订阅密钥：

* 通过查询字符串传递，如以下计算机视觉 API 示例所示：

  ```
  https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
  ```

* 在 HTTP 请求标头中指定：

  ```
  ocp-apim-subscription-key: <Your subscription key>
  ```

* 使用客户端库时，请通过 ComputerVisionClient 的构造函数传递密钥，并在客户端的某个属性中指定区域：

    ```
    var visionClient = new ComputerVisionClient(new ApiKeyServiceClientCredentials("Your subscriptionKey"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com"
    }
    ```

## <a name="upload-an-image-to-the-computer-vision-api-service"></a>将图像上传到计算机视觉 API 服务

执行计算机视觉 API 调用的基本方法是直接上传图像以返回标记、说明和名人。 为此，可以发送一个“POST”请求并在其 HTTP 正文中包含二进制图像，另外包含从图像读取的数据。 所有计算机视觉 API 调用的上传方法相同。 唯一的差别在于指定的查询参数。 

对于指定的图像，使用以下选项之一获取标记和说明：

### <a name="option-1-get-a-list-of-tags-and-a-description"></a>选项 1：获取标记列表和说明

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using System.IO;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

ImageAnalysis imageAnalysis;
var features = new VisualFeatureTypes[] { VisualFeatureTypes.Tags, VisualFeatureTypes.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageAnalysis = await visionClient.AnalyzeImageInStreamAsync(fs, features);
}
```

### <a name="option-2-get-a-list-of-tags-only-or-a-description-only"></a>选项 2：仅获取标记列表，或仅获取说明

（仅对于标记）运行：

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/tag?subscription-key=<Your subscription key>
var tagResults = await visionClient.TagImageAsync("http://contoso.com/example.jpg");
```

（仅对于说明）运行：

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/describe?subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageDescription = await visionClient.DescribeImageInStreamAsync(fs);
}
```

## <a name="get-domain-specific-analysis-celebrities"></a>获取特定领域的分析（名人）

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>选项 1：范围分析 - 仅分析指定的模型
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

对于此选项，所有其他查询参数 {visualFeatures, details} 无效。 如果要查看所有支持的模型，请使用：

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.1/models 
var models = await visionClient.ListModelsAsync();
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-86-categories-taxonomy"></a>选项 2：增强分析 - 使用 86 类别分类法进行分析，以提供更多详细信息

对于你要从一个或多个特定于域的模型获取常规图像分析结果以及详细信息的应用程序，请使用模型查询参数扩展 v1 API。

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?details=celebrities
```

调用此方法时，请先调用 [86 类别](../Category-Taxonomy.md)分类器。 如果任一类别与某个已知或匹配模型的类别匹配，将执行第二轮分类器调用。 例如，如果“details=all”，或者“details”包括“celebrities”，则会在调用 86 类别分类器后调用 celebrities 模型。 结果包含人员类别。 相比“选项 1”，此方法会增大对名人感兴趣的用户所遇到的延迟

在这种情况下，所有 v1 查询参数的行为相同。 如果未指定 visualFeatures=categories，会隐式启用它。

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>检索并了解 JSON 输出以进行分析

下面是一个示例：

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

字段 | 类型 | 内容
------|------|------|
Tags  | `object` | 标记数组的顶级对象。
tags[].Name | `string`    | 标记分类器中的关键字。
tags[].Score    | `number`    | 置信度评分，介于 0 和 1 之间。
description     | `object`    | 说明的顶级对象。
description.tags[] |    `string`    | 标记列表。  如果置信度不足，因此无法生成标题，则标记可能是可供调用方使用的唯一信息。
description.captions[].text    | `string`    | 描述图像的短语。
description.captions[].confidence    | `number`    | 短语的置信度评分。

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>检索并了解特定于域的模型的 JSON 输出

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>选项 1：范围分析 - 仅分析指定的模型

输出是一个标记数组，如以下示例中所示：

```json
{  
  "result":[  
    {  
      "name":"golden retriever",
      "score":0.98
    },
    {  
      "name":"Labrador retriever",
      "score":0.78
    }
  ]
}
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-the-86-categories-taxonomy"></a>选项 2：增强分析 - 使用“86 类别”分类法进行分析，以提供更多详细信息

对于使用“选项 2”（增强分析）的特定于域的模型，类别返回类型将会扩展，如以下示例中所示：

```json
{  
  "requestId":"87e44580-925a-49c8-b661-d1c54d1b83b5",
  "metadata":{  
    "width":640,
    "height":430,
    "format":"Jpeg"
  },
  "result":{  
    "celebrities":[  
      {  
        "name":"Richard Nixon",
        "faceRectangle":{  
          "left":107,
          "top":98,
          "width":165,
          "height":165
        },
        "confidence":0.9999827
      }
    ]
  }
}
```

类别字段是原始分类法中一个或多个 [86 类别](../Category-Taxonomy.md)的列表。 以下划线结尾的类别将匹配该类别及其子级（例如，在 celebrities 模型中匹配“people_”或“people_group”）。

字段    | 类型    | 内容
------|------|------|
categories | `object`    | 顶级对象
categories[].name     | `string`    | 86 类别分类法列表中的名称。
categories[].score    | `number`    | 置信度评分，介于 0 和 1 之间。
categories[].detail     | `object?`      | （可选）详细信息对象。

如果多个类别匹配（例如，86 类别分类器在 model=celebrities 时同时返回“people_”和“people_young”的评分），则详细信息将附加到最宽泛级别的匹配项（在该示例中为“people_”）。

## <a name="error-responses"></a>错误响应

这些错误与 vision.analyze 中的错误相同，另外还会出现 NotSupportedModel 错误 (HTTP 400)，该错误在使用“选项 1”和“选项 2”时都可能会返回。 对于“选项 2”（增强分析），如果无法识别详细信息中指定的任何模型，则即使一个或多个模型有效，API 也会返回 NotSupportedModel。 若要确定哪些模型受支持，可以调用 listModels。

## <a name="next-steps"></a>后续步骤

要使用 REST API，请转到[计算机视觉 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b)。
