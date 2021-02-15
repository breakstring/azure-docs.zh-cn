---
title: 如何在 Azure Cosmos DB 中配置多区域写入
description: 了解如何使用 Azure Cosmos DB 中的不同 Sdk 为应用程序配置多区域写入。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/10/2020
ms.author: mjbrown
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, "seo-nov-2020"
ms.openlocfilehash: 6f71f4c0ec353f36614ea6dcabf4d698b31baacb
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94336720"
---
# <a name="configure-multi-region-writes-in-your-applications-that-use-azure-cosmos-db"></a>在应用程序中配置使用 Azure Cosmos DB 的多区域写入
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

创建具有多个写入区域的帐户后，必须在应用程序中对 ConnectionPolicy 进行两项更改，以便 DocumentClient 启用多区域写入和多宿主功能 Azure Cosmos DB。 在 ConnectionPolicy 中，将 UseMultipleWriteLocations 设置为 true，并将部署应用程序的区域的名称传递给 SetCurrentLocation。 这将根据传入位置的地理接近性填充 PreferredLocations 属性。 如果稍后将新区域添加到帐户中，则无需更新或重新部署应用程序，它将自动检测距离较近的区域，并在发生区域事件时自动定位到该区域。

> [!Note]
> 最初配置有单个写入区域的 Cosmos 帐户可配置为具有零停机时间的多个写入区域。 若要了解详细信息，请参阅[配置多个写入区域](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a name="azure-portal"></a><a id="portal"></a> Azure 门户

若要从 Azure 门户中启用多区域写入，请使用以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 导航到你的 Azure Cosmos 帐户，然后从菜单中打开 " **全局复制数据** " 窗格。

1. 在 " **多区域写入** " 选项下，选择 " **启用** "。 它会自动向读取和写入区域添加现有区域。

1. 通过选择地图上的图标或选择 " **添加区域** " 按钮，可以添加其他区域。 你添加的所有区域都启用了读取和写入。

1. 更新区域列表后，选择 " **保存** " 以应用更改。

   :::image type="content" source="./media/how-to-multi-master/enable-multi-region-writes.png" alt-text="使用 Azure 门户启用多区域写入的屏幕截图" lightbox="./media/how-to-multi-master/enable-multi-region-writes.png":::

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET SDK v2

若要在应用程序中启用多区域写入，请将设置 `UseMultipleWriteLocations` 为 `true` 。 此外，将 `SetCurrentLocation` 设置为在其中部署应用程序并复制 Azure Cosmos DB 的区域：

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>.NET SDK v3

若要在应用程序中启用多区域写入，请将设置 `ApplicationRegion` 为要在其中部署应用程序的区域和复制 Cosmos DB：

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

（可选）可以使用 `CosmosClientBuilder` 和 `WithApplicationRegion` 来获得相同的结果：

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-v4-sdk"></a><a id="java4-multi-region-writes"></a> Java V4 SDK

若要在应用程序中启用多区域写入， `.multipleWriteRegionsEnabled(true)` 请 `.preferredRegions(preferredRegions)` 在客户端生成器中调用和，其中 `preferredRegions` 是一个 `List` 包含一个元素的元素，即要在其中部署应用程序的区域，以及复制 Cosmos DB 的位置：

# <a name="async"></a>[异步](#tab/api-async)

   [Java SDK V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) 异步 API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ConfigureMultimasterAsync)]

# <a name="sync"></a>[Sync](#tab/api-sync)

   [Java SDK V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) 同步 API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ConfigureMultimasterSync)]

--- 

## <a name="async-java-v2-sdk"></a><a id="java2-multi-region-writes"></a> Async Java V2 SDK

Java V2 SDK 使用 Maven [com.microsoft.azure::azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)。 若要在应用程序中启用多区域写入，请设置 `policy.setUsingMultipleWriteLocations(true)` 并将其设置 `policy.setPreferredLocations` 为要在其中部署应用程序的区域和复制 Cosmos DB：

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Node.js、JavaScript 和 TypeScript SDK

若要在应用程序中启用多区域写入，请将设置 `connectionPolicy.UseMultipleWriteLocations` 为 `true` 。 此外，将 `connectionPolicy.PreferredLocations` 设置为在其中部署应用程序并复制 Cosmos DB 的区域：

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a name="python-sdk"></a><a id="python"></a>Python SDK

若要在应用程序中启用多区域写入，请将设置 `connection_policy.UseMultipleWriteLocations` 为 `true` 。 此外，将 `connection_policy.PreferredLocations` 设置为在其中部署应用程序并复制 Cosmos DB 的区域。

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>后续步骤

请阅读以下文章：

* [使用会话令牌在 Azure Cosmos DB 中管理一致性](how-to-manage-consistency.md#utilize-session-tokens)
* [Azure Cosmos DB 中的冲突类型和解决策略](conflict-resolution-policies.md)
* [Azure Cosmos DB 中的高可用性](high-availability.md)
* [Azure Cosmos DB 中的一致性级别](consistency-levels.md)
* [在 Azure Cosmos DB 中选择适当的一致性级别](./consistency-levels.md)
* [Azure Cosmos DB 中的一致性、可用性和性能权衡](./consistency-levels.md)
* [各种一致性级别的可用性和性能权衡](./consistency-levels.md)
* [全局缩放预配的吞吐量](./request-units.md)
* [全局分发：在幕后](global-dist-under-the-hood.md)