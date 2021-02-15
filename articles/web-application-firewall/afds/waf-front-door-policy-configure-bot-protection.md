---
title: 通过 Azure 前门 (预览) 为 Web 应用程序防火墙配置机器人保护
description: 了解如何使用 Azure 门户在 Azure Web 应用程序防火墙中配置机器人保护规则 (WAF) 前门。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 2357c51f47bcb9bd8bbc6c408cb6d8edbab4d10e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267000"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>为 Web 应用程序防火墙 (预览配置机器人保护) 
本文介绍如何使用 Azure 门户在 Azure Web 应用程序防火墙中配置机器人保护规则 (WAF) 前门。 还可以使用 CLI、Azure PowerShell 或 Azure 资源管理器模板配置机器人保护规则。

> [!IMPORTANT]
> 机器人防护规则集当前为公共预览版，并提供预览版服务级别协议。 某些功能可能不受支持或者受限。  有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必备条件

按照 [使用 Azure 门户为 Azure 前门创建 WAF 策略](waf-front-door-create-portal.md)中所述的说明，为前门创建基本的 WAF 策略。

## <a name="enable-bot-protection-rule-set"></a>启用机器人保护规则集

在创建 Web 应用程序防火墙策略时，请在 " **托管规则** " 页中，选择 "首先查找 **托管规则集** " 部分，从下拉菜单中选择 "规则 **Microsoft_BotManager_1 .0** " 旁边的复选框，然后选择 " **查看 + 创建**"。

   ![机器人保护规则](.././media/waf-front-door-configure-bot-protection/botmanager112019.png)

## <a name="next-steps"></a>后续步骤

- 了解如何 [监视 WAF](waf-front-door-monitor.md)。
