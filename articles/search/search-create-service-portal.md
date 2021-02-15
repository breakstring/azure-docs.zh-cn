---
title: 在门户中创建搜索服务
titleSuffix: Azure Cognitive Search
description: 本门户快速入门介绍如何在 Azure 门户中设置 Azure 认知搜索资源。 选择资源组、区域以及 SKU 或定价层。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/23/2021
ms.openlocfilehash: 57867cc4fb539b07fc1e4117f6e956078c41e2c6
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746690"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>快速入门：在门户中创建 Azure 认知搜索服务

[Azure 认知搜索](search-what-is-azure-search.md)是用于将全文搜索体验添加到自定义应用的 Azure 资源。 可轻松地将其与其他提供数据或额外处理的 Azure 服务集成，与网络服务器上的应用集成，或与其他云平台上运行的软件集成。

本文介绍如何在 [Azure 门户](https://portal.azure.com/)中创建搜索服务。

[![动画 GIF](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

更喜欢 PowerShell？ 使用 Azure 资源管理器[服务模板](https://azure.microsoft.com/resources/templates/101-azure-search-create/)。 有关如何入门的帮助，请参阅[使用 PowerShell 管理 Azure 认知搜索](search-manage-powershell.md)。

## <a name="before-you-start"></a>开始之前

以下服务属性在服务生存期内是固定的，更改任何属性都需要新的服务。 因为它们是固定的，所以在填写每个属性时，请考虑以下与用法相关的注意事项：

* 服务名称变为 URL 终结点的一部分（[查看提示](#name-the-service)，了解有用的服务名称）。
* [服务层](search-sku-tier.md)会影响计费，应设置容量上限。 某些功能在免费层上不可用。
* 服务区域可能决定某些方案的可用性。 如果需要[高安全性功能](search-security-overview.md)或 [AI 扩充](cognitive-search-concept-intro.md)，则需要在与其他服务相同的区域中创建 Azure 认知搜索，或在提供相关功能的区域中进行创建。 

## <a name="subscribe-free-or-paid"></a>订阅（免费或付费）

[打开免费的 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)并使用免费信用额度试用付费 Azure 服务。 信用额度用完后，请保留帐户并继续使用免费的 Azure 服务，如网站。 除非显式更改设置并要求付费，否则不会对信用卡收取任何费用。

还可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。 MSDN 订阅每月提供可用来试用付费版 Azure 服务的信用额度。 

## <a name="find-azure-cognitive-search"></a>查找 Azure 认知搜索

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 单击左上角的加号（“+创建资源”）。

1. 使用搜索栏查找“Azure 认知搜索”，或通过“Web” > “Azure 认知搜索”导航到资源 。

:::image type="content" source="media/search-create-service-portal/find-search3.png" alt-text="在门户中创建资源" border="false":::

## <a name="choose-a-subscription"></a>选择订阅

如果有多个订阅，请选择一个用于搜索服务。 如果要实现[双重加密](search-security-overview.md#double-encryption)或依赖于托管服务标识的其他功能，请选择与用于 Azure Key Vault 或对其使用托管标识的其他服务相同的订阅。

## <a name="set-a-resource-group"></a>设置资源组

资源组是用于保存 Azure 解决方案相关资源的容器。 它对于搜索服务而言是必需的。 它还可用于统一管理资源（包括成本）。 一个资源组可以包含一个服务，也可以包含同时使用的多个服务。 例如，如果使用 Azure 认知搜索为 Azure Cosmos DB 数据库编制索引，则可以将这两个服务纳入同一个资源组进行管理。 

若不将资源合并到单个组中，或现有资源组中包含的资源用于不相关的解决方案，请新建一个仅用于 Azure 认知搜索资源的资源组。 

:::image type="content" source="media/search-create-service-portal/new-resource-group.png" alt-text="创建新的资源组" border="false":::

随着时间的变化，你可以统一跟踪当前成本和预计成本，或者查看单个资源的费用。 以下屏幕截图显示了在将多个资源合并到一个组时，可以看到的成本信息的种类。

:::image type="content" source="media/search-create-service-portal/resource-group-cost-management.png" alt-text="在资源组级别管理成本" border="false":::

> [!TIP]
> 资源组可以简化清理，因为删除某个组会删除其中的所有服务。 对于使用多个服务项目的原型，将它们放在同一资源组中可在项目结束后更加轻松地进行清理。

## <a name="name-the-service"></a>为服务命名

在“实例详细信息”中的“URL”字段内提供服务名称。 该名称是 URL 终结点的一部分，API 调用针对此终结点发出：`https://your-service-name.search.windows.net`。 例如，如果希望终结点为 `https://myservice.search.windows.net`，则输入 `myservice`。

服务名称要求：

* 它必须在 search.windows.net 命名空间中唯一
* 它的长度必须为 2 到 60 个字符。
* 必须使用小写字母、数字或短划线（“-”）
* 前 2 个字符或最后一个字符不能为短划线（“-”）
* 不能在任何位置使用连续的短划线（“--”）

> [!TIP]
> 如果你认为今后会用到多个服务，我们建议根据命名约定在服务名称中包含区域（或位置）。 同一区域中的服务可以免费交换数据，因此，如果 Azure 认知搜索位于美国西部，而你在美国西部还有其他服务，则在决定如何合并或附加资源时，使用类似于 `mysearchservice-westus` 的名称就无需导航到属性页。

## <a name="choose-a-location"></a>选择位置

Azure 认知搜索在大多数区域中可用。 支持的区域列表可在[定价页](https://azure.microsoft.com/pricing/details/search/)中找到。

> [!Note]
> 印度中部和阿拉伯联合酋长国北部目前无法提供新服务。 对于已在这些区域中的服务，你可以无限制地纵向扩展，并且你的服务在该区域是完全受支持的。 这些限制是临时的，仅限于新服务。 如果这些限制不再适用，我们将删除此说明。
>
> 双重加密仅在特定区域中可用。 有关详细信息，请参阅[双重加密](search-security-overview.md#double-encryption)。

### <a name="requirements"></a>要求

 如果使用 AI 扩充，请在认知服务所在的相同区域中创建搜索服务。 *将 Azure 认知搜索和认知服务归置在同一区域中是 AI 扩充的必要条件*。

 具有业务连续性和灾难恢复 (BCDR) 要求的客户应在[区域对](../best-practices-availability-paired-regions.md#azure-regional-pairs)中创建其服务。 例如，如果你在北美运营，则可以为每个服务选择“美国东部和美国西部”或“美国中北部和美国中南部”。

### <a name="recommendations"></a>建议

如果使用多个 Azure 服务，请选择同时托管数据或应用程序服务的区域。 这样做可以最大程度地减少或避免出站数据的带宽费用（当服务位于同一区域时，出站数据没有任何费用）。

## <a name="choose-a-pricing-tier"></a>选择一个定价层

Azure 认知搜索当前以[多个定价层](https://azure.microsoft.com/pricing/details/search/)提供：免费、基本、标准或存储优化。 每个层都有自己的[容量和限制](search-limits-quotas-capacity.md)。 有关相关指南，请参阅[选择定价层](search-sku-tier.md)。

“基本”和“标准”是生产工作负荷的最常用选项，但大多数客户会从“免费”服务开始。 各个层之间的主要差别在于分区大小和速度，以及可创建的对象数限制。

请记住，创建服务后无法更改定价层。 如果需要更高或较低的层，必须重新创建该服务。

## <a name="create-your-service"></a>创建服务

提供所需的输入后，继续创建服务。 

:::image type="content" source="media/search-create-service-portal/new-service3.png" alt-text="查看并创建服务" border="false":::

服务在几分钟内即可部署。 可以通过 Azure 通知监视进度。 考虑将该服务固定到仪表板，以方便将来访问。

:::image type="content" source="media/search-create-service-portal/monitor-notifications.png" alt-text="监视和固定服务" border="false":::

## <a name="get-a-key-and-url-endpoint"></a>获取密钥和 URL 终结点。

除非使用门户访问新服务，否则以编程方式访问新服务需要提供 URL 终结点和身份验证 API 密钥。

1. 在“概览”页的右侧找到并复制 URL 终结点。

2. 在“密钥”页上，复制其中一个管理密钥（它们等同）。 在服务上创建、更新和删除对象需要管理员 API 密钥。 相反，查询键提供索引内容的读取访问权限。

   :::image type="content" source="media/search-create-service-portal/get-url-key.png" alt-text="包含 URL 终结点的服务概览页" border="false":::

基于门户的任务不需终结点和密钥。 门户已链接到具有管理员权限的 Azure 认知搜索资源。 有关门户演练，请从[快速入门：在门户中创建 Azure 认知搜索索引](search-get-started-portal.md)。

## <a name="scale-your-service"></a>扩展服务

预配服务后，可以对其进行扩展以满足需求。 如果为 Azure 认知搜索服务选择了“标准”层，则可以采用两个维度扩展服务：副本和分区。 如果已选择基本层，仅可以添加副本。 如果预配了免费服务，则扩展不可用。

“分区”允许服务存储和搜索更多文档*_。

“副本”允许服务处理负载更高的搜索查询。

添加资源会增加每月账单费用。 可以通过[定价计算器](https://azure.microsoft.com/pricing/calculator/)来了解添加资源对账单明细的影响。 请记住，可以根据负载来调整资源。 例如，可以通过增加资源来创建完整的初始索引，在以后再将资源减少到与增量索引编制相适应的某个程度。

> [!Important]
> 一个服务必须具有[2 个用于只读 SLA 的副本和 3 个用于读/写 SLA 的副本](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

1. 在 Azure 门户中转到“搜索服务”页。
2. 在左侧导航窗格中，选择“设置”>“缩放”。
3. 使用滑块添加任一类型的资源。

:::image type="content" source="media/search-create-service-portal/settings-scale.png" alt-text="通过副本和分区添加容量" border="false":::

> [!Note]
> 所在的层级越高，每个分区的存储大小和速度就越高。 有关详细信息，请参阅[容量和限制](search-limits-quotas-capacity.md)。

## <a name="when-to-add-a-second-service"></a>何时添加第二个服务

大多数客户只使用定价层上预配的一个服务，以提供[适当的资源平衡](search-sku-tier.md)。 一个服务可以托管多个索引（但受制于[所选层的最大限制](search-capacity-planning.md)），各索引之间相互隔离。 在 Azure 认知搜索中，请求只能定向到一个索引，从而将从同一服务中的其他索引意外或故意检索数据的可能性降至最低。

尽管大多数客户只使用一个服务，但若有以下操作要求，则可能需要提供服务冗余：

+ [业务连续性和灾难恢复 (BCDR)](../best-practices-availability-paired-regions.md)。 Azure 认知搜索在发生服务中断时不提供即时故障转移。

+ [多租户体系结构](search-modeling-multitenant-saas-applications.md)有时会调用两个或更多服务。

+ 全局部署的应用程序可能需要在每个地理位置中使用搜索服务，以最大程度地减小延迟。

> [!NOTE]
> 在 Azure 认知搜索中，无法分离索引操作和查询操作；因此永远无需为分离的工作负荷创建多个服务。 查询索引时，始终是在创建该索引时所在的服务中查询（不能在一个服务中创建索引，然后将其复制到另一个服务）。

无需为实现高可用性添加第二个服务。 在同一服务中使用 2 个或更多个副本，便可实现查询的高可用性。 副本更新是连续的，这意味着当服务更新推出时，至少有一个副本能正常工作。有关运行时间的详细信息，请参阅[服务级别协议](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

## <a name="next-steps"></a>后续步骤

预配服务后，可以继续在门户中创建第一个索引。

> [!div class="nextstepaction"]
> [快速入门：在门户中创建 Azure 认知搜索索引](search-get-started-portal.md)

希望优化并节省云支出？

> [!div class="nextstepaction"]
> [使用成本管理开始分析成本](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)