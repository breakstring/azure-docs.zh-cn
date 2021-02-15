---
title: 有关执行组件类型序列化的 Reliable Actors 说明
description: 讨论了定义可序列化类的基本要求，这些类可用于定义 Service Fabric Reliable Actors 的状态和接口
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 2a1118e394046b217a288663659a2c910098e992
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576003"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>有关 Service Fabric Reliable Actors 类型序列化的说明
所有方法的参数、执行组件接口中每个方法返回的任务的结果类型和执行组件的状态管理器中存储的对象都必须是[数据协定可序列化](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer)。 这同样适用于[执行组件事件接口](service-fabric-reliable-actors-events.md)中定义的方法的参数。 （执行组件事件接口方法始终返回 void。）

## <a name="custom-data-types"></a>自定义数据类型
在此示例中，以下执行组件接口定义了一个返回自定义数据类型（名为 `VoicemailBox`）的方法：

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

```Java
public interface VoiceMailBoxActor extends Actor
{
    CompletableFuture<VoicemailBox> getMailBoxAsync();
}
```

此接口由使用状态管理器来存储 `VoicemailBox` 对象的执行组件实现：

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class VoiceMailBoxActorImpl extends FabricActor implements VoicemailBoxActor
{
    public VoiceMailBoxActorImpl(ActorService actorService, ActorId actorId)
    {
         super(actorService, actorId);
    }

    public CompletableFuture<VoicemailBox> getMailBoxAsync()
    {
         return this.stateManager().getStateAsync("Mailbox");
    }
}

```

在此示例中，如果 `VoicemailBox` 对象出现以下情况，将序列化该对象：

* 在执行组件实例和调用方之间传输该对象。
* 该对象保存在状态管理器中，即持久保存在磁盘中，并且已复制到其他节点。

Reliable Actor 框架使用 DataContract 序列化。 因此，自定义数据对象及其成员必须分别使用 **DataContract** 和 **DataMember** 属性进行批注。

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```
```Java
public class Voicemail implements Serializable
{
    private static final long serialVersionUID = 42L;

    private UUID id;                    //getUUID() and setUUID()

    private String message;             //getMessage() and setMessage()

    private GregorianCalendar receivedAt; //getReceivedAt() and setReceivedAt()
}
```


```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```
```Java
public class VoicemailBox implements Serializable
{
    static final long serialVersionUID = 42L;
    
    public VoicemailBox()
    {
        this.messageList = new ArrayList<Voicemail>();
    }

    private List<Voicemail> messageList;   //getMessageList() and setMessageList()

    private String greeting;               //getGreeting() and setGreeting()
}
```


## <a name="next-steps"></a>后续步骤
* [执行组件生命周期和垃圾回收](service-fabric-reliable-actors-lifecycle.md)
* [执行组件计时器和提醒](service-fabric-reliable-actors-timers-reminders.md)
* [执行组件事件](service-fabric-reliable-actors-events.md)
* [执行组件可重入性](service-fabric-reliable-actors-reentrancy.md)
* [执行组件多态性和面向对象的设计模式](service-fabric-reliable-actors-polymorphism.md)
* [执行组件诊断和性能监视](service-fabric-reliable-actors-diagnostics.md)
