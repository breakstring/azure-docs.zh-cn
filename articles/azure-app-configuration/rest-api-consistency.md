---
title: Azure 应用配置 REST API-一致性
description: 使用 Azure 应用配置确保实时一致性的参考页面 REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: b324d23ce9abc1eb3893f316365aff828de2063d
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932619"
---
# <a name="real-time-consistency"></a>实时一致性

由于某些分布式系统的性质，很难隐式强制实施请求之间的实时一致性。 解决方案是以多个同步令牌的形式提供协议支持。 同步令牌是可选的。

## <a name="initial-request"></a>初始请求

若要保证不同客户端实例和请求之间的实时一致性，请使用可选的 `Sync-Token` 请求和响应标头。

语法：

```http
Sync-Token: <id>=<value>;sn=<sn>
```

|参数|描述|
|--|--|
| `<id>` | 令牌 ID（不透明） |
| `<value>` | 令牌值（不透明）。 允许 base64 编码的字符串。 |
| `<sn>` | 令牌序列号（版本）。 "高" 表示相同令牌的较新版本。 可提供更好的并发性和客户端缓存。 由于令牌版本是包含的，因此客户端可以选择仅使用令牌的最后一个版本。 请求不需要此参数。 |

## <a name="response"></a>响应

服务为每个响应提供一个 `Sync-Token` 标头。

```http
Sync-Token: jtqGc1I4=MDoyOA==;sn=28
```

## <a name="subsequent-requests"></a>后续请求

任何后续请求都获得与提供的 `Sync-Token` 相关的实时一致响应保证。

```http
Sync-Token: <id>=<value>
```

如果省略请求中的 `Sync-Token` 标头，则服务可能会在一小段时间内使用缓存数据进行响应 (多达几秒钟的时间，) 在内部进行。 如果在读取之前立即发生更改，则此行为可能导致不一致的读取。

## <a name="multiple-sync-tokens"></a>多个同步令牌

服务器可能会响应单个请求的多个同步令牌。 若要保持下一次请求的实时一致性，客户端必须与所有接收到的同步令牌进行响应。 多个标头值必须以逗号分隔。

```http
Sync-Token: <token1-id>=<value>,<token2-id>=<value>
```
