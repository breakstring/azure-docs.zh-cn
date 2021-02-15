---
title: Azure 服务总线常见问题解答 (FAQ) | Microsoft Docs
description: 本文提供了一些有关 Azure 服务总线的常见问题解答 (FAQ)。
ms.topic: article
ms.date: 01/20/2021
ms.openlocfilehash: 3a96cf94ca4a7edd115f12b3e2eded11a5894e04
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98693388"
---
# <a name="azure-service-bus---frequently-asked-questions-faq"></a>Azure 服务总线 - 常见问题解答 (FAQ)

本文讨论了一些关于 Microsoft Azure 服务总线的常见问题解答。 还可以访问 [Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)了解常规的 Azure 定价和支持信息。


## <a name="general-questions-about-azure-service-bus"></a>关于 Azure 服务总线的一般问题
### <a name="what-is-azure-service-bus"></a>什么是 Azure 服务总线？
[Azure 服务总线](service-bus-messaging-overview.md) 是一个异步消息传送云平台，可让你在分离的系统之间发送数据。 Microsoft 以服务的形式提供此功能，这意味着你无需托管你自己的硬件即可使用它。

### <a name="what-is-a-service-bus-namespace"></a>什么是服务总线命名空间？
[命名空间](service-bus-create-namespace-portal.md)提供了用于对应用程序中的服务总线资源进行寻址的范围容器。 必须创建命名空间才能使用服务总线，而且这也是开始使用的第一步。

### <a name="what-is-an-azure-service-bus-queue"></a>什么是 Azure 服务总线队列？
[服务总线队列](service-bus-queues-topics-subscriptions.md)是用于存储消息的实体。 有多个应用程序，或者有多个需要彼此通信的分布式应用程序部分时，队列特别有用。 队列和发行中心的相似之处在于，两者都会接收多个产品（消息），再从该处送出。

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>什么是 Azure 服务总线主题和订阅？
主题可被视为队列，使用多个订阅时，它成为更丰富的消息传送模型；实质上是一种一对多的通信工具。 此发布/订阅模型（或 pub/sub）启用了一个应用程序，该应用程序将消息发送到具有多个订阅的主题中，进而使多个应用程序接收到该消息。

### <a name="what-is-a-partitioned-entity"></a>什么是分区实体？
传统的队列或主题由单个消息中转站进行处理并存储在一个消息存储中。 仅在基本和标准消息传递层中受支持，[分区队列或主题](service-bus-partitioning.md)由多个消息中转站处理，并存储在多个消息传送存储中。 此功能意味着分区的队列或主题的总吞吐量不再受到单个消息中转站或消息存储的性能限制。 此外，消息传送存储的临时中断不会导致分区队列或主题不可用。

使用分区实体时不保证排序。 如果某个分区不可用，仍可从其他分区发送和接收消息。

 [高级 SKU](service-bus-premium-messaging.md) 中不再支持分区实体。 

### <a name="where-does-azure-service-bus-store-data"></a><a name="in-region-data-residency"></a>Azure 服务总线将数据存储在何处？
Azure 服务总线标准层利用 Azure SQL 数据库提供其后端存储层。 对于除巴西南部和东南部以外的所有区域，数据库备份将托管在不同的区域中 (通常) 的 Azure 配对区域。 对于巴西南部和东南亚区域，数据库备份存储在同一区域中，以适应这些区域的数据驻留要求。

Azure 服务总线高级层在你选择的区域中存储元数据和数据。 为 Azure 服务总线高级命名空间设置异地灾难恢复时，会将元数据复制到所选的次要区域。


### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>需要在防火墙上打开哪些端口？ 
可以将以下协议与 Azure 服务总线配合使用，以便发送和接收消息：

- 高级消息队列协议 1.0 (AMQP)
- 具有 TLS 的超文本传输协议 1.1 (HTTPS)

请查看下表，了解需要打开哪些出站 TCP 端口，以便使用这些协议与 Azure 服务总线通信：

| 协议 | Port | 详细信息 | 
| -------- | ----- | ------- | 
| AMQP | 5671 | 具有 TLS 的 AMQP。 请参阅 [AMQP 协议指南](service-bus-amqp-protocol-guide.md) | 
| HTTPS | 443 | 此端口用于 HTTP/REST API 和 AMQP-over-WebSockets |

