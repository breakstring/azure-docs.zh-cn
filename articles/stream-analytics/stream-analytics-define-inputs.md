---
title: 将数据作为 Azure 流分析的输入进行流式传输
description: 了解如何在 Azure 流分析中设置数据连接。 输入包括来自事件的数据流，也包括引用数据。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2020
ms.openlocfilehash: 5f10fed66475cda8fd700a4737727101e2465870
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019357"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>将数据作为流分析的输入进行流式传输

流分析与下述三种资源提供的作为输入的 Azure 数据流进行一流的集成：

- [Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/) 
- [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) 

这些输入资源与流分析作业可以属于同一 Azure 订阅，也可以属于不同的订阅。

### <a name="compression"></a>压缩

流分析支持跨所有数据流输入源的压缩功能。 支持的压缩类型有：无、GZip 和 Deflate 压缩。 对压缩的支持不可用于参考数据。 如果输入格式为压缩的 Avro 数据，则会以透明方式对其进行处理。 不需要通过 Avro 序列化指定压缩类型。 

## <a name="create-edit-or-test-inputs"></a>创建、编辑或测试输入

可以使用 [Azure 门户](stream-analytics-quick-create-portal.md)、[Visual Studio](stream-analytics-quick-create-vs.md) 和 [Visual Studio Code](quick-create-visual-studio-code.md) 在流式处理作业上添加输入和查看或编辑现有输入。 还可以给根据 Azure 门户、[Visual Studio](stream-analytics-vs-tools-local-run.md) 和 [Visual Studio Code](visual-studio-code-local-run.md) 的示例数据测试输入连接和测试查询。 编写查询时，将在 FROM 子句中列出输入。 可以在门户的“查询”页中获取可用输入的列表。 若要使用多个输入，可以对其执行 `JOIN` 操作，也可以编写多个 `SELECT` 查询。


## <a name="stream-data-from-event-hubs"></a>从事件中心对数据进行流式传输

Azure 事件中心提供高度可缩放的发布-订阅事件投资者。 事件中心每秒可收集数百万个事件，使你能够处理和分析互连设备与应用程序生成的海量数据。 事件中心和流分析一起提供进行实时分析所需的端到端解决方案。 可以通过事件中心将事件实时馈送到 Azure 中，以便流分析作业对这些事件进行实时处理。 例如，用户可以将 Web 点击操作、传感器读数或联机日志事件发送到事件中心。 然后可以创建流分析作业，将事件中心用作输入数据流，以便进行实时筛选、聚合和关联操作。

`EventEnqueuedUtcTime` 是事件到达事件中心的时间戳，也是事件从事件中心发送到流分析的默认时间戳。 若要在事件有效负载中使用时间戳以流方式处理数据，必须使用 [TIMESTAMP BY](/stream-analytics-query/timestamp-by-azure-stream-analytics) 关键字。

### <a name="event-hubs-consumer-groups"></a>事件中心使用者组

应对每个流分析事件中心输入进行配置，使之拥有自己的使用者组。 如果作业包含自联接或具有多个输入，则某些输入可能会由下游的多个读取器读取。 这种情况会影响单个使用者组中的读取器数量。 为了避免超出针对事件中心设置的每个分区每个使用者组 5 个读取器的限制，最好是为每个流分析作业指定一个使用者组。 对于标准层事件中心，还有 20 个使用者组的限制。 有关详细信息，请参阅[排查 Azure 流分析输入问题](stream-analytics-troubleshoot-input.md)。

### <a name="create-an-input-from-event-hubs"></a>从事件中心创建输入

下表介绍了 Azure 门户的“新输入”页中用于从事件中心流式传输数据输入的每个属性：

