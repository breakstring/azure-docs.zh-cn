---
title: 'Azure 事件网格 (的合作伙伴载入概述) '
description: 概述了如何作为事件网格伙伴进行加入。
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 2a2e33395cabd368d5d5d870dd0461e4cbd37e0d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081189"
---
# <a name="partner-onboarding-overview-azure-event-grid"></a>Azure 事件网格 (的合作伙伴载入概述) 

本文介绍如何私下使用 Azure 事件网格合作伙伴资源以及如何成为公开提供的合作伙伴主题类型。

你不需要特殊权限即可作为事件网格伙伴开始使用与发布事件关联的事件网格资源类型。 事实上，你可以立即使用它们将事件发布到你自己的 Azure 订阅，并在考虑成为合作伙伴时测试资源模型。

> [!NOTE]
> 有关如何使用 Azure 门户加入事件网格伙伴的分步说明，请参阅 Azure 门户) 中的 " [作为事件网格伙伴集成" (](onboard-partner.md)。 

## <a name="how-partner-events-work"></a>合作伙伴事件的工作原理
合作伙伴事件功能采用事件网格已经用于从 Azure 资源（例如 Azure 存储和 Azure IoT 中心）发布事件的现有体系结构，并使这些工具公开供任何人使用。 默认情况下，使用这些工具仅专用于你的 Azure 订阅。 若要公开事件，请填写表单，并 [联系事件网格团队](mailto:gridpartner@microsoft.com)。

使用合作伙伴活动功能，可以将事件发布到 Azure 事件网格，以实现多租户消耗。

## <a name="onboarding-and-event-publishing-overview"></a>加入和事件发布概述

### <a name="partner-flow"></a>合作伙伴流

1. 创建 Azure 租户（如果还没有租户）。
1. 使用 Azure CLI 创建新的事件网格 `partnerRegistration` 。 此资源包括显示名称、说明、安装 URI 等信息。

    ![创建合作伙伴主题](./media/partner-onboarding-how-to/create-partner-registration.png)

