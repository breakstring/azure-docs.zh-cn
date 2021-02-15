---
title: Azure Cosmos DB 查询语言中的 ACOS
description: 了解 Azure Cosmos DB 中的 ACOS (arccosice) SQL 系统函数如何返回其余弦是指定数值表达式的角度（以弧度为单位）
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ed5adaad6c0ee160fd55341e1d7c48fcf3e49a28
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93332791"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 返回角度（弧度），其余弦是指定的数值表达式；也被称为反余弦。  
  
## <a name="syntax"></a>语法
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>参数
  
*numeric_expr*  
   是一个数值表达式。  
  
## <a name="return-types"></a>返回类型
  
  返回一个数值表达式。  
  
## <a name="examples"></a>示例
  
  以下示例返回 -1 的 `ACOS`。  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 下面是结果集。  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="remarks"></a>备注

此系统函数不会使用索引。

## <a name="next-steps"></a>后续步骤

- [数学函数 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [系统函数 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 简介](introduction.md)
