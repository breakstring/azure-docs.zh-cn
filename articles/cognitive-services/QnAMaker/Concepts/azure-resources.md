---
title: Azure 资源-QnA Maker
description: QnA Maker 使用多个 Azure 源，每个都有不同的用途。 了解如何单独使用这些功能，可以规划和选择正确的定价层，或者知道何时更改定价层。 了解如何结合使用它们，可以在出现问题时查找并解决问题。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: f46a0938ebb8d9fe7e032162120056dca96b9567
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979756"
---
# <a name="azure-resources-for-qna-maker"></a>适用于 QnA Maker 的 Azure 资源

QnA Maker 使用多个 Azure 源，每个都有不同的用途。 了解如何单独使用这些功能，可以规划和选择正确的定价层，或者知道何时更改定价层。 了解如何 _结合_ 使用它们，可以在出现问题时查找并解决问题。

## <a name="resource-planning"></a>资源规划

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

当你首次在原型阶段开发 QnA Maker 知识库时，有一种是用于测试和生产的单个 QnA Maker 资源。

在移动到项目的开发阶段时，应考虑以下事项：

* 您的知识库系统将包含多少种语言？
* 您需要在哪些区域中提供您的知识库？
* 系统将保留每个域中的多少个文档？

计划使用单个 QnA Maker 资源包含具有相同语言、同一区域和相同使用者域组合的所有知识库。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

当你首次开发 QnA Maker 托管知识库时，在 "原型" 阶段中，有一个用于测试和生产的单独 QnA Maker 托管资源。

在移动到项目的开发阶段时，应考虑以下事项：

* 您的知识库系统将包含多少种语言？
* 您需要在哪些区域中提供您的知识库？
* 系统将保留每个域中的多少个文档？

---

## <a name="pricing-tier-considerations"></a>定价层注意事项

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

通常，你需要考虑三个参数：

