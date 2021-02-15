---
title: 商业应用商店中的 Azure Active Directory 和事务 SaaS 产品
description: 了解 Azure Active Directory 如何使用 Microsoft 商业应用商店中的事务 SaaS 产品/服务。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 674f267d3d99dd22c1ae06b6d32587761d5983ce
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124911"
---
# <a name="azure-ad-and-transactable-saas-offers-in-the-commercial-marketplace"></a>商业应用商店中的 Azure AD 和事务 SaaS 产品

Microsoft 的基于云的标识和访问管理服务 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) 可帮助用户登录和访问内部和外部资源。 在 Microsoft 商用 marketplace 中，Azure AD 使事务 SaaS 对于所有人（包括出版商、买家和用户）而言都更简单、更安全。 使用 Azure AD，发布者可以自动将用户预配到其软件即服务 (SaaS) 应用程序，买家本身就可以管理这些已设置的用户。 

此外， [Azure AD 单一登录](../active-directory/manage-apps/what-is-single-sign-on.md) (SSO) 在用户登录到 Azure AD 中的应用时提供安全性和便利性。 更快的订婚和优化体验还能让买方和用户信心与发布者的 SaaS 应用程序的第一次交互。 这提供了一种正面的印象，可构建可见性并鼓励重复业务。

按照本文中的指南，你将有助于在商业应用商店中验证你的 SaaS 产品/服务。 有关认证的详细信息，请阅读详细的 [商业 marketplace 认证策略](/legal/marketplace/certification-policies#100-general)，包括 [特定于 SaaS](/legal/marketplace/certification-policies#1000-software-as-a-service-saas)的证书。

## <a name="before-you-begin"></a>开始之前

在合作伙伴中心 [创建 SaaS 产品/服务](./create-new-saas-offer.md) 时，可以从产品/服务列表中显示的一组特定列表选项中进行选择。 你的选择将决定你的产品/服务在商业应用商店中的事务处理方式。 通过 Microsoft 销售的产品/服务称为事务产品。 我们会代表你为所有事务产品/服务向客户收费。 如果你选择通过 Microsoft 进行销售，并以你的名义为我们托管事务 (" **是"** 选项) ，则已选择创建事务产品/服务，这篇文章适用于你。 建议你完全阅读。

如果你选择仅通过商业应用商店列出你的产品/服务，并独立 (" **无** ") 选项，则可以使用三个选项来了解潜在客户如何访问你的产品/服务：立即获取 (免费) 、免费试用版，并与我联系。 如果选择 " **立即获取" (免费)** 或 **免费试用版** ，则不需要本文。 有关详细信息，请参阅 [在商业应用商店中构建免费或试用 SaaS 产品/服务的登陆页面](./azure-ad-free-or-trial-landing-page.md) 。 如果选择 " **联系我** "，则不会有直接的发布者责任。 继续在合作伙伴中心创建产品/服务。

## <a name="how-azure-ad-works-with-the-commercial-marketplace-for-saas-offers"></a>Azure AD 如何与 SaaS 产品/服务的商业 marketplace 一起工作

Azure AD 可实现商业 marketplace 解决方案的无缝购买、履单和管理。 图1显示了发布者、买家和用户如何与购买和激活订阅进行交互。 它还展示了客户如何使用和管理他们从商业市场获取的 SaaS 应用程序。 在此图中，买方是从商业市场启动购买的 SaaS 应用程序用户。

如图1所示，当买家选择你的产品/服务时，他们将开始一系列工作流，其中包括购买、订阅和用户管理。 在此链中，你作为发布者负责某些要求，Microsoft 在关键点提供了支持。

**_图1：在商用 marketplace 中使用 SaaS 产品/服务的 Azure AD_* _

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow.png" alt-text="说明购买管理、订阅管理和可选的用户管理过程步骤。":::

以下各节提供了有关每个进程步骤的要求的详细信息。

## <a name="process-steps-for-purchase-management"></a>采购管理的处理步骤

此图显示了用于购买管理的四个过程步骤。

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-1-4.png" alt-text="说明购买管理、订阅管理和可选的用户管理过程步骤。":::

下表提供了有关购买管理过程步骤的详细信息。

| 处理步骤 | 发布者操作 | 发布服务器的建议或必需的 |
| ------------ | ------------- | ------------- |
| 1. 买家使用 Azure ID 标识登录到商业市场，并选择 SaaS 产品/服务。 | 不需要发布服务器操作。 | 不适用 |
| 2. 购买后，买家会在 Azure Marketplace 中选择 _ *配置帐户* *，或立即在 AppSource 中进行 **配置** ，这会将买方定向到发布者登录页面以获取此优惠。 购买者必须能够利用 Azure AD SSO 登录到发布服务器的 SaaS 应用程序，并且必须仅要求最小许可，无需 Azure AD 管理员批准。 | 为产品/服务设计 [登陆页面](azure-ad-transactable-saas-landing-page.md) ，使其 Azure AD 或 MICROSOFT 帐户 (MSA) 标识，并简化所需的任何其他设置或设置。 | 必需 |
| 3. 发布者请求 SaaS 履单 API 的购买详细信息。 | 使用从登陆页面的应用程序 ID 生成的 [访问令牌](./partner-center-portal/pc-saas-registration.md) ， [调用解析终结点](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) 来检索有关采购的详细信息。 | 必需 |
| 4. 通过 Azure AD 和 Microsoft Graph API，发布者可以收集在发布者的 SaaS 应用程序中预配买方所需的公司和用户详细信息。  | 分解 Azure AD 用户令牌以查找名称和电子邮件，或 [调用 MICROSOFT GRAPH API](/graph/use-the-api) 并使用委派的权限来 [检索](/graph/api/user-get) 有关登录用户的信息。 | 必需 |
||||

## <a name="process-steps-for-subscription-management"></a>订阅管理的过程步骤

下图显示了订阅管理的两个过程步骤。

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-5-6.png" alt-text="说明购买管理、订阅管理和可选的用户管理过程步骤。":::

下表介绍了有关订阅管理过程步骤的详细信息。

| 处理步骤 | 发布者操作 | 发布服务器的建议或必需的 |
| ------------ | ------------- | ------------- |
| 5. 发布服务器通过 SaaS 履单 API 管理 SaaS 应用程序的订阅。 | 通过 [SaaS 履单 api](./partner-center-portal/pc-saas-fulfillment-api-v2.md)处理订阅更改和其他管理任务。<br><br>此步骤需要访问令牌，如步骤3中所述。 | 必需 |
| 6. 使用按流量计费时，发布者将使用事件发送到计量服务 API。 | 如果 SaaS 应用使用基于使用情况的计费，请通过 [Marketplace 计量服务 api](./partner-center-portal/marketplace-metering-service-apis.md)进行使用通知。<br><br>此步骤需要访问令牌，如步骤3中所述。 | 需要进行计量 |
||||

## <a name="process-steps-for-user-management"></a>用户管理的过程步骤

下图显示了用户管理的三个过程步骤。

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-7-9.png" alt-text="说明购买管理、订阅管理和可选的用户管理过程步骤。":::

过程步骤7到步骤9是可选的用户管理过程步骤。 它们为支持 Azure AD 单一登录 (SSO) 的出版商提供额外的好处。 下表描述了有关用户管理过程步骤的详细信息。

| 处理步骤 | 发布者操作 | 发布服务器的建议或必需的 |
| ------------ | ------------- | ------------- |
| 7. 买方公司的 Azure AD 管理员可以选择通过 Azure AD 来管理用户和组的访问权限。 | 如果 Azure AD SSO 设置为用户 (步骤 9) ，则不需要发布服务器操作。 | 不适用 |
| 8. Azure AD 预配服务在 Azure AD 与发布服务器的 SaaS 应用程序之间进行通信。 | [实现 SCIM 终结点](../active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md) ，以便在添加或删除用户时接收来自 Azure AD 的更新。 | 建议 |
| 9. 在授权和预配应用程序后，买方公司的用户可以使用 Azure AD SSO 登录到发布服务器的 SaaS 应用程序。 | [使用 AZURE AD SSO](../active-directory/manage-apps/what-is-single-sign-on.md) 使用户能够使用一个帐户登录到发布服务器的 SaaS 应用程序。 | 建议 |
||||

## <a name="next-steps"></a>后续步骤

- [在商业应用商店中构建事务 SaaS 产品/服务的登陆页面](azure-ad-transactable-saas-landing-page.md)
- [在商业应用商店中为免费或试用版 SaaS 服务构建登陆页面](azure-ad-free-or-trial-landing-page.md)
- [如何在商业应用商店中创建 SaaS 产品/服务](create-new-saas-offer.md)