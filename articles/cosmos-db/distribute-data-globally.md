---
title: 使用 Azure Cosmos DB 在全球范围内分发数据
description: 了解全球分布式多模型数据库服务，了解如何使用 Azure Cosmos DB 的全局数据库（一种全球分布式多模型数据库服务）进行全球范围的异地复制、多区域写入、故障转移和数据恢复。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.custom: seo-nov-2020
ms.openlocfilehash: 7fedd45585698aef9248318a1b055cb656f25d02
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335122"
---
# <a name="distribute-your-data-globally-with-azure-cosmos-db"></a>使用 Azure Cosmos DB 在全球范围内分发数据
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

如今的应用程序需要具备高响应能力并始终联机。 若要实现低延迟和高可用性，需要在靠近用户的数据中心部署这些应用程序的实例。 这些应用程序通常部署在多个数据中心，称为全球分布式应用程序。 全球分布式应用程序需要全球分布式数据库，以便在全球范围内以透明方式复制数据，从而确保应用程序能在靠近用户的数据副本上执行操作。 

Azure Cosmos DB 是一种全球分布式数据库系统，可用于从数据库的本地副本读取和写入数据。 Azure Cosmos DB 以透明方式将数据复制到与 Cosmos 帐户关联的所有区域。 Azure Cosmos DB 是一个全局分布式数据库服务，旨在提供低延迟、吞吐量弹性缩放和明确定义的语义，以实现数据一致性和高可用性。 简而言之，如果应用程序在世界各地都需要快速响应时间，则需要始终处于联机状态，并且需要吞吐量和存储的无限制、弹性可伸缩性，则应 Azure Cosmos DB 上生成应用程序。

可将数据库配置为全局分布，并使其可在任何 Azure 区域中使用。 为了降低延迟，请将数据放置在更靠近用户的位置。 选择所需的区域数目取决于应用程序的全球覆盖范围以及用户所处的位置。 Cosmos DB 以透明方式将数据复制到与 Cosmos 帐户关联的所有区域。 它提供全局分布式 Azure Cosmos 数据库和容器的单个系统映像，使应用程序能够在本地读取和写入。 

使用 Azure Cosmos DB 可以随时添加或删除与帐户关联的区域。 无需暂停或重新部署应用程序即可添加或删除区域。 得益于该服务原生提供的多宿主功能，它始终都能保持高可用性。

:::image type="content" source="./media/distribute-data-globally/deployment-topology.png" alt-text="高度可用的部署拓扑" border="false":::

## <a name="key-benefits-of-global-distribution"></a>全局分布的重要优势

**构建全局活动-活动应用。** 通过其 novel 多区域写入复制协议，每个区域都支持写入和读取。 多区域写入功能还可实现以下功能：

- 无限弹性写入和读取可伸缩性。
- 在全球 99.999% 的读写可用性。
- 在 99% 的时间内，在 10 毫秒内为读写提供服务。

使用 Azure Cosmos DB 的多宿主 API，应用程序始终知道最靠近的区域并将请求发送到该区域。 无需进行任何配置更改就能识别最靠近的区域。 在 Azure Cosmos 帐户中添加和删除区域时，无需重新部署或暂停应用程序，它始终会保持高可用性。

**生成高响应能力的应用。** 应用程序可以在为数据库选择的所有区域中执行近乎实时的读写。 Azure Cosmos DB 在内部处理区域之间的数据复制，并保证提供所选的一致性级别。

**生成高度可用的应用。** 在世界各地的多个区域中运行数据库可提高数据库的可用性。 如果一个区域不可用，其他区域可自动处理应用程序请求。 Azure Cosmos DB 为多区域数据库提供 99.999% 的读取和写入可用性。

**在区域性中断期间保持业务连续性。** Azure Cosmos DB 支持在区域性中断期间进行[自动故障转移](how-to-manage-database-account.md#automatic-failover)。 在区域性中断期间，Azure Cosmos DB 会继续维持其延迟、可用性、一致性和吞吐量方面的 SLA。 为帮助确保整个应用程序高度可用，Cosmos DB 提供手动故障转移 API 来模拟区域性中断。 使用此 API 可以执行常规业务连续性演练。

**全局缩放读写吞吐量。** 可以使每个区域都可写，并在世界各地弹性缩放读取和写入。 你的应用程序在 Azure Cosmos 数据库或容器上配置的吞吐量是在与 Azure Cosmos 帐户关联的所有区域之间预配的。 预配的吞吐量有 [SLA 的资金保障](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)。

**从多个明确定义的一致性模型中进行选择。** Azure Cosmos 数据库复制协议提供了五种明确定义、实用且直观的一致性模型。 每个模型在一致性与性能之间进行了权衡。 使用这些一致性模型可轻松生成全球分布式应用程序。

## <a name="next-steps"></a><a id="Next Steps"></a>后续步骤

阅读以下文章详细了解全局分布：

* [全球分布 - 揭秘](global-dist-under-the-hood.md)
* [如何在应用程序中配置多区域写入](how-to-multi-master.md)
* [配置多宿主客户端](how-to-manage-database-account.md#configure-multiple-write-regions)
* [在 Azure Cosmos DB 帐户中添加或删除区域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [为 SQL API 帐户创建自定义冲突解决策略](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Cosmos DB 中的可编程一致性模型](consistency-levels.md)
* [为你的应用程序选择适当的一致性级别](./consistency-levels.md)
* [跨 Azure Cosmos DB API 的一致性级别](./consistency-levels.md)
* [各种一致性级别的可用性和性能权衡](./consistency-levels.md)