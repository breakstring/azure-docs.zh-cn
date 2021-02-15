---
title: 为 Microsoft 商用 marketplace 规划托管服务产品/服务
description: 如何在 Microsoft 合作伙伴中心使用商用 Marketplace 计划为 Azure Marketplace 计划新的托管服务产品/服务。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.date: 12/23/2020
ms.openlocfilehash: f096e53f8054039f361bde1c5f2adffac615c53d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371934"
---
# <a name="how-to-plan-a-managed-service-offer-for-the-microsoft-commercial-marketplace"></a>如何为 Microsoft 商用 marketplace 规划托管服务产品/服务

本文介绍了通过合作伙伴中心向 Microsoft 商业应用商店发布托管服务产品/服务的要求。

托管服务是 Azure Marketplace 产品/服务，可通过 Azure Lighthouse 实现跨租户和多租户管理。 若要了解详细信息，请参阅 [什么是 Azure Lighthouse？](../lighthouse/overview.md) 当客户购买托管服务产品/服务时，他们可以委派一个或多个订阅或资源组。 然后，可以使用 Azure Lighthouse 的 [azure 委托资源管理](../lighthouse/concepts/azure-delegated-resource-management.md) 功能来处理这些资源。

## <a name="eligibility-requirements"></a>资格要求

