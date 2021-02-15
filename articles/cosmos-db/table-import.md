---
title: 将现有数据迁移到 Azure Cosmos DB 中的表 API 帐户
description: 了解如何将本地或云数据迁移或导入到 Azure Cosmos DB 中的 Azure 表 API 帐户。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: e876ca028532bb3721146e90a91d68c4c12bf79f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096069"
---
# <a name="migrate-your-data-to-azure-cosmos-db-table-api-account"></a>将数据迁移到 Azure Cosmos DB 表 API 帐户
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

本教程说明如何导入要在 Azure Cosmos DB [表 API](table-introduction.md) 中使用的数据。 如果已将数据存储在 Azure 表存储中，可以使用数据迁移工具或 AzCopy 将数据导入 Azure Cosmos DB 表 API。 如果已将数据存储在 Azure Cosmos DB 表 API（预览版）帐户中，则必须使用数据迁移工具来迁移数据。 

本教程涵盖以下任务：

> [!div class="checklist"]
> * 使用数据迁移工具导入数据
> * 使用 AzCopy 导入数据
> * 从表 API（预览版）迁移到表 API 

## <a name="prerequisites"></a>先决条件

* **增加吞吐量：** 数据迁移的持续时间取决于为单个容器或一组容器设置的吞吐量。 请确保对于较大的数据迁移增加吞吐量。 完成迁移后，减少吞吐量以节约成本。 有关在 Azure 门户中增加吞吐量的详细信息，请参阅 Azure Cosmos DB 中的性能级别和定价层。

* **创建 Azure Cosmos DB 资源：** 在开始迁移数据之前，从 Azure 门户预先创建所有表。 如果要迁移到具有数据库级别吞吐量的 Azure Cosmos DB 帐户，请确保在创建 Azure Cosmos DB 表时提供分区键。

## <a name="data-migration-tool"></a>数据迁移工具

可以使用命令行形式的 Azure Cosmos DB 数据迁移工具 (dt.exe) 将现有 Azure 表存储数据导入到表 API GA 帐户，或者将数据从表 API（预览版）帐户迁移到表 API GA 帐户。 目前不支持其他源。 基于 UI 的数据迁移工具 (dtui.exe) 目不支持表 API 帐户。 

若要执行表数据迁移，请完成以下任务：

1. 从 [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool) 下载迁移工具。
2. 使用适用于你的方案的命令行参数运行 `dt.exe`。 `dt.exe` 采用以下格式的命令：

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

此命令支持的选项为：

* **/ErrorLog：** 可选。 要重定向数据传输失败的 CSV 文件的名称
* **/OverwriteErrorLog：** 可选。 覆盖错误日志文件
* **/ProgressUpdateInterval：** 可选，默认值为 00:00:01。 刷新屏幕上数据传输进度的时间间隔
* **/ErrorDetails：** 可选，默认值为 None。 指定应针对以下错误显示详细错误信息：None、Critical、All
* **/EnableCosmosTableLog：** 可选。 将日志定向到 cosmos 表帐户。 如果设置，这会默认为目标帐户连接字符串，除非还提供了 /CosmosTableLogConnectionString。 如果同时运行多个 DT 实例，这将很有用。
* **/CosmosTableLogConnectionString：** 可选。 将日志定向到远程 cosmos 表帐户的 ConnectionString。

### <a name="command-line-source-settings"></a>命令行源设置

将 Azure 表存储或表 API 预览版定义为迁移源时，请使用以下源选项。

* **/s:AzureTable：** 从 Azure 表存储读取数据
* **/s.ConnectionString：** 表终结点的连接字符串。 这可从 Azure 门户中检索
* **/s.LocationMode：** 可选，默认值为 PrimaryOnly。 指定连接到 Azure 表存储时要使用的位置模式：PrimaryOnly、PrimaryThenSecondary、SecondaryOnly、SecondaryThenPrimary
* **/s.Table：** Azure 表的名称
* **/s.InternalFields：** 设置为 All 以进行表迁移，因为导入需要 RowKey 和 PartitionKey。
* **/s.Filter：** 可选。 要应用的筛选器字符串
* **/s.Projection：** 可选。 要选择的列的列表

在从 Azure 表存储进行导入时，若要检索源连接字符串，请打开 Azure 门户并单击“存储帐户” > “帐户” > “访问密钥”，然后使用复制按钮复制 **连接字符串** 。  

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="显示“存储帐户”>“帐户”>“访问密钥”选项并突出显示“复制”按钮的屏幕截图。":::

