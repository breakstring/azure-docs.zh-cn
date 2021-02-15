---
title: Azure 数据工厂实体的命名规则-版本1
description: 介绍数据工厂 v1 实体的命名规则。
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 83621a7ceeae32ea4b55e3f22fff61d50e8cdb60
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380162"
---
# <a name="rules-for-naming-azure-data-factory-entities"></a>Azure 数据工厂实体的命名规则
> [!NOTE]
> 本文适用于数据工厂版本 1。 如果使用当前版本的数据工厂服务，请参阅[数据工厂中的命名规则](../naming-rules.md)。

下表提供了数据工厂项目的命名规则。

| 名称 | 名称唯一性 | 验证检查 |
|:--- |:--- |:--- |
| Data Factory |在 Microsoft Azure 内唯一。 名称不区分大小写，即 `MyDF` 和 `mydf` 指的是同一个数据工厂。 |<ul><li>一个数据工厂绑定到一个 Azure 订阅。</li><li>对象名称必须以字母或数字开头，并且只能包含字母、数字和短划线 (-) 字符。</li><li>每个短划线 (-) 字符的前后必须紧接字母或数字。 容器名称中不允许使用连续短划线。</li><li>名称长度为 3-63 个字符。</li></ul> |
| 链接服务/表/管道 |在数据工厂中唯一。 名称不区分大小写。 |<ul><li>表名称的最大字符数：260。</li><li>对象名称必须以字母、数字或下划线 (_) 开头。</li><li>不允许使用以下字符： "."、"+"、"？"、"/"、"<"、">"、"*"、"%"、"&"、"："、" \\ "</li></ul> |
| 资源组 |在 Microsoft Azure 内唯一。 名称不区分大小写。 |<ul><li>最大字符数：1000。</li><li>名称可包含字母、数字和以下字符： "-"、"_"、"，" 和 "."</li></ul> |