| 属性 | 说明 |
| --- | --- |
| **输入别名** |在作业查询中用于引用此输入的友好名称。 |
| **订阅** | 选择事件中心资源所在的订阅。 | 
| **事件中心命名空间** | 事件中心命名空间是包含一组消息传递实体的容器。 创建新的事件中心后，另请创建命名空间。 |
| **事件中心名称** | 要用作输入的事件中心的名称。 |
| **事件中心策略名称** | 提供对事件中心的访问权限的共享访问策略。 每个共享访问策略具有名称、所设权限以及访问密钥。 此选项会自动进行填充，除非已选择手动提供事件中心设置的选项。|
| **事件中心使用者组**（推荐） | 强烈建议对每个流分析作业使用不同的使用者组。 此字符串标识的使用者组用于从事件中心引入数据。 如果未指定任何使用者组，流分析作业将使用 $Default 使用者组。  |
| **分区键** | 这是一个可选字段，它只有在将作业配置为使用[兼容性级别](./stream-analytics-compatibility-level.md) 1.2 或更高版本时才可用。 如果你的输入是按属性分区的，则可以在此处添加此属性的名称。 如果你的查询包含此属性的 PARTITION BY 或 GROUP BY 子句，则此属性用于提高该查询性能。 如果此作业使用兼容性级别 1.2 或更高版本，则此字段默认为“PartitionId”。 |
| **事件序列化格式** | 传入数据流的序列化格式（JSON、CSV、Avro 或[其他（Protobuf、XML、专有…）](custom-deserializer.md)。  请确保该 JSON 格式符合规范，对于十进制数字不包括前导 0。 |
| **编码** | 目前只支持 UTF-8 这种编码格式。 |
| **事件压缩类型** | 用于读取传入数据流的压缩类型，例如 None（默认）、GZip 或 Deflate。 |

如果数据来自事件中心流输入，则可以在流分析查询中访问以下元数据字段：

| 属性 | 说明 |
| --- | --- |
| **EventProcessedUtcTime** |流分析处理事件的日期和时间。 |
| **EventEnqueuedUtcTime** |事件中心收到事件的日期和时间。 |
| **PartitionId** |输入适配器的从零开始的分区 ID。 |

例如，可以使用这些字段编写如以下示例所示的查询：

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> 当使用事件中心作为 IoT 中心路由的终结点时，可通过 [GetMetadataPropertyValue 函数](/stream-analytics-query/getmetadatapropertyvalue)访问 IoT 中心元数据。
> 

## <a name="stream-data-from-iot-hub"></a>从 IoT 中心流式传输数据

Azure IoT 中心是已针对 IoT 方案进行优化，具有高度可缩放的发布-订阅事件引入器。

在流分析中，来自 IoT 中心的事件的默认时间戳是事件到达 IoT 中心的时间戳，即 `EventEnqueuedUtcTime`。 若要在事件有效负载中使用时间戳以流方式处理数据，必须使用 [TIMESTAMP BY](/stream-analytics-query/timestamp-by-azure-stream-analytics) 关键字。

### <a name="iot-hub-consumer-groups"></a>IoT 中心使用者组

应该对每个流分析 IoT 中心输入进行配置，使之拥有自己的使用者组。 如果作业包含自联接或具有多个输入，则某些输入可能会由下游的多个读取器读取。 这种情况会影响单个使用者组中的读取器数量。 为了避免超出针对 Azure IoT 中心设置的每个分区每个使用者组 5 个读取器的限制，最好是为每个流分析作业指定一个使用者组。

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>将 IoT 中心配置为数据流输入

下表介绍了将 IoT 中心配置为流输入时，Azure 门户的“新输入”页中的每个属性。

| 属性 | 说明 |
| --- | --- |
| **输入别名** | 在作业查询中用于引用此输入的友好名称。|
| **订阅** | 选择 IoT 中心资源所在的订阅。 | 
| **IoT 中心** | 可用作输入的 IoT 中心的名称。 |
| **终结点** | IoT 中心终结点。|
| **共享访问策略名称** | 提供对 IoT 中心的访问权限的共享访问策略。 每个共享访问策略具有名称、所设权限以及访问密钥。 |
| **共享访问策略密钥** | 用于授予对 IoT 中心的访问权限的共享访问密钥。  此选项会自动进行填充，除非已选择手动提供 IoT 中心设置的选项。 |
| **使用者组** | 强烈建议对每个流分析作业使用不同的使用者组。 用于从 IoT 中心引入数据的使用者组。 流分析使用 $Default 所有者组，除非指定了其他组。  |
| **分区键** | 这是一个可选字段，它只有在将作业配置为使用[兼容性级别](./stream-analytics-compatibility-level.md) 1.2 或更高版本时才可用。 如果你的输入是按属性分区的，则可以在此处添加此属性的名称。 如果你的查询包含此属性的 PARTITION BY 或 GROUP BY 子句，则此属性用于提高该查询性能。 如果此作业使用兼容性级别 1.2 或更高版本，则此字段默认为“PartitionId”。 |
| **事件序列化格式** | 传入数据流的序列化格式（JSON、CSV、Avro 或[其他（Protobuf、XML、专有…）](custom-deserializer.md)。  请确保该 JSON 格式符合规范，对于十进制数字不包括前导 0。 |
| **编码** | 目前只支持 UTF-8 这种编码格式。 |
| **事件压缩类型** | 用于读取传入数据流的压缩类型，例如 None（默认）、GZip 或 Deflate。 |