在从 Azure 表存储进行导入时，若要检索源连接字符串，请打开 Azure 门户并单击“存储帐户” > “帐户” > “访问密钥”，然后使用复制按钮复制 **连接字符串** 。  

:::image type="content" source="./media/table-import/cosmos-connection-string.png" alt-text="HBase 源选项的屏幕截图":::

[示例 Azure 表存储命令](#azure-table-storage)

[示例 Azure Cosmos DB 表 API（预览版）命令](#table-api-preview)

### <a name="command-line-target-settings"></a>命令行目标设置

将 Azure Cosmos DB 表 API 定义为迁移目标时，请使用以下目标选项。

* **/t:TableAPIBulk：** 批量将数据上传到 Azure CosmosDB 表
* **/t.ConnectionString：** 表终结点的连接字符串
* **/t.TableName：** 指定要写入的表的名称
* **/t.Overwrite：** 可选，默认值为 false。 指定是否应覆盖现有值
* **/t.MaxInputBufferSize：** 可选，默认值为 1GB。 将数据刷新到接收器之前要缓冲的输入字节的大致估计值
* **/t.Throughput：** 可选，如果未指定，则为服务默认值。 指定要为表配置的吞吐量
* **/t.MaxBatchSize：** 可选，默认值为 2MB。 指定批大小（以字节为单位）

<a id="azure-table-storage"></a>
### <a name="sample-command-source-is-azure-table-storage"></a>示例命令：源是 Azure 表存储

下面的命令行示例展示了如何从 Azure 表存储导入到表 API：

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

<a id="table-api-preview"></a>
### <a name="sample-command-source-is-azure-cosmos-db-table-api-preview"></a>示例命令：源是 Azure Cosmos DB 表 API（预览版）

下面的命令行示例展示了如何从表 API 预览版导入到表 API GA：

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Table API preview account name>;AccountKey=<Table API preview account key>;TableEndpoint=https://<Account Name>.documents.azure.com; /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>使用 AzCopy 迁移数据

使用 AzCopy 命令行实用工具是将数据从 Azure 表存储迁移到 Azure Cosmos DB 表 API 的另一个选项。 若要使用 AzCopy，首先需要根据[从表存储导出数据](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage)中所述导出数据，然后根据 [Azure Cosmos DB 表 API](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage) 中所述将数据导入到 Azure Cosmos DB。

在执行到 Azure Cosmos DB 的导入时，请参阅以下示例。 注意，/Dest 值使用的是 cosmosdb，而不是 core。

示例导入命令：

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="migrate-from-table-api-preview-to-table-api"></a>从表 API（预览版）迁移到表 API

> [!WARNING]
> 如果希望立即享受正式版表带来的好处，请根据本部分中所述迁移现有的预览版表，否则，我们将在接下来的几周内为现有的预览版客户执行自动迁移，但请注意，自动迁移的预览版表将具有一些限制，而新创建的表则不会有这些限制。

表 API 现在已为正式版 (GA)。 表的预览版和 GA 版之间有一些差异，这同时体现于在云中运行的代码和在客户端运行的代码。 因此，建议不要尝试将预览版 SDK 客户端与 GA 表 API 帐户混合使用，反之亦然。 对于希望在生产环境中继续使用其现有表的表 API 预览版客户，需要从预览版迁移到 GA 环境，或者等待自动迁移。 如果等待自动迁移，则会向你发送通知，指明迁移的表的限制。 在迁移后，将能够在现有帐户上创建没有限制的新表（只有迁移的表才会有限制）。

要从表 API（预览版）迁移到正式版表 API，请执行以下操作：

1. 如[创建数据库帐户](create-table-dotnet.md#create-a-database-account)中所述，创建一个新的 Azure Cosmos DB 帐户并将其 API 类型设置为 Azure 表。

2. 将客户端更改为使用[表 API SDK](table-sdk-dotnet.md) 的 GA 版。

3. 使用数据迁移工具将客户端数据从预览版表迁移到 GA 表。 [数据迁移工具](#data-migration-tool)中提供了将数据迁移工具用于此用途的说明。 

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 使用数据迁移工具导入数据
> * 使用 AzCopy 导入数据
> * 从表 API（预览版）迁移到表 API

现在可以继续学习下一教程并了解如何使用 Azure Cosmos DB 表 API 查询数据。 

> [!div class="nextstepaction"]
>[如何查询数据？](../cosmos-db/tutorial-query-table.md)