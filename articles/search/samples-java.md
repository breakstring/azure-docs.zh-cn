---
title: Java 示例
titleSuffix: Azure Cognitive Search
description: 找到 Azure 认知搜索的 Java 代码演示示例，这些示例使用适用于 Java 的 Azure .NET SDK。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: b5ae38a3dc4a9324a4141314106d67c96c06c8e6
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955031"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Azure 认知搜索的 Java 代码示例

了解演示 Azure 认知搜索解决方案的功能和工作流的 Java 代码示例。 这些示例使用 azure [**SDK For Java**](/azure/developer/java/sdk)的 [**azure 认知搜索客户端库**](/java/api/overview/azure/search-documents-readme)，你可以浏览以下链接。

| 目标 | 链接 |
|--------|------|
| 包下载 | [search.maven.org/artifact/com.azure/azure-search-documents](https://search.maven.org/artifact/com.azure/azure-search-documents) |
| API 参考 | [com.azure.search.documents](/java/api/com.azure.search.documents)  |
| API 测试用例 | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test) |
| 源代码 | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src)  |

## <a name="sdk-samples"></a>SDK 示例

Azure SDK 开发团队中的代码示例演示了 API 使用。 可在 GitHub 上 [**的 azure/azure-sdk/tree/master/sdk/搜索/Azure 搜索-文档/src/示例**](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) 中找到这些示例。

| 示例 | 说明 |
|---------|-------------|
| [搜索索引创建](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | 演示如何创建[搜索索引](search-what-is-an-index.md)。 |
| [同义词创建](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | 演示如何创建[同义词映射](search-synonyms.md)。  |
| [搜索索引器创建](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | 演示如何创建[索引器](search-indexer-overview.md)。 |
| [搜索索引器数据源创建](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | 演示如何创建索引器数据源，对于基于索引器为[受支持的 Azure 数据源](search-indexer-overview.md#supported-data-sources)编制索引而言，这些数据源是必需的。 |
| [技能组创建](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  演示如何创建[技能组](cognitive-search-working-with-skillsets.md)，这些技能组是附加的索引器，并且在编制索引期间执行基于 AI 的扩充。 |
| [加载文档](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | 演示如何在[数据导入](search-what-is-data-import.md)操作中将文档上传或合并到索引中。 |
| [查询语法](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | 演示如何设置[基本查询](search-query-overview.md)。 |

## <a name="doc-samples"></a>文档示例

认知搜索团队中的代码示例演示了功能和工作流。 其中的许多示例在教程、快速入门和操作指南文章中进行了引用。 可在 GitHub 上的 [**Azure 示例/azure-**](https://github.com/Azure-Samples/azure-search-java-samples) 示例中找到这些示例。

| 示例 | 文章 | 
|---------|-------------|
| [快速入门](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/quickstart) | [快速入门的源代码：在 Java 和 REST 中创建搜索索引](search-get-started-java.md)。 此示例尚未更新 Java SDK。 它调用 REST Api。 |

> [!Tip]
> 请尝试使用[示例浏览器](/samples/browse/?languages=java&products=azure-cognitive-search)来搜索 Github 中的 Microsoft 代码示例，搜索时可按产品、服务和语言进行筛选。

## <a name="other-samples"></a>其他示例

以下示例也由认知搜索团队发布，但在文档中未被引用。 相关的自述文件提供了使用说明。

| 示例 | 说明 |
|---------|-------------|
| [搜索-java-入门](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-getting-started) | 使用 Java SDK 客户端库创建、加载和查询搜索索引。 此示例当前是独立的。 |
| [search-java-indexer-demo](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/search-java-indexer-demo) | 演示使用 Java 编写的 Azure Cosmos DB 索引器。 此示例尚未更新 Java SDK。 它调用 REST Api。|