在 AMQP 通过端口 5671 使用时，通常还需要使用 HTTPS 端口进行出站通信，因为客户端 SDK 执行的一些管理操作和从 Azure Active Directory（使用时）获取令牌的操作都是通过 HTTPS 运行的。 

正式的 Azure SDK 通常使用 AMQP 协议通过服务总线发送和接收消息。 

[!INCLUDE [service-bus-websockets-options](../../includes/service-bus-websockets-options.md)]

.NET Framework 的较旧 WindowsAzure.ServiceBus 包可选择使用旧的“服务总线消息传送协议”(SBMP)，也称为“NetMessaging”。 此协议使用 TCP 端口 9350-9354。 此包的默认模式用于自动检测这些端口是否可用于通信，如果不可用，将通过端口 443 切换到具有 TLS 的 WebSockets。 可以通过在 [`ServiceBusEnvironment.SystemConnectivity`](/dotnet/api/microsoft.servicebus.servicebusenvironment.systemconnectivity) 设置上设置 `Https` [ConnectivityMode](/dotnet/api/microsoft.servicebus.connectivitymode) 来替代此设置并强制执行此模式，这将全局应用于应用程序。

### <a name="what-ip-addresses-do-i-need-to-add-to-allow-list"></a>需要将哪些 IP 地址添加到允许列表？
若要查找要添加到允许列表以进行连接的正确 IP 地址，请执行以下步骤：

1. 从命令提示符处运行以下命令： 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. 记下 `Non-authoritative answer` 中返回的 IP 地址。 

如果对命名空间使用区域冗余，则需执行一些额外的步骤： 

1. 首先，在命名空间中运行 nslookup。

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. 记下“非权威回答”部分中的名称，该名称采用下述格式之一： 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. 为每一个运行 nslookup，使用后缀 s1、s2 和 s3 获取所有三个在三个可用性区域中运行的实例的 IP 地址。 

    > [!NOTE]
    > `nslookup` 命令返回的 IP 地址不是静态 IP 地址。 但是，在删除基础部署或将其移至其他群集之前，该地址保持不变。