1. 在要发布事件的每个区域中创建一个或多个伙伴命名空间。 事件网格服务设置发布终结点 (例如 `https://contoso.westus-1.eventgrid.azure.net/api/events`) 和访问密钥。

    ![创建合作伙伴命名空间](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. 为客户提供一种在系统中注册他们需要合作伙伴的方法。
1. 请与事件网格团队联系，让他们知道你希望你的合作伙伴主题类型成为公共的。

### <a name="customer-flow"></a>客户流

1. 你的客户访问 Azure 门户以记下所需的 Azure 订阅 ID 和资源组。
1. 客户通过您的系统请求合作伙伴主题。 在响应中，你将创建一个到你的合作伙伴命名空间的事件隧道。
1. 事件网格在客户的 Azure 订阅和资源组中创建一个 **待定** 合作伙伴主题。

    ![创建事件通道](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. 客户通过 Azure 门户激活合作伙伴主题。 事件现在可能会从你的服务流向客户的 Azure 订阅。

    ![激活合作伙伴主题](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>资源模型
以下资源模型用于合作伙伴事件。

### <a name="partner-registrations"></a>合作伙伴注册
* 资源：`partnerRegistrations`
* 使用者：合作伙伴
* 说明：将软件的全局元数据捕获 (SaaS) 伙伴 (例如，名称、显示名称、说明、安装 URI) 。
    
    创建或更新合作伙伴注册是适用于合作伙伴的自助服务操作。 此自助服务功能使合作伙伴能够构建并测试完整的端到端流程。
    
    客户可发现只有 Microsoft 批准的合作伙伴注册。
* 作用域：在合作伙伴的 Azure 订阅中创建。 在客户公开后，元数据对客户可见。

### <a name="partner-namespaces"></a>合作伙伴命名空间
* 资源：`partnerNamespaces`
* 使用者：合作伙伴
* 说明:提供用于将客户事件发布到的区域资源。 每个伙伴命名空间都有一个发布终结点和一个身份验证密钥。 命名空间也是合作伙伴请求给定客户的合作伙伴主题以及列出活动客户的方式。
* 范围：位于合作伙伴的订阅中。

### <a name="event-channel"></a>事件通道
* 资源：`partnerNamespaces/eventChannels`
* 使用者：合作伙伴
* 说明：事件通道是客户合作伙伴主题的镜像。 通过创建事件通道并在元数据中指定客户的 Azure 订阅和资源组，您可以向事件网格发出信号，为客户创建合作伙伴主题。 事件网格发出 Azure 资源管理器调用，以便在客户的订阅中创建相应的合作伙伴主题。 创建的合作伙伴主题处于挂起状态。 每个事件通道和合作伙伴主题之间都有一对一的链接。
* 范围：位于合作伙伴的订阅中。

### <a name="partner-topics"></a>合作伙伴主题
* 资源：`partnerTopics`
* 使用者：客户
* 说明：合作伙伴主题类似于事件网格中的自定义主题和系统主题。 每个合作伙伴主题都与特定的源 (相关联，例如 `Contoso:myaccount`) 和特定的合作伙伴主题类型 (例如 Contoso) 。 客户可在合作伙伴主题上创建事件订阅，以将事件路由到各种事件处理程序。

    客户无法直接创建此资源。 创建合作伙伴主题的唯一方法是通过创建事件通道的合作伙伴操作完成。
* 范围：在客户订阅中。

### <a name="partner-topic-types"></a>合作伙伴主题类型
* 资源：`partnerTopicTypes`
* 使用者：客户
* 说明：合作伙伴主题类型是租户范围的资源类型，使客户能够发现已批准的合作伙伴主题类型的列表。 URL 如下所示 https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes)
* 范围：Global

## <a name="publish-events-to-event-grid"></a>将事件发布到事件网格
当你在 Azure 区域中创建合作伙伴命名空间时，你将获得一个区域终结点和相应的身份验证密钥。 对于该命名空间中的所有客户事件通道，将事件批次发布到此终结点。 Azure 事件网格基于事件中的源字段映射每个事件和相应的合作伙伴主题。

### <a name="event-schema-cloudevents-v10"></a>事件架构： CloudEvents v1。0
使用 CloudEvents 1.0 架构将事件发布到 Azure 事件网格。 事件网格支持结构化模式和批处理模式。 CloudEvents 1.0 是合作伙伴命名空间的唯一受支持的事件架构。

### <a name="example-flow"></a>示例流程

1.  发布服务对 `https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` 执行 HTTP POST。
1.  在请求中包含一个名为 aeg-sas-key 的标头值，其中包含身份验证密钥。 此密钥是在创建合作伙伴命名空间的过程中预配的。 例如，有效的标头值为 aeg-sas-key：VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==.
1.  将 Content-type 标头设置为 "application/cloudevents + json"。字符集 = 8a "。
1.  使用与该区域相对应的批事件，对发布 URL 运行 HTTP POST 查询。 例如：

``` json
[
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketcreated",
    "source" : " com.contoso.account1",
    "subject" : "tickets/123",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
},
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketclosed",
    "source" : "https://contoso.com/account2",
    "subject" : "tickets/456",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
}
]
```

发布到合作伙伴命名空间终结点后，会收到响应。 响应是标准 HTTP 响应代码。 一些常见的响应如下所示：

| 结果                             | 响应              |
|------------------------------------|-----------------------|
| Success                            | 200 正常                |
| 事件数据的格式不正确    | 400 错误请求       |
| 访问密钥无效                 | 401 未授权      |
| 终结点不正确                 | 404 未找到         |
| 数组或事件超出大小限制 | 413 有效负载太大 |

## <a name="references"></a>参考

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [ARM 模板](/azure/templates/microsoft.eventgrid/allversions)
  * [ARM 模板架构](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [REST API](/rest/api/eventgrid/version2020-04-01-preview/partnernamespaces)
  * [CLI 扩展](/cli/azure/ext/eventgrid/)

### <a name="sdks"></a>SDK
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [JS](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Go](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>后续步骤
- [合作伙伴主题概述](partner-events-overview.md)
- [合作伙伴主题载入窗体](https://aka.ms/gridpartnerform)
- [Auth0 合作伙伴主题](auth0-overview.md)
- [如何使用 Auth0 合作伙伴主题](auth0-how-to.md)
