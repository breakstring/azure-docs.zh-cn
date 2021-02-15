---
title: 数据存储 - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS 将加密的数据存储在与密钥指定的区域对应的 Azure 数据存储中。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: a58bcff4e39c4a4a907cd8567b47b074ff299bd5
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008446"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>语言理解 (LUIS) 认知服务中的数据存储和删除

LUIS 存储在 Azure 数据存储中加密的数据，该数据存储对应于密钥指定 [的区域](luis-reference-regions.md) 。 

* 用于训练模型的数据（如实体、意向和最谈话）将在应用程序的生存期内保存在 LUIS 中。 如果所有者或参与者删除了该应用，则会将此数据随其一起删除。 如果应用程序在90天内未被使用，则会将其删除。 

* 应用程序作者可以选择在发送到已发布应用程序的最谈话上 [启用日志记录](luis-how-to-review-endpoint-utterances.md#log-user-queries-to-enable-active-learning) 。 如果启用，最谈话将保存30天，并可由应用程序作者查看。 如果在发布应用程序时未启用日志记录，则不存储此数据。

## <a name="export-and-delete-app"></a>导出和删除应用
用户对于[导出](luis-how-to-start-new-app.md#export-app)和[删除](luis-how-to-start-new-app.md#delete-app)应用拥有完全的控制权。 

## <a name="utterances"></a>陈述

话语可以存储在两个不同的位置。 

* 在 **创作过程** 期间，创建话语并将其存储在意向中。 成功的 LUIS 应用需要意向中的话语。 应用发布并在终结点接收查询后，终结点请求的查询字符串 `log=false` 将确定是否存储了终结点话语。 如果存储了终结点，它将成为可在门户的“生成”部分、“审核终结点话语”部分中找到的主动学习话语的一部分。 
* 当你 **审核终结点话语** 时，如果向意向添加一条话语，该话语将不再作为要审核的终结点话语的一部分存储。 它将添加到应用的意向中。 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>从意向中删除示例话语

删除用于训练 [LUIS](luis-reference-regions.md) 的示例陈述。 如果从 LUIS 应用中删除某个示例陈述，则会将其从 LUIS Web 服务中删除，导致其无法导出。

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>从主动学习中删除审核中的话语

可以从 LUIS 在[“查看终结点陈述”页](luis-how-to-review-endpoint-utterances.md)中建议的用户陈述列表中删除陈述。 从此列表中删除表述可以防止系统再将其作为建议提出来，但不会将其从日志中删除。

如果不想主动学习话语，则可以[禁用主动学习](luis-how-to-review-endpoint-utterances.md#disable-active-learning)。 禁用主动学习也会禁止记录。

### <a name="disable-logging-utterances"></a>禁止记录话语
[禁用主动学习](luis-how-to-review-endpoint-utterances.md#disable-active-learning)会禁止记录。


<a name="accounts"></a>

## <a name="delete-an-account"></a>删除帐户
如果你未迁移，则可以删除你的帐户，你的所有应用及其示例言语和日志都将被删除。 帐户和数据被永久删除之前，数据将保留 90 天。

从“设置”页可以删除帐户。 在右上角导航栏中选择帐户名可转到“设置”页。

## <a name="delete-an-authoring-resource"></a>删除创作资源
如果已[迁移到创作资源](./luis-migration-authoring.md)，则从 Azure 门户删除资源本身将删除与该资源关联的所有应用程序及其示例言语和日志。 数据将保留 90 天，然后会被永久删除。    

若要删除资源，请转到 [Azure 门户](https://ms.portal.azure.com/#home)，然后选择你的 LUIS 创作资源。 请访问“概述”选项卡，然后单击页面顶部的“删除”按钮。 然后确认资源已删除。 

## <a name="data-inactivity-as-an-expired-subscription"></a>数据处于非活动状态会被视为过期订阅
出于数据保留和删除的原因，Microsoft 有权自行将处于非活动状态的 LUIS 应用视为过期订阅。 如果应用在过去 90 天内满足以下条件，则将被视为处于非活动状态： 

* 未对应用发出任何调用。
* 未进行修改。
* 未分配有当前密钥。
* 无任何用户登录应用。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解导出和删除应用](luis-how-to-start-new-app.md)