若要发布托管服务产品/服务，你必须在云平台中获得金牌或银 Microsoft 资格。 此资格认证向客户展示你的专业技能。 有关详细信息，请参阅 [Microsoft 合作伙伴网络胜任度](https://partner.microsoft.com/membership/competencies)。

产品/服务必须满足在 Azure Marketplace 上发布的所有适用的 [商业市场认证策略](/legal/marketplace/certification-policies) 。

## <a name="customer-leads"></a>潜在顾客

您必须将产品/服务连接到客户关系管理 (CRM) 系统以收集客户信息。 系统将要求客户允许共享其信息。 这些客户详细信息以及提供程序的名称、ID 和在线商店，将被发送到已配置的 CRM 系统。 商业应用商店支持不同类型的 CRM 系统，以及使用 Azure 表或使用电源自动配置 HTTPS 终结点的选项。

你可以在创建产品/服务或之后随时添加或修改 CRM 连接。 有关详细指南，请参阅 [商业 marketplace 产品/服务的客户领导](partner-center-portal/commercial-marketplace-get-customer-leads.md)。

## <a name="legal-contracts"></a>法律协定

在合作伙伴中心的 "属性" 页中，系统将要求你提供产品/服务的使用 **条款和条件** 。 你可以直接在 "合作伙伴中心" 中输入你的条款，或提供可找到这些条款的 URL。 在购买产品/服务之前，客户将需要接受这些条款和条件。

## <a name="offer-listing-details"></a>产品/服务列表详细信息

当你在合作伙伴中心创建托管服务产品/服务时，你将输入文本、图像、文档和其他产品/服务详细信息。 这是客户在 Azure Marketplace 中发现你的产品/服务时可看到的内容。 请参阅以下示例：

:::image type="content" source="media/example-managed-service.png" alt-text="说明托管服务产品在 Azure Marketplace 上的显示方式。":::

**拨出说明**

1. 徽标
1. 名称
1. 简短说明
1. 类别
1. 法律合同和隐私策略
1. 说明
1. 屏幕截图/视频
1. 有用链接

以下示例演示了产品/服务列表在 Azure 门户中的显示方式：

:::image type="content" source="media/example-managed-service-azure-portal.png" alt-text="说明了此产品/服务在 Azure 门户中的显示方式。":::

**拨出说明**

1. 名称
2. 说明
3. 有用链接
4. 屏幕截图/视频

> [!NOTE]
> 如果产品/服务的语言不是英语，则产品/服务列表可以采用这种语言，但说明必须以英语短语 "此服务可使用的产品/服务 &lt; 内容>" 提供。 你还可以提供与产品/服务列表详细信息中所用语言不同的支持文档。

为了帮助更轻松地创建你的产品/服务，请提前准备其中一些项。 除非另有说明，否则需要以下各项。

**名称**：此内容将显示为商业应用商店中的产品/服务列表的标题。 该名称可以是商标字。 它不能包含表情符号 (，除非它们是商标和版权符号) 并且必须限制为50个字符。

**搜索结果摘要**：说明产品/服务的用途或目标，以100个字符或更少。 此摘要用于商业应用商店中列出的搜索结果。 它不应与标题完全相同。 请考虑包含 top SEO 关键字。

**简短说明**：提供产品/服务的简短说明 (最多256个字符) 。 它将显示在 Azure 门户中的产品/服务列表中。

**说明**：以3000个字符或更少的时间来描述你的产品/服务。 此说明将显示在 "商业应用商店" 列表中。 考虑包括价值主张、关键优势、类别或行业关联以及任何必要的披露。

下面是有关如何撰写描述的一些提示：

* 在前几句话中清晰描述你的产品/服务的价值，包括：
    * 从该产品/服务中受益的用户类型。
    * 客户需要或颁发服务地址。
* 请记住，前几句话可能会显示在搜索结果中。
* 使用行业特定的词汇。

您可以使用 HTML 标记来设置说明格式。 有关 HTML 格式设置的信息，请参阅 [商业市场提供说明中支持的 html 标记](./supported-html-tags.md)。

**隐私策略链接**：提供在网站上托管的隐私策略的 URL。 你需要负责确保你的产品/服务符合隐私法律和法规，并提供有效的隐私策略。

**有用的链接** (可选) ：上传有关产品/服务的补充联机文档。

**联系人信息**：在公司中提供两个人的姓名、电子邮件地址和电话号码 (你可以) 其中之一：支持联系人和工程联系人。 我们将使用此信息与你的产品/服务进行沟通。 此信息不会向客户显示，但可以提供给云解决方案提供商 (CSP) 合作伙伴

**支持 url** (可选) ：如果你有 Azure 全球客户和/或 azure 政府客户的支持网站，请提供这些 url。

**Marketplace 媒体–徽标**：提供适用于你的产品/服务的大徽标徽标的 PNG 文件。 合作伙伴中心将使用它来创建中等徽标和小徽标。 稍后可以选择将这些徽标替换为不同的图像。

* 在 Azure Marketplace 上的产品/服务列表中，大徽标 (从 216 x 216 到 350 x 350 px) 出现。
* 创建新资源时，会显示中等徽标 (90 x 90 px) 。
* 小型徽标 (48 x 48 px) 用于 Azure Marketplace 搜索结果。

请遵守徽标适用的下述准则：

* 请确保图像未延伸。
* Azure 设计具有简单的调色板。 限制徽标上的主要和次要颜色数。
* Azure 门户颜色为白色和黑色。 不要将它们用作徽标的背景。 建议简单的主要颜色使徽标突出。
* 如果使用透明背景，请确保徽标和文本不是白色、黑色或蓝色。
* 徽标的外观应为 "平面"。 避免渐变。 不要在徽标上放置文本，即使是公司或品牌名称也不可以。

**Marketplace 媒体–屏幕截图** (可选) ：添加最多五个图像，用于演示产品/服务的工作方式。 所有图像的大小必须为 1280 x 720 像素。PNG 格式。

**Marketplace 媒体–视频** (可选) ：最多上传5个视频来演示产品。 视频必须托管在 YouTube 或 Vimeo 上，并且缩略图 (1280 x 720 PNG 文件) 。

## <a name="preview-audience"></a>预览版受众

预览观众可以在发布到 Azure Marketplace 之前访问你的产品/服务，以便对其进行测试。 在合作伙伴中心的 " **预览观众** " 页上，可以定义受限的预览受众。

> [!NOTE]
> 预览受众不同于私有计划。 私有计划仅适用于所选的特定群体。 这使您可以与特定客户协商自定义计划。

可以将邀请发送到 Microsoft 帐户 (MSA) 或 Azure Active Directory (Azure AD) 电子邮件地址。 手动添加最多10个电子邮件地址，或使用 .csv 文件最多导入20个电子邮件地址。 如果你的产品/服务已处于活动阶段，你仍可以定义预览受众来测试产品/服务的任何更改或更新。

## <a name="plans-and-pricing"></a>规划和定价

托管服务提供至少需要一个计划。 计划定义解决方案范围、限制和关联定价（如果适用）。 你可以为你的产品/服务创建多个计划，为你的客户提供不同的技术和定价选项。 有关计划（包括私有计划）的一般指南，请参阅 [适用于商业 marketplace 产品/服务的计划和定价](plans-pricing.md)。

托管服务仅支持一个定价模型： **自带许可证 (BYOL)**。 这意味着你将直接向客户收取费用，并且 Microsoft 不会向你收取任何费用。

## <a name="next-steps"></a>后续步骤

* [创建托管服务产品](./create-managed-service-offer.md)
* [套餐列出最佳做法](./gtm-offer-listing-best-practices.md)