如果使用的流数据来自 IoT 中心，则可以在流分析查询中访问以下元数据字段：

| 属性 | 说明 |
| --- | --- |
| **EventProcessedUtcTime** | 处理事件的日期和时间。 |
| **EventEnqueuedUtcTime** | IoT 中心收到事件的日期和时间。 |
| **PartitionId** | 输入适配器的从零开始的分区 ID。 |
| **IoTHub.MessageId** | 用于关联 IoT 中心内的双向通信的 ID。 |
| **IoTHub.CorrelationId** | 用于 IoT 中心内的消息响应和反馈的 ID。 |
| **IoTHub.ConnectionDeviceId** | 用于发送此消息的身份验证 ID。 此值由 IoT 中心在服务绑定的消息上加盖标记。 |
| **IoTHub.ConnectionDeviceGenerationId** | 用于发送此消息的经过身份验证设备的生成 ID。 此值由 IoT 中心在服务绑定的消息上加盖标记。 |
| **IoTHub.EnqueuedTime** | IoT 中心收到消息的时间。 |


## <a name="stream-data-from-blob-storage-or-data-lake-storage-gen2"></a>从 Blob 存储或 Data Lake Storage Gen2 流式传输数据
对于需要在云中存储大量非结构化数据的情况，可以使用 Azure Blob 存储或 Azure Data Lake Storage Gen2 (ADLS Gen2) 提供的经济高效且可缩放的解决方案。 Blob 存储或 ADLS Gen2 中的数据通常被视为静态数据，但这些数据可以由流分析处理为数据流。 

通过流分析来使用这样的输入时，日志处理是一种常用方案。 在此方案中，首先从某个系统捕获遥测数据文件，然后根据需要对这些数据进行分析和处理以提取有意义的数据。

流分析中 Blob 存储或 ADLS Gen2 事件的默认时间戳是上次修改该事件时的时间戳，即 `BlobLastModifiedUtcTime`。 如果一个 blob 在 13:00 上传到存储帐户，并且 Azure 流分析作业在 13:01 使用“立即”选项启动，则不会选取该 blob，因为它的修改时间在作业运行期间之外。

如果 blob 在 13:00 上传到存储帐户容器，并且 Azure 流分析作业使用“自定义时间”在 13:00 或更早时间启动，则将选取该 blob，因为其修改时间在作业运行期间内。

如果在 13:00 使用“立即”启动 Azure 流分析作业，并在 13:01 将 blob 上传到存储帐户容器，则 Azure 流分析会选取该 blob。 分配给每个 blob 的时间戳仅基于 `BlobLastModifiedTime`。 blob 所在的文件夹与分配的时间戳无关。 例如，如果有一个 `BlobLastModifiedTime` 为 2019-11-11 的 blob 2019/10-01/00/b1.txt，则分配给此 blob 的时间戳为 2019-11-11。

若要在事件有效负载中使用时间戳以流方式处理数据，必须使用 [TIMESTAMP BY](/stream-analytics-query/stream-analytics-query-language-reference) 关键字。 如果该 blob 文件可用，则流分析作业每一秒都会从 Azure Blob 存储或 ADLS Gen2 输入中拉取数据。 如果 blob 文件不可用，则存在指数回退，且最长时间延迟为 90 秒。

CSV 格式的输入需要标头行来定义数据集的字段，并且所有标头行字段必须是唯一的。

