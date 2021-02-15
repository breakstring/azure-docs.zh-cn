---
title: 使用安全策略 | Microsoft Docs
description: 本文介绍如何使用 Azure 安全中心的安全策略。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 19128f0372f9a5bda0d16155167a507eccaf436a
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986575"
---
# <a name="manage-security-policies"></a>管理安全策略

本文介绍如何配置安全策略，以及如何在安全中心查看这些策略。 

## <a name="introduction-to-security-policies"></a>安全策略简介

安全策略定义工作负荷的所需配置，并帮助确保遵守公司或监管机构的安全要求。

Azure 安全中心根据所选的策略提供安全建议。 安全中心策略基于 Azure Policy 中创建的策略计划。 可以使用 [Azure Policy](../governance/policy/overview.md) 来管理策略，以及跨管理组和多个订阅设置策略。

安全中心提供以下选项来让用户使用安全策略：

* **查看和编辑内置默认策略** -启用安全中心时，名为 "Azure 安全基准测试" 的计划会自动分配到所有安全中心注册的订阅。 若要自定义此计划，可在其中启用或禁用单个策略。 要了解现成可用的选项，请参阅[内置安全策略](./policy-reference.md)列表。

* **添加自己的自定义策略** - 如果希望自定义要应用到自己的订阅的安全计划，可以在安全中心执行此操作。 如果计算机不遵循创建的策略，则你会收到建议。 有关生成和分配自定义策略的说明，请参阅[使用自定义安全策略](custom-security-policies.md)。

* **添加合规性策略** - 安全中心的合规性仪表板显示环境内的所有评估在特定标准或法规（例如 Azure CIS、NIST SP 800-53 R4、SWIFT CSP CSCF-v2020）上下文中的状态。 有关详细信息，请参阅[改善合规性](security-center-compliance-dashboard.md)。


## <a name="manage-your-security-policies"></a>管理安全策略

要在安全中心内查看安全策略，请执行以下操作：

1. 在“安全中心”仪表板中，选择“安全策略” 。

    :::image type="content" source="./media/security-center-policies/security-center-policy-mgt.png" alt-text="策略管理页面":::

   在“策略管理”屏幕中，可以看到管理组数、订阅数、工作区数以及管理组结构。

1. 选择想要查看其策略的订阅或管理组。

1. 此时会显示该订阅或管理组的安全策略页。 其中显示了可用和已分配的策略。

    :::image type="content" source="./media/tutorial-security-policy/security-policy-page.png" alt-text="安全中心的安全策略页" lightbox="./media/tutorial-security-policy/security-policy-page.png":::

    > [!NOTE]
    > 如果默认策略旁边有一个标签“MG 已继承”，则表示该策略已分配到某个管理组，并已由当前你正在查看的订阅继承。


1. 从此页提供的可用选项中进行选择：

    1. 要使用行业策略，请选择“添加更多标准”。 有关详细信息，请参阅[更新为动态符合性包](update-regulatory-compliance-packages.md)。

    1. 要分配和管理自定义计划，请选择“添加自定义计划”。 有关详细信息，请参阅[使用自定义安全策略](custom-security-policies.md)。

    1. 要查看和编辑默认策略，请选择“查看有效策略”并按如下所述继续操作。 

        :::image type="content" source="./media/security-center-policies/policy-screen.png" alt-text="有效策略屏幕":::

       此“安全策略”屏幕反映在所选订阅或管理组中分配的策略所执行的操作。
       
       * 使用顶部的链接打开在订阅或管理组中应用的每个策略 **分配**。 可以使用这些链接访问分配，以及编辑或禁用策略。 例如，如果你发现特定的策略分配正在有效地拒绝终结点保护，可使用该链接来编辑或禁用该策略。
       
       * 在策略列表中，可以看到策略有效应用于订阅或管理组。 将考虑适用于该范围的每个策略的设置，并显示该策略执行的操作的累积效果。 例如，如果一个分配禁用此策略，而另一个设置为 AuditIfNotExist，则累积效果将应用 AuditIfNotExist。 更积极的效果始终优先。
       
       * 策略的效果可以是：追加、审核、AuditIfNotExists、拒绝、DeployIfNotExists 和禁用。 有关如何应用效果的详细信息，请参阅[了解策略效果](../governance/policy/concepts/effects.md)。

       > [!NOTE]
       > 查看已分配的策略时，可以看到多个分配并且可以看到每个分配如何自行配置。


## <a name="who-can-edit-security-policies"></a>谁可以编辑安全策略？

你可以在 Azure Policy 门户中通过 REST API 或 Windows PowerShell 编辑安全策略。

安全中心使用 Azure 基于角色的访问控制 (Azure RBAC)，后者提供可分配到 Azure 用户、组和服务的内置角色。 用户打开安全中心时，只能看到与他们可访问的资源相关的信息。 这意味着，已为用户分配了资源订阅的所有者、参与者或读取者角色。   还有两个特定的安全中心角色：

- **安全读取者**：有权查看安全中心项，例如建议、警报、策略和运行状况。 无法执行更改。
- **安全管理员**：与安全读取者具有相同的查看权限。 还可以更新安全策略并消除警报。


## <a name="disable-security-policies-and-disable-recommendations"></a>禁用安全策略和禁用建议

如果安全计划触发与环境无关的建议，你可以阻止该建议再次出现。 若要禁用建议，请禁用生成该建议的特定策略。

如果已经使用安全中心的合规工具应用了法规标准，而你想要禁用的建议是这项法规标准所要求的，那么，你想要禁用的建议仍将会出现。 如果该建议对于合规性来说是必要的，那么，即使已在内置计划中禁用了策略，法规标准计划中的策略仍将触发该建议。 你无法禁用法规标准计划中的策略。

有关建议的详细信息，请参阅[管理安全建议](security-center-recommendations.md)。

1. 在安全中心的“策略和符合性”部分，选择“安全策略” 。

    :::image type="content" source="./media/tutorial-security-policy/policy-management.png" alt-text="在 Azure 安全中心启动策略管理过程":::

2. 选择要禁用其建议的订阅或管理组。

   > [!NOTE]
   > 请记住，管理组将其策略应用于其订阅。 因此，如果禁用了某个订阅策略，而该订阅属于仍使用同一策略的管理组，则你将继续收到策略建议。 仍将从管理级别应用该策略，且仍将生成建议。

1. 选择“查看有效策略”。

    :::image type="content" source="./media/tutorial-security-policy/view-effective-policy.png" alt-text="如何打开分配给订阅的有效策略":::

1. 选择分配的策略。

   ![选择策略](./media/tutorial-security-policy/security-policy.png)

1. 在“参数”部分中，搜索调用要禁用的建议的策略，然后从下拉列表中选择“禁用”

   ![禁用策略](./media/tutorial-security-policy/disable-policy.png)

1. 选择“保存” 。

   > [!NOTE]
   > 禁用策略更改可能需要长达 12 小时才会生效。

## <a name="next-steps"></a>后续步骤
本页介绍了安全策略。 如需相关信息，请参阅以下页面：

- [了解如何使用 PowerShell 设置策略](../governance/policy/assign-policy-powershell.md) - 
- [了解如何在 Azure Policy 中编辑安全策略](../governance/policy/tutorials/create-and-manage.md) - 
- [了解如何使用 Azure Policy 跨订阅或在管理组上设置策略](../governance/policy/overview.md)。
- [了解如何对管理组中的所有订阅启用安全中心](onboard-management-group.md)