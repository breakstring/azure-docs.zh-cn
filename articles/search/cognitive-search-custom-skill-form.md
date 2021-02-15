---
title: '窗体识别器自定义技能 (c # ) '
titleSuffix: Azure Cognitive Search
description: '了解如何使用 c # 和 Visual Studio 创建窗体识别器自定义技能。'
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: a3b073cdb90e0c427bfbca15c1440b9122672610
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880129"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>示例：创建窗体识别器自定义技能

在此 Azure 认知搜索技能组合示例中，你将学习如何使用 c # 和 Visual Studio 创建窗体识别器自定义技能。 表单识别器将分析文档并提取键/值对和表数据。 通过将窗体识别器包装到 [自定义技能界面](cognitive-search-custom-skill-interface.md)，可以将此功能添加为端对端扩充管道中的一个步骤。 然后，管道可以加载文档和执行其他转换。

## <a name="prerequisites"></a>先决条件

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)（版本不限）。
- 至少5种相同类型的窗体。 您可以使用本指南中提供的示例数据。

## <a name="create-a-form-recognizer-resource"></a>创建表单识别器资源

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>训练模型

使用此技能之前，需要使用输入窗体训练窗体识别器模型。 请按照 " [卷快速入门](../cognitive-services/form-recognizer/quickstarts/client-library.md?pivots=programming-language-rest-api) " 了解如何训练模型。 您可以使用该快速入门中提供的示例窗体，也可以使用自己的数据。 训练模型后，将其 ID 值复制到安全位置。

## <a name="set-up-the-custom-skill"></a>设置自定义技能

