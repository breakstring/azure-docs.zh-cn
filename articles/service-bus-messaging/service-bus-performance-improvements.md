---
title: 使用 Azure 服务总线提高性能的最佳做法
description: 介绍如何使用服务总线在交换中转消息时优化性能。
ms.topic: article
ms.date: 01/15/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 70f2fe88cf363572bcbca71115ba08dc0ed10e6d
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664692"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>使用服务总线消息传递改进性能的最佳实践

本文介绍如何使用 Azure 服务总线在交换中转消息时优化性能。 本文的第一部分介绍提高性能的不同机制。 第二部分指导用户针对给定的场景以能够提供最佳性能的方式使用服务总线。

在本文中，术语“客户端”是指任何访问服务总线的实体。 客户端可以充当发送方或接收方的角色。 术语“发送方”用于向服务总线队列或主题发送消息的服务总线队列客户端或主题客户端。 术语“接收方”是指从服务总线队列或订阅接收消息的服务总线队列客户端或订阅客户端。

## <a name="protocols"></a>协议
服务总线支持客户端通过以下三种协议之一发送和接收消息：

1. 高级消息队列协议 (AMQP)
2. 服务总线邮件协议 (SBMP)
3. 超文本传输协议 (HTTP)

AMQP 最有效，因为它可以保持与服务总线的连接。 它还实现 [批处理](#batching-store-access) 和 [预提取](#prefetching)。 除非明确提到，本文中的所有内容都假定使用 AMQP 或 SBMP。

> [!IMPORTANT]
> SBMP 仅适用于 .NET Framework。 AMQP 是 .NET Standard 的默认设置。

## <a name="choosing-the-appropriate-service-bus-net-sdk"></a>选择适当的服务总线 .NET SDK
有三种受支持的 Azure 服务总线 .NET Sdk。 它们的 API 相似，选择起来可能很困难。 请参阅下表，了解如何做出决定。 Azure. 传送服务 SDK 是最新的，我们建议将其用于其他 Sdk。 无论是新式的、高性能且跨平台的，均可兼容 Azure。 此外，它们还支持 AMQP over Websocket，是开源项目的 Azure .NET SDK 集合的一部分。

| NuGet 包 | 主命名空间 | 平台最低版本 | 协议 |
|---------------|----------------------|---------------------|-------------|
| [Azure. 消息传送。](https://www.nuget.org/packages/Azure.Messaging.ServiceBus) | `Azure.Messaging.ServiceBus`<br>`Azure.Messaging.ServiceBus.Administration` | .NET Core 2.0<br>.NET Framework 4.6.1<br>Mono 5.4<br>Xamarin.iOS 10.14<br>Xamarin.Mac 3.8<br>Xamarin.Android 8.0<br>通用 Windows 平台 10.0.16299 | AMQP<br>HTTP |
| [Microsoft。](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/) | `Microsoft.Azure.ServiceBus`<br>`Microsoft.Azure.ServiceBus.Management` | .NET Core 2.0<br>.NET Framework 4.6.1<br>Mono 5.4<br>Xamarin.iOS 10.14<br>Xamarin.Mac 3.8<br>Xamarin.Android 8.0<br>通用 Windows 平台 10.0.16299 | AMQP<br>HTTP |
| [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) | `Microsoft.ServiceBus`<br>`Microsoft.ServiceBus.Messaging` | .NET Framework 4.6.1 | AMQP<br>SBMP<br>HTTP |

若要详细了解最低的 .NET Standard 平台支持，请参阅 [.NET 实现支持](/dotnet/standard/net-standard#net-implementation-support)。

## <a name="reusing-factories-and-clients"></a>重用工厂和客户端
# <a name="azuremessagingservicebus-sdk"></a>[Azure 消息传送。](#tab/net-standard-sdk-2)
与服务交互的服务总线对象，如 [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient)、 [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender)、 [ServiceBusReceiver](/dotnet/api/azure.messaging.servicebus.servicebusreceiver)和 [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor)，应注册为依赖关系注入作为单一实例 (或实例化一次并共享) 。 ServiceBusClient 可注册为与 [ServiceBusClientBuilderExtensions](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/servicebus/Azure.Messaging.ServiceBus/src/Compatibility/ServiceBusClientBuilderExtensions.cs)进行依赖关系注入。 

建议你在发送或接收每条消息后不要关闭或释放这些对象。 关闭或释放特定于实体的对象 (ServiceBusSender/接收器/处理器) 会导致向下移动到服务总线服务的链接。 释放 ServiceBusClient 会导致与服务总线服务的连接断开。 建立连接是一项成本高昂的操作，可通过重复使用同一 ServiceBusClient 并从同一 ServiceBusClient 实例创建必要的特定于实体的对象来避免这一操作。 这些客户端对象可安全地用于并发异步操作及从多个线程安全地使用。

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

应当将服务总线客户端对象（例如 [`IQueueClient`][QueueClient] 或 [`IMessageSender`][MessageSender] 的实现）注册为单一实例进行依赖关系注入（或将其实例化一次后共享）。 发送消息后，建议不关闭消息工厂、队列、主题或订阅客户端，并在发送下一条消息时再重新创建它们。 关闭消息工厂将删除与服务总线服务的连接。 重新创建工厂时将建立新的连接。 建立连接是一项成本高昂的操作，可通过针对多个操作重复使用相同的工厂和客户端对象来避免这一操作。 这些客户端对象可安全地用于并发异步操作及从多个线程安全地使用。

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

`QueueClient` 或 `MessageSender` 之类的服务总线客户端对象通过 [MessagingFactory][MessagingFactory] 对象创建，后者还提供对连接的内部管理。 发送消息后，建议不关闭消息工厂、队列、主题或订阅客户端，并在发送下一条消息时再重新创建它们。 关闭消息工厂将删除与服务总线服务的连接，并且会在重新创建工厂时建立新的连接。 建立连接是一项成本高昂的操作，可通过针对多个操作重复使用相同的工厂和客户端对象来避免这一操作。 这些客户端对象可安全地用于并发异步操作及从多个线程安全地使用。

---

## <a name="concurrent-operations"></a>并发操作
发送、接收、删除等操作需要一段时间。 这一时间包括服务总线服务处理该操作的时间，外加延迟处理请求和响应的时间。 若要增加每次操作的数目，操作必须同时执行。

客户端通过执行异步操作来计划并发操作。 前一个请求完成之前便启动下一个请求。 以下代码片段是异步发送操作的示例：

# <a name="azuremessagingservicebus-sdk"></a>[Azure 消息传送。](#tab/net-standard-sdk-2)
```csharp
var messageOne = new ServiceBusMessage(body);
var messageTwo = new ServiceBusMessage(body);

var sendFirstMessageTask =
    sender.SendMessageAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    sender.SendMessageAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");

```

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

```csharp
var messageOne = new Message(body);
var messageTwo = new Message(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

```csharp
var messageOne = new BrokeredMessage(body);
var messageTwo = new BrokeredMessage(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

---

以下代码是异步接收操作的示例。

# <a name="azuremessagingservicebus-sdk"></a>[Azure 消息传送。](#tab/net-standard-sdk-2)

```csharp
var client = new ServiceBusClient(connectionString);
var options = new ServiceBusProcessorOptions 
{

      AutoCompleteMessages = false,
      MaxConcurrentCalls = 20
};
await using ServiceBusProcessor processor = client.CreateProcessor(queueName,options);
processor.ProcessMessageAsync += MessageHandler;
processor.ProcessErrorAsync += ErrorHandler;

static Task ErrorHandler(ProcessErrorEventArgs args)
{
    Console.WriteLine(args.Exception);
    return Task.CompletedTask;
};

static async Task MessageHandler(ProcessMessageEventArgs args)
{
    Console.WriteLine("Handle message");
    await args.CompleteMessageAsync(args.Message);
}

await processor.StartProcessingAsync();
```

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

请参阅 GitHub 存储库，了解完整的<a href="https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues" target="_blank">源代码示例 <span class="docon docon-navigate-external x-hidden-focus"></span></a>：

```csharp
var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);

static Task LogErrorAsync(Exception exception)
{
    Console.WriteLine(exception);
    return Task.CompletedTask;
};

receiver.RegisterMessageHandler(
    async (message, cancellationToken) =>
    {
        Console.WriteLine("Handle message");
        await receiver.CompleteAsync(message.SystemProperties.LockToken);
    },
    new MessageHandlerOptions(e => LogErrorAsync(e.Exception))
    {
        AutoComplete = false,
        MaxConcurrentCalls = 20
    });
```

使用连接字符串、队列名称和“扫视锁定”接收模式来实例化 `MessageReceiver` 对象。 接下来，使用 `receiver` 实例注册消息处理程序。

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

请参阅 GitHub 存储库，了解完整的<a href="https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/SendersReceiversWithQueues" target="_blank">源代码示例 <span class="docon docon-navigate-external x-hidden-focus"></span></a>：

```csharp
var factory = MessagingFactory.CreateFromConnectionString(connectionString);
var receiver = await factory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);

// Register the handler to receive messages asynchronously
receiver.OnMessageAsync(
    async message =>
    {
        Console.WriteLine("Handle message");
        await message.CompleteAsync();
    },
    new OnMessageOptions
    {
        AutoComplete = false,
        MaxConcurrentCalls = 20
    });
```

`MessagingFactory` 从连接字符串创建 `factory` 对象。 使用 `factory` 实例时，会实例化 `MessageReceiver`。 接下来，使用 `receiver` 实例注册 OnMessage 处理程序。

---

## <a name="receive-mode"></a>接收模式

在创建队列或订阅客户端时，可以指定接收模式：*扫视-锁定* 或 *接收和删除*。 默认接收模式是 `PeekLock`。 在默认模式下操作时，客户端会发送从服务总线接收消息的请求。 客户端收到消息后，将发送完成消息的请求。

将接收模式设置为 `ReceiveAndDelete` 时，这两个步骤会合并到单个请求中。 这些步骤减少了操作的总体数目，并可以提高总消息吞吐量。 性能提高的同时也出现丢失消息的风险。

服务总线不支持“接收并删除”操作的事务。 此外，在客户端想要延迟消息或将其放入[死信队列](service-bus-dead-letter-queues.md)的情况下，需要使用扫视-锁定语义。

## <a name="client-side-batching"></a>客户端批处理

客户端批处理允许队列或主题客户端延迟一段时间发送消息。 如果客户端在这段时间内发送其他消息，则会将这些消息以单个批次传送。 客户端批处理还会导致队列或订阅客户端将多个 **完成** 请求批处理为单个请求。 批处理仅适用于异步 **发送** 和 **完成** 操作。 同步操作会立即发送到服务总线服务。 不会针对扫视或接收操作执行批处理，也不会跨客户端执行批处理。

# <a name="azuremessagingservicebus-sdk"></a>[Azure 消息传送。](#tab/net-standard-sdk-2)
.NET Standard SDK 的批处理功能尚未公开要操作的属性。

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

.NET Standard SDK 的批处理功能尚未公开要操作的属性。

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

默认情况下，客户端的批处理间隔时间为 20 毫秒。 可通过在创建消息工厂之前，设置 [BatchFlushInterval][BatchFlushInterval] 属性，更改批处理的间隔时间。 此设置会影响此工厂创建的所有客户端。

要禁用批处理，则将 [BatchFlushInterval][BatchFlushInterval] 属性设置为 **TimeSpan.Zero**。 例如：

```csharp
var settings = new MessagingFactorySettings
{
    NetMessagingTransportSettings =
    {
        BatchFlushInterval = TimeSpan.Zero
    }
};
var factory = MessagingFactory.Create(namespaceUri, settings);
```

批处理不会影响可计费的消息操作的数目，且仅适用于使用 [Microsoft.ServiceBus.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) 库的服务总线客户端协议。 HTTP 协议不支持批处理。

> [!NOTE]
> 设置 `BatchFlushInterval` 可确保批处理从应用程序的角度来看是隐式的。 例如，应用程序进行 `SendAsync` 和 `CompleteAsync` 调用而不进行具体的批量调用。
>
> 显式客户端批处理可以通过利用下述方法调用来实现：
> ```csharp
> Task SendBatchAsync(IEnumerable<BrokeredMessage> messages);
> ```
> 在这里，消息的组合大小必须小于定价层支持的最大大小。

---

## <a name="batching-store-access"></a>批处理存储访问

为了增加队列、主题或订阅的吞吐量，服务总线在写入其内部存储时会对多条消息进行批处理。 

- 如果对队列启用了批处理，则将消息写入存储以及从存储中删除消息都将批量进行。 
- 如果对主题启用了批处理，则会将消息批量写入存储。 
- 如果对订阅启用了批处理，则会从存储区批量删除消息。 
- 如果对实体启用了批量存储访问，服务总线会将此实体的存储写入操作延迟多达 20 毫秒的时间。

> [!NOTE]
> 使用批处理不存在丢失消息的风险，即使在 20 毫秒的批处理间隔结束时出现服务总线故障，也是如此。

在此间隔期间发生的其他存储操作会被添加到此批中。 批量存储访问仅影响发送和完成操作；接收操作不会受到影响 。 批量存储访问是实体上的一个属性。 将跨所有启用了批量存储访问的实体实施批处理。

在创建新队列、主题或订阅时，默认情况下启用批量存储访问。


# <a name="azuremessagingservicebus-sdk"></a>[Azure 消息传送。](#tab/net-standard-sdk-2)
若要禁用批量存储访问，需要 `ServiceBusAdministrationClient` 的实例。 `CreateQueueOptions`从将属性设置为的队列说明创建 `EnableBatchedOperations` `false` 。

```csharp
var options = new CreateQueueOptions(path)
{
    EnableBatchedOperations = false
};
var queue = await administrationClient.CreateQueueAsync(options);
```


# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

若要禁用批量存储访问，需要 `ManagementClient` 的实例。 根据队列说明创建队列，将 `EnableBatchedOperations` 属性设置为 `false`。

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = await managementClient.CreateQueueAsync(queueDescription);
```

有关详细信息，请参阅以下文章：
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

若要禁用批量存储访问，需要 `NamespaceManager` 的实例。 根据队列说明创建队列，将 `EnableBatchedOperations` 属性设置为 `false`。

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = namespaceManager.CreateQueue(queueDescription);
```

有关详细信息，请参阅以下文章：
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

批量存储访问不影响可计费的消息传送操作的数目。 它是队列、主题或订阅的一个属性。 它不依赖于接收模式以及客户端和服务总线服务之间所使用的协议。

## <a name="prefetching"></a>预提取

[预提取](service-bus-prefetch.md)允许队列或订阅客户端在接收消息时从服务加载其他消息。 客户端将这些消息存储在本地缓存中。 缓存的大小取决于 `QueueClient.PrefetchCount` 或 `SubscriptionClient.PrefetchCount` 属性。 启用预提取的每个客户端维护其自己的缓存。 客户端之间不共享缓存。 如果客户端启动接收操作，而其缓存是空的，则服务会传输一批消息。 批的大小等于缓存的大小或 256 KB，以二者中较小者为准。 如果客户端启动接收操作，并且缓存中包含一条消息，则从缓存中提取该消息。

预提取一条消息后，服务将锁定此预提取的消息。 通过此锁定操作，其他接收方则无法接收到此预提取的消息。 如果接收方在锁定过期之前无法完成此消息，则该消息便对其他接收方可用。 预提取的消息的副本则保留在缓存中。 使用过期的缓存副本的接收方会在尝试完成该消息时接收到一个异常。 默认情况下，消息锁定在 60 秒后过期。 这一值可延长到 5 分钟。 若要防止使用过期消息，请将缓存大小设置为小于客户端可在锁超时间隔内使用的消息数。

使用 60 秒的默认锁定时限时，`PrefetchCount` 的合理值是工厂所有接收方最大处理速率的 20 倍。 例如，某个工厂创建了 3 个接收方，并且每个接收方每秒可以处理最多 10 个消息。 预提取计数不应超过 20 X 3 X 10 = 600。 默认情况下，`PrefetchCount` 设置为 0，这表示不会从服务中提取额外消息。

预提取消息会增加队列或订阅的总体吞吐量，因为它减少了消息操作或往返行程的总数。 但是，提取第一条消息会耗用更长的时间（消息大小增加所致）。 由于预提取的消息已由客户端下载，因此从缓存接收这些消息的速度将变快。

服务器会在向客户端发送消息时检查消息的“生存时间 (TTL)”属性。 收到消息时，客户端不检查消息的 TTL 属性。 即使消息由客户端缓存时该消息的 TTL 已结束，仍可接收该消息。

预提取不会影响可计费的消息传送操作的数目，且仅适用于服务总线客户端协议。 HTTP 协议不支持预提取。 预提取可用于同步和异步接收操作。

# <a name="azuremessagingservicebus-sdk"></a>[Azure 消息传送。](#tab/net-standard-sdk-2)
有关详细信息，请参阅下述 `PrefetchCount` 属性：

- [ServiceBusReceiver. PrefetchCount](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.prefetchcount)
- [ServiceBusProcessor. PrefetchCount](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.prefetchcount)

可以在 [ServiceBusReceiverOptions](/dotnet/api/azure.messaging.servicebus.servicebusreceiveroptions) 或 [ServiceBusProcessorOptions](/dotnet/api/azure.messaging.servicebus.servicebusprocessoroptions)中设置这些属性的值。

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

有关详细信息，请参阅下述 `PrefetchCount` 属性：

* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

有关详细信息，请参阅下述 `PrefetchCount` 属性：

* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="prefetching-and-receivebatch"></a>预提取和 ReceiveBatch
尽管同时预提取多个消息的概念与成批处理消息 (`ReceiveBatch`) 的语义类似，但在将这些方法结合使用时，必须注意一些细微的差异。

预提取是客户端（`QueueClient` 和 `SubscriptionClient`）上的配置（或模式），`ReceiveBatch` 是一个操作（具有请求-响应语义）。

将这些方法结合使用时，请考虑以下情况：

* 预提取的消息数应大于或等于预期从 `ReceiveBatch` 接收的消息数。
* 预提取的消息数最多可以是每秒处理的消息数的 n/3 倍，其中 n 为默认的锁定持续时间。

使用贪婪方法（即让预提取计数保持在高水平）时存在一些挑战，因为这意味着消息被锁定到特定接收方。 建议在上面提到的阈值之间尝试预提取值，凭经验识别适合的值。

## <a name="multiple-queues"></a>多个队列

如果单个队列或主题无法满足预期，则使用多个消息实体。 在使用多个实体时，为每个实体创建专用客户端，而不是针对所有实体使用同一个客户端。

## <a name="development-and-testing-features"></a>开发和测试功能

> [!NOTE]
> 本部分仅适用于 Windowsazure.storage SDK，因为例如，Azure. 和 Azure 不公开此功能。

服务总线有一项专用于开发但 **永远不应在生产配置中使用** 的功能：[`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering]。

将新的规则或筛选器添加到主题时，可通过 [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] 验证新的筛选器表达式是否可以按预期使用。

## <a name="scenarios"></a>方案

以下各节介绍典型的消息传递方案，并概述首选服务总线设置。 吞吐速率分为小（小于 1 条消息/秒）、中等（1 条消息/秒或更大，但不超过 100 条消息/秒）和高（100 条消息/秒或更大）。 客户端数分为小（5 个或更少）、中等（5 个以上但小于或等于 20 个）和大（超过 20 个）。

### <a name="high-throughput-queue"></a>高吞吐量队列

目标：将单个队列的吞吐量最大化。 发送方和接收方的数目较小。

* 如要增加面向队列的总发送速率，则使用多个消息工厂来创建发送方。 为每个发送方使用异步操作或多个线程。
* 如要增加从队列接收的总体接收速率，则使用多个消息工厂来创建接收方。
* 使用异步操作可利用客户端批处理。
* 将批处理间隔时间设置为 50 毫秒以减少服务总线客户端协议传输的数量。 如果使用多个发送方，则将批处理间隔时间增加到 100 毫秒。
* 将批量存储访问保留为启用状态。 该访问会增加可将消息写入队列的总速率。
* 将预提取计数设置为工厂所有接收方最大处理速率的 20 倍。 此计数会减少服务总线客户端协议传输的数量。

### <a name="multiple-high-throughput-queues"></a>多个高吞吐量队列

目标：将多个队列的整体吞吐量最大化。 单个队列的吞吐量中等或高。

要在多个队列之间获得最大的吞吐量，则使用所述设置将单个队列的吞吐量最大化。 此外，使用不同工厂创建向不同的队列发送或从其接收的客户端。

### <a name="low-latency-queue"></a>低延迟队列

目标：将队列或主题的延迟时间最小化。 发送方和接收方的数目较小。 队列的吞吐量较小或为中等。

* 禁用客户端批处理。 客户端会立即发送一条消息。
* 禁用批量存储访问。 该服务会立即将消息写入存储。
* 如果使用单个客户端，将预提取计数设置为接收方处理速率的 20 倍。 如果多条消息同时到达队列，服务总线客户端协议会将这些消息全部同时传输。 当客户端收到下一条消息时，该消息便已存在于本地缓存中。 缓存应较小。
* 如果使用多个客户端，则将预提取计数设置为 0。 通过设置此计数，在第一个客户端仍在处理第一条消息时，第二个客户端可以接收第二条消息。

### <a name="queue-with-a-large-number-of-senders"></a>包含大量发送方的队列

目标：使包含大量发件人的队列或主题的吞吐量最大化。 每个发送方均以中等速率发送消息。 接收方的数目较小。

服务总线允许最多 1000 个与消息传送实体之间的并发连接。 该限制在命名空间级别强制实施，队列数、主题数或订阅数的上限受到单个命名空间的并发连接数的限制。 就队列而言，此数值在发送方和接收方之间共享。 如果发件人需要所有 1000 个连接，则将队列替换为主题和单个订阅。 主题接受来自发送方的最多 1000 个并发连接。 订阅接受来自接收方的其他 1000 个并发连接。 如果需要超过 1000 个并发发送方，则发送方应通过 HTTP 向服务总线协议发送消息。

若要使吞吐量最大化，请执行以下步骤：

* 如果每个发送方处于不同进程中，则每个进程仅使用单个工厂。
* 使用异步操作可利用客户端批处理。
* 使用 20 毫秒的默认批处理间隔时间以减少服务总线客户端协议传输的数量。
* 将批量存储访问保留为启用状态。 此访问会增加可将消息写入队列或主题的总速率。
* 将预提取计数设置为工厂所有接收方最大处理速率的 20 倍。 此计数会减少服务总线客户端协议传输的数量。

### <a name="queue-with-a-large-number-of-receivers"></a>包含大量接收方的队列

目标：使包含大量接收方的队列或订阅的接收速率最大化。 每个接收方以中等接收速率接收消息。 发送方的数目较小。

服务总线允许最多 1000 个与实体之间的并发连接。 如果队列需要超过 1000 个接收方，则将队列替换为主题和多个订阅。 每个订阅可支持最多 1000 个并发连接。 或者，接收方可通过 HTTP 协议访问队列。

若要使吞吐量最大化，请遵循以下准则：

* 如果每个接收方处于不同进程中，每个进程仅使用单个工厂。
* 接收方可使用同步或异步操作。 如果独立接收方的接收速率给定为中等级别，客户端对“完成”请求的批处理不会影响接收方吞吐量。
* 将批量存储访问保留为启用状态。 此访问会减少实体的总负载。 这还将降低可将消息写入队列或主题的总速率。
* 将预提取计数设置为较小值（例如，PrefetchCount = 10）。 此计数可防止接收方在其他接收方已缓存大量消息时处于闲置状态。

### <a name="topic-with-a-few-subscriptions"></a>带有几个订阅的主题

目标：最大限度地提高具有几个订阅的主题的吞吐量。 消息由多个订阅接收，这意味着对所有订阅的组合接收速率比发送速率要大得多。 发送方的数目较小。 每个订阅的接收方的数目较小。

若要使吞吐量最大化，请遵循以下准则：

* 如要增加面向主题的总发送速率，则使用多个消息工厂来创建发送方。 为每个发送方使用异步操作或多个线程。
* 如要增加从订阅接收的总体接收速率，则使用多个消息工厂来创建接收方。 为每个接收方使用异步操作或多个线程。
* 使用异步操作可利用客户端批处理。
* 使用 20 毫秒的默认批处理间隔时间以减少服务总线客户端协议传输的数量。
* 将批量存储访问保留为启用状态。 此访问会增加可将消息写入主题的总写入速率。
* 将预提取计数设置为工厂所有接收方最大处理速率的 20 倍。 此计数会减少服务总线客户端协议传输的数量。

### <a name="topic-with-a-large-number-of-subscriptions"></a>包含大量订阅的主题

目标：使包含大量订阅的主题的吞吐量最大化。 消息由多个订阅接收，这意味着对所有订阅的组合接收速率比发送速率要大得多。 发送方的数目较小。 每个订阅的接收方的数目较小。

如果所有消息都路由到所有订阅，具有大量订阅的主题则通常会公开低的总吞吐量。 这是因为每个消息均被接收了许多次，且主题及其全部订阅中的所有消息均存储在相同的存储内。 此处的假设是，每个订阅的发送方和接收方的数目都较小。 服务总线支持每个主题最多 2,000 个订阅。

若要使吞吐量最大化，则尝试执行以下步骤：

* 使用异步操作可利用客户端批处理。
* 使用 20 毫秒的默认批处理间隔时间以减少服务总线客户端协议传输的数量。
* 将批量存储访问保留为启用状态。 此访问会增加可将消息写入主题的总写入速率。
* 将预提取计数设置为预期接收速率的 20 倍（以秒为单位）。 此计数会减少服务总线客户端协议传输的数量。

<!-- .NET Standard SDK, Microsoft.Azure.ServiceBus -->
[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender

<!-- .NET Framework SDK, Microsoft.Azure.ServiceBus -->
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnableFiltering]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing

<!-- Local links -->
[Partitioned messaging entities]: service-bus-partitioning.md
