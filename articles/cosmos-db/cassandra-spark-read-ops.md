---
title: 使用 Spark 读取 Cassandra API 表数据
titleSufix: Azure Cosmos DB
description: 本文介绍如何读取 Azure Cosmos DB 中的 Cassandra API 表中的数据。
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 06/02/2020
ms.custom: seodec18
ms.openlocfilehash: ceede96cbf3be12a6129e27d34e318e4c4163458
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93073490"
---
# <a name="read-data-from-azure-cosmos-db-cassandra-api-tables-using-spark"></a>使用 Spark 读取 Azure Cosmos DB Cassandra API 表中的数据
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

 本文介绍如何从 Spark 读取存储在 Azure Cosmos DB Cassandra API 中的数据。

## <a name="cassandra-api-configuration"></a>Cassandra API 配置
```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")
//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```
## <a name="dataframe-api"></a>数据帧 API

### <a name="read-table-using-sessionreadformat-command"></a>使用 session.read.format 命令读取表

```scala
val readBooksDF = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load

readBooksDF.explain
readBooksDF.show
```
### <a name="read-table-using-sparkreadcassandraformat"></a>使用 spark.read.cassandraFormat 读取表 

```scala
val readBooksDF = spark.read.cassandraFormat("books", "books_ks", "").load()
```

### <a name="read-specific-columns-in-table"></a>读取表中特定的列

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

### <a name="apply-filters"></a>应用筛选器

可以将谓词向下推送到数据库，以便更好地优化 Spark 查询。 谓词是返回 true 或 false 的查询的条件，通常位于 WHERE 子句中。 谓词向下推送会筛选数据库查询中的数据，减少从数据库中检索到的条目数，提高查询性能。 默认情况下，Spark 数据集 API 会自动将有效的 WHERE 子句向下推送到数据库。 

```scala
val df = spark.read.cassandraFormat("books", "books_ks").load
df.explain
val dfWithPushdown = df.filter(df("book_pub_year") > 1891)
dfWithPushdown.explain

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

物理计划的 PushedFilters 节包括 GreaterThan 向下推送筛选器。 

:::image type="content" source="./media/cassandra-spark-read-ops/pushdown-predicates.png" alt-text="分区":::

## <a name="rdd-api"></a>RDD API

### <a name="read-table"></a>读取表
```scala
val bookRDD = sc.cassandraTable("books_ks", "books")
bookRDD.take(5).foreach(println)
```

### <a name="read-specific-columns-in-table"></a>读取表中特定的列

```scala
val booksRDD = sc.cassandraTable("books_ks", "books").select("book_id","book_name").cache
booksRDD.take(5).foreach(println)
```

## <a name="sql-views"></a>SQL 视图 

### <a name="create-a-temporary-view-from-a-dataframe"></a>从 dataframe 创建临时视图

```scala
spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load.createOrReplaceTempView("books_vw")
```

### <a name="run-queries-against-the-view"></a>针对视图运行查询

```sql
select * from books_vw where book_pub_year > 1891
```

## <a name="next-steps"></a>后续步骤

以下是有关从 Spark 使用 Azure Cosmos DB Cassandra API 的其他文章：
 
 * [upsert 操作](cassandra-spark-upsert-ops.md)
 * [删除操作](cassandra-spark-delete-ops.md)
 * [聚合操作](cassandra-spark-aggregation-ops.md)
 * [表复制操作](cassandra-spark-table-copy-ops.md)