### <a name="where-can-i-find-the-ip-address-of-the-client-sendingreceiving-messages-tofrom-a-namespace"></a>我可以在哪里找到客户端向命名空间发送/从中接收消息的 IP 地址？ 
我们不记录客户端向命名空间发送或从中接收消息的 IP 地址。 重新生成密钥，以便所有现有的客户端将无法进行身份验证并查看 [Azure 基于角色的访问控制 (Azure RBAC)](authenticate-application.md#azure-built-in-roles-for-azure-service-bus) 设置，以确保仅允许的用户或应用程序可以访问该命名空间。 

如果使用的是高级命名空间，请使用 [IP 筛选](service-bus-ip-filtering.md)、[虚拟网络服务终结点](service-bus-service-endpoints.md)和[专用终结点](private-link-service.md)来限制对命名空间的访问。 

## <a name="best-practices"></a>最佳实践
### <a name="what-are-some-azure-service-bus-best-practices"></a>Azure 服务总线的最佳实践有哪些？
请参阅[使用服务总线改进性能的最佳做法][Best practices for performance improvements using Service Bus] - 此文介绍了如何在交换消息时优化性能。

### <a name="what-should-i-know-before-creating-entities"></a>创建实体前的须知事项有哪些？
队列和主题的以下属性是固定不变的。 预配实体时，请考虑此限制，因为只有创建新的替代实体才可修改这些属性。

* 分区
* 会话
* 重复检测
* 快速实体

## <a name="pricing"></a>定价
本部分回答了一些关于服务总线定价结构的常见问题解答。

[服务总线定价和计费](https://azure.microsoft.com/pricing/details/service-bus/)译文介绍了服务总线中的计费标准。 有关服务总线定价选项的具体信息，请参阅[服务总线定价详细信息](https://azure.microsoft.com/pricing/details/service-bus/)。

还可以访问 [Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)了解常规的 Azure 定价信息。 

### <a name="how-do-you-charge-for-service-bus"></a>服务总线如何收取费用？
有关服务总线定价的完整信息，请参阅[服务总线定价][Pricing overview]。 除标示的价格外，还需为在其中部署应用程序的数据中心之外的相关数据输出支付费用。

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-isnt"></a>服务总线的哪些使用情况受数据传输限制？ 哪些不受限制？
在给定 Azure 区域内的任何数据传输和入站数据传输均不收费。 区域外的数据传输需收取输出费用，详见[此处](https://azure.microsoft.com/pricing/details/bandwidth/)。

### <a name="does-service-bus-charge-for-storage"></a>服务总线是否对存储收费？
不能。 服务总线不对存储收费。 但是，对每个队列/主题可以保留的数据最大量设有配额限制。 请参阅下一个常见问题。

### <a name="i-have-a-service-bus-standard-namespace-why-do-i-see-charges-under-resource-group-system"></a>我有一个服务总线标准命名空间。 为什么我在资源组 '$system' 下看到收费信息？
Azure 服务总线最近升级了计费组件。 由于此更改，如果你有服务总线标准命名空间，则可能会在资源组 '$system' 下看到资源 '/subscriptions/<azure_subscription_id>/resourceGroups/$system/providers/Microsoft.ServiceBus/namespaces/$system' 的行项目。

这些费用表示已预配服务总线标准命名空间的每个 Azure 订阅的基本费用。 

请务必注意，这些费用不是新的，即它们也存在于以前的计费模型中。 唯一的更改是它们现在列在 '$system' 下。 这是由于新的计费系统中的限制所致，新的计费系统将订阅级别的费用分组，而不是绑定到 '$system' 资源 ID 下的特定资源。

## <a name="quotas"></a>配额

有关服务总线限制和配额的列表，请参阅[服务总线配额概述][Quotas overview]。

### <a name="how-to-handle-messages-of-size--1-mb"></a>如何处理大小 > 1 MB 的消息？
服务总线消息服务（队列和主题/订阅）允许应用程序发送大小不超过 256 KB（标准层）或 1 MB（高级层）的消息。 若要处理大小超过 1 MB 的消息，请使用[此博客文章](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern)中所述的声明检查模式。

## <a name="troubleshooting"></a>故障排除
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>为什么在从其他订阅中删除命名空间后无法创建该命名空间？ 
从订阅中删除命名空间时，请等待 4 个小时，然后才能在另一个订阅中使用相同的名称重新创建它。 否则，可能会收到以下错误消息：`Namespace already exists`。 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Azure 服务总线 API 生成了哪些异常？建议采取什么操作？
有关可能的服务总线异常的列表，请参阅[异常概述][Exceptions overview]。

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>什么是共享访问签名？哪些语言支持生成签名？
共享访问签名是基于 SHA-256 安全哈希或 URI 的身份验证机制。 有关如何在 Node.js、PHP、Java、Python 和 C# 中生成自己的签名的信息，请参阅[共享访问签名][Shared Access Signatures]一文。

## <a name="subscription-and-namespace-management"></a>订阅和命名空间管理
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>如何将命名空间迁移到另一个 Azure 订阅中？

可以使用 [Azure 门户](https://portal.azure.com)或 PowerShell 命令，将命名空间从一个 Azure 订阅移到另一个 Azure 订阅。 若要执行此操作，命名空间必须已处于活动状态。 执行这些命令的用户必须是源订阅和目标订阅的管理员。

#### <a name="portal"></a>门户

若要使用 Azure 门户将服务总线命名空间迁移到其他订阅，可按照[此处](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal)的说明操作。 

#### <a name="powershell"></a>PowerShell

下面的 PowerShell 命令序列将命名空间从一个 Azure 订阅迁移到另一个 Azure 订阅。 若要执行此操作，命名空间必须已经处于活动状态，而且运行 PowerShell 命令的用户必须同时是源订阅和目标订阅的管理员。

```powershell
# Create a new resource group in target subscription
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```
## <a name="is-it-possible-to-disable-tls-10-or-11-on-service-bus-namespaces"></a>是否可以在服务总线命名空间上禁用 TLS 1.0 或1.1？
不能。 不能在服务总线命名空间上禁用 TLS 1.0 或1.1。 在连接到服务总线的客户端应用程序中，使用 TLS 1.2 或更高版本。 有关详细信息，请参阅使用 [Azure 服务总线强制使用 TLS 1.2-Microsoft 技术社区](https://techcommunity.microsoft.com/t5/messaging-on-azure/enforcing-tls-1-2-use-with-azure-service-bus/ba-p/370912)。

## <a name="next-steps"></a>后续步骤
若要了解有关服务总线的详细信息，请参阅以下文章：

* [Azure 服务总线高级版简介（博客文章）](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Azure 服务总线高级版简介 (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [服务总线概述](service-bus-messaging-overview.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
