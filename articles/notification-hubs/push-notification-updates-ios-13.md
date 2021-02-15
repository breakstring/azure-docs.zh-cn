---
title: Azure 通知中心 iOS 13 更新 | Microsoft Docs
description: 了解 Azure 通知中心的 iOS 13 中断性变更
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: df8560bec3671a9f05628ee6ed8ea95c31e9b16f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88998044"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>适用于 iOS 13 的 Azure 通知中心更新

Apple 最近对其公共推送服务做了一些更改；这些更改主要与 iOS 13 和 Xcode 版本相适应。 本文描述这些更改对 Azure 通知中心的影响。

## <a name="apns-push-payload-changes"></a>APNS 推送有效负载更改

### <a name="apns-push-type"></a>APNS 推送类型

Apple 现在要求开发人员通过 APNS API 中的新 `apns-push-type` 标头将通知标识为警报或后台通知。 [Apple 文档](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)中指出：“此标头的值必须准确反映通知有效负载的内容。 如果存在不匹配的情况，或者如果所需系统上缺少该标头，APNs 可能会返回错误，延迟通知的传送，或删除整个通知。”

现在，开发人员必须在通过 Azure 通知中心发送通知的应用程序中设置此标头。 由于技术限制，对于包含此属性的请求，客户必须将基于令牌的身份验证用于 APNS 凭据。 如果将基于证书的身份验证用于 APNS 凭据，则必须改用基于令牌的身份验证。

以下代码示例演示如何在通过 Azure 通知中心发送的通知请求中设置此标头属性。

#### <a name="template-notifications---net-sdk"></a>模板通知 - .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>本机通知 - .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

#### <a name="direct-rest-calls"></a>直接 REST 调用

```csharp
var request = new HttpRequestMessage(method, $"<resourceUri>?api-version=2017-04");
request.Headers.Add("Authorization", createToken(resourceUri, KEY_NAME, KEY_VALUE));
request.Headers.Add("ServiceBusNotification-Format", "apple");
request.Headers.Add("apns-push-type", "alert");
```

为了帮助你完成这种过渡，当 Azure 通知中心检测到未设置 `apns-push-type` 的通知时，服务将从通知请求推断推送类型，并自动设置值。 请记住，必须将 Azure 通知中心配置为使用基于令牌的身份验证以设置所需的标头；有关详细信息，请参阅 [APNS 的基于令牌的 (HTTP/2) 身份验证](./notification-hubs-push-notification-http2-token-authentication.md)。

## <a name="apns-priority"></a>APNS 优先级

另一个次要的，但需要对发送通知的后端应用程序进行更改的更改是，现在必须将后台通知的 `apns-priority` 标头设置为 5。 许多应用程序将 `apns-priority` 标头设置为 10（表示立即传送），或者不设置此标头并获取默认值（也是 10）。

对于后台通知，不再允许将此值设置为 10，必须为每个请求设置值。 如果缺少此值，Apple 将不会传送后台通知。 例如：

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>SDK 更改

多年以来，iOS 开发人员一直都是使用发送到推送令牌委托的 `deviceToken` 数据的 `description` 属性来提取推送令牌，而后端应用程序使用该令牌将通知发送到设备。 对于 Xcode 11，该 `description` 属性已更改为不同的格式。 开发人员对此属性使用的现有代码现已中断。 我们更新了 Azure 通知中心 SDK 以适应此项更改，因此，请将应用程序使用的 SDK 更新为 2.0.4 或更高版本的 [Azure 通知中心 iOS SDK](https://github.com/Azure/azure-notificationhubs-ios)。
