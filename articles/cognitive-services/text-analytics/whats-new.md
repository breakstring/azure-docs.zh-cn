---
title: 文本分析 API 中的新增功能
titleSuffix: Text Analytics - Azure Cognitive Services
description: 本文介绍了 Azure 认知服务文本分析的新版本和新功能。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 45b52f98f9f3670f2a68c22cfe9e27a86b2a74cc
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220694"
---
# <a name="whats-new-in-the-text-analytics-api"></a>文本分析 API 中有哪些新功能？

文本分析 API 会持续更新。 为了让大家随时了解最新的开发成果，本文介绍了新版本和新功能。

## <a name="january-2021"></a>2021 年 1 月

* `2021-01-15`[命名实体识别](how-tos/text-analytics-how-to-entity-linking.md)v3. x 的模型版本，它提供 
  * 为 [几个常规实体类别](named-entity-types.md)扩展了语言支持。 
  * 提高了所有支持的 v3 语言的 "常规实体" 类别的 AI 质量。 

* `2021-01-05`[语言检测](how-tos/text-analytics-how-to-language-detection.md)的模型版本，它提供了其他[语言支持](language-support.md?tabs=language-detection)。

这些模型版本目前在美国东部地区不可用。 

> [!div class="nextstepaction"]
> [了解有关新 NER 模型的详细信息](https://azure.microsoft.com/updates/text-analytics-ner-improved-ai-quality)

## <a name="december-2020"></a>2020 年 12 月

* [更新](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) 文本分析 API 的定价详细信息

## <a name="november-2020"></a>2020 年 11 月

* 一个 [新的终结点](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Analyze) ，具有文本分析 API 的3.1 版-预览版。3对于新的异步 [分析 API](how-tos/text-analytics-how-to-call-api.md?tabs=analyze)，它支持批处理 NER、PII 和关键短语提取操作。
* 一个 [新的终结点](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) ，具有文本分析 API 的3.1 版-预览版。3对于 [运行状况](how-tos/text-analytics-for-health.md) 托管 API 的新异步文本分析，支持批处理。
* 上面列出的两项新功能仅在以下区域提供： `West US 2` 、 `East US 2` 、 `Central US` `North Europe` 和 `West Europe` 区域。
*  (巴西) `pt-BR` 现在支持 [情绪分析](how-tos/text-analytics-how-to-sentiment-analysis.md) v3. x，从模型版本开始 `2020-04-01` 。 它增加了对葡萄牙语的现有 `pt-PT` 支持。
* 更新了客户端库，其中包括异步分析和运行状况操作的文本分析。 可在 GitHub 上找到示例：

    * [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
    * [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
    * [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)


> [!div class="nextstepaction"]
> [详细了解文本分析 API 3.1 版-预览版3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Languages)

## <a name="october-2020"></a>2020 年 10 月

* 从模型版本开始，对情绪分析 v3. x 的印地语支持 `2020-04-01` 。 
* `2020-09-01`V3/languages 终结点的模型版本，增加了语言检测和准确性改进。
* 印度中部和阿拉伯联合酋长国北部中的 v3 可用性。

## <a name="september-2020"></a>2020 年 9 月

### <a name="general-api-updates"></a>常规 API 更新

* 为文本分析3.1 公共预览版发布新 URL，以支持对以下命名实体识别 v3 终结点进行更新： 
    * `/pii` 现在，终结点 `redactedText` 在响应 JSON 中包含新的属性，其中，在输入文本中检测到的 PII 实体将替换 `*` 为这些实体中的每个字符的。
    * `/linking` 现在，终结点在 `bingID` 响应 JSON 中包括链接实体的属性。
* 以下文本分析预览版 API 终结点于2020年9月4日停用：
    * ws 2.1-预览
    * v3.0-preview
    * 3.0-预览。1
    
> [!div class="nextstepaction"]
> [详细了解文本分析 API 3.1 版-预览版2](quickstarts/client-libraries-rest-api.md)

### <a name="text-analytics-for-health-container-updates"></a>运行状况容器更新的文本分析

以下更新仅特定于运行状况容器文本分析的九月版本。
* 已将带有新型号版本的标记的新容器映像 `1.1.013530001-amd64-preview` `2020-09-03` 发布到容器预览存储库。 
* 此模型版本提供实体识别、缩写检测和延迟增强功能的改进。

> [!div class="nextstepaction"]
> [详细了解文本分析的运行状况](how-tos/text-analytics-for-health.md)

## <a name="august-2020"></a>2020 年 8 月

### <a name="general-api-updates"></a>常规 API 更新

* V3 的模型版本 `2020-07-01` `/keyphrases` `/pii` 和 `/languages` 终结点，这些终结点用于添加：
    * 命名实体识别的其他政府和国家特定 [实体类别](named-entity-types.md?tabs=personal) 。
    * 情绪分析 v3 中的挪威语和土耳其语支持。
* 对于超过已发布 [数据限制](concepts/data-limits.md)的 v3 API 请求，将返回 HTTP 400 错误。 
* 返回偏移量的终结点现在支持可选的 `stringIndexType` 参数，该参数将调整返回的 `offset` `length` 值和值，以匹配支持的 [字符串索引方案](concepts/text-offsets.md)。

### <a name="text-analytics-for-health-container-updates"></a>运行状况容器更新的文本分析

以下更新仅特定于运行状况容器文本分析的8月发行版。

* 新模型-版本文本分析用于运行状况： `2020-07-24`
* 用于发送运行状况请求文本分析的新 URL： `http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health` (请注意，需要使用浏览器缓存才能使用此新容器映像中包含的 demo web 应用) 

JSON 响应中的以下属性已更改：

* `type` 已重名为 `category` 
* `score` 已重名为 `confidenceScore`
* JSON 输出的字段中的实体 `category` 现在采用 pascal 大小写格式。 以下实体已重命名：
    * `EXAMINATION_RELATION` 已重命名为 `RelationalOperator`。
    * `EXAMINATION_UNIT` 已重命名为 `MeasurementUnit`。
    * `EXAMINATION_VALUE` 已重命名为 `MeasurementValue`。
    * `ROUTE_OR_MODE` 已重命名 `MedicationRoute` 。
    * 关系实体已 `ROUTE_OR_MODE_OF_MEDICATION` 重命名为 `RouteOfMedication` 。

添加了以下实体：

* NER
    * `AdministrativeEvent`
    * `CareEnvironment`
    * `HealthcareProfession`
    * `MedicationForm` 

* 关系提取
    * `DirectionOfCondition`
    * `DirectionOfExamination`
    * `DirectionOfTreatment`

> [!div class="nextstepaction"]
> [了解有关运行状况容器文本分析的详细信息](how-tos/text-analytics-for-health.md)

## <a name="july-2020"></a>2020 年 7 月 

### <a name="text-analytics-for-health-container---public-gated-preview"></a>文本分析 for health 容器-公共封闭预览

运行状况容器的文本分析现在处于公共封闭预览版中，可让你从临床文档中的非结构化英语文本中提取信息，例如：患者进气窗体、医生说明、研究论文和解雇汇总。 目前，你不会对运行状况容器使用情况的文本分析计费。

容器提供以下功能：

* 命名实体识别
* 关系提取
* 实体链接
* 否定

## <a name="may-2020"></a>2020 年 5 月

### <a name="text-analytics-api-v3-general-availability"></a>文本分析 API v3 正式发布

文本分析 API v3 现已正式发布，具有以下更新：

* 模型版本 `2020-04-01`
* 每个功能的新[数据限制](concepts/data-limits.md)
* 更新了[情绪分析 (SA) v3](how-tos/text-analytics-how-to-sentiment-analysis.md) 的[语言支持](language-support.md)
* 用于实体链接的单独终结点 
* [命名实体识别 (NER) v3](how-tos/text-analytics-how-to-entity-linking.md) 中的新“Address”实体类别。
* NER v3 中的新子类别：
   * 位置 - 地理
   * 位置 - 结构
   * 组织 - 证券交易所
   * 组织 - 医疗
   * 组织 - 体育
   * 事件 - 文化
   * 事件 - 自然
   * 事件 - 体育

已在 JSON 响应中添加了以下属性：
   * 情绪分析中的 `SentenceText`
   * 每个文档的 `Warnings` 

JSON 响应中以下属性的名称已更改（如果适用）：

* `score` 已重名为 `confidenceScore`
    * `confidenceScore` 的精度为小数点后两位。 
* `type` 已重名为 `category`
* `subtype` 已重名为 `subcategory`

> [!div class="nextstepaction"]
> [详细了解文本分析 API v3](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>文本分析 API 3.1 公共预览版
   * 新情绪分析功能- [观点挖掘](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * `PII`) 域筛选器的新个人 ( () 中的受保护的健康信息 `PHI` 。

> [!div class="nextstepaction"]
> [详细了解文本分析 API v3.1 预览版](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>2020 年 2 月

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>SDK 对文本分析 API v3 公共预览版的支持

作为[统一 Azure SDK 版本](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)的一部分，文本分析 API v3 SDK 现已作为以下编程语言的公共预览版提供：
   * [C#](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-csharp&tabs=version-3)
   * [Python](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-python&tabs=version-3)
   * [JavaScript (Node.js)](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-javascript&tabs=version-3)
   * [Java](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-java&tabs=version-3)
   
> [!div class="nextstepaction"]
> [详细了解文本分析 API v3 SDK](./quickstarts/client-libraries-rest-api.md?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>命名实体识别 v3 公共预览版

其他实体类型现已在命名实体识别 (NER) v3 公共预览服务中提供，因为我们展开了在文本中找到的 "常规" 和 "个人" 信息实体的检测。 此更新引入了 [模型版本](concepts/model-versioning.md) `2020-02-01` ，其中包括：

* 以下常规实体类型的识别仅 (英语) ：
    * PersonType
    * 产品
    * 事件
    * 地缘政治实体 (GPE) 作为 "位置" 下的子类型
    * 技能

* 识别以下个人信息实体类型 (仅) 英语：
    * 人员
    * 组织
    * 在数量下作为子类型的年龄
    * 日期作为 DateTime 下的子类型
    * 电子邮件 
    * 电话号码 (仅限我们) 
    * URL
    * IP 地址

### <a name="october-2019"></a>2019 年 10 月

#### <a name="named-entity-recognition-ner"></a>命名实体识别 (NER)

* 用于识别个人信息实体类型的 [新终结点](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionPii) 仅 (英语) 

* 用于 [实体识别](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionGeneral) 和 [实体链接](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesLinking)的不同终结点。

* [模型版本](concepts/model-versioning.md) `2019-10-01` ，其中包括：
    * 扩展了文本中实体的检测和分类。 
    * 以下新实体类型的识别：
        * 电话号码
        * IP 地址

实体链接支持英语和西班牙语。 NER 语言支持因实体类型而异。

#### <a name="sentiment-analysis-v3-public-preview"></a>情绪分析 v3 公共预览版

* 用于分析情绪的 [新终结点](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/Sentiment) 。
* [模型版本](concepts/model-versioning.md) `2019-10-01` ，其中包括：

    * API 文本分类和评分的准确性和详细信息的显著改进。
    * 为文本中的不同情绪自动添加标签。
    * 在文档和句子级别上情绪分析和输出。 

它支持英语 (`en`) 、日语 (`ja`) 、简体中文 (`zh-Hans`) 、繁体中文 (`zh-Hant`) 、法语 () 、 `fr` 意大利语 () 、 `it` 西班牙语 (`es`) 、荷兰语 (`nl`) 、葡萄牙语 (`pt`) 和德语 (`de`) ，以及以下区域提供：、 `Australia East` `Central Canada` 、 `Central US` `East Asia` `East US` `East US 2` `North Europe` `Southeast Asia` `South Central US` `UK South` `West Europe` `West US 2` 、、、、、、、、、、、、、、和。 

> [!div class="nextstepaction"]
> [详细了解情绪分析 v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>后续步骤

* [什么是文本分析 API？](overview.md)  
* [示例用户方案](text-analytics-user-scenarios.md)
* [情绪分析](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [语言检测](how-tos/text-analytics-how-to-language-detection.md)
* [实体识别](how-tos/text-analytics-how-to-entity-linking.md)
* [关键短语提取](how-tos/text-analytics-how-to-keyword-extraction.md)