* **需要的服务吞吐量**：
    * 根据需要为应用服务选择合适的[应用计划](https://azure.microsoft.com/pricing/details/app-service/plans/)。 可以[纵向扩展](../../../app-service/manage-scale-up.md)或收缩应用。
    * 这还会影响 Azure **认知搜索** SKU 选择，有关详细信息，请参阅 [此处](../../../search/search-sku-tier.md)。 此外，你可能还需要用副本调整认知搜索 [容量](../../../search/search-capacity-planning.md) 。

* **知识库的大小和数量**：针对你的方案选择合适的 [Azure 搜索 SKU](https://azure.microsoft.com/pricing/details/search/)。 通常，根据不同的使用者域的数量决定所需的知识库数。 一旦使用者域 (适用于一种语言) 应在一个知识库中。

    可以在特定的层中发布 N-1 个知识库，其中，N 是该层中允许的最大索引数。 还需要检查每个层允许的文档最大大小和数量。

    例如，如果你的层有 15 个允许的索引，则可以发布 14 个知识库（每个已发布的知识库有 1 个索引）。 第 15 个索引用于所有知识库以进行创作和测试。

* **源文档的数量**：QnA Maker 管理服务的免费 SKU 将可以通过门户和 API 管理的文档数限制为 3（每个文档的大小限制为 1 MB）。 标准 SKU 对于可以管理的文档数没有限制。 请 [在此处](https://aka.ms/qnamaker-pricing)查看更多详细信息。

下表提供了一些概要准则。

|                            | QnA Maker 管理 | 应用服务 | Azure 认知搜索 | 限制                      |
| -------------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| **试验**        | 免费 SKU             | 免费层   | 免费层    | 分别最多发布 2 KB、50 MB 大小  |
| **开发/测试环境**   | 标准 SKU         | 共享      | 基本        | 分别最多发布 14 KB、2 GB 大小    |
| **生产环境** | 标准 SKU         | 基本       | 标准     | 分别最多发布 49 KB、25 GB 大小 |

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

通常，你需要考虑三个参数：

* **需要的服务吞吐量**：
    * QnA Maker 托管 (预览版) 是一项免费服务，吞吐量目前限制为管理 Api 和预测 Api 的10个 TPS。
    * 这还会影响 Azure **认知搜索** SKU 选择，有关详细信息，请参阅 [此处](../../../search/search-sku-tier.md)。 此外，你可能还需要用副本调整认知搜索 [容量](../../../search/search-capacity-planning.md) 。

* **知识库的大小和数量**：针对你的方案选择合适的 [Azure 搜索 SKU](https://azure.microsoft.com/pricing/details/search/)。 通常，根据不同的使用者域的数量决定所需的知识库数。 一旦使用者域 (适用于一种语言) 应在一个知识库中。

    使用 QnA Maker 托管 (预览版，) 可以选择使用一种或多种语言为 Kb 设置 QnA Maker 服务。 在 QnA Maker 管理的 (预览版) 服务中创建第一个知识库时，可以进行此选择。

    ![QnA Maker 托管 (预览版) 多语言知识库选择](../media/concept-plan-your-knowledge-base/qnamaker-v2-select-multilanguage-knowledge-base.png)

    您可以在特定层中发布单语言或 N/2 知识库（其中 N 是该层允许的最大索引）的 N-1 知识库。 还需要检查每个层允许的文档最大大小和数量。

    例如，如果您的层有15个允许的索引，则可以为每个已发布知识库)  (1 个索引发布14个知识库。 第 15 个索引用于所有知识库以进行创作和测试。 如果选择使用不同语言的知识库，则只能发布7个知识库。

* **作为源的文档数**： QnA Maker 管理的 (预览版) 是免费服务，并且对于可以添加为源的文档数没有限制。 请 [在此处](https://aka.ms/qnamaker-pricing)查看更多详细信息。

下表提供了一些概要准则。

|                            |Azure 认知搜索 | 限制                      |
| -------------------------- |------------ | -------------------------------- |
| **试验**        |免费层    | 分别最多发布 2 KB、50 MB 大小  |
| **开发/测试环境**   |基本        | 分别最多发布 14 KB、2 GB 大小    |
| **生产环境** |标准     | 分别最多发布 49 KB、25 GB 大小 |

---

## <a name="recommended-settings"></a>建议的设置

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

|目标 QPS | 应用服务 | Azure 认知搜索 |
| -------------------- | ----------- | ------------ |
| 3             | S1，1个副本   | S1，1个副本    |
| 50         | S3，10个副本       | S1、12个副本         |
| 80         | S3，10个副本      |  S3、12个副本  |
| 100         | P3V2，10个副本  | S3，12个副本，3个分区   |
| 200至250         | P3V2，20个副本 | S3，12个副本，3个分区    |

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

QnA Maker 托管是一项免费服务，吞吐量当前每秒上限为10个事务，用于管理 Api 和预测 Api。 若要为服务每秒实现10个事务，我们建议 Azure 认知搜索的 S1 (1 实例) SKU。

---

## <a name="when-to-change-a-pricing-tier"></a>何时更改定价层

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

|升级|原因|
|--|--|
|[升级](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) QnA Maker 管理 SKU|你需要在知识库中具有更多的 QnA 对或文档源。|
|[升级](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) 应用服务 SKU 和检查认知搜索层并 [创建认知搜索副本](../../../search/search-capacity-planning.md)|您的知识库需要提供客户端应用程序的更多请求，例如聊天机器人。|
|[升级](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Azure 认知搜索服务|你计划有多个知识库。|

通过 [在 Azure 门户中更新应用服务](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)来获取最新的运行时更新。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

[升级](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) 当你计划有很多知识库时，Azure 认知搜索服务。

---

## <a name="keys-in-qna-maker"></a>QnA Maker 中的密钥

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

QnA Maker 服务处理两种类型的密钥： **创作密钥** 和用于在应用服务中托管的运行时的 **查询终结点密钥** 。

通过 Api 向服务发出请求时使用这些密钥。

![密钥管理](../media/qnamaker-how-to-key-management/key-management.png)

|名称|位置|目的|
|--|--|--|
|创作/订阅密钥|[Azure 门户](https://azure.microsoft.com/free/cognitive-services/)|这些密钥用来访问 [QnA Maker 管理服务 API](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)。 通过这些 Api，您可以编辑知识库中的问题和解答，并发布您的知识库。 这些密钥是在创建新的 QnA Maker 服务时创建的。<br><br>在 "**密钥**" 页上的 "**认知服务**" 资源上查找这些密钥。|
|查询终结点密钥|[QnA Maker 门户](https://www.qnamaker.ai)|这些键用于查询已发布的知识库终结点，以获取用户问题的响应。 通常在聊天机器人中或连接到 QnA Maker 服务的客户端应用程序代码中使用此查询终结点。 这些密钥是在您发布 QnA Maker 知识库时创建的。<br><br>在 " **服务设置** " 页中查找这些密钥。 在下拉菜单上页面右上角的用户菜单中找到此页。|

### <a name="find-authoring-keys-in-the-azure-portal"></a>在 Azure 门户中查找创作密钥

你可以从创建 QnA Maker 资源的 Azure 门户查看和重置创作密钥。 这些密钥可能称为订阅密钥。

1. 中转到 Azure 门户中的 QnA Maker 资源，并选择具有 _认知服务_ 类型的资源：

    ![QnA Maker 资源列表](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. 中转到 **密钥和终结点**：

    ![QnA Maker 托管 (预览版) 订阅密钥](../media/qnamaker-how-to-key-management/subscription-key-v2.png)

### <a name="find-query-endpoint-keys-in-the-qna-maker-portal"></a>在 QnA Maker 门户中查找查询终结点密钥

终结点与资源位于同一区域，因为终结点键用于调用知识库。

可以从 [QnA Maker 门户](https://qnamaker.ai)管理终结点密钥。

1. 登录到 [QnA Maker 门户](https://qnamaker.ai)，中转到你的配置文件，然后选择 " **服务设置**"：

    ![终结点密钥](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. 查看或重置密钥：

    > [!div class="mx-imgBorder"]
    > ![终结点密钥管理器](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >如果你认为密钥已泄露，请刷新你的密钥。 这可能需要相应地更改客户端应用程序或机器人代码。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

QnA Maker 托管 (预览版) 服务处理两种密钥： **创作密钥** 和 **Azure 认知搜索密钥** ，用于访问客户订阅中的服务。

通过 Api 向服务发出请求时使用这些密钥。

![密钥管理托管预览版](../media/qnamaker-how-to-key-management/qnamaker-v2-key-management.png)

|名称|位置|目的|
|--|--|--|
|创作/订阅密钥|[Azure 门户](https://azure.microsoft.com/free/cognitive-services/)|这些密钥用来访问 [QnA Maker 管理服务 API](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)。 通过这些 Api，您可以编辑知识库中的问题和解答，并发布您的知识库。 这些密钥是在创建新的 QnA Maker 服务时创建的。<br><br>在 "**密钥**" 页上的 "**认知服务**" 资源上查找这些密钥。|
|Azure 认知搜索管理密钥|[Azure 门户](../../../search/search-security-api-keys.md)|这些密钥用于与在用户的 Azure 订阅中部署的 Azure 认知搜索服务通信。 将 Azure 认知搜索与 QnA Maker 托管 (预览版) 服务相关联时，管理密钥会自动传递到 QnA Maker 服务。 <br><br>可以在 "**密钥**" 页上的 " **Azure 认知搜索** 资源上找到这些密钥。|

### <a name="find-authoring-keys-in-the-azure-portal"></a>在 Azure 门户中查找创作密钥

你可以从 Azure 门户中查看和重置创作密钥，你可以在其中创建 QnA Maker 托管 (预览版) 资源。 这些密钥可能称为订阅密钥。

1. 请在 Azure 门户中中转到 QnA Maker 托管 (预览版) 资源，并选择具有 *认知服务* 类型的资源：

    ![QnA Maker 托管 (预览版) 资源列表](../media/qnamaker-how-to-key-management/qnamaker-v2-resource-list.png)

2. 中转到 **密钥和终结点**：

    ![QnA Maker 托管 (预览版) 订阅密钥](../media/qnamaker-how-to-key-management/subscription-key-v2.png)

### <a name="update-the-resources"></a>更新资源

了解如何升级知识库使用的资源。 QnA Maker 托管 () 预览版，请在预览阶段 **免费** 。 

---

## <a name="management-service-region"></a>管理服务区域

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

QnA Maker 的管理服务仅用于 QnA Maker 门户和初始数据处理。 此服务仅在 **美国西部** 区域提供。 此美国西部服务中未存储任何客户数据。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

在 QnA Maker 托管 (预览版中) 管理和预测服务共同位于同一区域中。 目前 QnA Maker 托管 (预览版) 在 **美国中南部、北欧和澳大利亚东部** 提供。

---

## <a name="resource-naming-considerations"></a>资源命名注意事项

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

QnA Maker 资源的资源名称（如 `qna-westus-f0-b` ）还用于命名其他资源。

使用 "Azure 门户创建" 窗口，可以创建 QnA Maker 资源，并为其他资源选择定价层。

> [!div class="mx-imgBorder"]
> ![用于创建 QnA Maker 资源的 Azure 门户屏幕截图](../media/concept-azure-resource/create-blade.png)

创建资源后，它们具有相同的名称，可选的 Application Insights 资源除外，该资源将字符 postpends 到名称。

> [!div class="mx-imgBorder"]
> ![Azure 门户资源列表的屏幕截图](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> 创建 QnA Maker 资源时，请创建新的资源组。 这样，你就可以在按资源组搜索时查看与 QnA Maker 资源关联的所有资源。

> [!TIP]
> 使用命名约定在资源或资源组的名称中指示定价层。 如果在创建新知识库或添加新文档时收到错误，则认知搜索定价层限制是常见问题。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

QnA Maker 管理的 (预览版) 资源的资源名称 `qna-westus-f0-b` 也用于命名其他资源。

使用 "Azure 门户创建" 窗口，可以创建 QnA Maker 托管 (预览) 资源，并为其他资源选择定价层。

> [!div class="mx-imgBorder"]
> ![创建资源后，QnA Maker 托管 (预览) 资源创建 Azure 门户的屏幕截图 ](../media/qnamaker-how-to-setup-service/enter-qnamaker-v2-info.png) ，它们具有相同的名称。

> [!div class="mx-imgBorder"]
> ![Azure 门户资源列表 QnA Maker 托管 (预览的屏幕截图) ](../media/qnamaker-how-to-setup-service/resources-created-v2.png)

> [!TIP]
> 创建 QnA Maker 资源时，请创建新的资源组。 这样，你就可以在按资源组搜索时查看与 QnA Maker 管理的 (预览版) 资源关联的所有资源。

> [!TIP]
> 使用命名约定在资源或资源组的名称中指示定价层。 如果在创建新知识库或添加新文档时收到错误，则认知搜索定价层限制是常见问题。

---

## <a name="resource-purposes"></a>资源目的

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

使用 QnA Maker 创建的每个 Azure 资源都有特定用途：

* QnA Maker 资源
* 认知搜索资源
* 应用服务
* 应用计划服务
* Application Insights 服务

### <a name="qna-maker-resource"></a>QnA Maker 资源

QnA Maker 资源提供对创作和发布 Api 的访问，以及自然语言处理 (NLP) 的第二排名层 (在运行时 QnA 对的 ranker #2) 。

第二个排名适用于智能筛选器，这些筛选器可以包含元数据和跟进提示。

#### <a name="qna-maker-resource-configuration-settings"></a>QnA Maker 资源配置设置

在 [QnA Maker 门户](https://qnamaker.ai)中创建新知识库时，" **语言** " 设置是在资源级别应用的唯一设置。 您在为资源创建第一个知识库时选择语言。

### <a name="cognitive-search-resource"></a>认知搜索资源

[认知搜索](../../../search/index.yml)资源用于：

* 存储 QnA 对
* 在运行时提供 QnA 对的初始排名 (ranker #1) 

#### <a name="index-usage"></a>索引使用情况

资源保留一个索引作为测试索引，其余索引与每个已发布的知识库关联。

定价为包含15个索引的资源将保留14个发布的知识库，而一个索引用于测试所有知识库。 此测试索引按知识库分区，以便使用 "交互式测试" 窗格的查询将使用测试索引，但仅从与特定知识库关联的特定分区返回结果。

#### <a name="language-usage"></a>语言用法

在 QnA Maker 资源中创建的第一个知识库用于确定认知搜索资源及其所有索引的 _单一_ 语言集。 只能为 QnA Maker 服务 _设置一种语言_ 。

#### <a name="using-a-single-cognitive-search-service"></a>使用单个认知搜索服务

如果通过门户创建 QnA 服务及其依赖项 (如搜索) ，系统会为您创建一个搜索服务并将其链接到 QnA Maker 服务。 创建这些资源后，你可以更新应用服务设置，以使用以前存在的搜索服务，并删除刚刚创建的搜索服务。

了解 [如何将 QnA Maker 配置](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) 为使用不同的认知服务资源，而不是创建 QnA Maker 资源创建过程的一部分。

### <a name="app-service-and-app-service-plan"></a>应用服务和应用服务计划

[应用服务](../../../app-service/index.yml)由客户端应用程序用于通过运行时终结点访问已发布的知识库。

若要查询已发布的知识库，所有发布的知识库均使用相同的 URL 终结点，但在路由中指定 **知识库 ID** 。

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application Insights](../../../azure-monitor/app/app-insights-overview.md) 用于收集聊天日志和遥测数据。 查看常见的 [Kusto 查询](../how-to/get-analytics-knowledge-base.md) 以获取有关服务的信息。

### <a name="share-services-with-qna-maker"></a>与 QnA Maker 共享服务

QnA Maker 创建多个 Azure 资源。 若要减少成本共享的管理和权益，请使用下表来了解可以和不能共享的内容：

|服务|共享|原因|
|--|--|--|
|认知服务|X|不能通过设计|
|应用服务计划|✔|为应用服务计划分配的固定磁盘空间。 如果共享同一应用服务计划的其他应用使用了大量磁盘空间，QnAMaker 应用服务实例将会遇到问题。|
|应用服务|X|不能通过设计|
|Application Insights|✔|可以共享|
|搜索服务|✔|1. `testkb` 是 QnAMaker 服务的保留名称; 其他名称不能使用它。<br>2. 名称的同义词映射 `synonym-map` 是为 QnAMaker 服务保留的。<br>3. 已发布的知识库数受搜索服务层限制。 如果有可用的可用索引，则其他服务可以使用这些索引。|

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

使用 QnA Maker 托管 () 预览版创建的每个 Azure 资源都有特定用途：

* QnA Maker 资源
* 认知搜索资源

### <a name="qna-maker-resource"></a>QnA Maker 资源

QnA Maker 托管的 (预览版) 资源提供对创作和发布 Api 的访问，承载排名运行时以及提供遥测。

### <a name="azure-cognitive-search-resource"></a>Azure 认知搜索资源

[认知搜索](../../../search/index.yml)资源用于：

* 存储 QnA 对
* 在运行时提供 QnA 对的初始排名 (ranker #1) 

#### <a name="index-usage"></a>索引使用情况

你可以在特定层中发布单语言或 N/2 知识库（其中 N 是 Azure 认知搜索层中允许的最大索引）的 N-1 知识库。 还需要检查每个层允许的文档最大大小和数量。

例如，如果您的层有15个允许的索引，则可以为每个已发布知识库)  (1 个索引发布14个知识库。 第 15 个索引用于所有知识库以进行创作和测试。 如果选择使用不同语言的知识库，则只能发布7个知识库。

#### <a name="language-usage"></a>语言用法

使用 QnA Maker 托管 (预览版，) 可以选择使用一种或多种语言为知识库设置 QnA Maker 服务。 在 QnA Maker 服务中创建第一个知识库时，请选择此选项。 请参阅 [此处](#pricing-tier-considerations) 如何启用每个知识库的语言设置。

---

## <a name="next-steps"></a>后续步骤

* 了解 QnA Maker [知识库](../index.yml)
* 了解 [知识库生命周期](development-lifecycle-knowledge-base.md)
* 查看服务和知识库 [限制](../limits.md)
