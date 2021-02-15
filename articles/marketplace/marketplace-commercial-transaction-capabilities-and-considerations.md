---
title: Microsoft 商业市场交易功能
description: 本文介绍商业市场交易选项的定价、计费、开票和支出注意事项。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 59768e4fc04613a5ecac6a76059382499b7aaa86
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986456"
---
# <a name="commercial-marketplace-transact-capabilities"></a>商业市场交易功能

本文介绍了 Microsoft 商业应用商店的定价、计费、开票和支出注意事项。

## <a name="transactions-by-listing-option"></a>按列表选项列出的事务

发布者或 Microsoft 负责管理商业市场中的产品/服务的软件许可证交易。 你为产品/服务选择的列表选项决定了管理交易的人员。 有关每个发布选项的可用性和说明，请参阅 [列表选项简介](determine-your-listing-type.md)

### <a name="contact-me-free-trial-and-byol-options"></a>与我联系、免费试用和 BYOL 选项

发布者可以选择 " _联系我_ " 和 " _免费试用版_"，这些选项用于促销和用户购置目的。 对于某些产品/服务类型，发布者可以选择 "自带许可证 (BYOL") 选项，使客户能够使用他们直接购买的许可证购买你的产品/服务的订阅。 使用这些选项，Microsoft 不直接参与发布者的软件许可证交易，因此没有相关的交易费。

发布者负责支持软件许可证事务的所有方面。 这包括但不限于订单、履单、计量、计费、开票、支付和收集。 通过 "联系我" 列表选项，发布者将保留从客户那里收集的100% 的出版商软件许可费用。

### <a name="transact-publishing-option"></a>交易发布选项

选择通过 Microsoft 进行销售可以利用 Microsoft 商业功能，并提供从发现和评估到购买与实现的端到端体验。 事务的产品/服务是 Microsoft 为发布者提供软件许可证的资金。 事务产品/服务根据现有的 Microsoft 订阅或信用卡计费，允许 Microsoft 代表发布者托管云 marketplace 交易。

在合作伙伴中心创建新产品/服务时，将要选择交易选项。 仅在你的产品/服务类型适合交易时，才会显示此选项。

## <a name="transact-overview"></a>交易概述

使用 "transact-sql" 选项时，Microsoft 允许向客户的 Azure 订阅销售第三方软件和某些产品/服务类型。 选择产品/服务的定价模型时，发布者必须考虑基础结构费用计费和你自己的软件许可费用。

目前以下产品/服务类型支持交易发布选项：

- 虚拟机
- Azure 应用程序
- SaaS 应用程序

### <a name="billing-infrastructure-costs"></a>计费基础结构成本

对于 **虚拟机** 和 **azure 应用程序**，azure 基础结构使用费用会按客户的 azure 订阅计费。 在客户的发票上，基础结构使用费与软件提供商的许可费分开计价和显示。

对于 SaaS 应用，发布者必须将 Azure 基础结构使用费和软件许可费视为单一费用项。  它表示为客户的固定费用。 直接对发布服务器进行 Azure 基础结构使用和计费。 客户将无法看到实际的基础结构使用费。 发布者通常选择将 Azure 基础结构使用费捆绑到其软件许可证定价中。 软件许可费用不按流量计费，也不基于用户消耗。

## <a name="pricing-models"></a>定价模型

根据所使用的事务选项，订阅费用如下所示：

