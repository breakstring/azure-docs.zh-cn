---
title: 云合作伙伴门户 API 参考-Microsoft 商用 marketplace
description: 市场 API 操作的说明、要使用的先决条件和列表。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 57d3d1c6bb14db3eb2ca499069934a628d2f7fea
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425771"
---
# <a name="cloud-partner-portal-api-reference"></a>云合作伙伴门户 API 参考

> [!NOTE]
> 云合作伙伴门户 Api 与集成，并将在合作伙伴中心继续工作。 转换引入了少量更改。 请查看本文档中列出的对 [CPP api 所做的更改](#changes-to-cpp-apis-after-the-migration-to-partner-center) ，以确保你的代码在转换到合作伙伴中心后继续工作。 CPP Api 仅适用于过渡到合作伙伴中心之前已集成的现有产品;新产品应使用合作伙伴中心提交 Api。

云合作伙伴门户 REST API允许以编程方式检索和操作工作负载、产品/服务和发布者配置文件。 Api 使用 azure 基于角色的访问控制 (Azure RBAC) 在处理时间强制执行正确的权限。

本参考提供云合作伙伴门户 REST API 的技术详细信息。 本文档中的有效负载示例仅供参考，随着新功能的添加可能会发生变化。

## <a name="prerequisites-and-considerations"></a>先决条件和注意事项

在使用 API 之前，应该查看：

- [先决条件](./cloud-partner-portal-api-prerequisites.md)一文，了解如何向帐户添加服务主体，并获取 Azure Active Directory (Azure AD) 访问令牌以进行身份验证。
- 两种 [并发控制](./cloud-partner-portal-api-concurrency-control.md) 策略可用于调用这些 api。
- 其他 API [注意事项](./cloud-partner-portal-api-considerations.md)，如版本控制和错误处理。

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>迁移到合作伙伴中心后对 CPP Api 的更改

| **API** | **更改描述** | **影响** |
| ------- | ---------------------- | ---------- |
| POST 发布、上线、取消 | 对于已迁移的产品/服务，响应标头将采用不同的格式，但将以相同的方式继续工作，这表示有一个相对路径用于检索操作状态。 | 在发送某产品/服务的任何相应 POST 请求时，位置标头将采用以下两种格式之一，具体取决于产品/服务的迁移状态：<ul><li>未迁移的产品/服务<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>已迁移的产品/服务<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| GET 操作 | 对于先前在响应中受支持的 "通知电子邮件" 字段的产品/服务类型，此字段将被弃用，并且不再为已迁移的产品/服务返回。 | 对于已迁移的产品/服务，我们将不再向请求中指定的一列电子邮件地址发送通知。 相反，API 服务将与 合作伙伴中心内的通知电子邮件进程保持一致来发送电子邮件。 具体而言，通知将发送至合作伙伴中心内在你的帐户设置的卖家联系人信息部分中设定的电子邮件地址，告知你操作进度。<br><br>请在合作伙伴中心的 [帐户设置](https://partner.microsoft.com/dashboard/account/management) 的 "卖方联系人信息" 部分中查看电子邮件地址集，以确保为通知提供了正确的电子邮件。  |

## <a name="common-tasks"></a>常见任务

本参考详细介绍了用于执行以下常见任务的 API。

### <a name="offers"></a>产品

- [检索所有产品/服务](./cloud-partner-portal-api-retrieve-offers.md)
- [检索特定产品/服务](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [检索产品/服务状态](./cloud-partner-portal-api-retrieve-offer-status.md)
- [创建产品/服务](./cloud-partner-portal-api-creating-offer.md)
- [发布产品/服务](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Operations

- [检索操作](./cloud-partner-portal-api-retrieve-operations.md)
- [取消操作](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>发布应用

- [投入使用](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>其他任务

- [设置虚拟机套餐的定价](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>疑难解答

- [排查身份验证错误](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
