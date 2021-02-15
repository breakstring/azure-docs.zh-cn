---
title: 将输入映射到输出字段
titleSuffix: Azure Cognitive Search
description: 提取并扩充源数据字段，然后映射到 Azure 认知搜索索引中的输出字段。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 58bb87d5af785d3cffd96f3bd02477f97ed967a9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001297"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>如何将 AI 扩充字段映射到可搜索索引

![索引器阶段](./media/cognitive-search-output-field-mapping/indexer-stages-output-field-mapping.png "索引器阶段")

在本文中，你将了解如何将扩充输入字段映射到可搜索索引中的输出字段。 [定义技能组合](cognitive-search-defining-skillset.md)后，你必须将直接添加值的任何技能的输出字段映射到搜索索引中的给定字段。

将内容从扩充文档移动到索引中必须进行输出字段映射。  扩充的文档实际上是一个信息树，虽然索引中支持复杂类型，但有时你可能需要将扩充的树中的信息转换为更简单的类型（例如字符串数组）。 使用输出字段映射，可以通过平展信息来执行数据形状转换。 即使没有定义技能组，此阶段也可能运行，但输出字段映射总是发生在技能组执行后。

输出字段映射示例：

* 作为技能组的一部分，你提取了文档的每一页中提到的组织的名称。 现在，你想要将每个组织名称映射到 Edm.Collection(Edm.String) 类型的索引中的字段。

* 作为技能组的一部分，你生成了名为“document/translated_text”的新节点。 你希望将此节点上的信息映射到索引中的特定字段。

* 你没有技能组，但正在从 Cosmos DB 数据库中索引复杂类型。 你希望获取该复杂类型上的节点，并将其映射到索引中的字段。

> [!NOTE]
> 我们最近在输出字段映射上启用了映射函数的功能。 有关映射函数的更多详细信息，请参阅[字段映射函数](./search-indexer-field-mappings.md#field-mapping-functions)

## <a name="use-outputfieldmappings"></a>使用 outputFieldMappings

要映射字段，请按如下所示将 `outputFieldMappings` 添加到索引器定义：

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
```

请求正文的结构如下：

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions",
            "mappingFunction": {
                "name": "base64Decode"
            }
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```

对于每个输出字段映射，请设置数据在扩充的文档树中的位置 (sourceFieldName)，以及如索引中引用的字段名称 (targetFieldName)。

## <a name="flattening-information-from-complex-types"></a>平展复杂类型的信息 

sourceFieldName 中的路径可以表示一个元素或多个元素。 在上述示例中，```/document/content/sentiment``` 表示单个数字值，而 ```/document/content/organizations/*/description``` 表示多个组织说明。 

如果有多个元素，它们将“平展”成包含每个元素的数组。 

更具体地说，对于 ```/document/content/organizations/*/description``` 示例，“描述”字段中的数据在编制索引之前将类似于说明的平面数组：

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

这是一个重要原则，因此我们将提供另一个示例。 假设扩充树中存在复杂类型的数组。 假设有一个名为 customEntities 的成员，它具有一个复杂类型数组，如下面所述。

```json
"document/customEntities": 
[
    {
        "name": "heart failure",
        "matches": [
            {
                "text": "heart failure",
                "offset": 10,
                "length": 12,
                "matchDistance": 0.0
            }
        ]
    },
    {
        "name": "morquio",
        "matches": [
            {
                "text": "morquio",
                "offset": 25,
                "length": 7,
                "matchDistance": 0.0
            }
        ]
    }
    //...
]
```

假设你的索引中有一个名为“疾病”的字段，该字段的类型为 Collection(Edm.String)，你希望在该字段中存储每个实体的名称。 

这可以通过使用“\*”符号来轻松实现，如下所示：

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

此操作只是简单将各个 customEntities 元素的名称平展为单个字符串数组，如下所示：

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>后续步骤
将扩充字段映射到可搜索字段后，你可以设置每个可搜索字段中的字段属性[作为索引定义的一部分](search-what-is-an-index.md)。

有关字段映射的详细信息，请参阅 [Azure 认知搜索索引器中的字段映射](search-indexer-field-mappings.md)。