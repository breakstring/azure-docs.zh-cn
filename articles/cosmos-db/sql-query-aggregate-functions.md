---
title: Azure Cosmos DB 中的聚合函数
description: 了解 SQL 聚合函数语法以及 Azure Cosmos DB 支持的聚合函数类型。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.openlocfilehash: c0d953c8d99582f63744d51b505852b5c44bc409
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552366"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Azure Cosmos DB 中的聚合函数
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

聚合函数对 `SELECT` 子句中的一组值执行计算，并返回单个值。 例如，下面的查询返回容器中项的计数：

```sql
    SELECT COUNT(1)
    FROM c
```

## <a name="types-of-aggregate-functions"></a>聚合函数的类型

SQL API 支持以下聚合函数。 `SUM` 和 `AVG` 对数值执行运算，`COUNT`、`MIN` 和 `MAX` 可以处理数字、字符串、布尔值和 null。

| 函数 | 描述 |
|-------|-------------|
| [AVG](sql-query-aggregate-avg.md) | 在表达式中返回多个值的平均值。 |
| [COUNT](sql-query-aggregate-count.md) | 在表达式中返回项的数目。 |
| [MAX](sql-query-aggregate-max.md) | 在表达式中返回最大值。 |
| [MIN](sql-query-aggregate-min.md) | 在表达式中返回最小值。 |
| [SUM](sql-query-aggregate-sum.md) | 在表达式中返回所有值的总和。 |


也可以使用 VALUE 关键字来仅返回聚合的标量值。 例如，以下查询将值的计数作为单个值返回：

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

结果有：

```json
    [ 2 ]
```

还可以将聚合与筛选器结合使用。 例如，以下查询返回包含 `WA` 州地址的项计数。

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

结果有：

```json
    [ 1 ]
```

## <a name="remarks"></a>备注

这些聚合系统函数将从[范围索引](index-policy.md#includeexclude-strategy)中获益。 如果希望 `AVG` `COUNT` 在属性上执行、、 `MAX` 、 `MIN` 或， `SUM` 应在 [索引策略中包含相关路径](index-policy.md#includeexclude-strategy)。

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB 简介](introduction.md)
- [系统函数](sql-query-system-functions.md)
- [用户定义的函数](sql-query-udfs.md)