- **立即获取 (免费)** –免费获取软件许可证。 使用免费产品/服务不会向客户收取 Azure Marketplace 费用。 免费产品/服务不能转换为付费产品/服务。 客户必须订购付费产品/服务。
- **自带许可 (BYOL)** - 直接在发布者和客户之间管理所有适用的软件许可证费用。 Microsoft 仅收取 Azure 基础结构使用费。 如果产品市场中列出了产品/服务，则在商业市场上获得访问或使用该产品/服务的客户不会收取商业 marketplace 费用。
- **订阅定价** –软件许可证费用以按固定费率或按座位计费的每月或每年、定期订阅费用形式提供。 定期客户取消或未使用的服务不会按比例计费。 如果客户在订阅期的中间升级或降级订阅，则可能会按比例计费。
- **基于使用情况的定价** –对于 Azure 虚拟机产品/服务，将根据其对产品/服务的使用程度向客户收费。 对于虚拟机映像，客户按发布者设置的每小时 Azure Marketplace 费用收费，用于从 VM 映像部署的虚拟机。 不同虚拟机大小的小时费用可能一致也可能不同。 不足一小时按分钟收费。 计划按月计费。
- **按流量计费** –对于 Azure 应用程序产品和 SaaS 产品/服务，发布者可以使用 [Marketplace 计量服务](./partner-center-portal/marketplace-metering-service-apis.md) 根据所选的计量维度来计费消耗量。 例如，处理的带宽、票证或电子邮件。 发布者可以为每个计划定义一个或多个计量维度。 发布者负责跟踪每个客户的使用情况，其中每个指标都是在产品/服务中定义的。 事件应该在一小时内报告给 Microsoft。 Microsoft 将在适用帐单周期内基于发布者报告的使用情况信息向客户收费。
- **免费试用** –从30天到6个月（具体取决于产品/服务类型）的软件许可证免费。 如果发布者在同一产品/服务内的多个计划中提供免费试用版，客户可以切换到其他计划的免费试用版，但不会重新启动。 对于虚拟机产品/服务，客户需要支付 Azure 基础结构成本，以便在试用期期间使用产品/服务。 试用期到期后，将根据标准费率自动向客户收取最近一次的计划，除非在试用期结束之前取消。

> [!NOTE]
> 根据使用解决方案后的使用量计费的产品/服务不支持退款。

