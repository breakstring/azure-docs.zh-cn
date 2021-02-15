---
title: Azure Cosmos DB 中的保留容量以优化成本
description: 了解如何购买 Azure Cosmos DB 预留容量以节省计算成本。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 5c2d1d286572b21879742a1a9c6ab3975441373d
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602687"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>使用 Azure Cosmos DB 预留容量优化成本
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 预留容量可通过预付为期一年或三年的 Azure Cosmos DB 资源预留费用为你省钱。 使用 Azure Cosmos DB 预留容量，可以获得为 Cosmos DB 资源预配的吞吐量的折扣。 资源示例包括数据库和容器（表、集合和图）。

Azure Cosmos DB 预留容量可以大幅降低 Cosmos DB 成本，相比正常价格最多可以降低 65%，前提是提前承诺使用一年或三年的服务。 预留容量提供一种计费折扣，并且不会影响 Azure Cosmos DB 资源的运行时状态。

Azure Cosmos DB 预留容量涵盖为资源预配的吞吐量的费用。 但它不涵盖存储和网络费用。 购买预留后，不再像即用即付资费一样收取与预留属性相应的吞吐量费用。 预留相关详细信息，请参阅 [Azure 预留](../cost-management-billing/reservations/save-compute-costs-reservations.md)一文。

