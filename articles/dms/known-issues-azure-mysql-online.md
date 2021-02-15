---
title: 已知问题：联机迁移到 Azure Database for MySQL
titleSuffix: Azure Database Migration Service
description: 了解在使用 Azure 数据库迁移服务时联机迁移到 Azure Database for MySQL 的已知问题和迁移限制。
services: database-migration
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 11659bcbdf77d04c0f4e6f8bc7aca30c716fc924
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606883"
---
# <a name="online-migration-issues--limitations-to-azure-db-for-mysql-with-azure-database-migration-service"></a>使用 Azure 数据库迁移服务联机迁移到 Azure DB for MySQL 的问题和限制

以下部分描述了在从 MySQL 联机迁移到 Azure Database for MySQL 时存在的已知问题和限制。

## <a name="online-migration-configuration"></a>联机迁移配置


- 源 MySQL 服务器版本必须是 5.6.35、5.7.18 或以上
- Azure Database for MySQL 支持：
  - MySQL 社区版
  - InnoDB 引擎
- 相同版本的迁移。 不支持将 MySQL 5.6 迁移到 Azure Database for MySQL 5.7。 不支持向/从 MySQL 8.0 进行迁移。
- 在 my.ini (Windows) 或 my.cnf (Unix) 中启用二进制日志记录
  - 将 Server_id 设为大于或等于 1 的任意数字，例如 Server_id=1（仅适用于 MySQL 5.6）
  - 设置 log-bin = \<path>（仅适用于 MySQL 5.6）
  - 设置 binlog_format = row
  - Expire_logs_days = 5（建议 - 仅适用于 MySQL 5.6）
- 用户必须具有 ReplicationAdmin 角色。
- 为源 MySQL 数据库定义的排序规则与目标 Azure Database for MySQL 中定义的排序规则相同。
- Azure Database for MySQL 中源 MySQL 数据库与目标数据库的架构必须匹配。
- 目标 Azure Database for MySQL 中的架构不能包含外键。 使用以下查询来删除外键：
    ```sql
    SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME,
      CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
        KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
        AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['schema_name']) Queries
      GROUP BY SchemaName;
    ```

    运行查询结果中的 drop foreign key（第二列）。
- 目标 Azure Database for MySQL 中的架构不能包含任何触发器。 在目标数据库中删除触发器：
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>数据类型限制

- **限制**：如果源 MySQL 数据库中存在 JSON 数据类型，则在连续同步期间迁移将会失败。

    **解决方法**：在源 MySQL 数据库中将 JSON 数据类型修改为中等长度的文本或长文本。

- **限制**：如果表中没有主键，连续同步将会失败。

    **解决方法**：暂时为表设置一个主键，以便迁移能够继续。 数据迁移完成后，可以删除该主键。

## <a name="lob-limitations"></a>LOB 限制

大型对象 (LOB) 列可能会增大。 MySQL、中等长度文本、长文本、Blob、中等 Blob、长 Blob 等都属于 LOB 数据类型。

- **限制**：如果将 LOB 数据类型用作主键，迁移将会失败。

    **解决方法**：将主键替换为不属于 LOB 的其他数据类型或列。

- **限制**：如果大型对象 (LOB) 列的长度超过“Limit LOB size”参数的长度（不应超过 64 KB），则可能会在目标位置截断数据。 可使用以下查询检查 LOB 列的长度：
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **解决方法**：如果 LOB 对象大于 64 KB，请使用“Allow unlimited LOB size”参数。 请注意，使用“Allow unlimited LOB size”参数进行的迁移将比使用“Limit LOB size”参数进行的迁移要慢。

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>从 AWS RDS MySQL 联机迁移时的限制

尝试从 AWS RDS MySQL 联机迁移到 Azure Database for MySQL 时，你可能会遇到以下错误。

- **错误：** 数据库“{0}”在目标上有外键。 修复目标并启动新的数据迁移活动。 在目标上执行以下脚本以列出外键

  **限制**：如果架构中有外键，则迁移的初始加载和连续同步会失败。
  **解决方法**：请在 MySQL Workbench 中执行以下脚本来提取“删除外键”脚本和“添加外键”脚本：

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **错误：** 服务器上不存在数据库“{0}”。 提供的 MySQL 源服务器区分大小写。 请检查数据库名称。

  **限制**：使用命令行接口 (CLI) 将 MySQL 数据库迁移到 Azure 时，用户可能会遇到此错误。 服务在源服务器上找不到该数据库，原因可能是提供了错误的数据库名称，或者该数据库不存在于所列的服务器上。 请注意，数据库名称区分大小写。

  **解决方法**：提供准确的数据库名称，然后重试。

- **错误：** 数据库“{database}”中存在同名的表。 Azure Database for MySQL 不支持区分大小写的表。

  **限制**：如果源数据库中有两个同名的表，则会出现此错误。 Azure Database for MySQL 不支持区分大小写的表。

  **解决方法**：将表名称更新为唯一名称，然后重试。

- **错误：** 目标数据库 {database} 为空。 请迁移架构。

  **限制**：如果目标 Azure Database for MySQL 数据库没有所需的架构，则会出现此错误。 若要将数据迁移到目标，需要进行架构迁移。

  **解决方法**：[将架构从源数据库迁移到目标数据库](./tutorial-mysql-azure-mysql-online.md#migrate-the-sample-schema)。

## <a name="other-limitations"></a>其他限制

- 不支持在开头和末尾包含左右大括号 {  } 的密码字符串。 连接到源 MySQL 和目标 Azure Database for MySQL 时，都存在这种限制。
- 不支持以下 DDL：
  - 所有分区 DDL
  - 删除表
  - 重命名表
- 不支持使用 *alter table <表名> add column <列名>* 语句将列添加到表的开头或中间。 *alter table <表名> add column <列名>* 会在表的末尾添加列。
- 不支持基于一部分列数据创建的索引。 以下示例语句仅使用一部分列数据创建索引：

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- 在 Azure 数据库迁移服务中，可在单个迁移活动中迁移的数据库数目限制为 4 个。

- Azure DMS 不支持 CASCADE 引用操作，这有助于当父表中删除或更新行时，自动删除或更新子表中的匹配行。 有关详细信息，请参见 MySQL 文档中的[外键约束](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html)一文中的“引用操作”部分。 Azure DMS 要求在初始数据加载过程中在目标数据库服务器中删除外键约束，并且不能使用引用操作。 如果你的工作负载依赖于通过此引用操作更新相关子表，建议改为执行[转储并还原](../mysql/concepts-migrate-dump-restore.md)。 

- **错误：** 行太大 (> 8126)。 将某些列更改为 TEXT 或 BLOB 可能会有帮助。 在当前的行格式中，0 字节的 BLOB 前缀以内联方式存储。

  **限制**：使用 InnoDB 存储引擎迁移到 Azure Database for MySQL 且任意表行过大（>8126 字节）时，会发生此错误。

  **解决方法**：更新其行大小大于 8126 字节的表的架构。 我们建议不要更改严格模式，因为更改后会截断数据。 不支持更改 page_size。