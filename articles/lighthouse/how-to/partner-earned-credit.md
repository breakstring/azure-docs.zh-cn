---
title: 链接合作伙伴 ID 以跟踪对委派资源的影响
description: 了解如何关联你的合作伙伴 ID 以接收通过 Azure Lighthouse 管理的客户资源的合作伙伴获得的信用 (PEC) 。
ms.date: 02/12/2021
ms.topic: how-to
ms.openlocfilehash: 4c18aae38570ab3fd84df7d45fb18e35404158be
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372087"
---
# <a name="link-your-partner-id-to-track-your-impact-on-delegated-resources"></a>链接合作伙伴 ID 以跟踪对委派资源的影响 

如果你是 [Microsoft 合作伙伴网络](https://partner.microsoft.com/)的成员，则可以将合作伙伴 ID 链接到用于管理委派的客户资源的凭据，从而允许 Microsoft 识别和识别驱动 Azure 客户成功的合作伙伴。 此链接还允许 [CSP (云解决方案提供商) ](/partner-center/csp-overview) 合作伙伴接收适用于 [托管 (服务的合作伙伴获得的信用额度) ](/partner-center/partner-earned-credit) ，适用于已 [签署 Microsoft 客户协议 (MCA) ](/partner-center/confirm-customer-agreement) 的客户，并 [在 Azure 计划下](/partner-center/azure-plan-get-started)。

若要获得 Azure Lighthouse 活动的识别，需要在管理租户中将 [MPN ID](../../cost-management-billing/manage/link-partner-id.md) 与至少一个用户帐户链接，并确保链接的帐户有权访问你的每个载入订阅。

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>在加入新客户时关联你的合作伙伴 ID

如果适用) ，请使用以下过程链接你的合作伙伴 ID (并启用合作伙伴获得的信用额度。 若要完成这些步骤，需要了解 [MPN 合作伙伴 ID](/partner-center/partner-center-account-setup#locate-your-mpn-id) 。 请务必使用合作伙伴配置文件中显示的“关联 MPN ID”。

为简单起见，我们建议在租户中创建一个服务主体帐户，将其链接到 **关联的 MPN ID**，然后向其授予对你所登记的每个客户的访问权限， [该内置角色适用于 PEC](/partner-center/azure-roles-perms-pec)。

1. 在管理租户中[创建服务主体用户帐户](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)。 在此示例中，我们将使用此服务主体帐户的名称 *提供程序自动化帐户* 。
1. 使用该服务主体帐户， [链接到](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) 管理租户中关联的 MPN ID。 只需执行此操作一次。
1. 当你 [使用 ARM 模板](onboard-customer.md) 或 [托管服务产品/服务](publish-managed-services-offers.md)来加入客户时，请确保加入授权，其中包含提供程序自动化帐户，作为具有 [适用于 PEC 的 Azure 内置角色](/partner-center/azure-roles-perms-pec)的用户。

按照这些步骤操作，你管理的每个客户租户将与你的合作伙伴 ID 相关联。 提供程序自动化帐户无需在客户租户中进行身份验证或执行任何操作。

:::image type="content" source="../media/lighthouse-pal.jpg" alt-text="显示合作伙伴 ID 链接过程与 Azure Lighthouse 的关系图。":::

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>将你的合作伙伴 ID 添加到之前载入的客户

如果已载入客户，你可能不想要执行其他部署来添加提供程序自动化帐户服务主体。 相反，你可以使用已有权在该客户的租户中工作的用户帐户链接 **关联的 MPN ID** 。 确保该帐户已被授予可 [用于 PEC 的 Azure 内置角色](/partner-center/azure-roles-perms-pec)。

将帐户链接到管理租户中 [关联的 MPN ID](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id) 后，你将能够跟踪对该客户的影响的识别。

## <a name="confirm-partner-earned-credit"></a>确认合作伙伴获得的信用额度

可以 [在 Azure 门户中查看 pec 详细信息](/partner-center/partner-earned-credit-explanation#azure-cost-management) ，并确认哪些成本已获得 pec 权益。 请记住，PEC 仅适用于已签署 MCA 并在 Azure 计划下的 CSP 客户。

如果按照上述步骤操作，但未看到预期的关联，请在 Azure 门户中打开支持请求。

你还可以使用 [合作伙伴中心 SDK](/partner-center/develop/get-invoice-unbilled-consumption-lineitems) 并筛选 `rateOfPartnerEarnedCredit` 到以自动对订阅进行 PEC 验证。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Microsoft 合作伙伴网络](/partner-center/mpn-overview)。
- 了解 [如何计算和支付 PEC](/partner-center/partner-earned-credit-explanation)。
- 将[客户](onboard-customer.md)加入 Azure Lighthouse。