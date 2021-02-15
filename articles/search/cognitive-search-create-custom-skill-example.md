---
title: 使用必应实体搜索 API 的自定义技能示例
titleSuffix: Azure Cognitive Search
description: 演示如何使用在 Azure 认知搜索中映射到 AI 的索引管道的自定义技能中的 "必应实体搜索服务"。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 5755e14e53d359fd8b322939bf1325d21536d593
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020178"
---
# <a name="example-create-a-custom-skill-using-the-bing-entity-search-api"></a>示例：使用必应实体搜索 API 创建自定义技能

在此示例中，了解如何创建 Web API 自定义技能。 此技能将接受位置、公众人物和组织，并返回其说明。 该示例使用 [Azure 函数](https://azure.microsoft.com/services/functions/) 包装 [必应实体搜索 API](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) 以便实现自定义技能接口。

## <a name="prerequisites"></a>先决条件

+ 如果不熟悉自定义技能应实现的输入/输出接口，请阅读[自定义技能接口](cognitive-search-custom-skill-interface.md)一文。

+ [!INCLUDE [cognitive-services-bing-entity-search-signup-requirements](../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

+ 安装 [Visual Studio 2019](https://www.visualstudio.com/vs/) 或更高版本，包括 Azure 开发工作负荷。

## <a name="create-an-azure-function"></a>创建 Azure 函数

尽管此示例使用 Azure Function 来托管 Web API，但并非必须如此。  只要满足[认知技能的接口需求](cognitive-search-custom-skill-interface.md)，采用的方法并不重要。 但是，可通过 Azure Functions 轻松创建自定义技能。

### <a name="create-a-function-app"></a>创建函数应用

1. 在 Visual Studio 中，从“文件”菜单中选择“新建” > “项目”   。

1. 在“新建项目”对话框中，选择“已安装”，展开“Visual C#” > “云”，选择“Azure Functions”，键入项目的名称，然后选择“确定”      。 函数应用名称必须像 C# 命名空间一样有效，因此请勿使用下划线、连字符或任何其他的非字母数字字符。

1. 选择“Azure Functions v2 (.NET Core)”  。 也可以使用版本 1 执行此操作，但下面的代码基于 v2 模板编写。

1. 选择“HTTP 触发器”作为类型 

1. 对于存储帐户，可选择“无”，因为此函数不需要任何存储  。

1. 选择“确定”以创建函数项目和 HTTP 触发的函数  。

### <a name="modify-the-code-to-call-the-bing-entity-search-service"></a>修改代码以调用必应实体搜索服务

Visual Studio 将创建一个项目，并在该项目中创建一个包含所选函数类型的样本代码的类。 方法中的 *FunctionName* 属性设置函数的名称。 *HttpTrigger* 属性指定该函数将由某个 HTTP 请求触发。

现在，将文件 Function1.cs 的所有内容替换为以下代码  ：

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace SampleSkills
{
    /// <summary>
    /// Sample custom skill that wraps the Bing entity search API to connect it with a 
    /// AI enrichment pipeline.
    /// </summary>
    public static class BingEntitySearch
    {
        #region Credentials
        // IMPORTANT: Make sure to enter your credential and to verify the API endpoint matches yours.
        static readonly string bingApiEndpoint = "https://api.cognitive.microsoft.com/bing/v7.0/entities/";
        static readonly string key = "<enter your api key here>";  
        #endregion

        #region Class used to deserialize the request
        private class InputRecord
        {
            public class InputRecordData
            {
                public string Name { get; set; }
            }

            public string RecordId { get; set; }
            public InputRecordData Data { get; set; }
        }

        private class WebApiRequest
        {
            public List<InputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to serialize the response

        private class OutputRecord
        {
            public class OutputRecordData
            {
                public string Name { get; set; } = "";
                public string Description { get; set; } = "";
                public string Source { get; set; } = "";
                public string SourceUrl { get; set; } = "";
                public string LicenseAttribution { get; set; } = "";
                public string LicenseUrl { get; set; } = "";
            }

            public class OutputRecordMessage
            {
                public string Message { get; set; }
            }

            public string RecordId { get; set; }
            public OutputRecordData Data { get; set; }
            public List<OutputRecordMessage> Errors { get; set; }
            public List<OutputRecordMessage> Warnings { get; set; }
        }

        private class WebApiResponse
        {
            public List<OutputRecord> Values { get; set; }
        }
        #endregion

        #region Classes used to interact with the Bing API
        private class BingResponse
        {
            public BingEntities Entities { get; set; }
        }
        private class BingEntities
        {
            public BingEntity[] Value { get; set; }
        }

        private class BingEntity
        {
            public class EntityPresentationinfo
            {
                public string[] EntityTypeHints { get; set; }
            }

            public class License
            {
                public string Url { get; set; }
            }

            public class ContractualRule
            {
                public string _type { get; set; }
                public License License { get; set; }
                public string LicenseNotice { get; set; }
                public string Text { get; set; }
                public string Url { get; set; }
            }

            public ContractualRule[] ContractualRules { get; set; }
            public string Description { get; set; }
            public string Name { get; set; }
            public EntityPresentationinfo EntityPresentationInfo { get; set; }
        }
        #endregion

        #region The Azure Function definition

        [FunctionName("EntitySearch")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("Entity Search function: C# HTTP trigger function processed a request.");

            var response = new WebApiResponse
            {
                Values = new List<OutputRecord>()
            };

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            var data = JsonConvert.DeserializeObject<WebApiRequest>(requestBody);

            // Do some schema validation
            if (data == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema.");
            }
            if (data.Values == null)
            {
                return new BadRequestObjectResult("The request schema does not match expected schema. Could not find values array.");
            }

            // Calculate the response for each value.
            foreach (var record in data.Values)
            {
                if (record == null || record.RecordId == null) continue;

                OutputRecord responseRecord = new OutputRecord
                {
                    RecordId = record.RecordId
                };

                try
                {
                    responseRecord.Data = GetEntityMetadata(record.Data.Name).Result;
                }
                catch (Exception e)
                {
                    // Something bad happened, log the issue.
                    var error = new OutputRecord.OutputRecordMessage
                    {
                        Message = e.Message
                    };

                    responseRecord.Errors = new List<OutputRecord.OutputRecordMessage>
                    {
                        error
                    };
                }
                finally
                {
                    response.Values.Add(responseRecord);
                }
            }

            return (ActionResult)new OkObjectResult(response);
        }

        #endregion

        #region Methods to call the Bing API
        /// <summary>
        /// Gets metadata for a particular entity based on its name using Bing Entity Search
        /// </summary>
        /// <param name="entityName">The name of the entity to extract data for.</param>
        /// <returns>Asynchronous task that returns entity data. </returns>
        private async static Task<OutputRecord.OutputRecordData> GetEntityMetadata(string entityName)
        {
            var uri = bingApiEndpoint + "?q=" + entityName + "&mkt=en-us&count=10&offset=0&safesearch=Moderate";
            var result = new OutputRecord.OutputRecordData();

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage {
                Method = HttpMethod.Get,
                RequestUri = new Uri(uri)
            })
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                HttpResponseMessage response = await client.SendAsync(request);
                string responseBody = await response?.Content?.ReadAsStringAsync();

                BingResponse bingResult = JsonConvert.DeserializeObject<BingResponse>(responseBody);
                if (bingResult != null)
                {
                    // In addition to the list of entities that could match the name, for simplicity let's return information
                    // for the top match as additional metadata at the root object.
                    return AddTopEntityMetadata(bingResult.Entities?.Value);
                }
            }

            return result;
        }

        private static OutputRecord.OutputRecordData AddTopEntityMetadata(BingEntity[] entities)
        {
            if (entities != null)
            {
                foreach (BingEntity entity in entities.Where(
                    entity => entity?.EntityPresentationInfo?.EntityTypeHints != null
                        && (entity.EntityPresentationInfo.EntityTypeHints[0] == "Person"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Organization"
                            || entity.EntityPresentationInfo.EntityTypeHints[0] == "Location")
                        && !String.IsNullOrEmpty(entity.Description)))
                {
                    var rootObject = new OutputRecord.OutputRecordData
                    {
                        Description = entity.Description,
                        Name = entity.Name
                    };

                    if (entity.ContractualRules != null)
                    {
                        foreach (var rule in entity.ContractualRules)
                        {
                            switch (rule._type)
                            {
                                case "ContractualRules/LicenseAttribution":
                                    rootObject.LicenseAttribution = rule.LicenseNotice;
                                    rootObject.LicenseUrl = rule.License.Url;
                                    break;
                                case "ContractualRules/LinkAttribution":
                                    rootObject.Source = rule.Text;
                                    rootObject.SourceUrl = rule.Url;
                                    break;
                            }
                        }
                    }

                    return rootObject;
                }
            }

            return new OutputRecord.OutputRecordData();
        }
        #endregion
    }
}
```

请确保*key* `key` 根据注册必应实体搜索 API 时获得的密钥，在常量中输入自己的密钥值。

为方便起见，此示例在单个文件中包含了所有必需的代码。 可以在 [power skills 存储库](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Text/BingEntitySearch)中找到该同一技能的结构略好的版本。

当然，你可以将文件从 `Function1.cs` 重命名为 `BingEntitySearch.cs`。

## <a name="test-the-function-from-visual-studio"></a>从 Visual Studio 中测试函数

按 F5 运行程序并测试函数行为  。 在本例中，我们将使用下面的函数来查找两个实体。 使用 Postman 或 Fiddler 发出如下所示的调用：

```http
POST https://localhost:7071/api/EntitySearch
```

### <a name="request-body"></a>请求正文
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

### <a name="response"></a>响应
你应该看到类似于以下示例的响应：

```json
{
    "values": [
        {
            "recordId": "e1",
            "data": {
                "name": "Pablo Picasso",
                "description": "Pablo Ruiz Picasso was a Spanish painter [...]",
                "source": "Wikipedia",
                "sourceUrl": "http://en.wikipedia.org/wiki/Pablo_Picasso",
                "licenseAttribution": "Text under CC-BY-SA license",
                "licenseUrl": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "errors": null,
            "warnings": null
        },
        "..."
    ]
}
```

## <a name="publish-the-function-to-azure"></a>将函数发布到 Azure

当你对函数行为满意时，可将其发布。

1. **在“解决方案资源管理器”** 中，右键单击该项目并选择“发布”  。 选择“新建” > “发布”   。

1. 如果尚未将 Visual Studio 连接到 Azure 帐户，请选择“添加帐户...” 

1. 请按照屏幕上的提示操作。 系统会要求你为要使用的应用服务、Azure 订阅、资源组、托管计划和存储帐户指定唯一名称。 如果尚不拥有这些资源，可创建新资源组、新托管计划和存储帐户。 完成后，选择“创建” 

1. 部署完成后，请记下站点 URL。 这是 Azure 中你的函数应用的地址。 

1. 在 [Azure 门户](https://portal.azure.com)中，导航到资源组，然后查找你发布的 `EntitySearch` 函数。 在“管理”部分下，应可看到主机密钥  。 对默认主机密钥选择“复制”图标   。  

## <a name="test-the-function-in-azure"></a>在 Azure 中测试函数

现在有了默认主机密钥，按以下方式测试函数：

```http
POST https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]
```

### <a name="request-body"></a>请求正文
```json
{
    "values": [
        {
            "recordId": "e1",
            "data":
            {
                "name":  "Pablo Picasso"
            }
        },
        {
            "recordId": "e2",
            "data":
            {
                "name":  "Microsoft"
            }
        }
    ]
}
```

此示例生成的结果应与之前在本地环境中运行函数时看到的结果相同。

## <a name="connect-to-your-pipeline"></a>连接到管道
现在有了新的自定义技能，可将其添加到技能组合。 下面的示例展示了如何调用技能来向文档中添加组织说明（这可以进行扩展以应用于位置和人员）。 将 `[your-entity-search-app-name]` 替换为你的应用的名称。

```json
{
    "skills": [
      "[... your existing skills remain here]",  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new Bing entity search custom skill",
        "uri": "https://[your-entity-search-app-name].azurewebsites.net/api/EntitySearch?code=[enter default host key here]",
          "context": "/document/merged_content/organizations/*",
          "inputs": [
            {
              "name": "name",
              "source": "/document/merged_content/organizations/*"
            }
          ],
          "outputs": [
            {
              "name": "description",
              "targetName": "description"
            }
          ]
      }
  ]
}
```

此处，我们期望内置的[实体识别技能](cognitive-search-skill-entity-recognition.md)出现在技能集中，并用组织列表扩充了文档。 下面提供了一个实体提取技能配置作为参考，它足以生成我们需要的数据：

```json
{
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "name": "#1",
    "description": "Organization name extraction",
    "context": "/document/merged_content",
    "categories": [ "Organization" ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/merged_content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "organizations",
            "targetName": "organizations"
        }
    ]
},
```

## <a name="next-steps"></a>后续步骤
祝贺！ 现已创建第一个自定义技能。 现在，可按照相同的模式添加自己的自定义功能。 单击以下链接了解详细信息。

+ [强大技能：自定义技能的存储库](https://github.com/Azure-Samples/azure-search-power-skills)
+ [将自定义技能添加到 AI 扩充管道](cognitive-search-custom-skill-interface.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [创建技能组合 (REST)](/rest/api/searchservice/create-skillset)
+ [如何映射扩充的域](cognitive-search-output-field-mapping.md)