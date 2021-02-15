---
title: 使用 Apache Phoenix 在 Azure HDInsight 中运行 Apache Base 查询
description: 了解如何将 Apache Zeppelin 与 Phoenix 配合使用来运行 Apache Base 查询。
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 10/14/2019
ms.openlocfilehash: 50a72d0400b23162e05b17b37bdad48783261072
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944765"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>使用 Apache Zepperin 在 Azure HDInsight 中的 Apache HBase 上运行 Apache Phoenix 查询

Apache Phoenix 是构建在 HBase 基础之上的开源大规模并行关系数据库层。 Phoenix 允许你在 HBase 上使用类似于 SQL 的查询。 Phoenix 在底层使用 JDBC 驱动程序，可让你创建、删除和更改 SQL 表、索引、视图与序列。  还可以使用 Phoenix 以单个方式或以批量方式更新行。 Phoenix 使用 NOSQL 本机编译而不是使用 MapReduce 来编译查询，可让用户基于 HBase 创建低延迟的应用程序。

Apache Zeppelin 是一个基于 Web 的开源笔记本，可用于使用交互式数据分析和语言（例如 SQL 和 Scala）创建数据驱动的协作文档。 它帮助数据开发人员和数据科学家开发、组织、执行和共享用于数据操作的代码。 它使你可以直观显示结果，而无需借助于命令行或需要群集详细信息。

HDInsight 用户可以使用 Apache Zeppelin 查询 Phoenix 表。 Apache Zeppelin 已与 HDInsight 群集集成，无需额外步骤即可使用该群集。 只需使用 JDBC 解释器创建一个 Zeppelin Notebook，然后开始编写 Phoenix SQL 查询

## <a name="prerequisites"></a>先决条件

HDInsight 上的 Apache HBase 群集。 请参阅 [Apache HBase 入门](./apache-hbase-tutorial-get-started-linux.md)。

## <a name="create-an-apache-zeppelin-note"></a>创建 Apache Zeppelin 笔记

1. 请将以下 URL 中的 `CLUSTERNAME` 替换为你的群集的名称：`https://CLUSTERNAME.azurehdinsight.net/zeppelin`。 然后在 Web 浏览器中输入该 URL。 输入群集登录用户名和密码。

1. 在 Zeppelin 页上，选择“创建新笔记”  。

    ![HDInsight 交互式查询 zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. 在“创建新笔记”对话框中，键入或选择以下值： 

    - 笔记名称：输入笔记的名称。
    - 默认解释器：从下拉列表中选择“jdbc”。 

    然后选择“创建笔记”  。

1. 确保笔记本标题显示“已连接”状态。 该状态由右上角的一个绿点表示。

    ![Zeppelin 笔记本状态](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Zeppelin 笔记本状态")

1. 创建一个 HBase 表。 输入以下命令，然后按 **Shift + Enter**：

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    第一行中的 **%jdbc(phoenix)** 语句告诉笔记本使用 Phoenix JDBC 解释器。

1. 查看已创建的表。

    ```sql
    %jdbc(phoenix)
    SELECT DISTINCT table_name
    FROM SYSTEM.CATALOG
    WHERE table_schem is null or table_schem <> 'SYSTEM';
    ```

1. 在表中插入值。

    ```sql
    %jdbc(phoenix)
    UPSERT INTO dbo.Company VALUES(1, 'Microsoft');
    UPSERT INTO dbo.Company (name, company_id) VALUES('Apache', 2);
    ```

1. 查询表。

    ```sql
    %jdbc(phoenix)
    SELECT * FROM dbo.Company;
    ```

1. 删除记录。

    ```sql
    %jdbc(phoenix)
    DELETE FROM dbo.Company WHERE COMPANY_ID=1;
    ```

1. 删除表。

    ```sql
    %jdbc(phoenix)
    DROP TABLE dbo.Company;
    ```

## <a name="next-steps"></a>后续步骤

- [Apache Phoenix 现在支持 Azure HDInsight 中的 Zeppelin](/archive/blogs/ashish/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight)
- [Apache Phoenix 语法](https://phoenix.apache.org/language/index.html)