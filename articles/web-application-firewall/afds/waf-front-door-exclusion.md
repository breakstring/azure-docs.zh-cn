---
title: Azure 前门中的 Web 应用程序防火墙排除列表-Azure 门户
description: 本文提供了有关 Azure 前面带有 Azure 门户的排除列表配置的信息。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/10/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: c2c84b508ee86ebdd82dbcc7040106142187c506
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563454"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Web 应用程序防火墙 (带有前门服务排除列表的 WAF)  

有时，Web 应用程序防火墙 (WAF) 可能会阻止你要为应用程序提供的请求。 例如，Active Directory 插入用于身份验证的令牌。 这些令牌可以包含特殊字符，这些字符可能会从 WAF 规则触发误报。 WAF 排除列表允许你忽略 WAF 评估中的某些请求属性。  可以使用  [PowerShell](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0)、 [AZURE CLI](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add)、 [Rest API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)或 Azure 门户配置排除列表。 下面的示例演示了 Azure 门户配置。 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>使用 Azure 门户配置排除列表
可以从 WAF 门户中的 **托管规则** 下访问 **管理排除** 项

![管理排除 ](../media/waf-front-door-exclusion/exclusion1.png)
 ![ 管理 exclusion_add](../media/waf-front-door-exclusion/exclusion2.png)

 示例排除列表： ![ 管理 exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

此示例不包括 " *用户* 标头" 字段中的值。 有效的请求可能包含包含触发 SQL 注入规则的字符串的 *user* 字段。 在这种情况下，可以排除 *user* 参数，以便 WAF 规则不会评估字段中的任何内容。

可以按名称向排除列表添加以下属性。 你使用的字段值不会针对 WAF 规则进行评估，但会对其名称进行计算。 排除列表删除了对该字段值的检查。

* 请求头名称
* 请求 cookie 名称
* 查询字符串参数名称
* 请求正文 post 参数名称

可以指定请求标头、正文、cookie 或查询字符串属性的完全匹配项。  也可以选择指定部分匹配项。 以下运算符是支持的匹配条件：

- **等于** ：此运算符用于完全匹配。 例如，若要选择名为 **bearerToken** 的标头，请在选择器设置为 **bearerToken** 时使用 equals 运算符。
- **开头为** ：此运算符与以指定选择器值开头的所有字段匹配。
- **结尾为** ：此运算符与以指定选择器值结尾的所有请求字段匹配。
- **包含** ：此运算符与包含指定选择器值的所有请求字段匹配。
- **等于任何值** ：此运算符与所有请求字段匹配。 * 是选择器值。

标头和 cookie 名称不区分大小写。

如果 "标头值"、"cookie 值"、"post 参数值" 或 "查询参数值" 为某些规则产生误报，则可以排除该规则的部分请求：


|WAF 日志中的 matchVariableName  |门户中的规则排除  |
|---------|---------|
|CookieValue： SOME_NAME        |请求 cookie 名称等于 SOME_NAME|
|HeaderValue： SOME_NAME        |请求标头名称等于 SOME_NAME|
|PostParamValue： SOME_NAME     |请求正文 post 参数名称等于 SOME_NAME|
|QueryParamValue： SOME_NAME    |查询字符串参数名称等于 SOME_NAME|


目前仅支持 WAF 日志中上述 matchVariableNames 的规则排除项。 对于任何其他 matchVariableNames，必须禁用发出误报的规则，或创建显式允许这些请求的自定义规则。 特别是，当 matchVariableName 为 CookieName、HeaderName、PostParamName 或 QueryParamName 时，这意味着该名称本身就会触发规则。 规则排除目前不支持这些 matchVariableNames。


如果排除名为 *FOO* 的请求正文 post 参数，则任何规则都不会在 WAF 日志中显示 POSTPARAMVALUE： FOO 作为 matchVariableName。 但是，你可能仍会看到一个规则，该规则具有与 post 参数 FOO 的值匹配的 matchVariableName InitialBodyContents，因为 post 参数值是 InitialBodyContents 的一部分。

您可以将排除列表应用于托管规则集内的所有规则、特定规则组的规则或单个规则，如前面的示例所示。

## <a name="define-exclusion-based-on-web-application-firewall-logs"></a>基于 Web 应用程序防火墙日志定义排除
 [Azure Web 应用程序防火墙监视和日志记录](waf-front-door-monitor.md) 显示已阻止请求的匹配详细信息。 如果 "标头值"、"cookie 值"、"post 参数值" 或 "查询参数值" 为某些规则产生误报，则可以将该请求的该部分从规则中排除。 下表显示了 WAF 日志中的示例值和相应的排除条件。

|WAF 日志中的 matchVariableName    |门户中的规则排除|
|--------|------|
|CookieValue： SOME_NAME  |请求 cookie 名称等于 SOME_NAME|
|HeaderValue： SOME_NAME  |请求标头名称等于 SOME_NAME|
|PostParamValue： SOME_NAME|  请求正文 post 参数名称等于 SOME_NAME|
|QueryParamValue： SOME_NAME| 查询字符串参数名称等于 SOME_NAME|


## <a name="next-steps"></a>后续步骤

配置 WAF 设置后，请了解如何查看 WAF 日志。 有关详细信息，请参阅 [前门诊断](../afds/waf-front-door-monitor.md)。