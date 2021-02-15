---
title: Azure 事件网格概念
description: 介绍 Azure 事件网格及其概念。 定义事件网格的几个关键组件。
ms.topic: conceptual
ms.date: 01/21/2021
ms.openlocfilehash: 6edc8a3980bfea15f28cfb7114bb9f8350a47a3f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685697"
---
# <a name="concepts-in-azure-event-grid"></a>Azure 事件网格中的概念

本文介绍了 Azure 事件网格中的主要概念。

## <a name="events"></a>事件

事件是完全描述系统中所发生情况的最小信息量。 每个事件都具有通用信息，如事件源、事件发生的时间和唯一标识符。 此外，每个事件还具有仅与特定事件类型相关的特定信息。 例如，与在 Azure 存储中创建的有关新文件的事件将包含有关该文件的详细信息，如 `lastTimeModified` 值。 再如，事件中心事件具有 Capture 文件的 URL。 

事件允许的最大大小为 1 MB。 超过 64 KB 的事件以 64 KB 为增量计费。 至于在事件中发送的属性，请参阅 [Azure 事件网格事件架构](event-schema.md)。

## <a name="publishers"></a>发布服务器

发布服务器是决定将事件发送到事件网格的用户或组织。 Microsoft 发布了几个 Azure 服务的事件。 可以从自己的应用程序发布事件。 在 Azure 外部托管服务的组织可以通过事件网格发布事件。

## <a name="event-sources"></a>事件源

事件源即事件发生的位置。 每个事件源都与一个或多个事件类型相关。 例如，Azure 存储是 blob 创建事件的事件源。 IoT 中心是设备创建的事件的事件源。 你的应用程序是你定义的自定义事件的事件源。 事件源负责将事件发送到事件网格。

有关实现任何受支持的事件网格源的信息，请参阅 [Azure 事件网格中的事件源](overview.md#event-sources)。

## <a name="topics"></a>主题

事件网格主题提供源在其中发送事件的终结点。 发布服务器创建事件网格主题，并决定事件源需要一个主题还是多个主题。 主题用于收集相关事件。 为了对某些类型的事件作出响应，订阅服务器将决定要订阅的主题。

系统主题是 Azure 服务（如 Azure 存储、Azure 事件中心和 Azure 服务总线）提供的内置主题。 可以在 Azure 订阅中创建系统主题并订阅这些主题。 有关详细信息，请参阅[系统主题概述](system-topics.md)。 

 自定义主题是应用程序和第三方的主题。 在创建或分配了对自定义主题的访问权限后，就会在订阅中看到该自定义主题。 有关详细信息，请参阅[自定义主题](custom-topics.md)。 设计应用程序时，可以灵活地决定要创建多少个主题。 对于大型解决方案，请为每个类别的相关事件创建自定义主题。 例如，假设有一个应用程序发送与修改用户帐户和处理订单相关的事件。 任何事件处理程序都不太可能同时需要这两个类别的事件。 请创建两个自定义主题，让事件处理程序订阅其感兴趣的主题。 对于小型解决方案，可能希望将所有事件发送到单个主题。 事件订阅者可以筛选他们所需的事件类型。

还有另一种类型的主题： **合作伙伴**。 使用 [合作伙伴活动](partner-events-overview.md) 功能，第三方 SaaS 提供程序可以从其服务发布事件，使其可供订阅这些事件的使用者使用。 SaaS 提供程序 **公开了订阅** 者用于使用事件的主题类型。 它还通过分离事件发布者和订阅服务器所使用的资源的问题和所有权来提供干净的 "发布" 子模型。

## <a name="event-subscriptions"></a>事件订阅

订阅告诉事件网格你有兴趣接收主题上的哪些事件。 在创建订阅时，你提供处理事件的终结点。 可以筛选发送到终结点的事件。 可以按事件类型或使用者模式进行筛选。 有关详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。

有关创建订阅的示例，请参阅：

* [事件网格的 Azure CLI 示例](cli-samples.md)
* [事件网格的 Azure PowerShell 示例](powershell-samples.md)
* [事件网格的 Azure 资源管理器模板](template-samples.md)

有关获取当前事件网格订阅的信息，请参阅[查询事件网格订阅](query-event-subscriptions.md)。

## <a name="event-subscription-expiration"></a>事件订阅过期
事件订阅在该日期后自动过期。 为仅在有限时间内需要的事件订阅设置一个过期日期，你不需要担心清理这些订阅。 例如，创建事件订阅来测试某个方案时，你可能想要设置过期日期。 

有关设置过期日期的示例，请参阅[使用高级筛选器进行订阅](how-to-filter-events.md#subscribe-with-advanced-filters)。

## <a name="event-handlers"></a>事件处理程序

从事件网格的角度看，事件处理程序是发送事件的位置。 处理程序将执行一些进一步的操作来处理事件。 事件网格支持多个处理程序类型。 可以使用受支持的 Azure 服务或你自己的 webhook 作为处理程序。 根据处理程序的类型，事件网格遵循不同机制，以保证事件的传递。 对于 HTTP webhook 事件处理程序，在处理程序返回状态代码 `200 – OK` 之前，将重试事件。 对于 Azure 存储队列，将重试事件，直到队列服务成功处理推送到队列的消息。

有关实现任何受支持的事件网格处理程序的信息，请参阅 [Azure 事件网格中的事件处理程序](event-handlers.md)。

## <a name="security"></a>安全性

事件网格可为订阅主题和发布主题提供安全性。 订阅时，必须对资源或事件网格主题具有足够的权限。 发布时，必须具有该主题的 SAS 令牌或密钥身份验证。 有关详细信息，请参阅[事件网格安全性和身份验证](security-authentication.md)。

## <a name="event-delivery"></a>事件传送

如果事件网格无法确认订阅服务器的终结点已接收到事件，它将重新传递该事件。 有关详细信息，请参阅[事件网格消息传递和重试](delivery-and-retry.md)。

## <a name="batching"></a>批处理

使用自定义主题时，必须始终在数组中发布事件。 对于低吞吐量方案，可采用单批；但对于大容量用例，建议在每次发布时对多个事件进行批处理，以实现更高的效率。 批处理最大可以为 1 MB，事件的最大大小为 1 MB。 

## <a name="next-steps"></a>后续步骤

* 有关事件网格的介绍，请参阅[关于事件网格](overview.md)。
* 若要快速开始使用事件网格，请参阅[使用 Azure 事件网格创建和路由自定义事件](custom-event-quickstart.md)。
