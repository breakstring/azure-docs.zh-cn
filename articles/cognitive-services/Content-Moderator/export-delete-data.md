---
title: 导出或删除用数据 - 内容审查器
titleSuffix: Azure Cognitive Services
description: 你对数据具有完全控制。 了解如何在内容审查器中查看、导出或删除数据。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 9f74fdc9cd30e1dfbd4df6c94842a9dccb435ef4
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913647"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>在内容审查器中导出或删除用数据

内容审查器收集用户数据来使服务运转，但客户对使用[评审工具](https://contentmoderator.cognitive.microsoft.com/)和[审核与评审 API](./api-reference.md) 来查看、导出及删除其数据拥有完全控制权限。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

有关如何在内容审查器中导出和删除用户数据的详细信息，请参阅下表。

| 数据 | 导出操作 | 删除操作 |
| ---- | ---------------- | ---------------- |
| 帐户信息（订阅密钥） | 不适用 | 使用 Azure 门户（Azure 订阅）进行删除。 或者使用  按钮。 |
| 用于自定义匹配的图像 | 调用[获取图像 ID API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676)。 映像以单向专有哈希格式存储，并且无法提取实际映像。 | 调用[删除所有图像 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686)。 或者使用 Azure 门户删除内容审查器资源。 |
| 用于自定义匹配的术语 | 调用[获取所有词条 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e) | 调用[删除所有词条 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d)。 或者使用 Azure 门户删除内容审查器资源。 |
| Tags | 不适用 | 使用评审 UI 中“标记设置”页面上可用于每个标记的“删除”图标  或者使用  按钮。 |
| 审阅 | 调用[获取评审 API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | 使用  按钮。
| 用户 | 不适用 | 使用  。 或者使用  按钮。 |