要更改与产品/服务关联的使用费用的发布者，请首先从商业应用商店中删除该产品/服务中的产品/服务 (或特定计划) 。 应根据 [Microsoft 发行者协议](https://go.microsoft.com/fwlink/?LinkID=699560)的要求执行删除操作。 然后，发布者可以在产品/服务) 中发布新产品/服务 (或计划，包括新的使用费用。 有关删除产品/服务或计划的信息，请参阅 [停止销售产品/服务或计划](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan)。

### <a name="free-contact-me-and-bring-your-own-license-byol-pricing"></a>免费，与我联系，自带许可证 (BYOL) 定价

当使用 "立即获取产品" (免费) 、与我联系或 BYOL 选项发布产品/服务时，Microsoft 不会扮演为你的软件许可费用提供销售事务的角色。 与列表和免费试用版发布选项一样，发布者将保留100% 的软件许可证费用。

### <a name="usage-based-and-subscription-pricing"></a>基于使用情况和订阅定价

在发布产品/服务或订阅交易时，Microsoft 将提供技术和服务来处理软件许可证购买、退货和退款。 在这种情况下，发布者出于上述目的授权 Microsoft 充当代理人。 发布者允许 Microsoft 促成软件许可交易，同时保留自身的卖家、提供商、分销商和许可方的称号。

Microsoft 使客户能在 Microsoft 商业市场和你的最终用户许可协议的条款和条件约束下订购、许可和使用你的软件。 创建产品/服务时，必须提供自己的最终用户许可协议或选择[标准协定](./standard-contract.md)。

### <a name="free-software-trials"></a>免费软件试用

对于事务发布方案，你可以将软件许可证免费提供30到120天，具体取决于订阅。 此折扣功能不包括使用合作伙伴解决方案引致的 Azure 基础结构使用费用。

### <a name="private-offers"></a>专用产品/服务

除了使用产品/服务类型和计费模型将产品/服务转化为收益外，还可以交易专用产品/服务，采用经过协商的、特定于交易的定价或者自定义配置。 专用产品/服务受全部三个交易发布选项的支持。

此选项允许采用比公开提供的产品/服务更高或更低的定价。 你可以使用专用产品/服务进行折扣或向产品/服务添加高级版。 可以在产品/服务级别 allowlisting 其 Azure 订阅，使其可供一个或多个客户使用。

### <a name="commercial-marketplace-service-fees"></a>商业 marketplace 服务费用

当客户从商用 marketplace 购买你的产品/服务时，我们将收取20% 的标准存储服务费用。 有关此费用的详细信息，请参阅 [Microsoft 发行者协议](https://go.microsoft.com/fwlink/?LinkID=699560)第5c 部分。

对于发布到商业应用商店的某些事务产品/服务，你可能会有超过10% 的商店服务费用。 对于合格的产品/服务，它必须已由 Microsoft 指定为 Azure IP 共同销售 incentivized。 在每个日历月结束之前至少需要支付5个工作日，才能获得降低的 Marketplace 服务费用。 一旦满足了资格，就会在下个月的第一天生效，直到 Azure IP 共同销售 incentivized 的状态为 "丢失"。 有关 IP 共同销售资格的详细信息，请参阅 [共同销售状态的要求](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status)。

降低的 Marketplace 服务费用适用于 Azure IP 共同销售的 incentivized SaaS、Vm、托管应用以及通过商业市场提供的任何其他合格事务 IaaS 解决方案。 付费 SaaS 产品/服务与一个 Microsoft 团队应用相关联，或至少 Microsoft 365 两个 (Excel、PowerPoint、Word、Outlook 和 SharePoint) 的外接程序，并已发布到 Microsoft AppSource 也可获得此折扣。

### <a name="examples"></a>示例

**基于使用情况** 

基于使用情况的定价具有以下成本结构：

|你的许可证费用  | 每小时 $1.00   |
|---------|---------|
|Azure 使用费用（D1/1 核）    |   每小时 $0.14     |
|*由 Microsoft 向客户收费*    |  *每小时 $1.14*       |
||

在此场景中，Microsoft 按每小时 $1.14 收取所发布的 VM 映像的使用费用。

|Microsoft 收费  | 每小时 $1.14  |
|---------|---------|
|Microsoft 将许可证费用的 80% 支付给你|   每小时 $0.80     |
|Microsoft 保留许可证费用的 20%  |  每小时 $0.20       |
|Microsoft 保留 100% 的 Azure 使用费 | 每小时 $0.14 |
||

**自带许可 (BYOL)**

BYOL 的成本构成如下：

|你的许可证费用  | 许可证费用由你协商确定和收取  |
|---------|---------|
|Azure 使用费用（D1/1 核）    |   每小时 $0.14     |
|*由 Microsoft 向客户收费*    |  *每小时 $0.14*       |
||

在此场景中，Microsoft 按每小时 $0.14 收取所发布的 VM 映像的使用费用。

|Microsoft 收费  | 每小时 $0.14  |
|---------|---------|
|Microsoft 保留 Azure 使用费用    |   每小时 $0.14     |
|Microsoft 保留许可证费用的 0%   |  每小时 $0.00       |
||

**SaaS 应用订阅**

此选项必须配置为通过 Microsoft 进行销售，并且可以在每月或每年的基础上收取固定费用或按用户收费。 如果为 SaaS 产品/服务启用“通过 Microsoft 销售”选项，则成本构成如下：

| 你的许可证费用       | 100.00 美元/月  |
|--------------|---------|
| Azure 使用费用（D1/1 核）    | 直接向发布者而不是客户收费 |
| *由 Microsoft 向客户收费*    |  100.00 美元/月（发布者必须将所有产生的或者转嫁的基础结构费用纳入许可证费用中）  |
||

在这种情况下，Microsoft 向你收取 100.00 美元的软件许可证费用，并向发布者支付 80.00 美元。

在此方案中，Microsoft 收取 100.00 美元的软件许可证费用，并向发布者支付 90.00 美元：

|Microsoft 收费  | 100.00 美元/月  |
|---------|---------|
|Microsoft 将许可证费用的 80% 支付给你 <br> \* 对于所有符合资格的 SaaS 应用，Microsoft 将为你垫付许可证费用的 90%   |   80.00 美元/月 <br> \* 90.00 美元/月    |
|Microsoft 保留许可证费用的 20% <br> \* 对于所有符合资格的 SaaS 应用，Microsoft 保留许可证费用的 10%。  |  20.00 美元/月 <br> \* 10.00 美元     |

### <a name="customer-invoicing-payment-billing-and-collections"></a>客户开具发票、付款、计费和费用收集

**开具发票与付款** - 可以使用客户的首选发票开具方法来递送订阅或 PAYGO 软件许可证费用。

**企业协议** - 如果客户的首选发票开具方法是 Microsoft 企业协议，将使用此方法以逐项列出费用的形式对软件许可证进行计费，与任何特定于 Azure 的使用费分开。

**信用卡和每月发票** - 客户还可以使用信用卡和每月发票进行付款。 在这种情况下，将像企业协议方案一样对软件许可证进行计费：采用逐项列出费用的形式并与任何特定于 Azure 的使用费分开。

**免费信用额度和 Azure 预付款** –某些客户选择使用 azure 预付款预付 azure (之前称为货币承诺) 在企业协议中，或者提供免费信用额度用于 Azure。 尽管可以这些信用额度可用于支付 Azure 使用情况，但它们不能用于支付发布者软件许可证费用。

**计费和集合** –发布者软件许可证计费使用客户选择的发票方法，并遵循发票时间线。 将对没有企业协议的客户就市场软件许可证按月计费。 通过每季度显示的发票对具有企业协议的客户按月计费。

选择订阅或即用即付定价模型时，Microsoft 将充当发布者的代理，并且负责计费、付款和费用收集的所有方面。

### <a name="publisher-payout-and-reporting"></a>发布者付款和报表

Microsoft 作为代理收集的所有软件许可费用将收取 20% 的手续费（另行指定的除外），且会在发布者付款时扣除。

客户通常使用企业协议或支持信用卡的即用即付协议进行购买。 协议类型确定计费、发票、费用收集和付款时间。

>[!NOTE]
>在合作伙伴中心的“分析”部分可以找到交易发布选项的所有报告和见解。

#### <a name="billing-questions-and-support"></a>计费问题和支持

有关详细信息和法律政策，请参阅 (合作伙伴中心) 中提供的 [Microsoft 发布者协议](https://go.microsoft.com/fwlink/?LinkID=699560) 。

若在计费方面遇到问题且需要帮助，请联系[商业市场发布者支持](https://aka.ms/marketplacepublishersupport)。

## <a name="transact-requirements"></a>交易要求

本部分介绍不同产品/服务类型的交易要求。

### <a name="requirements-for-all-offer-types"></a>所有产品/服务类型的要求

- 无论产品/服务采用怎样的定价模型，交易发布选项都需要使用 Microsoft 帐户和财务信息。
- 必需的财务信息包括支出帐户和计税配置文件。
- 发布者必须位于 [受支持的国家或地区](sell-from-countries.md)。

有关设置这些帐户的详细信息，请参阅 [在合作伙伴中心管理商业 marketplace 帐户](partner-center-portal/manage-account.md)。

### <a name="requirements-for-specific-offer-types"></a>特定产品/服务类型的要求

交易发布选项仅适用于以下市场产品/服务类型：

- **Azure 虚拟机** –从免费、自带许可证或基于使用情况的定价模型中进行选择，并在服务级别定义的计划提供。 在客户的 Azure 帐单上，Microsoft 将发布者软件许可证费用与隐含的 Azure 基础结构费用分开显示。 Azure 基础结构费用取决于发布者软件使用情况。

- **Azure 应用程序：解决方案模板或托管应用** –必须预配一个或多个虚拟机，并提取虚拟机定价的总和。 对于单个计划的托管应用，可以选择固定费率每月订阅作为定价模型，而不是虚拟机定价。 在部分情况下，Azure 基础结构使用费将与软件许可证费用分开，单独传递给客户，但位于相同的账单上。 但是，如果你配置了托管应用产品/服务以实现 ISV 基础结构费用，则会向发布者计费 Azure 资源，并且客户会收到包含基础结构、软件许可证和管理服务成本的固定费用。

- **Saas 应用程序** -必须是多租户解决方案，使用 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 进行身份验证，并与 [SaaS 履单 api](partner-center-portal/pc-saas-fulfillment-api-v2.md)集成。 Azure 基础结构的使用情况是直接 (合作伙伴) 进行管理和计费的，因此，你必须将 Azure 基础结构使用费和软件许可费用视为单个成本项。 有关详细指南，请参阅 [在商业应用商店中创建新的 SaaS 产品/服务](./create-new-saas-offer.md)。

## <a name="next-steps"></a>后续步骤

- 查看发布选项中按套餐类型划分的资格要求，以便最终确定套餐的选择和配置。
- 有关解决方案如何映射到产品/服务类型和配置的示例，请参阅按在线商店发布模式。