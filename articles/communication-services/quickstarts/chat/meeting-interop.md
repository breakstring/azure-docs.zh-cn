---
title: Azure 通信服务的 Teams 互操作入门
titleSuffix: An Azure Communication Services quickstart
description: 在本快速入门中，你将了解如何通过 Azure 通信聊天客户端库加入 Teams 会议
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 59e259df5d9e564e11dbebd416569727c3857632
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932307"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>快速入门：将你的聊天应用连接到 Teams 会议

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

通过使用 JavaScript 客户端库将聊天解决方案连接到 Microsoft Teams，开始使用 Azure 通信服务。 

## <a name="prerequisites"></a>先决条件 

1.  [部署 Teams](/deployoffice/teams-install)。 
2. [聊天应用](./get-started.md)正常运行。 

## <a name="enable-teams-interoperability"></a>启用 Teams 互操作性 

以来宾用户身份加入 Teams 会议的通信服务用户仅在加入 Teams 会议通话后才能访问会议的聊天。 请参阅 [Teams 互操作](../voice-video-calling/get-started-teams-interop.md)文档，了解如何将通信服务用户添加到 Teams 会议通话。

你需要是拥有这两个实体的组织的成员才能使用此功能。

[!INCLUDE [Join Teams meetings](./includes/meeting-interop-javascript.md)]

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除通信服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 了解有关[清理资源](../create-communication-resource.md#clean-up-resources)的详细信息。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

- 查看我们的[出色聊天示例](../../samples/chat-hero-sample.md)
- 详细了解[聊天的工作原理](../../concepts/chat/concepts.md)