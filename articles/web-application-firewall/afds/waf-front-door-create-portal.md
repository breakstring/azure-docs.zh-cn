---
title: 教程：为 Azure Front Door 创建 WAF 策略 - Azure 门户
description: 本教程介绍如何使用 Azure 门户创建 Web 应用程序防火墙 (WAF) 策略。
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: victorh
ms.openlocfilehash: ba344c3b1570c041e1602bdfcde1b3a4055dc396
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132748"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>教程：使用 Azure 门户在 Azure Front Door 上创建 Web 应用程序防火墙策略

本教程介绍如何创建基本的 Azure Web 应用程序防火墙 (WAF) 策略，并将其应用到 Azure Front Door 上的前端主机。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建 WAF 策略
> * 将该策略关联到前端主机
> * 配置 WAF 规则

## <a name="prerequisites"></a>先决条件

遵照以下文档中的说明创建 Front Door 配置文件：[快速入门：创建 Front Door 配置文件](../../frontdoor/quickstart-create-front-door.md)。 

## <a name="create-a-web-application-firewall-policy"></a>创建 Web 应用程序防火墙策略

首先使用门户创建包含托管的默认规则集 (DRS) 的基本 WAF 策略。 

1. 在屏幕的左上方选择“创建资源”，搜索 **WAF** ，然后选择“Web 应用程序防火墙(预览版)”>“创建”。   
2. 在“创建 WAF 策略”页的“基本”选项卡中输入或选择以下信息，对剩余的设置保留默认值，然后选择“查看 + 创建”：   

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | 订阅            |选择 Front Door 订阅名称。|
    | 资源组          |选择 Front Door 资源组名称。|
    | 策略名称             |输入 WAF 策略的唯一名称。|

   :::image type="content" source="../media/waf-front-door-create-portal/basic.png" alt-text="“创建 WAF 策略”页的屏幕截图，其中包含订阅、资源组和策略名称的“查看 + 创建”按钮以及列表框。" border="false":::

3. 在“创建 WAF 策略”页的“关联”选项卡中选择“添加前端主机”，输入以下设置，然后选择“添加”：    

    | 设置                 | 值                                              |
    | ---                     | ---                                                |
    | Front Door              | 选择 Front Door 配置文件名称。|
    | 前端主机           | 选择 Front Door 主机的名称，然后选择“添加”。 |
    
    > [!NOTE]
    > 如果前端主机已关联到某个 WAF 策略，该主机将会灰显。必须先从关联的策略中删除该前端主机，然后将它重新关联到新的 WAF 策略。
1. 依次选择“查看 + 创建”、“创建”。  

## <a name="configure-web-application-firewall-rules-optional"></a>配置 Web 应用程序防火墙规则（可选）

### <a name="change-mode"></a>更改模式

创建 WAF 策略时，该策略默认处于“检测”模式。  在“检测”模式下，WAF 不会阻止任何请求，而会在 WAF 日志中记录与 WAF 规则匹配的请求。 
若要查看 WAF 的运作方式，可将模式设置从“检测”更改为“阻止”。   在“阻止”模式下，与默认规则集 (DRS) 中定义的规则匹配的请求将被阻止，并记录在 WAF 日志中。 

 :::image type="content" source="../media/waf-front-door-create-portal/policy.png" alt-text="“创建 WAF 策略”页的屏幕截图，其中包含订阅、资源组和策略名称的“查看 + 创建”按钮以及列表框。" border="false":::

### <a name="custom-rules"></a>自定义规则

可以通过选择“自定义规则”部分下的“添加自定义规则”来创建自定义规则。   这会启动自定义规则配置页。 以下示例配置一个自定义规则，当查询字符串包含 **blockme** 时，此规则会阻止相应的请求。

:::image type="content" source="../media/waf-front-door-create-portal/customquerystring2.png" alt-text="“创建 WAF 策略”页的屏幕截图，其中包含订阅、资源组和策略名称的“查看 + 创建”按钮以及列表框。" border="false":::

### <a name="default-rule-set-drs"></a>默认规则集 (DRS)

默认已启用 Azure 托管的默认规则集。 若要禁用规则组中的单个规则，请展开该规则组中的规则，选中规则编号前面的 **复选框** ，然后在上面的选项卡中选择“禁用”。  若要更改规则集中单个规则的操作类型，请选中规则编号前面的复选框，然后选择上面的“更改操作”选项卡。 

 :::image type="content" source="../media/waf-front-door-create-portal/managed2.png" alt-text="“创建 WAF 策略”页的屏幕截图，其中包含订阅、资源组和策略名称的“查看 + 创建”按钮以及列表框。" border="false":::

## <a name="clean-up-resources"></a>清理资源

不再需要时，请删除资源组及所有相关资源。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Front Door](../../frontdoor/front-door-overview.md)