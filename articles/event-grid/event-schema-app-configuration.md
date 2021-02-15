---
title: 作为事件网格源 Azure 应用配置
description: 本文介绍如何使用 Azure 应用配置作为事件网格事件源。 其中提供了架构，以及教程和操作指南文章的链接。
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: a64c6fead5e6d95ba11bc98d7e9a52e3021c3be2
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366766"
---
# <a name="azure-app-configuration-as-an-event-grid-source"></a>将配置作为事件网格源 Azure 应用
本文提供 Azure 应用程序配置事件的属性和架构。 有关事件架构的简介，请参阅 [Azure 事件网格事件架构](event-schema.md)。 它还提供了一个快速入门和教程列表，以将 Azure 应用配置作为事件来源。

## <a name="available-event-types"></a>可用事件类型

Azure 应用程序配置会发出以下事件类型：

| 事件类型 | 说明 |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModified | 创建或替换键/值时引发。 |
| Microsoft.AppConfiguration.KeyValueDeleted | 删除键/值时引发。 |

## <a name="example-event"></a>示例事件

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)
以下示例显示键/值修改事件的架构： 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

键/值删除事件的架构与此类似： 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```
# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)

以下示例显示键/值修改事件的架构： 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "source": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "type": "Microsoft.AppConfiguration.KeyValueModified",
  "time": "2019-05-31T20:05:03Z",
  "specversion": "1.0"
}]
```

键/值删除事件的架构与此类似： 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "source": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "type": "Microsoft.AppConfiguration.KeyValueDeleted",
  "time": "2019-05-31T20:05:03Z",
  "specversion": "1.0"
}]
```

---

## <a name="event-properties"></a>事件属性

# <a name="event-grid-event-schema"></a>[事件网格事件架构](#tab/event-grid-event-schema)
事件具有以下顶级数据：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `topic` | 字符串 | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| `subject` | string | 事件主题的发布者定义路径。 |
| `eventType` | 字符串 | 此事件源的一个注册事件类型。 |
| `eventTime` | string | 基于提供程序 UTC 时间的事件生成时间。 |
| `id` | 字符串 | 事件的唯一标识符。 |
| `data` | 对象 (object) | 应用配置事件数据。 |
| `dataVersion` | string | 数据对象的架构版本。 发布者定义架构版本。 |
| `metadataVersion` | string | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |


# <a name="cloud-event-schema"></a>[云事件架构](#tab/cloud-event-schema)

事件具有以下顶级数据：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| `source` | string | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| `subject` | string | 事件主题的发布者定义路径。 |
| `type` | string | 此事件源的一个注册事件类型。 |
| `time` | 字符串 | 基于提供程序 UTC 时间的事件生成时间。 |
| `id` | 字符串 | 事件的唯一标识符。 |
| `data` | 对象 (object) | 应用配置事件数据。 |
| `specversion` | 字符串 | CloudEvents 架构规范版本。 |

---

数据对象具有以下属性：

| properties | 类型 | 说明 |
| -------- | ---- | ----------- |
| `key` | 字符串 | 已修改或已删除的键/值的键。 |
| `label` | 字符串 | 已修改或已删除的键/值的标签（如果有）。 |
| `etag` | 字符串 | 对于 `KeyValueModified`，为新键/值的 etag。 对于 `KeyValueDeleted`，为已删除的键/值的 etag。 |


## <a name="tutorials-and-how-tos"></a>教程和操作指南

|标题 | 说明 |
|---------|---------|
| [使用事件网格响应 Azure 应用配置事件](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 将 Azure 应用配置与事件网格集成的概述。 |
| [使用事件网格进行数据更改通知](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 了解如何使用 Azure 应用配置事件订阅将键值修改事件发送到 web 终结点。 |

## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
* 有关使用 Azure 应用配置事件的简介，请参阅 [将事件网格用于数据更改通知](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json)。 