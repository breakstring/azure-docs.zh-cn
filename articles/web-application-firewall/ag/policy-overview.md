---
title: Azure Web 应用程序防火墙 (WAF) 策略概述
description: 本文概述 (WAF) 全局、每个站点和每个 URI 策略的 Web 应用程序防火墙。
services: web-application-firewall
ms.topic: article
author: winthrop28
ms.service: web-application-firewall
ms.date: 11/20/2020
ms.author: victorh
ms.openlocfilehash: 59ca0b85ba2aff29bdb2ad3379c1054041d2b4cb
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518730"
---
# <a name="azure-web-application-firewall-waf-policy-overview"></a>Azure Web 应用程序防火墙 (WAF) 策略概述

Web 应用程序防火墙策略包含所有 WAF 设置和配置。 这包括排除、自定义规则、托管规则等。 然后，将这些策略关联到应用程序网关 (全局) 、侦听器 (每站点) ，或 (每个 URI 的基于路径的规则) 使其生效。

你可以创建的策略数没有限制。 当你创建策略时，它必须关联到应用程序网关才能生效。 它可以与应用程序网关、侦听器和基于路径的规则的任意组合关联。

## <a name="global-waf-policy"></a>全局 WAF 策略

全局关联 WAF 策略时，应用程序网关 WAF 后面的每个站点均使用相同的托管规则、自定义规则、排除项和任何其他已配置的设置进行保护。

如果希望将单个策略应用到所有站点，可以将该策略与应用程序网关相关联。 有关详细信息，请参阅 [创建应用程序网关的 Web 应用程序防火墙策略](create-waf-policy-ag.md) ，使用 Azure 门户创建和应用 WAF 策略。 

## <a name="per-site-waf-policy"></a>每站点 WAF 策略

有了每站点 WAF 策略后，你就可以使用每站点策略保护单个 WAF 后多个具有不同安全需求的站点。 例如，如果 WAF 后方有五个站点，你可以通过五个单独的 WAF 策略（每个侦听器一个）来为每个站点自行定义排除项、自定义规则、托管规则集以及其他所有 WAF 设置。

假如应用程序网关应用了一项全局策略。 然后，你对该应用程序网关上的某个侦听器应用一个不同的策略。 该侦听器上的策略现在只对该侦听器生效。 该应用程序网关的全局策略依然适用于所有其他侦听器和未分配有特定策略的基于路径的规则。

## <a name="per-uri-policy"></a>每 URI 策略

若要向下更进一步的自定义，可将 WAF 策略与基于路径的规则相关联。 如果在需要不同策略的单一站点中有某些页面，则可以更改只影响给定 URI 的 WAF 策略。 这可能适用于支付或登录页，或者需要比 WAF 后面的其他站点更具体的 WAF 策略的任何其他 Uri。

与每站点 WAF 策略一样，更具体的策略会替代更具体的策略。 这意味着，URL 路径映射上的每个 URI 策略会重写其上方的任何每个站点或全局 WAF 策略。

### <a name="example"></a>示例

假设有三个站点： contoso.com、fabrikam.com 和 adatum.com 都位于同一应用程序网关后面。 你需要将 WAF 应用于所有三个站点，但你需要增加 adatum.com 的安全性，因为这是客户访问、浏览和购买产品的地方。

如有必要，可将全局策略应用到 WAF，其中包含一些基本设置、排除项或自定义规则（如有必要），阻止流量阻止某些误报。 在这种情况下，无需运行全局 SQL 注入规则，因为 fabrikam.com 和 contoso.com 是不带 SQL 后端的静态页面。 因此，你可以在全局策略中禁用这些规则。

此全局策略适用于 contoso.com 和 fabrikam.com，但需要更小心地处理 adatum.com，其中的登录信息和支付处理方式。 可以将每个站点策略应用于 adatum 侦听器并使 SQL 规则保持运行。 另外，假设有一个 cookie 阻止了某些流量，因此你可以为该 cookie 创建排除以停止误报。 

Adatum.com/payments URI 是需要注意的地方。 因此，请在该 URI 上应用另一个策略并使所有规则保持启用状态，同时删除所有排除项。

在此示例中，你有一个适用于两个站点的全局策略。 你有一个适用于单个站点的每个站点策略，然后是适用于一个基于路径的特定规则的每个 URI 策略。 有关此示例，请参阅使用 Azure PowerShell 为相应的 PowerShell [配置每站点 WAF 策略](per-site-policies.md) 。

## <a name="existing-waf-configurations"></a>现有的 WAF 配置

所有新的 Web 应用程序防火墙的 WAF 设置 (自定义规则、托管规则集配置、排除项等。 ) 存在于 WAF 策略中。 如果你有现有的 WAF，则这些设置可能仍存在于你的 WAF 配置中。 有关移动到新 WAF 策略的详细信息，请将 [WAF Config 迁移到 WAF 策略](./migrate-policy.md)。 


## <a name="next-steps"></a>后续步骤

- [使用 Azure PowerShell 创建每个站点和每个 URI 的策略](per-site-policies.md)。
