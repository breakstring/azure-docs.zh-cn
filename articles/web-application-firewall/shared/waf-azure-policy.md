---
title: Azure Web 应用程序防火墙和 Azure 策略
description: Azure Web 应用程序防火墙 (WAF) 与 Azure 策略结合，可帮助强制实施组织标准并评估 WAF 资源的符合性
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: rimansdo
ms.openlocfilehash: 7798d7e960286d4f8aa971eb2eb0b03d24bd6360
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589451"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Azure Web 应用程序防火墙和 Azure 策略

Azure Web 应用程序防火墙 (WAF) 与 Azure 策略结合，可帮助强制实施组织标准并评估 WAF 资源的符合性。 Azure 策略是一种管理工具，它提供了汇总视图来评估环境的整体状态，并能够向下钻取每个资源的每个策略粒度。 Azure 策略还有助于通过对现有资源进行大容量修正和对新资源进行自动修正来使资源符合要求。

## <a name="azure-policy-for-web-application-firewall"></a>用于 Web 应用程序防火墙的 Azure 策略

有几个内置的 Azure 策略定义可用于管理 WAF 资源。 策略定义及其功能的细分如下：

1. **应为 Azure 前门服务启用 Web 应用程序防火墙 (WAF)**：如果在创建资源时存在 WAF，则会评估 Azure 前门服务。 策略具有三个效果：审核、拒绝和禁用。 审核跟踪： Azure 前门服务没有 WAF，并允许用户查看 Azure 前门服务不符合的情况。 如果未连接 WAF，则 Deny 会阻止创建任何 Azure 前门服务。 禁用关闭此策略。

2. **应为应用程序网关启用 Web 应用程序防火墙 (WAF)**：如果在创建资源时存在 WAF，将对应用程序网关进行评估。 策略具有三个效果：审核、拒绝和禁用。 当应用程序网关没有 WAF，并允许用户查看应用程序网关不符合的情况时，审核跟踪。 如果未附加 WAF，则 Deny 会阻止创建任何应用程序网关。 禁用关闭此策略。

3. **Web 应用程序防火墙 (WAF) 应使用指定的 Azure 前门服务模式**：要求在 Azure 前门服务的所有 Web 应用程序防火墙策略上使用 "检测" 或 "防护" 模式。 策略具有三个效果：审核、拒绝和禁用。 WAF 不适合指定模式时的审核跟踪。 如果未在正确模式下，Deny 会阻止创建任何 WAF。 禁用关闭此策略。

4. **Web 应用程序防火墙 (WAF) 应使用应用程序网关的指定模式**：要求在应用程序网关的所有 Web 应用程序防火墙策略上使用 "检测" 或 "防护" 模式。 策略具有三个效果：审核、拒绝和禁用。 WAF 不适合指定模式时的审核跟踪。 如果未在正确模式下，Deny 会阻止创建任何 WAF。 禁用关闭此策略。

## <a name="launch-an-azure-policy"></a>启动 Azure 策略

1.  在 Azure 主页上的搜索栏中键入 "策略"，然后单击 "Azure 策略" 图标

2.  在 Azure 策略服务的 " **创作**" 下，选择 " **分配**"。

:::image type="content" source="../media/waf-azure-policy/policy-home.png" alt-text="Azure 策略中的 &quot;分配&quot; 选项卡":::

3.  在 "分配" 页上，选择顶部的 " **分配策略** " 图标。

:::image type="content" source="../media/waf-azure-policy/assign-policy.png" alt-text="&quot;分配策略&quot; 页上的 &quot;基本信息&quot; 选项卡":::

4.  在 "分配策略" 页的 "基本信息" 选项卡上，更新以下字段：
    1.  **作用域**：选择策略定义应该影响哪些 Azure 订阅和资源组。
    2.  **排除**：从策略分配中选择要排除的作用域中的任何资源。
    3.  **策略定义**：选择要应用于包含排除项的作用域的策略定义。 在搜索栏中键入 "Web 应用程序防火墙"，选择相关的 Web 应用程序防火墙 "Azure 策略"。

:::image type="content" source="../media/waf-azure-policy/policy-listing.png" alt-text="显示 &quot;可用定义&quot; 页上的 &quot;策略定义&quot; 选项卡的屏幕截图。":::

5.  选择 " **参数** " 选项卡，并更新策略分配参数。 若要进一步阐明参数的作用，请将鼠标悬停在参数名称旁边的信息图标上，以便进一步澄清。

6.  选择 " **查看 + 创建** " 以完成策略分配。 策略分配大约要花费15分钟，直到新资源处于活动状态。
