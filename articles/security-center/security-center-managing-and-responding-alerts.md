---
title: 在 Azure 安全中心管理安全警报 | Microsoft 文档
description: 本文档旨在帮助使用 Azure 安全中心功能来管理和响应安全警报。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: how-to
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2020
ms.author: memildin
ms.openlocfilehash: 3f319c35631f8c85cab4613df0f1c14f98356caa
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563341"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>在 Azure 安全中心管理和响应安全警报

> [!TIP]
> 此页上的信息是指 "安全警报" 页顶部的 "横幅" 中提供的新 (预览版) 警报体验。 
>
> :::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="带有新预览警报体验链接的横幅":::

本主题说明如何查看和处理安全中心的警报，并保护资源。

触发安全警报的高级检测仅适用于 Azure Defender。 有免费试用版可用。 若要升级，请参阅 [启用 Azure Defender](security-center-pricing.md#enable-azure-defender)。

## <a name="what-are-security-alerts"></a>什么是安全警报？
安全中心会自动收集、分析以及整合 Azure 资源、网络和所连合作伙伴解决方案（如，防火墙和终结点保护解决方案）的日志数据，检测真正的威胁并减少误报。 安全中心显示了一系列安全警报（按严重程度排序），并显示了快速调查问题所需的信息以及修复攻击的建议。

若要了解不同类型的警报，请参阅 [安全警报-参考指南](alerts-reference.md)。

有关安全中心如何生成警报的概述，请参阅 [Azure 安全中心如何检测和响应威胁](security-center-alerts-overview.md)。


## <a name="manage-your-security-alerts"></a>管理安全警报

1. 从安全中心的 "概述" 页上，选择页面顶部的 " **安全警报** " 磁贴，或侧栏中的链接。

    :::image type="content" source="media/security-center-managing-and-responding-alerts/overview-page-alerts-links.png" alt-text="从 Azure 安全中心的 &quot;概述&quot; 页获取 &quot;安全警报&quot; 页":::

    此时将打开 "安全警报" 页。

    :::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure 安全中心的安全警报列表":::

1. 若要筛选警报列表，请选择任何相关筛选器。 您可以选择通过 " **添加筛选器** " 选项添加进一步的筛选器。

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-small.png" alt-text="向警报视图添加筛选器" lightbox="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-large.png":::

    列表会根据所选的筛选选项进行更新。 筛选功能可能非常有用。 例如，假设正在调查系统中的潜在危害，需要处理过去 24 小时内发生的安全警报。


## <a name="respond-to-security-alerts"></a>响应安全警报

1. 在 " **安全警报** " 列表中，选择一个警报。 此时会打开一个侧窗格，其中显示警报和所有受影响的资源的说明。 

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-details-pane.png" alt-text="安全警报的迷你详细信息视图":::

    > [!TIP]
    > 在此侧窗格打开时，可以通过键盘上的向上和向下箭头快速查看警报列表。

1. 有关详细信息，请选择 " **查看完整详细信息**"。

    安全警报页面的左窗格显示有关安全警报的大致信息：标题、严重性、状态、活动时间、可疑活动的说明以及受影响的资源。 受影响的资源旁边是与资源相关的 Azure 标记。 在调查警报时，可以使用这些标记来推断资源的组织环境。

    右侧窗格包含“警报详细信息”选项卡，其中包含警报的更多详细信息，用于帮助你调查问题：IP 地址、文件、进程等。
     
    ![有关如何处理安全警报的建议](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    右侧窗格中还包含“执行操作”选项卡。使用此选项卡可以对安全警报执行其他操作。 操作，例如：
    - *缓解威胁* - 为此安全警报提供手动修正步骤
    - *防范将来的攻击* - 提供安全建议，帮助减少攻击面，提高安全状况，从而防范将来的攻击
    - *触发自动响应* - 提供可触发逻辑应用的选项，作为对此安全警报的响应
    - *禁止显示类似警报* -如果警报与组织无关，则提供禁止显示具有类似特征的未来警报的选项

    ![“执行操作”选项卡](./media/security-center-managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>请参阅

本文档介绍了如何查看安全警报。 请参阅以下页面，以获取相关材料：

- [配置警报抑制规则](alerts-suppression-rules.md)
- [自动执行对安全中心触发器的响应](workflow-automation.md)
- [安全警报 - 参考指南](alerts-reference.md)
