---
title: 在 Azure Cosmos DB SQL API 中创建容器
description: 了解如何使用 Azure 门户、.NET、Java、Python、Node.js 和其他 SDK 在 Azure Cosmos DB SQL API 中创建容器。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 302c5d6e8e523a11b8773f10bb6089e3bea09bdd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006842"
---
# <a name="create-a-container-in-azure-cosmos-db-sql-api"></a>在 Azure Cosmos DB SQL API 中创建容器
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

本文介绍如何通过不同方式在 Azure Cosmos DB SQL API 中创建容器。 它演示如何使用 Azure 门户、Azure CLI、PowerShell 或支持的 SDK 创建容器。 本文演示如何创建容器、指定分区键和预配吞吐量。

本文介绍如何通过不同方式在 Azure Cosmos DB SQL API 中创建容器。 如果你使用其他 API，请参阅[用于 MongoDB 的 API](how-to-create-container-mongodb.md)、[Cassandra API](how-to-create-container-cassandra.md)、[Gremlin API](how-to-create-container-gremlin.md) 和[表 API](how-to-create-container-table.md) 文章来创建容器。

> [!NOTE]
> 创建容器时，请确保不创建名称相同但大小写不同的两个容器。 这是因为 Azure 平台的某些部分不区分大小写，这可能会对具有此类名称的容器导致遥测和操作混乱/冲突。

## <a name="create-a-container-using-azure-portal"></a><a id="portal-sql"></a>使用 Azure 门户创建容器

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure Cosmos 帐户](create-sql-api-dotnet.md#create-account)或选择现有的帐户。

1. 打开“数据资源管理器”窗格，然后选择“新建容器” 。 接下来，请提供以下详细信息：

   * 表明要创建新数据库还是使用现有数据库。
   * 输入容器 ID。
   * 输入分区键。
   * 输入要进行预配的吞吐量（例如，1000 RU）。
   * 选择“确定” 。

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-sql.png" alt-text="“数据资源管理器”窗格的屏幕截图，其中突出显示了“新建容器”":::

## <a name="create-a-container-using-azure-cli"></a><a id="cli-sql"></a>使用 Azure CLI 创建容器

[使用 Azure CLI 创建容器](manage-with-cli.md#create-a-container)。 有关所有 Azure Cosmos DB API 的所有 Azure CLI 示例的列表，请参阅 [Azure Cosmos DB 的 Azure CLI 示例](cli-samples.md)。

## <a name="create-a-container-using-powershell"></a>使用 PowerShell 创建容器

[使用 PowerShell 创建容器](manage-with-powershell.md#create-container)。 有关所有 Azure Cosmos DB API 的所有 PowerShell 示例的列表，请参阅 [PowerShell 示例](powershell-samples.md)

## <a name="create-a-container-using-net-sdk"></a><a id="dotnet-sql"></a>使用 .NET SDK 创建容器

如果创建集合时遇到超时异常，请执行读取操作来验证是否已成功创建集合。 成功完成集合创建操作之前，读取操作将引发异常。 有关创建操作所支持的状态代码列表，请参阅 [Azure Cosmos DB 的 HTTP 状态代码](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)一文。

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

## <a name="next-steps"></a>后续步骤

* [Azure Cosmos DB 中的分区](partitioning-overview.md)
* [Azure Cosmos DB 中的请求单位](request-units.md)
* [在容器和数据库上预配吞吐量](set-throughput.md)
* [使用 Azure Cosmos 帐户](./account-databases-containers-items.md)