可从 [Azure 门户](https://portal.azure.com)购买 Azure Cosmos DB 预留容量。 通过[提前付款或按月付款](../cost-management-billing/reservations/prepare-buy-reservation.md)的方式为预留付款。 购买预留容量：

* 对于至少一个企业或个人订阅，你必须以即用即付费率作为所有者角色。  
* 对于企业订阅，必须在 [EA 门户](https://ea.azure.com)中启用“添加预留实例”。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。
* 对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理可以购买 Azure Cosmos DB 预留容量。

## <a name="determine-the-required-throughput-before-purchase"></a>在购买之前确定所需吞吐量

保留容量购买的大小应基于现有或即将部署 Azure Cosmos DB 资源每小时将使用的总吞吐量量（以小时为单位）。 例如：购买 30000 RU/s 保留容量（如果这是你的每小时使用模式）。 在此示例中，将使用即用即付费率对超过 30000 RU/s 的任何预配吞吐量进行计费。 如果在一小时内预配的吞吐量低于 30000 RU/秒，则该小时的额外预留容量将会浪费。

我们根据你的每小时使用情况模式计算购买建议。 会分析过去7、30和60天的使用情况，并购买保留容量购买，建议你节省费用。 您可以使用以下步骤在 Azure 门户中查看推荐的保留大小：

1. 登录到 [Azure 门户](https://portal.azure.com)。  

2. 选择 "**所有服务**  >  **保留**" "  >  **添加**"。

3. 从 " **购买预订** " 窗格中选择 " **Azure Cosmos DB**"。

4. 选择 " **建议** " 选项卡以查看建议的保留：

可以按以下属性筛选建议：

- **条款** (1 年或3年) 
- **计费频率** (每月或前) 
- **吞吐量类型** (RU/s 与多区域写入 RU/秒) 

此外，你可以将建议范围限定在单个资源组、单个订阅或整个 Azure 注册中。 

下面是一个示例建议：

:::image type="content" source="./media/cosmos-db-reserved-capacity/reserved-capacity-recommendation.png" alt-text="保留的容量建议":::

这种购买 30000 RU/秒预订的建议表明，在3年的保留期，30000 RU/秒预留大小将最大限度地节省费用。 在这种情况下，将根据 Azure Cosmos DB 使用量过去的30天来计算建议。 如果此客户预计过去30天的 Azure Cosmos DB 使用量代表将来使用，则可以通过购买 30000 RU/s 预订来最大限度地节省成本。

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>购买 Azure Cosmos DB 预留容量

1. 登录到 [Azure 门户](https://portal.azure.com)。  

2. 选择 "**所有服务**  >  **保留**" "  >  **添加**"。  

3. 从 " **采购预订** " 窗格中，选择 " **Azure Cosmos DB** " 以购买新预订。  

4. 填写必填字段，如下表所示：

   :::image type="content" source="./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png" alt-text="填写预留容量表":::

   |字段  |说明  |
   |---------|---------|
   |作用域   |   控制有多少订阅可以使用与预留关联的计费权益的选项。 它还控制将预留应用于特定订阅的方式。 <br/><br/>  如果选择“共享”，预留折扣将应用到计费上下文内任何订阅中运行的 Azure Cosmos DB 实例。 计费上下文基于 Azure 的注册方式。 对于企业客户，共享范围是注册范围，包括注册中的所有订阅。 对于即用即付客户，共享范围是由帐户管理员创建的即用即付费率的所有订阅。  <br/><br/>  如果选择“单个订阅”，预留折扣将应用到所选订阅中的 Azure Cosmos DB 实例。 <br/><br/> 如果选择 " **单个资源组**"，则预订折扣将应用于所选订阅中的 Azure Cosmos DB 实例和该订阅内的所选资源组。 <br/><br/> 购买预留容量后，可以更改预留范围。  |
   |订阅  |   用于支付 Azure Cosmos DB 预留容量费用的订阅。 所选订阅的付款方式用于对成本进行收费。 订阅必须是以下类型之一： <br/><br/>  企业协议 (产品/服务： BC-OP-NT-AZR-Ms-azr-0017p 或 BC-OP-NT-AZR-Ms-azr-0148p) ：对于企业订阅，将从注册的 Azure 预付款中扣除费用， (之前称为货币承诺) 余额，或者作为超额费用收费。 <br/><br/> 使用即用即付费率的单个订阅 (产品/服务： BC-OP-NT-AZR-Ms-azr-0003p 或 BC-OP-NT-AZR-0023P) ：对于使用即用即付费率的单个订阅，将对订阅上的信用卡或发票付款方式收取费用。    |
   | 资源组 | 将保留的容量折扣应用到的资源组。 |
   |术语  |   一年或三年。   |
   |吞吐量类型   |  吞吐量设置为 "请求单位"。 你可以为设置-单区域写入和多个区域写入购买预配吞吐量的预留。 吞吐量类型有两个值可供选择：每小时 100 RU/秒，100多区域每小时写入 RU/秒。|
   | 保留容量单位| 想预留的吞吐量的量。 可以通过确定每个区域的所有 Cosmos DB 资源（例如，数据库或容器）所需的吞吐量来计算此值。 然后，将其与 Cosmos 数据库关联的区域数相乘。 例如：如果有五个区域，每个区域 1 百万 RU/秒，则购买预留容量时应选 5 百万 RU/秒。 |


5. 填写该表单后，将计算购买保留容量所需的价格。 输出还会显示所选选项的折扣百分比。 下一步单击 **选择**

6. 在 " **购买预订** " 窗格中，查看预订的折扣和价格。 此预留价格适用于使用跨所有区域预配的吞吐量的 Azure Cosmos DB 资源。  

   :::image type="content" source="./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png" alt-text="保留的容量摘要":::

7. 选择 " **查看** " 和 " **立即购买**"。 购买成功后，会看到如下页面所示内容：

购买预留后，它立即应用到与预留期相符的所有现有 Azure Cosmos DB 资源。 如果还没有 Azure Cosmos DB 资源，则将在你部署符合预留期的新 Cosmos DB 实例时应用预留。 这两种情况的预留期均于成功购买后立即开始。

预留到期时，Azure Cosmos DB 实例会继续运行，按正常即用即付资费计费。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="next-steps"></a>后续步骤

预留折扣自动应用于与预留范围和属性相符的 Azure Cosmos DB 资源。 可以通过 Azure 门户、PowerShell、Azure CLI 或 API 更新预留的范围。

*  要了解预留容量折扣是如何应用到 Azure Cosmos DB 的，请参阅[了解 Azure 预留折扣](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md)。

* 若要了解有关 Azure 预留的详细信息，请参阅以下文章：

   * [什么是 Azure 预留？](../cost-management-billing/reservations/save-compute-costs-reservations.md)  
   * [管理 Azure 预留](../cost-management-billing/reservations/manage-reserved-vm-instance.md)  
   * [了解企业合约的预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)  
   * [了解即用即付订阅的预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
   * [合作伙伴中心 CSP 计划中的 Azure 预留](/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。