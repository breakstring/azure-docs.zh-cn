---
title: 作为事件网格源的 Azure 通信服务
description: 本文介绍如何使用 Azure 通信服务作为事件网格事件源。
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: mikben
ms.openlocfilehash: 862d5edeecf0be1de8026d21342ede2ef1851e8c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363587"
---
# <a name="azure-communication-services-as-an-event-grid-source"></a>作为事件网格源的 Azure 通信服务

> [!IMPORTANT]
> Azure 通信服务目前提供公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文提供 Azure 通信服务事件的属性和架构。 有关事件架构的简介，请参阅 [Azure 事件网格事件架构](event-schema.md)。

## <a name="available-event-types"></a>可用事件类型

事件网格使用[事件订阅](./concepts.md#event-subscriptions)将事件消息路由到订阅方。 

Azure 通信服务发出以下事件类型：

| 事件类型                                                  | 说明                                                                                    |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Microsoft.Communication.SMSReceived                         | 当通过与通信服务关联的电话号码收到短信时发布。 |
| Microsoft.Communication.SMSDeliveryReportReceived           | 当收到通信服务发送的短信的传递报告时发布。     |
| Microsoft.Communication.ChatMessageReceived*                | 在用户为其成员的聊天线程中收到用户的消息时发布。        |
| Microsoft.Communication.ChatMessageEdited*                   | 当在用户所属的聊天线程中编辑消息时发布。                |
| Microsoft.Communication.ChatMessageDeleted*                  | 当在用户所属的聊天线程中删除消息时发布。               |
| Microsoft.Communication.ChatThreadCreatedWithUser           | 当用户在聊天线程创建期间添加为成员时发布。           |
| Microsoft.Communication.ChatThreadWithUserDeleted           | 当删除用户所属的聊天线程时发布。                           |
| Microsoft.Communication.ChatThreadPropertiesUpdatedPerUser  | 当更新用户所属的聊天线程的属性时发布。              |
| Microsoft.Communication.ChatMemberAddedToThreadWithUser     | 当用户作为成员添加到聊天线程时发布。                                   |
| Microsoft.Communication.ChatMemberRemovedFromThreadWithUser | 当用户从聊天线程中删除时发布。                                         |


* 请确保在 "发送消息" API 调用中提供 "发送方名称"，以便触发这些事件。

## <a name="event-subjects"></a>事件主题

`subject`所有通信服务事件的字段都将标识事件所针对的用户、电话号码或实体。 公共前缀用于允许进行简单[事件网格筛选](./event-filtering.md)。

| 主题前缀                              | 通信服务实体 |
| ------------------------------------------- | ---------------------------- |
| `phonenumber/`                              | PSTN 电话号码            |
| `user/`                                     | 通信服务用户  |
| `thread/`                                   | 聊天线程。                 |

下面的示例演示一个筛选器，适用于发送到通信服务资源所拥有的所有 555 区号电话号码的所有短信和传递报告：

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Communication.SMSReceived",
    "Microsoft.Communication.SMSDeliveryReportReceived"
  ],
  "subjectBeginsWith": "phonenumber/1555",
}
```

## <a name="sample-event-responses"></a>示例事件响应

触发某个事件后，事件网格服务会将有关该事件的数据发送到订阅终结点。 本部分包含一个示例，介绍每个事件的数据外观。

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)

### <a name="microsoftcommunicationsmsdeliveryreportreceived-event"></a>Microsoft.Communication.SMSDeliveryReportReceived 事件

```json
[{
  "id": "Outgoing_202009180022138813a09b-0cbf-4304-9b03-1546683bb910",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/phonenumber/15555555555",
  "data": {
    "MessageId": "Outgoing_202009180022138813a09b-0cbf-4304-9b03-1546683bb910",
    "From": "15555555555",
    "To": "+15555555555",
    "DeliveryStatus": "Delivered",
    "DeliveryStatusDetails": "No error.",
    "ReceivedTimestamp": "2020-09-18T00:22:20.2855749Z",
    "DeliveryAttempts": [
      {
        "Timestamp": "2020-09-18T00:22:14.9315918Z",
        "SegmentsSucceeded": 1,
        "SegmentsFailed": 0
      }
    ]
  },
  "eventType": "Microsoft.Communication.SMSDeliveryReportReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:22:20Z"
}]
```
### <a name="microsoftcommunicationsmsreceived-event"></a>Microsoft.Communication.SMSReceived 事件

```json
[{
  "id": "Incoming_20200918002745d29ebbea-3341-4466-9690-0a03af35228e",
  "topic": "/subscriptions/50ad1522-5c2c-4d9a-a6c8-67c11ecb75b8/resourcegroups/acse2e/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/phonenumber/15555555555",
  "data": {
    "MessageId": "Incoming_20200918002745d29ebbea-3341-4466-9690-0a03af35228e",
    "From": "15555555555",
    "To": "15555555555",
    "Message": "Great to connect with ACS events ",
    "ReceivedTimestamp": "2020-09-18T00:27:45.32Z"
  },
  "eventType": "Microsoft.Communication.SMSReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:27:47Z"
}]
```

### <a name="microsoftcommunicationchatmessagereceived-event"></a>Microsoft.Communication.ChatMessageReceived 事件

```json
[{
  "id": "c13afb5f-d975-4296-a8ef-348c8fc496ee",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/{thread-id}/sender/{id-of-message-sender}/recipient/{id-of-message-recipient}",
  "data": {
    "messageBody": "Welcome to Azure Communication Services",
    "messageId": "1600389507167",
    "senderId": "8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e0d-e5aa-0e04-343a0d00037c",
    "senderDisplayName": "John",
    "composeTime": "2020-09-18T00:38:27.167Z",
    "type": "Text",
    "version": 1600389507167,
    "recipientId": "8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e1a-3090-6a0b-343a0d000409",
    "transactionId": "WGW1YmwRzkupk0UI0QA9ZA.1.1.1.1.1797783722.1.9",
    "threadId": "19:46df844a4c064bfaa2b3b30e385d1018@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatMessageReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:38:28.0946757Z"
}
]
```

### <a name="microsoftcommunicationchatmessageedited-event"></a>Microsoft.Communication.ChatMessageEdited 事件

```json
[{
  "id": "18247662-e94a-40cc-8d2f-f7357365309e",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/sender/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "editTime": "2020-09-18T00:48:47.361Z",
    "messageBody": "Let's Chat about new communication services.",
    "messageId": "1600390097873",
    "senderId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe",
    "senderDisplayName": "Bob(Admin)",
    "composeTime": "2020-09-18T00:48:17.873Z",
    "type": "Text",
    "version": 1600390127361,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "bbopOa1JZEW5NDDFLgH1ZQ.2.1.2.1.1822032097.1.5",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatMessageEdited",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:48:48.037823Z"
}]
```

### <a name="microsoftcommunicationchatmessagedeleted-event"></a>Microsoft.Communication.ChatMessageDeleted 事件
```json
[{
  "id": "08034616-cf11-4fc2-b402-88963b93d083",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/sender/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "deleteTime": "2020-09-18T00:48:47.361Z",
    "messageId": "1600390099195",
    "senderId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe",
    "senderDisplayName": "Bob(Admin)",
    "composeTime": "2020-09-18T00:48:19.195Z",
    "type": "Text",
    "version": 1600390152154,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "mAxUjeTsG06NpObXkFcjVQ.1.1.2.1.1823015063.1.5",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatMessageDeleted",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:49:12.6698791Z"
}]
```

### <a name="microsoftcommunicationchatthreadcreatedwithuser-event"></a>Microsoft.Communication.ChatThreadCreatedWithUser 事件 

```json
[{
  "id": "06c7c381-bb0a-4fff-aedd-919df1d52137",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:7bdf5504a23f41a79d1bd472dd40044a@thread.v2/createdBy/8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "createdBy": "8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_06014f-6001fc107f",
    "properties": {
      "topic": "Chat about new commuication services",
    },
    "members": [
      {
        "displayName": "Bob",
        "memberId": "8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003f0"
      },
      {
        "displayName": "John",
        "memberId": "8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003f1"
      }
    ],
    "createTime": "2020-09-17T22:06:09.988Z",
    "version": 1600380369988,
    "recipientId": "8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "9ZxrGXVXCkOTygd5iwsvAQ.1.1.1.1.1440874720.1.1",
    "threadId": "19:7bdf5504a23f41a79d1bd472dd40044a@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatThreadCreatedWithUser",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-17T22:06:10.3235137Z"
}]
```

### <a name="microsoftcommunicationchatthreadwithuserdeleted-event"></a>Microsoft.Communication.ChatThreadWithUserDeleted 事件

```json
[{
  "id": "7f4fa31b-e95e-428b-a6e8-53e2553620ad",
  "topic":"/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/deletedBy/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "deletedBy": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe",
    "deleteTime": "2020-09-18T00:49:26.3694459Z",
    "createTime": "2020-09-18T00:46:41.559Z",
    "version": 1600390071625,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "MoZlSM2j7kSD2b5X8bjH7Q.1.1.2.1.1823539230.1.1",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatThreadWithUserDeleted",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:49:26.4269056Z"
}]
```

### <a name="microsoftcommunicationchatthreadpropertiesupdatedperuser-event"></a>Microsoft.Communication.ChatThreadPropertiesUpdatedPerUser 事件 

```json
[{
  "id": "47a66834-57d7-4f77-9c7d-676d45524982",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:a33a128babf04431b7fe8cbca82f4238@thread.v2/editedBy/8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e88-2b7f-ac00-343a0d0005a8/recipient/8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e88-15fa-ac00-343a0d0005a7",
  "data": {
    "editedBy": "8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e88-2b7f-ac00-343a0d0005a8",
    "editTime": "2020-09-18T00:40:38.4914428Z",
    "properties": {
      "topic": "Communication in Azure"
    },
    "createTime": "2020-09-18T00:39:02.541Z",
    "version": 1600389638481,
    "recipientId": "8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e88-15fa-ac00-343a0d0005a7",
    "transactionId": "+ah9tVwqNkCT6nUGCKIvAg.1.1.1.1.1802895561.1.1",
    "threadId": "19:a33a128babf04431b7fe8cbca82f4238@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatThreadPropertiesUpdatedPerUser",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:40:38.5804349Z"
}]
```

### <a name="microsoftcommunicationchatmemberaddedtothreadwithuser-event"></a>Microsoft.Communication.ChatMemberAddedToThreadWithUser 事件

```json
[{
  "id": "4abd2b49-d1a9-4fcc-9cd7-170fa5d96443",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/memberAdded/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "time": "2020-09-18T00:47:13.1867087Z",
    "addedBy": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f1",
    "memberAdded": {
      "displayName": "John Smith",
      "memberId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe"
    },
    "createTime": "2020-09-18T00:46:41.559Z",
    "version": 1600390033176,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "pVIjw/pHEEKUOUJ2DAAl5A.1.1.1.1.1818361951.1.1",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatMemberAddedToThreadWithUser",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:47:13.2342692Z"
}]
```

### <a name="microsoftcommunicationchatmemberremovedfromthreadwithuser-event"></a>Microsoft.Communication.ChatMemberRemovedFromThreadWithUser 事件

```json
[{
  "id": "b3701976-1ea2-4d66-be68-4ec4fc1b4b96",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/memberRemoved/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "time": "2020-09-18T00:47:51.1461742Z",
    "removedBy": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f1",
    "memberRemoved": {
      "displayName": "John",
      "memberId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe"
    },
    "createTime": "2020-09-18T00:46:41.559Z",
    "version": 1600390071131,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "G9Y+UbjVmEuxAG3O4bEyvw.1.1.1.1.1819803816.1.1",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatMemberRemovedFromThreadWithUser",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:47:51.2244511Z"
}]
```

# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)

### <a name="microsoftcommunicationsmsdeliveryreportreceived-event"></a>Microsoft.Communication.SMSDeliveryReportReceived 事件

```json
[{
  "id": "Outgoing_202009180022138813a09b-0cbf-4304-9b03-1546683bb910",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/phonenumber/15555555555",
  "data": {
    "MessageId": "Outgoing_202009180022138813a09b-0cbf-4304-9b03-1546683bb910",
    "From": "15555555555",
    "To": "+15555555555",
    "DeliveryStatus": "Delivered",
    "DeliveryStatusDetails": "No error.",
    "ReceivedTimestamp": "2020-09-18T00:22:20.2855749Z",
    "DeliveryAttempts": [
      {
        "Timestamp": "2020-09-18T00:22:14.9315918Z",
        "SegmentsSucceeded": 1,
        "SegmentsFailed": 0
      }
    ]
  },
  "type": "Microsoft.Communication.SMSDeliveryReportReceived",
  "time": "2020-09-18T00:22:20Z",
  "specversion": "1.0"
}]
```
### <a name="microsoftcommunicationsmsreceived-event"></a>Microsoft.Communication.SMSReceived 事件

```json
[{
  "id": "Incoming_20200918002745d29ebbea-3341-4466-9690-0a03af35228e",
  "source": "/subscriptions/50ad1522-5c2c-4d9a-a6c8-67c11ecb75b8/resourcegroups/acse2e/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/phonenumber/15555555555",
  "data": {
    "MessageId": "Incoming_20200918002745d29ebbea-3341-4466-9690-0a03af35228e",
    "From": "15555555555",
    "To": "15555555555",
    "Message": "Great to connect with ACS events ",
    "ReceivedTimestamp": "2020-09-18T00:27:45.32Z"
  },
  "type": "Microsoft.Communication.SMSReceived",
  "time": "2020-09-18T00:27:47Z",
  "specversion": "1.0"
}]
```

### <a name="microsoftcommunicationchatmessagereceived-event"></a>Microsoft.Communication.ChatMessageReceived 事件

```json
[{
  "id": "c13afb5f-d975-4296-a8ef-348c8fc496ee",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/{thread-id}/sender/{id-of-message-sender}/recipient/{id-of-message-recipient}",
  "data": {
    "messageBody": "Welcome to Azure Communication Services",
    "messageId": "1600389507167",
    "senderId": "8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e0d-e5aa-0e04-343a0d00037c",
    "senderDisplayName": "John",
    "composeTime": "2020-09-18T00:38:27.167Z",
    "type": "Text",
    "version": 1600389507167,
    "recipientId": "8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e1a-3090-6a0b-343a0d000409",
    "transactionId": "WGW1YmwRzkupk0UI0QA9ZA.1.1.1.1.1797783722.1.9",
    "threadId": "19:46df844a4c064bfaa2b3b30e385d1018@thread.v2"
  },
  "type": "Microsoft.Communication.ChatMessageReceived",
  "time": "2020-09-18T00:38:28.0946757Z",
  "specversion": "1.0"
}
]
```

### <a name="microsoftcommunicationchatmessageedited-event"></a>Microsoft.Communication.ChatMessageEdited 事件

```json
[{
  "id": "18247662-e94a-40cc-8d2f-f7357365309e",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/sender/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "editTime": "2020-09-18T00:48:47.361Z",
    "messageBody": "Let's Chat about new communication services.",
    "messageId": "1600390097873",
    "senderId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe",
    "senderDisplayName": "Bob(Admin)",
    "composeTime": "2020-09-18T00:48:17.873Z",
    "type": "Text",
    "version": 1600390127361,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "bbopOa1JZEW5NDDFLgH1ZQ.2.1.2.1.1822032097.1.5",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "type": "Microsoft.Communication.ChatMessageEdited",
  "time": "2020-09-18T00:48:48.037823Z",
  "specversion": "1"
}]
```

### <a name="microsoftcommunicationchatmessagedeleted-event"></a>Microsoft.Communication.ChatMessageDeleted 事件
```json
[{
  "id": "08034616-cf11-4fc2-b402-88963b93d083",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/sender/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "deleteTime": "2020-09-18T00:48:47.361Z",
    "messageId": "1600390099195",
    "senderId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe",
    "senderDisplayName": "Bob(Admin)",
    "composeTime": "2020-09-18T00:48:19.195Z",
    "type": "Text",
    "version": 1600390152154,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "mAxUjeTsG06NpObXkFcjVQ.1.1.2.1.1823015063.1.5",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "type": "Microsoft.Communication.ChatMessageDeleted",
  "time": "2020-09-18T00:49:12.6698791Z",
  "specversion": "1.0"
}]
```

### <a name="microsoftcommunicationchatthreadcreatedwithuser-event"></a>Microsoft.Communication.ChatThreadCreatedWithUser 事件 

```json
[{
  "id": "06c7c381-bb0a-4fff-aedd-919df1d52137",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:7bdf5504a23f41a79d1bd472dd40044a@thread.v2/createdBy/8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "createdBy": "8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_06014f-6001fc107f",
    "properties": {
      "topic": "Chat about new communication services"
    },
    "members": [
      {
        "displayName": "Bob",
        "memberId": "8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003f0"
      },
      {
        "displayName": "John",
        "memberId": "8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003f1"
      }
    ],
    "createTime": "2020-09-17T22:06:09.988Z",
    "version": 1600380369988,
    "recipientId": "8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "9ZxrGXVXCkOTygd5iwsvAQ.1.1.1.1.1440874720.1.1",
    "threadId": "19:7bdf5504a23f41a79d1bd472dd40044a@thread.v2"
  },
  "type": "Microsoft.Communication.ChatThreadCreatedWithUser",
  "time": "2020-09-17T22:06:10.3235137Z",
  "specversion": "1.0"
}]
```

### <a name="microsoftcommunicationchatthreadwithuserdeleted-event"></a>Microsoft.Communication.ChatThreadWithUserDeleted 事件

```json
[{
  "id": "7f4fa31b-e95e-428b-a6e8-53e2553620ad",
  "source":"/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/deletedBy/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "deletedBy": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe",
    "deleteTime": "2020-09-18T00:49:26.3694459Z",
    "createTime": "2020-09-18T00:46:41.559Z",
    "version": 1600390071625,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "MoZlSM2j7kSD2b5X8bjH7Q.1.1.2.1.1823539230.1.1",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "type": "Microsoft.Communication.ChatThreadWithUserDeleted",
  "time": "2020-09-18T00:49:26.4269056Z",
  "specversion": "1.0"
}]
```

### <a name="microsoftcommunicationchatthreadpropertiesupdatedperuser-event"></a>Microsoft.Communication.ChatThreadPropertiesUpdatedPerUser 事件 

```json
[{
  "id": "47a66834-57d7-4f77-9c7d-676d45524982",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:a33a128babf04431b7fe8cbca82f4238@thread.v2/editedBy/8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e88-2b7f-ac00-343a0d0005a8/recipient/8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e88-15fa-ac00-343a0d0005a7",
  "data": {
    "editedBy": "8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e88-2b7f-ac00-343a0d0005a8",
    "editTime": "2020-09-18T00:40:38.4914428Z",
    "properties": {
      "topic": "Communication in Azure"
    },
    "createTime": "2020-09-18T00:39:02.541Z",
    "version": 1600389638481,
    "recipientId": "8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e88-15fa-ac00-343a0d0005a7",
    "transactionId": "+ah9tVwqNkCT6nUGCKIvAg.1.1.1.1.1802895561.1.1",
    "threadId": "19:a33a128babf04431b7fe8cbca82f4238@thread.v2"
  },
  "type": "Microsoft.Communication.ChatThreadPropertiesUpdatedPerUser",
  "time": "2020-09-18T00:40:38.5804349Z",
  "specversion": "1.0"
}]
```

### <a name="microsoftcommunicationchatmemberaddedtothreadwithuser-event"></a>Microsoft.Communication.ChatMemberAddedToThreadWithUser 事件

```json
[{
  "id": "4abd2b49-d1a9-4fcc-9cd7-170fa5d96443",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/memberAdded/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "time": "2020-09-18T00:47:13.1867087Z",
    "addedBy": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f1",
    "memberAdded": {
      "displayName": "John Smith",
      "memberId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe"
    },
    "createTime": "2020-09-18T00:46:41.559Z",
    "version": 1600390033176,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "pVIjw/pHEEKUOUJ2DAAl5A.1.1.1.1.1818361951.1.1",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "type": "Microsoft.Communication.ChatMemberAddedToThreadWithUser",
  "time": "2020-09-18T00:47:13.2342692Z",
  "specversion": "1.0"
}]
```

### <a name="microsoftcommunicationchatmemberremovedfromthreadwithuser-event"></a>Microsoft.Communication.ChatMemberRemovedFromThreadWithUser 事件

```json
[{
  "id": "b3701976-1ea2-4d66-be68-4ec4fc1b4b96",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/memberRemoved/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "time": "2020-09-18T00:47:51.1461742Z",
    "removedBy": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f1",
    "memberRemoved": {
      "displayName": "John",
      "memberId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe"
    },
    "createTime": "2020-09-18T00:46:41.559Z",
    "version": 1600390071131,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "G9Y+UbjVmEuxAG3O4bEyvw.1.1.1.1.1819803816.1.1",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "type": "Microsoft.Communication.ChatMemberRemovedFromThreadWithUser",
  "time": "2020-09-18T00:47:51.2244511Z",
  "specversion": "1.0"
}]
```

---

## <a name="tutorials"></a>教程

| 标题 | 说明 |
|---------|---------|
| [快速入门：处理短信事件](../communication-services/quickstarts/telephony-sms/handle-sms-events.md) | 演示如何使用事件网格订阅 SMS 事件。   |

## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](./overview.md)
* 有关 Azure 事件网格概念的简介，请参阅[事件网格中的概念？](./concepts.md)
* 有关 Azure 事件网格系统主题的简介，请参阅 [Azure 事件网格中的系统主题？](./system-topics.md)