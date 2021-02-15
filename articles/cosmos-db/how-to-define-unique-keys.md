---
title: 为 Azure Cosmos 容器定义唯一键
description: 了解如何使用 Azure 门户、PowerShell、.NET、Java 和其他各种 Sdk 定义 Azure Cosmos 容器的唯一密钥。
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 12/02/2019
ms.author: thweiss
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 55fc5222c1c245c56ba0a26caa816c5c845147c1
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336599"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>为 Azure Cosmos 容器定义唯一键
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

本文介绍在创建 Azure Cosmos 容器时定义[唯一键](unique-keys.md)的不同方法。 目前，可以通过使用 Azure 门户或通过其中一个 SDK 来执行此操作。

## <a name="use-the-azure-portal"></a>使用 Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure Cosmos 帐户](create-sql-api-dotnet.md#create-account)或选择现有帐户。

1. 打开“数据资源管理器”窗格，选择要使用的容器。

1. 单击“新建容器”。

1. 在“添加容器”对话框中，单击“+ 添加唯一键”，添加唯一键条目 。

1. 输入唯一键约束的路径

1. 如果需要，可通过单击“+ 添加唯一键”，添加更多唯一键条目

    :::image type="content" source="./media/how-to-define-unique-keys/unique-keys-portal.png" alt-text="Azure 门户中唯一键约束条目的屏幕截图":::

## <a name="use-powershell"></a>使用 PowerShell

要创建具有唯一键的容器，请参阅[创建具有唯一键和 TTL 的 Azure Cosmos 容器](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="use-the-net-sdk"></a>使用 .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

使用 [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) 创建新的容器时，可使用 `UniqueKeyPolicy` 对象定义唯一键约束。

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    PartitionKey = new PartitionKeyDefinition { Paths = new Collection<string>(new List<string> { "/myPartitionKey" }) },
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "/emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

使用 [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) 创建新容器时，请使用 SDK 的 Fluent API 以简洁且可读的方式声明唯一键。

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithUniqueKey()
        .Path("/firstName")
        .Path("/lastName")
        .Path("/emailAddress")
    .Attach()
    .WithUniqueKey()
        .Path("/address/zipCode")
    .Attach()
    .CreateIfNotExistsAsync();
```
---

## <a name="use-the-java-sdk"></a>使用 Java SDK

使用 [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) 创建新的容器时，可使用 `UniqueKeyPolicy` 对象定义唯一键约束。

```java
// create a new DocumentCollection object
DocumentCollection container = new DocumentCollection();
container.setId("container");

// create array of strings and populate them with the unique key paths
Collection<String> uniqueKey1Paths = new ArrayList<String>();
uniqueKey1Paths.add("/firstName");
uniqueKey1Paths.add("/lastName");
uniqueKey1Paths.add("/emailAddress");
Collection<String> uniqueKey2Paths = new ArrayList<String>();
uniqueKey2Paths.add("/address/zipCode");

// create UniqueKey objects and set their paths
UniqueKey uniqueKey1 = new UniqueKey();
UniqueKey uniqueKey2 = new UniqueKey();
uniqueKey1.setPaths(uniqueKey1Paths);
uniqueKey2.setPaths(uniqueKey2Paths);

// create a new UniqueKeyPolicy object and set its unique keys
UniqueKeyPolicy uniqueKeyPolicy = new UniqueKeyPolicy();
Collection<UniqueKey> uniqueKeys = new ArrayList<UniqueKey>();
uniqueKeys.add(uniqueKey1);
uniqueKeys.add(uniqueKey2);
uniqueKeyPolicy.setUniqueKeys(uniqueKeys);

// set the unique key policy
container.setUniqueKeyPolicy(uniqueKeyPolicy);

// create the container
client.createCollection(String.format("/dbs/%s", "database"), container, null);
```

## <a name="use-the-nodejs-sdk"></a>使用 Node.js SDK

使用 [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) 创建新的容器时，可使用 `UniqueKeyPolicy` 对象定义唯一键约束。

```javascript
client.database('database').containers.create({
    id: 'container',
    uniqueKeyPolicy: {
        uniqueKeys: [
            { paths: ['/firstName', '/lastName', '/emailAddress'] },
            { paths: ['/address/zipCode'] }
        ]
    }
});
```

## <a name="use-the-python-sdk"></a>使用 Python SDK

使用 [Python SDK](https://pypi.org/project/azure-cosmos/) 创建新的容器时，可将唯一键约束指定为作为参数传递的字典的一部分。

```python
client.CreateContainer('dbs/' + config['DATABASE'], {
    'id': 'container',
    'uniqueKeyPolicy': {
        'uniqueKeys': [
            {'paths': ['/firstName', '/lastName', '/emailAddress']},
            {'paths': ['/address/zipCode']}
        ]
    }
})
```

## <a name="next-steps"></a>后续步骤

- 了解有关[分区](partitioning-overview.md)的详细信息
- 探索[索引编制的工作原理](index-overview.md)
