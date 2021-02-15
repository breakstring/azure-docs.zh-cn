---
title: 透明数据加密 (T-SQL)
description: Azure Synapse Analytics (T-SQL) 中的透明数据加密 (TDE)
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: 80e5da6bb281806afe6bc980e35d70732bcd609c
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676266"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>透明数据加密 (TDE) 入门

> [!div class="op_single_selector"]
>
> * [安全概述](sql-data-warehouse-overview-manage-security.md)
> * [身份验证](sql-data-warehouse-authentication.md)
> * [加密（门户）](sql-data-warehouse-encryption-tde.md)
> * [加密 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>所需的权限

若要启用透明数据加密 (TDE)，用户必须是管理员或 dbmanager 角色的成员。

## <a name="enabling-encryption"></a>启用加密

执行下列步骤启用 TDE：

1. 使用在 master 数据库中充当管理员或 **dbmanager** 角色成员的登录名，连接到托管数据库的服务器上的 *master* 数据库
2. 执行以下语句来加密数据库。

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>禁用加密

执行以下步骤禁用 TDE：

1. 使用在 master 数据库中充当管理员或 **dbmanager** 角色成员的登录名，连接到 *master* 数据库
2. 执行以下语句来加密数据库。

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> 在更改 TDE 设置之前，必须恢复暂停的专用 SQL 池。

## <a name="verifying-encryption"></a>验证加密

若要验证加密状态，请执行以下步骤：

1. 使用在 master 数据库中充当管理员或 **dbmanager** 角色成员的登录名，连接到 *master* 数据库或实例数据库
2. 执行以下语句来加密数据库。

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

结果 ```1``` 表示数据库已加密，```0``` 表示数据库未加密。

## <a name="encryption-dmvs"></a>加密 DMV

* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
