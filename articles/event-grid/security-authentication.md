---
title: 对目标为事件处理程序的事件传递进行身份验证（Azure 事件网格）
description: 本文介绍在 Azure 事件网格中通过不同方式对目标为事件处理程序的传递进行身份验证。
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 98d7a4a0dee6c355ec340668bef7d8b306f97496
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633114"
---
# <a name="authenticate-event-delivery-to-event-handlers-azure-event-grid"></a>对目标为事件处理程序的事件传递进行身份验证（Azure 事件网格）
本文介绍如何对目标为事件处理程序的事件传递进行身份验证。 本文还介绍如何使用 Azure Active Directory (Azure AD) 或共享机密保护用于从事件网格接收事件的 Webhook 终结点。

## <a name="use-system-assigned-identities-for-event-delivery"></a>使用系统分配的标识进行事件传递
可以为主题或域启用系统分配的托管标识，并使用该标识将事件转发到支持的目标，如服务总线队列和主题、事件中心和存储帐户。

步骤如下： 

1. 使用系统分配的标识创建主题或域，或者更新现有主题或域以启用标识。 
1. 在目标（例如，服务总线队列）上将标识添加到相应角色（例如，服务总线数据发送方）。
1. 创建事件订阅时，请允许使用标识将事件传递到目标。 

有关详细的分步说明，请参阅[使用托管标识传递事件](managed-service-identity.md)。


## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>对 Webhook 终结点的事件传递进行身份验证
下面各部分介绍了如何对 Webhook 终结点的事件传递进行身份验证。 无论使用何种方法，都需要使用验证握手机制。 有关详细信息，请参阅 [Webhook 事件传递](webhook-event-delivery.md)。 


### <a name="using-azure-active-directory-azure-ad"></a>使用 Azure Active Directory (Azure AD)
可以使用 Azure AD 保护用于从事件网格接收事件的 Webhook 终结点。 需要创建 Azure AD 应用程序，并在授权事件网格的应用程序中创建角色和服务主体，同时还需要将事件订阅配置为使用 Azure AD 应用程序。 了解如何[使用事件网格配置 Azure Active Directory](secure-webhook-delivery.md)。

### <a name="using-client-secret-as-a-query-parameter"></a>使用客户端密码作为查询参数
还可以通过向在创建事件订阅时指定的 Webhook 目标 URL 添加查询参数来保护 Webhook 终结点。 将其中一个查询参数设置为客户端密码，如[访问令牌](https://en.wikipedia.org/wiki/Access_token)或共享密码。 事件网格服务会在发往 Webhook 的每个事件传递请求中加入所有查询参数。 Webhook 服务可以检索和验证密码。 如果更新了客户端密码，还需要更新事件订阅。 为了避免在此密码轮换期间出现传递失败，让 Webhook 在有限的时间内同时接受新旧密码，然后再使用新密码更新事件订阅。 

由于查询参数可能包含客户端密码，因此需要格外小心地处理它们。 它们以加密的形式存储，并且服务操作员无法访问。 它们不作为服务日志/跟踪的一部分进行记录。 检索事件订阅属性时，默认情况下不会返回目标查询参数。 例如：[--include-full-endpoint-url](/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-show) 参数将用于 Azure [CLI](/cli/azure)。

若要详细了解如何将事件传递到 Webhook，请参阅 [Webhook 事件传递](webhook-event-delivery.md)

> [!IMPORTANT]
Azure 事件网格只支持 HTTPS Webhook 终结点。 

## <a name="endpoint-validation-with-cloudevents-v10"></a>使用 CloudEvents v1.0 验证终结点
如果已熟悉事件网格，可能会注意到终结点验证握手以防止滥用。 CloudEvents v1.0 使用 **HTTP OPTIONS** 方法实现其自己的 [滥用保护语义](webhook-event-delivery.md)。 若要了解有关详细信息，请参阅 [用于事件传递的 HTTP 1.1 Web 挂钩-版本 1.0](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)。 当你使用 CloudEvents 架构进行输出时，事件网格将使用 CloudEvents 1.0 滥用防护来代替事件网格验证事件机制。 有关详细信息，请参阅 [将 CloudEvents v1.0 schema 与事件网格结合使用](cloudevents-schema.md)。 


## <a name="next-steps"></a>后续步骤
请参阅[对发布客户端进行身份验证](security-authenticate-publishing-clients.md)，了解如何对将事件发布到主题或域的客户端进行身份验证。 
