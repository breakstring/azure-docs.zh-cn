---
title: Azure 事件中心功能概述 | Microsoft Docs
description: 本文详细介绍 Azure 事件中心的功能和术语。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 8860a8aa83a17b12236dd47d79479a82846fa8a8
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791940"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Azure 事件中心的功能和术语

Azure 事件中心是可缩放的事件处理服务，它引入并处理大量事件和数据，具有低延迟和高可靠性。 有关简要概述，请参阅[什么是事件中心？](./event-hubs-about.md)。

本文基于[概述文章](./event-hubs-about.md)中的信息编写，并提供有关事件中心组件和功能的技术和实现详细信息。

> [!TIP]
> [ **Apache Kafka** 客户端的协议支持](event-hubs-for-kafka-ecosystem-overview.md)  (版本 >= 1.0) 提供网络终结点，使应用程序可以使用 Apache Kafka 与任何客户端使用事件中心。 大多数现有的 Kafka 应用程序可以简单地重新配置为指向事件中心命名空间而不是 Kafka 群集启动服务器。 
>
>从成本、运营工作量和可靠性的角度来看，Azure 事件中心是一种很好的替代方法，可用于部署和操作你自己的 Kafka 和 Zookeeper 群集以及不是本机到 Azure 的 Kafka 即服务产品/服务。 
>
> 除了获取 Apache Kafka broker 的核心功能，还可以访问 Azure 事件中心功能，如通过 [事件中心捕获](event-hubs-capture-overview.md)进行自动批处理和存档、自动缩放和平衡、灾难恢复、非特定于成本的可用性区域支持、灵活和安全的网络集成和多协议支持（包括防火墙友好的 AMQP over websocket 协议）。


