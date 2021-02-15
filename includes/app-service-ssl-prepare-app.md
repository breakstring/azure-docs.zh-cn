---
title: include 文件
description: include 文件
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 10/15/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8b3c1992a1cff18390f9d1332103e0650af418e2
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347706"
---
## <a name="prepare-your-web-app"></a>准备 Web 应用

若要为应用服务应用创建自定义 TLS/SSL 绑定或启用客户端证书，[应用服务计划](https://azure.microsoft.com/pricing/details/app-service/)必须位于“基本”、“标准”、“高级”或“独立”层级   。 在此步骤中，请确保 Web 应用位于受支持的定价层。

### <a name="sign-in-to-azure"></a>登录 Azure

打开 [Azure 门户](https://portal.azure.com)。

### <a name="navigate-to-your-web-app"></a>导航到 Web 应用

搜索并选择“应用服务”。 

![选择应用服务](./media/app-service-ssl-prepare-app/app-services.png)

在“应用服务”页上，选择 Web 应用的名称  。

![Azure 门户中应用服务页面的屏幕截图，其中显示一个包含所有正在运行的 Web 应用的列表，且突出显示了列表中的第一个应用。](./media/app-service-ssl-prepare-app/select-app.png)

你已登录到 Web 应用的管理页。  

### <a name="check-the-pricing-tier"></a>检查定价层

在 Web 应用页的左侧导航窗格中，滚动到“设置”  部分，然后选择“增加(应用服务计划)”  。

![扩展菜单](./media/app-service-ssl-prepare-app/scale-up-menu.png)

检查以确保 Web 应用不在 **F1** 或 **D1** 层中。 深蓝色的框突出显示了 Web 应用的当前层。

![检查定价层](./media/app-service-ssl-prepare-app/check-pricing-tier.png)

**F1** 或 **D1** 层不支持自定义 SSL。 如果需要增加，请按照下一部分中的步骤进行操作。 否则，请关闭“纵向扩展”  页，并跳过[纵向扩展应用服务计划](#scale-up-your-app-service-plan)部分。

### <a name="scale-up-your-app-service-plan"></a>纵向扩展应用服务计划

选择任何非免费层（**B1**、**B2**、**B3**，或“生产”  类别中的任何层）。 有关其他选项，请单击“查看其他选项”  。

单击“应用”  。

![选择定价层](./media/app-service-ssl-prepare-app/choose-pricing-tier.png)

看到以下通知时，说明缩放操作已完成。

![扩展通知](./media/app-service-ssl-prepare-app/scale-notification.png)

