---
title: 已知问题：联机迁移到 SQL 数据库
titleSuffix: Azure Database Migration Service
description: 了解在使用 Azure 数据库迁移服务联机迁移到 Azure SQL 数据库时的已知问题/迁移限制。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 748c3f8e38b98b2cbdcfecdf7d755827230fdb3d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962461"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database"></a>联机迁移到 Azure SQL 数据库时存在的已知问题/迁移限制

下面描述了从 SQL Server 联机迁移到 Azure SQL 数据库时存在的已知问题和限制。

> [!IMPORTANT]
> 将 SQL Server 联机迁移到 Azure SQL 数据库时，不支持迁移 SQL_variant 数据类型。

### <a name="migration-of-temporal-tables-not-supported"></a>不支持迁移时态表

**症状**

如果源数据库包含一个或多个时态表，在执行“完整数据加载”操作期间数据库迁移将会失败，并可能出现以下消息：

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![时态表错误示例](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**解决方法**

使用以下步骤。

1. 使用以下查询在源架构中查找时态表。

     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```

2. 在用于指定要迁移的表的“配置迁移设置”边栏选项卡中排除这些表。

3. 重新运行迁移活动。

**资源**

有关详细信息，请参阅[时态表](/sql/relational-databases/tables/temporal-tables?view=sql-server-2017)一文。

### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>迁移的表中包含一个或多个 hierarchyid 数据类型的列

**症状**

在执行“完整数据加载”操作期间，可能会出现一个 SQL 异常，指出“ntext 与 hierarchyid 不兼容”：

![hierarchyid 错误示例](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**解决方法**

使用以下步骤。

1. 使用以下查询来查找包含 hierarchyid 数据类型的列的用户表。

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ```

2. 在用于指定要迁移的表的“配置迁移设置”边栏选项卡中排除这些表。

3. 重新运行迁移活动。

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>在执行“完整数据加载”或“增量数据同步”期间发生迁移失败，并且与架构中的活动触发器发生各种完整性冲突

**解决方法**

使用以下步骤。

1. 使用以下查询在源数据库中查找当前处于活动状态的触发器：

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. 使用 [DISABLE TRIGGER (Transact-SQL)](/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017) 一文中提供的步骤在源数据库中禁用这些触发器。

3. 重新运行迁移活动。

### <a name="support-for-lob-data-types"></a>对 LOB 数据类型的支持

**症状**

如果大型对象 (LOB) 列的长度超过 32 KB，目标上的数据可能会截断。 可使用以下查询检查 LOB 列的长度：

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**解决方法**

如果 LOB 列大于 32 KB，请在 [请求 Azure 数据库迁移](mailto:AskAzureDatabaseMigrations@service.microsoft.com)时联系工程团队。

### <a name="issues-with-timestamp-columns"></a>时间戳列的问题

**症状**

Azure 数据库迁移服务不会迁移源时间戳值，而是在目标表中生成新的时间戳值。

**解决方法**

如果需要 Azure 数据库迁移服务来迁移存储在源表中的确切时间戳值，请在 [请求 Azure 数据库迁移](mailto:AskAzureDatabaseMigrations@service.microsoft.com)时联系工程团队。

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>发生数据迁移错误时，“数据库详细状态”边栏选项卡上不会提供其他详细信息

**症状**

如果“数据库详细状态”视图中显示迁移失败，选择顶部功能区中的“数据迁移错误”链接可能不会提供特定于该迁移失败的其他详细信息。

![发生数据迁移错误时不提供详细信息的示例](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**解决方法**

若要查看特定失败的详细信息，请执行以下步骤。

1. 关闭“数据库详细状态”边栏选项卡以显示“迁移活动”屏幕。

     ![迁移活动屏幕](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. 选择“查看错误详细信息”以查看可帮助排查迁移错误的具体错误消息。

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>SQLDB 联机迁移不支持地理数据类型

**症状**

迁移失败并出现包含以下文本的错误消息：

```output
"** encountered a fatal error", "errorEvents":<Table>.<Column> is of type 'GEOGRAPHY', which is not supported by 'Full Load' under 'Full LOB' support mode.
```

**解决方法**

尽管 Azure 数据库迁移服务支持将地理数据类型的数据脱机迁移到 Azure SQL 数据库，但不支持联机迁移地理数据类型。 在尝试使用 Azure 数据库迁移服务联机迁移此数据库之前，请使用替代方法将源中的数据类型更改为支持的类型。

### <a name="supported-editions"></a>支持的版本

**症状**

迁移失败并出现包含以下文本的错误消息：

```output
Migration settings validation error: The edition of the server [Business Intelligence Edition (64-bit)] does not match the supported edition(s) [Enterprise,Standard,Developer].
```

**解决方法**

只有企业版、标准版和开发人员版才提供使用 Azure 数据库迁移服务联机迁移到 Azure SQL 数据库的扩展支持。 在开始迁移之前，请确保使用受支持的版本。