> [!NOTE]
> 流分析不支持将内容添加到现有 blob 文件。 流分析将仅查看每个文件一次，并且在作业读取数据后对文件所做的任何更改都不会得到处理。 最佳做法是立即上传 blob 文件的全部数据，然后将其他较新的事件添加到其他全新的 blob 文件中。

如果持续添加许多 blob，并在添加 blob 时流分析对其进行处理，则在极少数情况下，可能会由于 `BlobLastModifiedTime` 的粒度而跳过某些 blob。 可以通过至少每隔两秒上传 blob 来缓解此问题。 如果此选项不可行，则可以使用事件中心流式传输大量事件。

### <a name="configure-blob-storage-as-a-stream-input"></a>将 Blob 存储配置为流输入 

下表介绍了将 Blob 存储配置为流输入时，Azure 门户的“新输入”页中的每个属性。

| 属性 | 说明 |
| --- | --- |
| **输入别名** | 在作业查询中用于引用此输入的友好名称。 |
| **订阅** | 选择存储资源所在的订阅。 | 
| **存储帐户** | 存储 Blob 文件所在的存储帐户的名称。 |
| **存储帐户密钥** | 与存储帐户关联的密钥。 此选项会自动进行填充，除非你选择手动提供这些设置的选项。 |
| **容器** | 容器为 blob 提供逻辑分组。 可以选择“使用现有”，以便使用现有的容器；也可以选择“新建”，以便创建新的容器。 |
| **路径模式**（可选） | 用于定位指定容器中的 blob 的文件路径。 如果要从容器的根目录中读取 blob，请不要设置路径模式。 在路径中，可以指定以下 3 个变量的一个或多个实例：`{date}`、`{time}` 或 `{partition}`<br/><br/>示例 1：`cluster1/logs/{date}/{time}/{partition}`<br/><br/>示例 2：`cluster1/logs/{date}`<br/><br/>`*` 字符不是路径前缀允许使用的值。 仅允许使用有效的 <a HREF="/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata">Azure blob 字符</a>。 不包括容器名称或文件名。 |
| **日期格式**（可选） | 如果在路径中使用日期变量，则为组织文件的日期格式。 示例： `YYYY/MM/DD` <br/><br/> 当 blob 输入在其路径中具有 `{date}` 或 `{time}` 时，将按升序时间顺序查看文件夹。|
| **时间格式**（可选） |  如果在路径中使用时间变量，则为组织文件的时间格式。 目前唯一支持的值是 `HH`，表示小时。 |
| **分区键** | 这是一个可选字段，它只有在将作业配置为使用[兼容性级别](./stream-analytics-compatibility-level.md) 1.2 或更高版本时才可用。 如果你的输入是按属性分区的，则可以在此处添加此属性的名称。 如果你的查询包含此属性的 PARTITION BY 或 GROUP BY 子句，则此属性用于提高该查询性能。 如果此作业使用兼容性级别 1.2 或更高版本，则此字段默认为“PartitionId”。 |
| **输入分区的计数** | 此字段只有在路径模式中存在 {partition} 的情况下才会存在。 此属性的值为 >=1 的整数。 无论 {partition} 出现在 pathPattern 中的什么位置，都会使用介于 0 与此字段的值之间的某个数字再减去 1。 |
| **事件序列化格式** | 传入数据流的序列化格式（JSON、CSV、Avro 或[其他（Protobuf、XML、专有…）](custom-deserializer.md)。  请确保该 JSON 格式符合规范，对于十进制数字不包括前导 0。 |
| **编码** | 对于 CSV 和 JSON，目前只支持 UTF-8 这种编码格式。 |
| **压缩** | 用于读取传入数据流的压缩类型，例如 None（默认）、GZip 或 Deflate。 |

如果数据来自 Blob 存储源，则可以在流分析查询中访问以下元数据字段：

| 属性 | 说明 |
| --- | --- |
| **BlobName** |提供事件源自的输入 blob 的名称。 |
| **EventProcessedUtcTime** |流分析处理事件的日期和时间。 |
| **BlobLastModifiedUtcTime** |上次修改 blob 的日期和时间。 |
| **PartitionId** |输入适配器的从零开始的分区 ID。 |

例如，可以使用这些字段编写如以下示例所示的查询：

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [快速入门：使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/