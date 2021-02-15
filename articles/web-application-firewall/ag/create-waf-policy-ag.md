---
title: 为应用程序网关创建 Web 应用程序防火墙 (WAF) 策略
description: 了解如何创建应用程序网关的 Web 应用程序防火墙策略。
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 02/08/2020
ms.author: victorh
ms.openlocfilehash: 26078c3757e42c3e290a5f4122461b287582fb80
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518815"
---
# <a name="create-web-application-firewall-policies-for-application-gateway"></a>创建应用程序网关的 Web 应用程序防火墙策略

将 WAF 策略与侦听器相关联，可允许单个 WAF 后面的多个站点受到不同策略的保护。 例如，如果 WAF 有五个站点，则可以为每个侦听器使用5个单独的 WAF 策略，) 为一个站点自定义排除项、自定义规则和托管规则集，而不会影响其他四个 (策略。 如果希望将单个策略应用到所有站点，只需将该策略与应用程序网关相关联，而不是将它应用于单个侦听器，使其全局应用。 策略还可以应用于基于路径的路由规则。 

你可以根据需要创建任意多个策略。 创建策略后，必须将它关联到应用程序网关才能生效，但它可以与应用程序网关和侦听器的任何组合关联。 

如果应用程序网关应用了策略，然后将不同的策略应用于该应用程序网关上的侦听器，则该侦听器的策略将生效，但仅用于侦听器 (的) 分配给该侦听器。 应用程序网关策略仍适用于未分配特定策略的所有其他侦听器。 

   > [!NOTE]
   > 防火墙策略关联到 WAF 后，必须始终有一个与该 WAF 关联的策略。 你可能会覆盖该策略，但不支持完全从 WAF 中解除策略关联。 

所有新的 Web 应用程序防火墙的 WAF 设置 (自定义规则、托管 rulset 配置、排除项等，在 WAF 策略内部 ) 。 如果你有现有的 WAF，则这些设置可能仍存在于你的 WAF 配置中。有关如何移动到新 WAF 策略的步骤，请参阅本文后面的将 [WAF Config 迁移到 WAF 策略](#migrate) 。 

## <a name="create-a-policy"></a>创建策略

首先，使用 Azure 门户 (DRS) 创建具有托管默认规则集的基本 WAF 策略。

1. 在门户的左上方，选择 " **创建资源**"。 搜索 " **WAF**"，选择 " **Web 应用程序防火墙**"，然后选择 " **创建**"。
2. 在 " **创建 WAF 策略** " 页上的 " **基本** 信息" 选项卡上，输入或选择以下信息，接受其余设置的默认值，然后选择 " **查看 + 创建**"：

   |设置  |值  |
   |---------|---------|
   |策略     |区域 WAF (应用程序网关) |
   |订阅     |选择订阅名称|
   |资源组     |选择你的资源组|
   |策略名称     |键入 WAF 策略的唯一名称。|
3. 在 " **关联** " 选项卡上，输入以下设置之一，然后选择 " **添加**"：

   |设置  |值  |
   |---------|---------|
   |关联应用程序网关     |选择应用程序网关配置文件名称。|
   |关联侦听器     |选择应用程序网关侦听器的名称，然后选择 " **添加**"。|

   > [!NOTE]
   > 如果已将策略分配给应用程序网关 (或已有策略的侦听器) ，则会覆盖原始策略，并将其替换为新策略。
4. 依次选择“查看 + 创建”、“创建”。  

   ![WAF 策略基础知识](../media/create-waf-policy-ag/waf-policy-basics.png)

## <a name="configure-waf-rules-optional"></a>配置 WAF 规则 (可选) 

创建 WAF 策略时，默认情况下它处于 *检测* 模式。 在“检测”  模式下，WAF 不会阻止任何请求。 相反，匹配 WAF 规则记录在 WAF 日志中。 若要查看操作中的 WAF，可以将模式设置更改为 " *阻止*"。 在阻止模式下，在所选 CRS 规则集中定义的匹配规则将被阻止和/或记录在 WAF 日志中。

## <a name="managed-rules"></a>托管规则

默认情况下启用 Azure 托管的 OWASP 规则。 若要禁用规则组中的单个规则，请展开该规则组中的规则，选中规则编号前面的复选框，并在上面的选项卡上选择 " **禁用** "。

[![托管规则 ](../media/create-waf-policy-ag/managed-rules.png)](../media/create-waf-policy-ag/managed-rules-lrg.png#lightbox)

## <a name="custom-rules"></a>自定义规则

若要创建自定义规则，请在 "**自定义规则**" 选项卡下选择 "**添加自定义规则**"。这将打开 "自定义规则配置" 页。 以下屏幕截图显示了一个示例自定义规则，该规则配置为在查询字符串包含文本 *blockme* 时阻止请求。

[![编辑自定义规则 ](../media/create-waf-policy-ag/edit-custom-rule.png)](../media/create-waf-policy-ag/edit-custom-rule-lrg.png#lightbox)

## <a name="migrate-your-waf-config-to-a-waf-policy"></a><a name="migrate"></a>将 WAF 配置迁移到 WAF 策略

如果有现成的 WAF，可能会注意到门户中的一些更改。 首先需要确定在 WAF 上启用的策略类型。 有三种可能的状态：

- 无 WAF 策略
- 仅自定义规则策略
- WAF 策略

可以通过在门户中查看 WAF，来确定你的的状态。 如果 WAF 设置可见，并且可在应用程序网关视图中更改，则 WAF 处于状态1。

[![WAF 配置 ](../media/create-waf-policy-ag/waf-configure.png)](../media/create-waf-policy-ag/waf-configure-lrg.png#lightbox)

如果选择 " **Web 应用程序防火墙** " 并显示关联的策略，则 WAF 的状态为 "2" 或 "状态 3"。 导航到策略后，如果它 **仅** 显示自定义规则和关联的应用程序网关，则它是自定义规则 "仅限" 策略。

![WAF 自定义规则](../media/create-waf-policy-ag/waf-custom-rules.png)

如果它还显示策略设置和托管规则，则它是完整的 Web 应用程序防火墙策略。 

![WAF 策略设置](../media/create-waf-policy-ag/waf-policy-settings.png)

## <a name="migrate-to-waf-policy"></a>迁移到 WAF 策略

如果仅有自定义规则 WAF 策略，则可能需要移动到新的 WAF 策略。 今后，防火墙策略将支持 WAF 策略设置、托管规则集、排除项和禁用的规则组。 实质上，以前在应用程序网关内完成的所有 WAF 配置现在都是通过 WAF 策略完成的。 

仅禁用对自定义规则的编辑 WAF 策略。 若要编辑任何 WAF 设置（例如禁用规则、添加排除项等），必须迁移到新的顶级防火墙策略资源。

为此，请创建一个 *Web 应用程序防火墙策略* ，并将其与你的应用程序网关 (s) 和侦听器 () 选择关联。 此新策略必须与当前 WAF config 完全相同，这意味着每个自定义规则、排除、禁用的规则等都必须复制到您要创建的新策略中。 一旦你有与应用程序网关关联的策略，你就可以继续更改 WAF 规则和设置。 还可以通过 Azure PowerShell 执行此操作。 有关详细信息，请参阅 [将 WAF 策略与现有的应用程序网关相关联](associate-waf-policy-existing-gateway.md)。

或者，你可以使用迁移脚本迁移到 WAF 策略。 有关详细信息，请参阅 [使用 Azure PowerShell 迁移 Web 应用程序防火墙策略](migrate-policy.md)。

## <a name="force-mode"></a>强制模式

如果不想将所有内容复制到与当前配置完全相同的策略，则可以将 WAF 设置为 "强制" 模式。 运行以下 Azure PowerShell 代码，你的 WAF 将处于强制模式。 然后，你可以将任何 WAF 策略关联到你的 WAF，即使它没有与配置完全相同的设置。 

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -Name <your Application Gateway name> -ResourceGroupName <your Resource Group name>
$appgw.ForceFirewallPolicyAssociation = $true
```

然后 procees 将 WAF 策略关联到应用程序网关的步骤。 有关详细信息，请参阅 [将 WAF 策略与现有的应用程序网关相关联。](associate-waf-policy-existing-gateway.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Web 应用程序防火墙 CRS 规则组和规则](application-gateway-crs-rulegroups-rules.md)的详细信息。
