---
title: 理解 Azure 流分析的输入
description: 本文介绍了有关 Azure 流分析作业中的输入的概念，将流式处理输入与引用数据输入进行了比较。
author: jseb225
ms.author: krishmam
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 8c62bb2aad266d577a5f4c6f6343d6ed3f4f1979
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016195"
---
# <a name="understand-inputs-for-azure-stream-analytics"></a>理解 Azure 流分析的输入

Azure 流分析作业连接到一个或多个数据输入。 每个输入定义一个到现有数据源的连接。 流分析接受来自多种事件源的数据，这包括事件中心、IoT 中心和 Blob 存储。 输入在为每个作业编写的流式处理 SQL 查询中通过名称进行引用。 在查询中，可以对多个输入进行联接来混合数据或者将流式处理数据与查找到的引用数据进行比较，并将结果传递到输出。 

流分析完美集成了作为输入的以下四种资源：
- [Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/) 
- [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 

这些输入资源与流分析作业可以属于同一 Azure 订阅，也可以属于不同的订阅。

可以使用 [Azure 门户](stream-analytics-quick-create-portal.md#configure-job-input)、  [AZURE POWERSHELL](/powershell/module/az.streamanalytics/New-azStreamAnalyticsInput)、 [.Net API](/dotnet/api/microsoft.azure.management.streamanalytics.inputsoperationsextensions)、 [REST API](/rest/api/streamanalytics/2016-03-01/inputs)和 [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) 来创建、编辑和测试流分析作业输入。

## <a name="stream-and-reference-inputs"></a>流输入和引用输入
将数据推送到数据源后，流分析作业就可使用该数据并对其进行实时处理。 输入分为两种类型：数据流输入和引用数据输入。

### <a name="data-stream-input"></a>数据流输入
数据流是一段时间内不受限制的事件序列。 流分析作业必须至少包含一个数据流输入。 事件中心、IoT 中心、Azure Data Lake Storage Gen2 和 Blob 存储均可作为数据流输入源。 事件中心用于从多个设备和服务收集事件流。 这些流可能包括社交媒体活动源、股票交易信息或传感器数据。 IoT 中心经过优化以从物联网 (IoT) 方案中连接的设备收集数据。  Blob 存储可用作按流的形式引入大容量数据（如日志文件）的输入源。  

有关流式处理数据输入的详细信息，请参阅[将数据作为输入流式传输到流分析中](stream-analytics-define-inputs.md)

### <a name="reference-data-input"></a>引用数据输入
流分析还支持称为“引用数据”  的输入。 引用数据是完全静态的或更改缓慢。 它通常用于执行关联和查找。 例如，可以将数据流输入中的数据联接到引用数据中的数据，就像执行 SQL 联接以查找静态值一样。 当前支持将 Azure Blob 存储、Azure Data Lake Storage Gen2 和 Azure SQL 数据库作为参考数据的输入源。 参考数据源 blob 的大小限制最多为 300 MB，具体取决于查询复杂性和分配的流单元（有关详细信息，请参阅参考数据文档的[大小限制](stream-analytics-use-reference-data.md#size-limitation)部分）。

有关引用数据输入的详细信息，请参阅[在流分析中使用引用数据进行查找](stream-analytics-use-reference-data.md)

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [快速入门：使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)