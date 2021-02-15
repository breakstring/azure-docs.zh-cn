---
title: 名片-窗体识别器
titleSuffix: Azure Cognitive Services
description: 使用格式识别器 API （使用和限制）了解与名片分析相关的概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 4cd762d6c264d95ecb1bd0f3f4c3a4d96eb5a57d
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585086"
---
# <a name="form-recognizer-prebuilt-business-cards-model"></a>表单识别器预生成的名片模型 

Azure 窗体识别器可以使用其预建的名片模型来分析和提取名片中的联系人信息。 它结合了强大的光学字符识别功能 (OCR) 功能与我们的业务卡理解模型结合了名片中的重要信息。 它提取个人联系信息、公司名称、职务等。 预构建的名片 API 在表单识别器2.1 版预览版中公开提供。 

## <a name="what-does-the-business-card-service-do"></a>名片服务有什么作用？

预构建的名片 API 从名片提取关键字段，并将它们以组织的 JSON 响应返回。

![Contoso 详细的 FOTT + JSON 输出](./media/business-card-example.jpg)



### <a name="fields-extracted"></a>提取的字段：

|名称| 类型 | 说明 | 文本 | 
|:-----|:----|:----|:----|
| ContactNames | 对象数组 | 从名片提取的联系人姓名 | [{"FirstName"： "John"，"LastName"： "Doe"}] |
| FirstName | string | 第一个 (给定) 联系人姓名 | "John" | 
| LastName | string | 上次 (家庭) 联系人姓名 |     "Doe" | 
| CompanyNames | 字符串数组 | 从名片提取的公司名称 | ["Contoso"] | 
| Departments | 字符串数组 | 联系人的部门或组织 | ["R&D"] | 
| JobTitles | 字符串数组 | 联系人职务 | ["软件工程师"] | 
| 电子邮件 | 字符串数组 | 联系人电子邮件已从名片提取 | ["johndoe@contoso.com"] | 
| 网站 | 字符串数组 | 从名片提取的网站 | ["https://www.contoso.com"] | 
| 地址 | 字符串数组 | 从名片提取的地址 | ["123 主要街道，Redmond，WA 98052"] | 
| MobilePhones | 电话号码的数组 | 从名片提取的移动电话号码 | ["+ 19876543210"] |
| 传真 | 电话号码的数组 | 从名片提取的传真电话号码 | ["+ 19876543211"] |
| WorkPhones | 电话号码的数组 | 从名片提取的工作电话号码 | ["+ 19876543231"] |
| OtherPhones     | 电话号码的数组 | 从名片中提取的其他电话号码 | ["+ 19876543233"] |


名片 API 还可以从名片返回所有已识别的文本。 此 OCR 输出包含在 JSON 响应中。  

### <a name="input-requirements"></a>输入要求 

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-business-card-operation"></a>"分析业务智能卡" 操作

" [分析](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync) " 名片会将名片的图像或 PDF 作为输入，并提取相关值。 调用返回一个名为的响应标头字段 `Operation-Location` 。 `Operation-Location`该值是一个 URL，其中包含要在下一步中使用的结果 ID。

|响应标头| 结果 URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-business-card-result-operation"></a>"获取分析业务智能卡" 结果操作

第二步是调用 " [获取分析业务智能卡" 结果](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeBusinessCardResult) 操作。 此操作采用 "分析业务智能卡" 操作创建的结果 ID 作为输入。 它将返回一个 JSON 响应，该响应包含具有以下可能值的 **状态** 字段。 此操作以迭代方式调用，直到它返回 **成功** 值。 使用3到5秒的间隔，以避免超出每秒 (RPS) 速率的请求数。

|字段| 类型 | 可能值 |
|:-----|:----:|:----|
|status | string | notStarted：分析操作尚未开始。<br /><br />正在运行：分析操作正在进行。<br /><br />失败：分析操作失败。<br /><br />succeeded：分析操作成功。|

当 " **状态** " 字段的值为 " **成功** " 时，如果请求，JSON 响应将包括业务卡理解和可选的文本识别结果。 业务卡理解结果被组织为命名字段值的字典，其中每个值都包含提取的文本、规范化值、边界框、置信度和对应的单词元素。 文本识别结果组织为带有文本、边界框和置信度信息的线条和单词的层次结构。

