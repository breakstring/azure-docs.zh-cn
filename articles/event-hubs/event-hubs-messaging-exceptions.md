---
title: Azure 事件中心 - 异常（旧版）
description: 本文提供 Azure 事件中心消息传送异常和建议的操作列表。
ms.topic: article
ms.date: 02/10/2021
ms.openlocfilehash: a76c98ec7d6d1f3370ed8787bf10d1d16a7baaa5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390889"
---
# <a name="event-hubs-messaging-exceptions---net-legacy"></a>事件中心消息传送异常 - .NET（旧版）
本部分列出了 .NET Framework API 生成的 .NET 异常。 

> [!IMPORTANT]
> 本文中列出的一些异常仅适用于旧版事件中心 .NET 库。 例如：Microsoft.ServiceBus.* 异常。
> 
> 有关新 .NET 库引发的 EventHubsException 的信息，请参阅 [EventHubsException - .NET](exceptions-dotnet.md)

## <a name="exception-categories"></a>异常类别

事件中心 .NET API 会生成以下类别的异常，以及在尝试修复这些异常时可以采取的相关操作：

 - 用户编码错误： 
 
   - [System.ArgumentException](/dotnet/api/system.argumentexception?view=netcore-3.1&preserve-view=true)
   - [System.InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1&preserve-view=true)
   - [System.OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1&preserve-view=true)
   - [System.Runtime.Serialization.SerializationException](/dotnet/api/system.runtime.serialization.serializationexception?view=netcore-3.1&preserve-view=true)
   
   常规操作：尝试在继续之前修复代码。
 
 - 设置/配置错误： 
 
   - [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception)
   - [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception)
   - [System.UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1&preserve-view=true)
   
   常规操作：检查配置，必要时进行更改。
   
 - 暂时性异常： 
 
   - [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception)
   - [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception)
   - [Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception)
   - [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)
   
   常规操作：重试操作或通知用户。
 
 - 其他异常： 
 
   - [System.Transactions.TransactionException](/dotnet/api/system.transactions.transactionexception?view=netcore-3.1&preserve-view=true)
   - [System.TimeoutException](#timeoutexception)
   - [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception)
   - [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception)
   
   常规操作：特定于异常类型；请参考以下部分中的表。 

## <a name="exception-types"></a>异常类型
下表列出了消息异常的类型及其原因，并说明可以采取的建议性操作。

| 异常类型 | 说明/原因/示例 | 建议的操作 | 自动/立即重试注意事项 |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1&preserve-view=true) |服务器在 [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) 控制的指定时间内未响应请求的操作。 服务器可能已完成请求的操作。 此异常可能是由于网络或其他基础结构延迟造成的。 |检查系统状态的一致性，并根据需要重试。<br /> 请参阅 [TimeoutException](#timeoutexception)。 | 在某些情况下，重试可能会有帮助；在代码中添加重试逻辑。 |
| [InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1&preserve-view=true) |不允许在服务器或服务中执行请求的用户操作。 有关详细信息，请查看异常消息。 例如，如果在 [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) 模式下收到消息，则 [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) 将生成此异常。 | 检查代码和文档。 确保请求的操作有效。 | 重试没有帮助。 |
| [OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1&preserve-view=true) | 尝试对已关闭、中止或释放的对象调用某个操作。 在极少数情况下，环境事务已释放。 | 检查代码并确保代码不会对已释放的对象调用操作。 | 重试没有帮助。 |
| [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1&preserve-view=true) | [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) 对象无法获取令牌，该令牌无效，或者令牌不包含执行操作所需的声明。 | 确保使用正确的值创建令牌提供程序。 检查访问控制服务的配置。 | 在某些情况下，重试可能会有帮助；在代码中添加重试逻辑。 |
| [ArgumentException](/dotnet/api/system.argumentexception?view=netcore-3.1&preserve-view=true)<br /> [ArgumentNullException](/dotnet/api/system.argumentnullexception?view=netcore-3.1&preserve-view=true)<br />[ArgumentOutOfRangeException](/dotnet/api/system.argumentoutofrangeexception?view=netcore-3.1&preserve-view=true) | 提供给该方法的一个或多个参数均无效。 提供给 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 或 [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) 的 URI 包含路径段。 提供给 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 或 [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) 的 URI 方案无效。 属性值大于 32 KB。 | 检查调用代码并确保参数正确。 | 重试不会解决问题。 |
| [Microsoft.ServiceBus.Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | 与操作关联的实体不存在或已被删除。 | 确保该实体存在。 | 重试不会解决问题。 |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | 客户端无法与事件中心建立连接。 |确保提供的主机名正确并且主机可访问。 | 如果存在间歇性的连接问题，重试可能会有帮助。 |
| [Microsoft.ServiceBus.Messaging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | 服务目前无法处理请求。 | 客户端可以等待一段时间，并重试操作。 <br /> 请参阅 [ServerBusyException](#serverbusyexception)。 | 客户端可在特定的时间间隔后重试操作。 如果重试导致其他异常，请检查该异常的重试行为。 |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | 在以下情况下，可能会引发一般消息异常：尝试使用属于其他实体类型（例如主题）的名称或路径创建 [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient)。 尝试发送大于 1 MB 的消息。 服务器或服务在处理请求期间遇到错误。 有关详细信息，请查看异常消息。 此异常通常是暂时性的异常。 | 检查代码，并确保只对消息正文使用可序列化对象（或使用自定义序列化程序）。 在文档中查看属性支持的值类型，并只使用支持的类型。 检查 [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) 属性。 如果为 **true**，可以重试操作。 | 重试行为的效果不确定，可能不会解决问题。 |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | 尝试使用已被该服务命名空间中另一实体使用的名称创建实体。 | 删除现有的实体，或者选择不同的名称来创建实体。 | 重试不会解决问题。 |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | 消息实体已达到其最大允许大小。 如果已经在每使用者组级别上打开最大接收方数（即 5），则可能会发生此异常。 | 通过从实体或其子队列接收消息在该实体中创建空间。 <br /> 请参阅 [QuotaExceededException](#quotaexceededexception) | 如果同时已删除消息，则重试可能会有帮助。 |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | 对已禁用的实体请求运行时操作。 |激活实体。 | 如果在此期间该实体已激活，则重试可能会有帮助。 |
| [Microsoft.ServiceBus.Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | 消息有效负载超出 1 MB 限制。 1 MB 限制适用于总消息大小，可能包括系统属性和任何 .NET 开销。 | 减少消息负载的大小，并重试操作。 |重试不会解决问题。 |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) 指示已超出某个特定实体的配额。

如果已经在每使用者组级别上打开最大接收方数 (5)，则可能会发生此异常。

### <a name="event-hubs"></a>事件中心
每个事件中心最多只能有 20 个使用者组。 尝试创建更多组时，会收到 [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception)。 

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1&preserve-view=true) 指示用户启动的操作所用的时间超过操作超时值。 

对于事件中心，超时作为连接字符串的一部分指定，或通过 [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder) 指定。 错误消息本身可能会有所不同，但它始终包含当前操作的指定超时值。 

预计会在维护操作（例如，事件中心服务更新或运行服务的资源上的 OS 更新）期间或操作间隙发生超时。 在 OS 更新期间，实体会四处移动，节点会更新或重启，这可能会导致超时。 有关 Azure 事件中心服务的服务级别协议 (SLA) 详细信息，请参阅[事件中心的 SLA](https://azure.microsoft.com/support/legal/sla/event-hubs/)。 


### <a name="common-causes"></a>常见原因
此错误有两个常见的原因：配置不正确或暂时性服务错误。

- **配置不正确** 运行条件下的操作超时值可能太小。 客户端 SDK 的操作超时默认值为 60 秒。 请查看代码是否将该值设置得过小。 网络和 CPU 使用率的状况可能会影响完成特定操作所用的时间，因此，操作超时不应设置为很小的值。
- **暂时性服务错误** 有时，事件中心服务在处理请求时会遇到延迟，例如，高流量时段。 在这种情况下，可以在延迟后重试操作，直到操作成功为止。 如果多次尝试同一操作后仍然失败，请访问 [Azure 服务状态站点](https://azure.microsoft.com/status/)，看是否有任何已知的服务中断。

## <a name="serverbusyexception"></a>ServerBusyException

[Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) 或 [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) 指示服务器已过载。 此异常有两个相关的错误代码。

### <a name="error-code-50002"></a>错误代码 50002
此错误发生的原因可能是以下之一：

- 负载未均匀分布在事件中心的所有分区上，并且一个分区达到了本地吞吐量单位限制。
    
    **解决方法**：修改分区分发策略，或尝试 [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) 可能会有所帮助。

- 事件中心命名空间没有足够的吞吐量单位（可以通过在 [Azure 门户](https://portal.azure.com)中检查事件中心命名空间窗口中的“指标”屏幕来确认）。 门户显示聚合（1 分钟）信息，但我们实时测量吞吐量 - 因此，这只是一个估计值。

    **解决方法**：增加命名空间上的吞吐量单位可有所帮助。 

    可以在 Azure 门户中的“事件中心命名空间”页的“缩放”页或“概述”页上配置吞吐量单位。   或者，可以使用“自动扩充”功能满足使用量需求，[“自动扩充”](event-hubs-auto-inflate.md)功能可通过增加吞吐量单位的数量自动进行纵向扩展。

    吞吐量单位 (TU) 适用于事件中心命名空间中的所有事件中心。 这意味着，TU 是在命名空间级别购买的，并在该命名空间下的事件中心之间共享。 每个 TU 为命名空间赋予以下功能：

    - 入口事件（发送到事件中心的事件）最多为每秒 1 MB，但每秒不超过 1000 个入口事件、管理操作或控制 API 调用。
    - 出口事件（从事件中心使用的事件）最多达每秒 2 MB，但不超过 4096 个。
    - 事件存储空间最多为 84 GB（对于默认的 24 小时保留期而言已足够）。
    
    在“概述”页上的“显示指标”部分，切换到“吞吐量”选项卡。选择图表以在在较大窗口中打开它，图表中的 x 轴上以 1 分钟为间隔。 查看高峰值并将其除以 60，以获得传入字节数/秒或传出字节数/秒。 在“请求”选项卡上，使用类似的方法计算高峰时每秒的请求数。 

    如果你看到的值大于 TU 的数量 * 限制（每秒 1 MB 的流入量或 1000 个流入量请求/秒、每秒 2 MB 的流出量），请使用事件中心命名空间的“缩放”（在左侧菜单上）页进行手动扩展或使用事件中心的[自动扩充](event-hubs-auto-inflate.md)功能来增加 TU 的数量。 请注意，自动扩充最多只能增加到 20 个 TU。 若要将 TU 提升到正好 40 个，请提交一个支持请求。

### <a name="error-code-50008"></a>错误代码50008

此错误很少发生。 但如果为命名空间运行代码的容器的 CPU 比较低时（在事件中心负载均衡器开始之前不超过几秒钟），则可能发生此错误。

**解决** 方法：限制对 GetRuntimeInformation 方法的调用。 Azure 事件中心每个使用者组最多支持每秒50个调用 GetRuntimeInfo。 达到限制后，你可能会收到类似于以下内容的异常：

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50008. Please wait 10 seconds and try again.
```


## <a name="next-steps"></a>后续步骤

访问以下链接可以了解有关事件中心的详细信息：

* [事件中心概述](./event-hubs-about.md)
* [创建事件中心](event-hubs-create.md)
* [事件中心常见问题](event-hubs-faq.md)