## <a name="namespace"></a>命名空间
事件中心命名空间提供 DNS 集成网络终结点和一系列访问控制和网络集成管理功能，例如 [IP 筛选](event-hubs-ip-filtering.md)、 [虚拟网络服务终结点](event-hubs-service-endpoints.md)和 [专用链接](private-link-service.md) ，并是 Kafka 行话) 中 (或主题的多个事件中心实例之一的管理容器。

## <a name="event-publishers"></a>事件发布者

向事件中心发送数据的任何实体都是 *事件发布者* (与 *事件* 生成者) 一起使用的同义词。 事件发布者可以使用 HTTPS 或 AMQP 1.0 或 Kafka 协议发布事件。 事件发布者通过 OAuth2 颁发的 JWT 令牌或特定于事件中心的共享访问签名 (SAS) 令牌获取发布访问权限来使用基于 Azure Active Directory 的授权。

### <a name="publishing-an-event"></a>发布事件

可以通过 AMQP 1.0、Kafka 协议或 HTTPS 发布事件。 事件中心服务提供 [REST API](/rest/api/eventhub/)、[.NET](event-hubs-dotnet-standard-getstarted-send.md)、[Java](event-hubs-java-get-started-send.md)、[Python](event-hubs-python-get-started-send.md)、[JavaScript](event-hubs-node-get-started-send.md) 和 [Go](event-hubs-go-get-started-send.md) 客户端库，用于将事件发布到事件中心。 对于其他运行时和平台，可以使用任何 AMQP 1.0 客户端，例如 [Apache Qpid](https://qpid.apache.org/)。 

是要使用 AMQP 还 HTTPS 根据具体的使用方案而定。 AMQP 除了需要使用传输级别安全 (TLS) 或 SSL/TLS 以外，还需要建立持久的双向套接字。 初始化会话时，AMQP 具有较高的网络成本，但是 HTTPS 需要为每个请求使用额外的 TLS 开销。 对于频繁发布服务器，AMQP 具有明显更高的性能，并且在与异步发布代码一起使用时，延迟时间可能更低。

您可以单独或分批发布事件。 单个发布的大小限制为 1 MB，无论是单个事件还是批处理，都是如此。 发布大于此阈值的事件将被拒绝。 

事件中心吞吐量通过使用分区和吞吐量-单元分配进行缩放 (如下) 所示。 发布者最好是不知道为事件中心选择的特定分区模型，而只是指定用于一致地将相关事件分配给同一分区的 *分区键* 。

![分区键](./media/event-hubs-features/partition_keys.png)

事件中心确保共享分区键值的所有事件存储在一起，并按到达顺序进行传递。 如果将分区键与发布者策略结合使用，则发布者的标识与分区键的值必须匹配。 否则会出错。

### <a name="event-retention"></a>事件保留

根据可配置的、基于时间的保留策略，从事件中心删除已发布的事件。 默认值和可能的最短保持期为1天 (24 小时) 。 对于事件中心标准版，最长保留期为7天。 对于事件中心专用层，最长保留期为90天。

> [!NOTE]
> 事件中心是一种实时事件流引擎，并不旨在用于替代数据库和/或永久保存的事件流。 
> 
> 事件流的历史记录越深，就越需要辅助索引查找给定流的特定历史切片。 事件负载和索引的检查不在事件中心的功能范围内 (或 Apache Kafka) 。 因此，数据库和专用分析存储和引擎（如 [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)、 [Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) 和 [Azure Synapse](../synapse-analytics/overview-what-is.md) ）都可以更好地用于存储历史事件。
>
> [事件中心捕获](event-hubs-capture-overview.md) 直接与 Azure Blob 存储和 Azure Data Lake Storage 集成，通过该集成，还可以 [直接将事件流入 Azure Synapse](store-captured-data-data-warehouse.md)。
>
> 如果要使用应用程序的 [事件源](/azure/architecture/patterns/event-sourcing) 模式，应将快照策略与事件中心的保留限制进行对齐。 请勿从开始时开始的原始事件中重新生成具体化视图。 如果你的应用程序在生产环境中投入使用一段时间并使用良好，并且你的投影生成器需要经历多年的变化事件，并且在尝试及时了解最新和正在进行的更改时，你肯定会后悔这样的策略。 


### <a name="publisher-policy"></a>发布者策略

使用事件中心可以通过 *发布者策略* 对事件发布者进行精细控制。 发布者策略是运行时功能，旨在为大量的独立事件发布者提供方便。 借助发布者策略，每个发布者在使用以下机制将事件发布到事件中心时可以使用自身的唯一标识符：

```http
//<my namespace>.servicebus.windows.net/<event hub name>/publishers/<my publisher name>
```

不需要提前创建发布者名称，但它们必须与发布事件时使用的 SAS 令牌匹配，以确保发布者标识保持独立。 使用发布者策略时，**PartitionKey** 值设置为发布者名称。 若要正常工作，这些值必须匹配。

## <a name="capture"></a>捕获

使用[事件中心捕获](event-hubs-capture-overview.md)，可以自动捕获事件中心的流式处理数据，并将其保存到所选 Blob 存储帐户或 Azure Data Lake 服务帐户。 可以从 Azure 门户启用“捕获”，并指定用于执行捕获的最小大小和时间窗口。 使用事件中心捕获，用户可以指定自己的 Azure Blob 存储帐户和容器或 Azure Data Lake 服务帐户（其中之一用于存储已捕获数据）。 捕获的数据以 Apache Avro 格式编写。

## <a name="partitions"></a>分区
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>SAS 令牌

事件中心使用在命名空间和事件中心级别提供的共享访问签名。 SAS 令牌是从 SAS 密钥生成的，它是以特定格式编码的 URL 的 SHA 哈希。 事件中心可以使用密钥（策略）的名称和令牌重新生成哈希，以便对发送者进行身份验证。 通常，为事件发布者创建的 SAS 令牌只对特定的事件中心具有“发送”权限。 此 SAS 令牌 URL 机制是“发布者策略”中介绍的发布者标识的基础。 有关使用 SAS 的详细信息，请参阅[使用服务总线进行共享访问签名身份验证](../service-bus-messaging/service-bus-sas.md)。

## <a name="event-consumers"></a>事件使用者

从事件中心读取事件数据的任何实体称为“事件使用者”。 所有事件中心使用者都通过 AMQP 1.0 会话进行连接，事件会在可用时通过该会话传送。 客户端不需要轮询数据可用性。

### <a name="consumer-groups"></a>使用者组

事件中心的发布/订阅机制通过“使用者组”启用。 使用者组是整个事件中心视图（状态、位置或偏移量）。 使用者组使多个消费应用程序都有各自独立的事件流视图，并按自身步调和偏移量独立读取流。

在流处理体系结构中，每个下游应用程序相当于一个使用者组。 如果要将事件数据写入长期存储，则该存储写入器应用程序就是一个使用者组。 然后，复杂的事件处理可由另一个独立的使用者组执行。 只能通过使用者组访问分区。 事件中心内始终有一个默认的使用者组，最多可为一个标准层事件中心创建 20 个使用者组。

每个使用者组的分区上最多可以有 5 个并发读取者，但是 **建议每个使用者组的分区上只有一个活动接收者**。 在单个分区中，每个读取者接收所有消息。 如果在同一分区上有多个读取者，则处理重复消息。 需在代码中处理此问题，这并非易于处理的。 但是，在某些情况下，这是一种有效的方法。

Azure SDK 提供的某些客户端是智能使用者代理，可以自动管理详细信息，以确保每个分区都有一个读取者，并确保正在读取事件中心的所有分区。 这样，你的代码的处理范围便可集中于从事件中心读取的事件，从而可以忽略分区的许多细节。 有关详细信息，请参阅[连接到分区](#connect-to-a-partition)。

以下示例显示了使用者组 URI 约定：

```http
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #1>
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #2>
```

下图显示了事件中心流处理体系结构：

![事件中心体系结构](./media/event-hubs-about/event_hubs_architecture.svg)

### <a name="stream-offsets"></a>流偏移量

偏移量  是事件在分区中的位置。 可以将偏移量视为客户端游标。 偏移量是事件的字节编号。 有了该偏移量，事件使用者（读取者）便可以在事件流中指定要从其开始读取事件的点。 可以时间戳或者偏移量值的形式指定偏移量。 使用者负责在事件中心服务的外部存储其自身的偏移量值。 在分区中，每个事件都包含一个偏移量。

![分区偏移](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>检查点

*检查点* 是读取者在分区事件序列中标记或提交其位置时执行的过程。 检查点操作由使用者负责，并在使用者组中的每个分区上进行。 这种责任意味着，对于每个使用者组而言，每个分区读取者必须跟踪它在事件流中的当前位置，当它认为数据流已完成时，可以通知服务。

如果读取者与分区断开连接，当它重新连接时，将开始读取前面由该使用者组中该分区的最后一个读取者提交的检查点。 当读取者建立连接时，它会将此偏移量传递给事件中心，以指定要从其开始读取数据的位置。 这样，用户便可以使用检查点将事件标记为已由下游应用程序“完成”，并且在不同计算机上运行的读取者之间发生故障转移时，还可以提供弹性。 若要返回到较旧的数据，可以在此检查点过程中指定较低的偏移量。 借助此机制，检查点可以实现故障转移复原和事件流重放。

> [!NOTE]
> 如果你在一个环境中使用 Azure Blob 存储作为检查点存储，该环境支持与 Azure 上通常可用的存储 Blob SDK 版本不同的版本，那么你需要使用代码将存储服务 API 版本更改为该环境支持的特定版本。 例如，如果在 [Azure Stack Hub 版本 2002 上运行事件中心](/azure-stack/user/event-hubs-overview)，则存储服务的最高可用版本为 2017-11-09。 在这种情况下，需要使用代码将存储服务 API 版本设定为 2017-11-09。 如需通过示例来了解如何以特定的存储 API 版本为目标，请参阅“GitHub 上的这些示例”： 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/) 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) 或 [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

### <a name="common-consumer-tasks"></a>常见的使用者任务

所有事件中心使用者都通过 AMQP 1.0 会话，一种状态感知型双向信道进行连接。 每个分区都提供一个 AMQP 1.0 会话，方便传输按分区隔离的事件。

#### <a name="connect-to-a-partition"></a>连接到分区

在连接到分区时，常见的做法是使用租用机制来协调读取者与特定分区的连接。 这样，便可以做到一个使用者组中每分区只有一个活动的读取者。 使用事件中心 SDK 中的客户端（充当智能使用者代理）可以简化检查点、租用和管理读取者的操作。 它们是：

- 适用于 .NET 的 [EventProcessorClient](/dotnet/api/azure.messaging.eventhubs.eventprocessorclient)
- 适用于 Java 的 [EventProcessorClient](/java/api/com.azure.messaging.eventhubs.eventprocessorclient)
- 适用于 Python 的 [EventHubConsumerClient](/python/api/azure-eventhub/azure.eventhub.aio.eventhubconsumerclient)
- 适用于 JavaScript/TypeScript 的 [EventHubConsumerClient](/javascript/api/@azure/event-hubs/eventhubconsumerclient)

#### <a name="read-events"></a>读取事件

为特定分区建立 AMQP 1.0 会话和链接后，事件中心服务会将事件传送到 AMQP 1.0 客户端。 与 HTTP GET 等基于提取的机制相比，此传送机制可以实现更高的吞吐量和更低的延迟。 将事件发送到客户端时，每个事件数据实例包含重要的元数据，例如，用于简化对事件序列执行的检查点操作的偏移量和序列号。

事件数据：
* Offset
* 序列号
* 正文
* 用户属性
* 系统属性

用户负责管理偏移量。

## <a name="next-steps"></a>后续步骤

有关事件中心的详细信息，请访问以下链接：

- 事件中心入门
    - [.NET](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [事件中心编程指南](event-hubs-programming-guide.md)
* [事件中心中的可用性和一致性](event-hubs-availability-and-consistency.md)
* [事件中心常见问题](event-hubs-faq.md)
* [事件中心示例](event-hubs-samples.md)