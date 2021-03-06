---
title: 使用 Azure 逻辑应用通过事件网格处理服务总线事件
description: 本文提供了使用 Azure 逻辑应用通过事件网格处理服务总线事件的步骤。
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 10/16/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: 93375f6047fbe4eda2132e024dab0e067e83ccf1
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95998963"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-logic-apps"></a>教程：使用 Azure 逻辑应用响应通过 Azure 事件网格收到的 Azure 服务总线事件
本教程介绍如何使用 Azure 逻辑应用对通过 Azure 事件网格收到的 Azure 服务总线事件做出响应。 

[!INCLUDE [service-bus-event-grid-prerequisites](../../includes/service-bus-event-grid-prerequisites.md)]

## <a name="receive-messages-by-using-logic-apps"></a>使用逻辑应用接收消息
在此步骤中，将创建一个通过 Azure 事件网格接收服务总线事件的 Azure 逻辑应用。 

1. 在 Azure 门户中创建逻辑应用。
    1. 依次选择“+ 创建资源”、“集成”、“逻辑应用”。 
    2. 在“逻辑应用 - 创建”页上，输入逻辑应用的 **名称**。
    3. 选择 **Azure 订阅**。 
    4. 为“资源组”选择“使用现有项”，然后选择以前创建的、用于其他资源（例如 Azure 函数、服务总线命名空间）的资源组。 
    5. 选择逻辑应用的 **位置**。 
    6. 选择“查看 + 创建”  。 
    1. 在“查看 + 创建”页面中，选择“创建”，以创建逻辑应用 。 
1. 在“逻辑应用设计器”页上，选择“模板”下的“空白逻辑应用”。 
1. 在设计器中执行以下步骤：
    1. 搜索“事件网格”。 
    2. 选择“发生资源事件时 - Azure 事件网格”。 

        ![逻辑应用设计器 - 选择事件网格触发器](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. 选择“登录”，输入 Azure 凭据，然后选择“允许访问”。 
5. 在“当资源事件发生时”页上执行以下步骤：
    1. 选择 Azure 订阅。 
    2. 对于“资源类型”，请选择“Microsoft.ServiceBus.Namespaces”。 
    3. 对于“资源名称”，请选择你的服务总线命名空间。 
    4. 选择“添加新参数”，然后选择“后缀筛选器”。 
    5. 对于“后缀筛选器”，请输入第二个服务总线主题订阅的名称。 
        ![逻辑应用设计器 - 配置事件](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. 在设计器中选择“+ 新建步骤”，然后执行以下步骤：
    1. 搜索“服务总线”。
    2. 在列表中选择“服务总线”。 
    3. 在“操作”列表中选择“获取消息”。 
    4. 选择“从主题订阅中获取消息(扫视锁定)”。 

        ![逻辑应用设计器 - 获取消息操作](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. 输入 **连接的名称**。 例如：**从主题订阅中获取消息**，并选择服务总线命名空间。 

        ![逻辑应用设计器 - 选择服务总线命名空间](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. 选择“RootManageSharedAccessKey”，然后选择“创建” 。

        ![逻辑应用设计器 - 选择共享访问密钥](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    8. 选择你的主题和订阅 。 
    
        ![显示选择主题和订阅的位置的屏幕截图。](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. 选择“+ 新建步骤”，然后执行以下步骤： 
    1. 选择“服务总线”。
    2. 在操作列表中选择“完成主题订阅中的消息”。 
    3. 选择你的服务总线 **主题**。
    4. 选择主题的第二个 **订阅**。
    5. 对于“消息的锁定标记”，请从“动态内容”中选择“锁定标记”。 

        ![逻辑应用设计器 - 完成消息](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. 在逻辑应用设计器的工具栏上选择“保存”以保存逻辑应用。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/save-logic-app.png" alt-text="保存逻辑应用":::
1. 如果尚未向主题发送测试消息，请按照[向服务总线主题发送消息](#send-messages-to-the-service-bus-topic)部分中的说明将消息发送到主题。 
1. 切换到逻辑应用的“概述”页。 “运行历史记录”中会显示已发送的消息的逻辑应用运行。 可能需要几分钟时间，你才能看到逻辑应用运行。 选择工具栏上的“刷新”，以刷新页面。 

    ![逻辑应用设计器 - 逻辑应用运行](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)
1. 选择一个逻辑应用运行，查看详细信息。 请注意，它在 for 循环中处理了 5 条消息。 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/logic-app-run-details.png" alt-text="逻辑应用运行详细信息":::    

## <a name="troubleshoot"></a>疑难解答
如果在等待和刷新一段时间后没有看到任何调用，请执行以下步骤： 

1. 确认消息已到达服务总线主题。 查看“服务总线主题”页面上的“传入消息”计数器 。 在本例中，我运行了两次 MessageSender 应用程序，因此我会看到 10 条消息（每次运行 5 条消息）。

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/topic-incoming-messages.png" alt-text="服务总线主题页面 - 传入消息":::    
1. 确认服务总线订阅中有“无活动消息”。 
    如果在此页面上未看到任何事件，请验证“服务总线订阅”页面没有显示任何“活动消息计数” 。 如果此计数器的数字大于零，则由于某些原因，订阅中的消息没有转发到处理程序函数（事件订阅处理程序）。 请验证是否已正确设置事件订阅。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/subscription-active-message-count.png" alt-text="服务总线订阅的活动消息计数":::    
1. 你还可在服务总线命名空间的“事件”页面上查看发送的事件数 。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-page.png" alt-text="事件页面 - 发送的事件数" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::
1. 还可在“事件订阅”页面上看到事件已发送。 可在“事件”页面上选择事件订阅来访问此页面。 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-delivered-events.png" alt-text="事件订阅页面 - 发送的事件数":::
## <a name="next-steps"></a>后续步骤

* 详细了解 [Azure 事件网格](../event-grid/index.yml)。
* 详细了解 [Azure Functions](../azure-functions/index.yml)。
* 详细了解 [Azure 应用服务的逻辑应用功能](../logic-apps/index.yml)。
* 详细了解 [Azure 服务总线](/azure/service-bus/)。


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png