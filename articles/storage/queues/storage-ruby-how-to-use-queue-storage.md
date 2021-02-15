---
title: 如何通过 Ruby 使用队列存储-Azure 存储
description: 了解如何使用 Azure 队列存储来创建和删除队列，以及插入、获取和删除消息。 用 Ruby 编写的相关示例。
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 12/08/2016
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 8558949e49bcf551c9276458d375fb9ac9636184
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587656"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>如何通过 Ruby 使用队列存储

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>概述

本指南演示如何使用 Microsoft Azure 队列存储服务执行常见方案。 相关示例是使用 Ruby Azure API 编写的。 介绍的方案包括 **插入**、**扫视**、**获取** 和 **删除** 队列消息以及 **创建和删除队列**。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>创建 Ruby 应用程序

创建 Ruby 应用程序。 有关说明，请参阅在 [Linux 应用服务中创建 Ruby 应用程序](../../app-service/quickstart-ruby.md)。

## <a name="configure-your-application-to-access-storage"></a>配置应用程序以访问存储

若要使用 Azure 存储，需要下载并使用 Ruby Azure 包，其中包括一组便于与存储 REST 服务进行通信的库。

<!-- docutune:ignore Terminal -->

### <a name="use-rubygems-to-obtain-the-package"></a>使用 RubyGems 获取该程序包

1. 使用命令行接口，例如 PowerShell (Windows)、Terminal (Mac) 或 Bash (Unix)。
2. `gem install Azure`在 "命令" 窗口中键入，安装 gem 和依赖项。

### <a name="import-the-package"></a>导入包

使用常用的文本编辑器将以下内容添加到要在其中使用存储的 Ruby 文件的顶部：

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>设置 Azure 存储连接

Azure 模块将读取环境变量 `AZURE_STORAGE_ACCOUNT` 和 `AZURE_STORAGE_ACCESS_KEY` 连接到 Azure 存储帐户所需的信息。 如果未设置这些环境变量，则必须先使用以下代码指定帐户信息 `Azure::QueueService` ：

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

从 Azure 门户中的经典或 Resource Manager 存储帐户中获取这些值：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到要使用的存储帐户。
3. 在右侧的 " **设置** " 边栏选项卡中，单击 " **访问密钥**"。
4. 在出现的 " **访问密钥** " 边栏选项卡中，你将看到访问密钥1和访问密钥2。 可以使用其中任意一个。
5. 单击复制图标以将键复制到剪贴板。

## <a name="how-to-create-a-queue"></a>如何：创建队列

下面的代码创建一个 `Azure::QueueService` 对象，该对象使你可以使用队列。

```ruby
azure_queue_service = Azure::QueueService.new
```

使用 `create_queue()` 方法创建具有指定名称的队列。

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>如何：在队列中插入消息

若要在队列中插入消息，请使用 `create_message()` 方法创建新消息并将其添加到队列中。

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>如何：扫视下一条消息

通过调用方法，可以查看队列前面的消息，而不必从队列中将其删除 `peek_messages()` 。 默认情况下，`peek_messages()` 扫视单条消息。 也可以指定要扫视的消息数。

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>如何：取消对下一条消息的排队

可通过两个步骤从队列中删除消息。

1. 调用时 `list_messages()` ，默认情况下会获取队列中的下一条消息。 也可以指定要获取的消息数。 返回的消息 `list_messages()` 对从此队列读取消息的任何其他代码不可见。 将传递以秒为单位的可见性超时值作为参数。
2. 若要完成从队列中删除消息，还必须调用 `delete_message()` 。

此删除消息的两步过程可确保当代码因硬件或软件故障而无法处理消息时，其他代码实例可以获取同一消息并重试。 代码在处理消息后会立即调用 `delete_message()`。

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue",
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>如何：更改已排队消息的内容

可以更改队列中现有消息的内容。 下面的代码使用 `update_message()` 方法来更新消息。 方法将返回一个元组，其中包含队列消息的 pop 接收方，以及一个 `DateTime` 表示消息将在队列中可见的时间的 UTC 值。

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message",
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>如何：出列消息的其他选项

可通过两种方式自定义队列中消息的检索。

1. 可以获取一批消息。
2. 可以设置更长或更短的不可见超时时间，从而允许代码使用更多或更少的时间来完全处理每个消息。

下面的代码示例使用 `list_messages()` 方法在一次调用中获取15条消息。 然后，它打印并删除每条消息。 它还将每条消息的不可见超时时间设置为 5 分钟。

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>如何：获取队列长度

可以获取队列中消息数的估计值。 `get_queue_metadata()`方法返回大概的消息计数和其他队列元数据。

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>如何：删除队列

若要删除队列及其中包含的所有消息，请对 `delete_queue()` 队列对象调用方法。

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>后续步骤

现在，你已了解有关队列存储的基础知识，请单击下面的链接了解更复杂的存储任务。

- 访问 [Azure 存储团队博客](/archive/blogs/windowsazurestorage/)
- 访问 GitHub 上的 [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) 存储库

有关本文中讨论的 Azure 队列存储与[如何使用服务总线队列](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/)中讨论的 Azure 服务总线队列之间的比较，请参阅[azure 队列存储和服务总线队列-比较与](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)对照