![示例名片输出](./media/business-card-results.png)

### <a name="sample-json-output"></a>示例 JSON 输出

请参阅以下成功的 JSON 响应示例： "readResults" 节点包含所有已识别的文本。 文本按页，然后按行，然后按单个单词进行组织。 "DocumentResults" 节点包含模型发现的特定于业务的值。 在这里，你将找到有用的联系人信息，如名字、姓氏、公司名称等。

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T17:41:19Z",
    "lastUpdatedDateTime": "2020-08-20T17:41:24Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel",
                   "lines": 
                             {
                        "text": "Dr. Avery Smith",
                        "boundingBox": [
                            419.3,
                            1154.6,
                            1589.6,
                            877.9,
                            1618.9,
                            1001.7,
                            448.6,
                            1278.4
                        ],
                        "words": [
                            {
                                "text": "Dr.",
                                "boundingBox": [
                                    419,
                            ]
    
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                    },
                                    "LastName": {
                                        "type": "string",
                                        "valueString": "Smith",
                                        "text": "Smith",
                                        "boundingBox": [
                                            1186,
                                            976,
                                            1585,
                                            879,
                                            1618,
                                            998,
                                            1218,
                                            1096
                                        ],
                                        "page": 1
                                    }
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.97
                            }
                        ]
                    },
                    "JobTitles": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Senior Researcher",
                                "text": "Senior Researcher",
                                "boundingBox": [
                                    451.8,
                                    1301.9,
                                    1313.5,
                                    1099.9,
                                    1333.8,
                                    1186.7,
                                    472.2,
                                    1388.7
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Departments": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Cloud & Al Department",
                                "text": "Cloud & Al Department",
                                "boundingBox": [
                                    480.1,
                                    1403.3,
                                    1590.5,
                                    1129.6,
                                    1612.6,
                                    1219.6,
                                    502.3,
                                    1493.3
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    },
                    "MobilePhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 7911 123456",
                                "boundingBox": [
                                    2434.9,
                                    1033.3,
                                    3072,
                                    836,
                                    3096.2,
                                    914.3,
                                    2459.1,
                                    1111.6
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "OtherPhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 9876 5432",
                                "boundingBox": [
                                    2473.2,
                                    1115.4,
                                    3139.2,
                                    907.7,
                                    3163.2,
                                    984.7,
                                    2497.2,
                                    1192.4
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Faxes": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 6789 2345",
                                "boundingBox": [
                                    2525,
                                    1185.4,
                                    3192.4,
                                    977.9,
                                    3217.9,
                                    1060,
                                    2550.5,
                                    1267.5
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Addresses": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "2 Kingdom Street Paddington, London, W2 6BD",
                                "text": "2 Kingdom Street Paddington, London, W2 6BD",
                                "boundingBox": [
                                    1230,
                                    2138,
                                    2535.2,
                                    1678.6,
                                    2614.2,
                                    1903.1,
                                    1309,
                                    2362.5
                                ],
                                "page": 1,
                                "confidence": 0.977
                            }
                        ]
                    },
                    "CompanyNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Contoso",
                                "text": "Contoso",
                                "boundingBox": [
                                    1152,
                                    1916,
                                    2293,
                                    1552,
                                    2358,
                                    1733,
                                    1219,
                                    2105
                                ],
                                "page": 1,
                                "confidence": 0.97
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

按照 [快速入门](./QuickStarts/client-library.md) 快速入门中的步骤使用 Python 和 REST API 来实现名片数据提取。

## <a name="customer-scenarios"></a>客户方案  

使用名片 API 提取的数据可用于执行各种任务。 提取此联系信息会自动为面向客户端角色的客户节省时间。 以下是我们的客户使用名片 API 完成的几个示例：

* 从名片提取联系人信息并快速创建电话联系人。 
* 与 CRM 集成，使用名片图像自动创建联系人。 
* 跟踪销售线索。  
* 从现有的名片图像批量提取联系信息。 

此名片 API 还支持 [AI Builder 业务智能卡处理功能](/ai-builder/prebuilt-business-card)。

## <a name="next-steps"></a>后续步骤

- 按照 [快速入门](./quickstarts/client-library.md) 中的步骤开始识别名片。

## <a name="see-also"></a>请参阅

* [什么是表单识别器？](./overview.md)
* [REST API 参考文档](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