本教程使用[Azure 搜索增强功能](https://github.com/Azure-Samples/azure-search-power-skills)GitHub 存储库中的[AnalyzeForm](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm)项目。 将此存储库克隆到本地计算机，并导航到 **远景/AnalyzeForm/** 以访问该项目。 然后在 Visual Studio 中打开 _AnalyzeForm_ 。 此项目将创建一个 Azure 函数资源，用于满足 [自定义技能接口](cognitive-search-custom-skill-interface.md) ，并可用于 azure 认知搜索扩充。 它采用窗体文档作为输入，并以文本形式输出 (，) 指定的键/值对。

首先，添加项目级环境变量。 在左侧窗格中找到 " **AnalyzeForm** " 项目，右键单击该项目，然后选择 " **属性**"。 在 " **属性** " 窗口中，单击 " **调试** " 选项卡，然后找到 " **环境变量** " 字段。 单击 " **添加** " 以添加以下变量：
* `FORMS_RECOGNIZER_ENDPOINT_URL` 值设置为终结点 URL。
* `FORMS_RECOGNIZER_API_KEY` 值设置为你的订阅密钥。
* `FORMS_RECOGNIZER_MODEL_ID` 值设置为您训练的模型的 ID。
* `FORMS_RECOGNIZER_RETRY_DELAY` 值设置为1000。 此值是程序重试查询前等待的时间（以毫秒为单位）。
* `FORMS_RECOGNIZER_MAX_ATTEMPTS` 值设置为100。 此值是程序在尝试获取成功响应时将查询服务的次数。

接下来，打开 _AnalyzeForm.cs_ 并找到 `fieldMappings` 变量，该变量引用文件 *上的field-mappings.js* 。 此文件 (和引用它的变量) 定义要从窗体中提取的键的列表，以及每个键的自定义标签。 例如，如果值为，则 `{ "Address:", "address" }, { "Invoice For:", "recipient" }` 表示脚本将仅保存检测到的 `Address:` 和字段的值 `Invoice For:` ，并且将分别使用和标记这些 `"address"` 值 `"recipient"` 。

最后，请记下 `contentType` 变量。 此脚本在 URL 引用的远程文档上运行给定的窗体识别器模型，因此内容类型为 `application/json` 。 如果要通过在 HTTP 请求中包含字节流来分析本地文件，则需要将更改 `contentType` 为文件的相应 [MIME 类型](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types) 。

## <a name="test-the-function-from-visual-studio"></a>从 Visual Studio 中测试函数

编辑项目后，将其保存，并在 Visual Studio 中将 **AnalyzeForm** 项目设置为启动项目 (如果尚未设置该项目) 。 然后按 **F5** 在本地环境中运行该函数。 使用 REST 服务（如 [Postman](https://www.postman.com/) ）调用函数。

### <a name="http-request"></a>HTTP 请求

你将发出以下请求来调用函数。

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>请求正文

从下面的请求正文模板开始。

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": { 
                "formUrl": "<your-form-url>",
                "formSasToken": "<your-sas-token>"
            }
        }
    ]
}
```

在这里，你将需要提供与你训练的窗体具有相同类型的窗体的 URL。 出于测试目的，可以使用您的一种训练窗体。 如果遵循了卷快速入门，则窗体将位于 Azure blob 存储帐户中。 打开 Azure 存储资源管理器，找到一个窗体文件，右键单击该文件，然后选择 " **获取共享访问签名**"。 下一个对话框窗口将提供 URL 和 SAS 令牌。 将这些字符串分别输入 `"formUrl"` 到 `"formSasToken"` 请求正文的和字段中。

> [!div class="mx-imgBorder"]
> ![Azure 存储资源管理器;选中 pdf 文档](media/cognitive-search-skill-form/form-sas.png)

如果要分析不在 Azure blob 存储中的远程文档，请在字段中粘贴其 URL， `"formUrl"` 并将该 `"formSasToken"` 字段留空。

> [!NOTE]
> 在技能组合中集成了技能后，将认知搜索提供 URL 和令牌。

### <a name="response"></a>响应

你应该看到类似于以下示例的响应：

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": {
                "address": "1111 8th st. Bellevue, WA 99501 ",
                "recipient": "Southridge Video 1060 Main St. Atlanta, GA 65024 "
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>将函数发布到 Azure

当你对函数行为满意时，可将其发布。

1. 在 Visual Studio 的 " **解决方案资源管理器** 中，右键单击项目，然后选择" **发布**"。 选择 "**新建**  >  **发布**"。
1. 如果尚未将 Visual Studio 连接到 Azure 帐户，请选择“添加帐户...”
1. 请按照屏幕上的提示操作。 为应用服务、Azure 订阅、资源组、托管计划以及要使用的存储帐户指定唯一名称。 如果还没有存储帐户，可以创建新的资源组和新的托管计划，并创建新的存储帐户。 完成后，请选择“创建”。
1. 部署完成后，请记下站点 URL。 此 URL 是 Azure 中函数应用的地址。 将其保存到临时位置。
1. 在 [Azure 门户](https://portal.azure.com)中，导航到资源组，然后查找 `AnalyzeForm` 已发布的函数。 在“管理”部分下，应可看到主机密钥。 复制 *默认* 主机密钥并将其保存到临时位置。

## <a name="connect-to-your-pipeline"></a>连接到管道

若要在认知搜索管道中使用此技能，需要向技能组合添加技能定义。 下面的 JSON 块是一个示例技能定义 (应更新输入和输出，以反映特定方案和技能组合环境) 。 `AzureFunctionEndpointUrl`将替换为函数 URL，并 `AzureFunctionDefaultHostKey` 将替换为主机密钥。

```json
{ 
  "description":"Skillset that invokes the Form Recognizer custom skill",
  "skills":[ 
    "[... your existing skills go here]",
    { 
      "@odata.type":"#Microsoft.Skills.Custom.WebApiSkill",
      "name":"formrecognizer",
      "description":"Extracts fields from a form using a pre-trained form recognition model",
      "uri":"[AzureFunctionEndpointUrl]/api/analyze-form?code=[AzureFunctionDefaultHostKey]",
      "httpMethod":"POST",
      "timeout":"PT30S",
      "context":"/document",
      "batchSize":1,
      "inputs":[ 
        { 
          "name":"formUrl",
          "source":"/document/metadata_storage_path"
        },
        { 
          "name":"formSasToken",
          "source":"/document/metadata_storage_sas_token"
        }
      ],
      "outputs":[ 
        { 
          "name":"address",
          "targetName":"address"
        },
        { 
          "name":"recipient",
          "targetName":"recipient"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

本指南介绍了如何从 Azure 窗体识别器服务创建自定义的技能。 若要了解有关自定义技能的详细信息，请参阅以下资源。 

* [Azure 搜索的强大技能：定制技能的存储库](https://github.com/Azure-Samples/azure-search-power-skills)
* [将自定义技能添加到 AI 扩充管道](cognitive-search-custom-skill-interface.md)
* [定义技能集](cognitive-search-defining-skillset.md)
* [创建技能组合 (REST) ](/rest/api/searchservice/create-skillset)
* [映射充实字段](cognitive-search-output-field-mapping.md)