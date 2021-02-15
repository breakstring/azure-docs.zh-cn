---
title: 使用 Apache Spark 在 Azure SQL 数据库中读取和写入数据
description: 了解如何在 HDInsight Spark 群集与 Azure SQL 数据库之间建立连接。 在 SQL 数据库中读取数据、写入数据，以及将数据流式传输到 SQL 数据库中
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 4d42cedbc5dc20c929703be106e732b4806f3902
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940602"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>使用 HDInsight Spark 群集在 Azure SQL 数据库中读取和写入数据

了解如何使用 Azure SQL 数据库将 Azure HDInsight 中的 Apache Spark 群集连接起来。 然后，在 SQL 数据库中读取、写入和流式传输数据。 本文中的说明使用 Jupyter Notebook 运行 Scala 代码片段。 但是，可以在 Scala 或 Python 中创建独立的应用程序，然后执行相同的任务。

## <a name="prerequisites"></a>先决条件

* Azure HDInsight Spark 群集。  遵照[在 HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)中的说明。

* Azure SQL 数据库。 按照[在 Azure SQL 数据库中创建数据库](../../azure-sql/database/single-database-create-quickstart.md)中的说明进行操作。 确保使用示例 **AdventureWorksLT** 架构和数据创建数据库。 另外，请确保创建服务器级防火墙规则，以允许客户端的 IP 地址访问 SQL 数据库。 同一篇文章中提供了有关添加防火墙规则的说明。 创建 SQL 数据库后，请确保准备好以下值。 从 Spark 群集连接到数据库时需要这些值。

    * 服务器名称。
    * 数据库名称。
    * Azure SQL 数据库管理员用户名/密码。

* SQL Server Management Studio (SSMS)。 遵照[使用 SSMS 连接和查询数据](../../azure-sql/database/connect-query-ssms.md)中的说明。

## <a name="create-a-jupyter-notebook"></a>创建 Jupyter Notebook

首先，创建与 Spark 群集关联的 Jupyter Notebook。 到时要使用此 Notebook 来运行本文中所用的代码片段。

1. 从 [Azure 门户网站](https://portal.azure.com/)打开群集。
1. 选择右侧的 "**群集仪表板**" 下面的 **Jupyter Notebook** 。  如果没有看到“群集仪表板”，请从左侧菜单中选择“概述”。 出现提示时，请输入群集的管理员凭据。

    ![Apache Spark 上的 Jupyter Notebook](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Spark 上的 Jupyter Notebook")

   > [!NOTE]  
   > 还可以通过在浏览器中打开以下 URL 来访问 Spark 群集上的 Jupyter Notebook。 将 **CLUSTERNAME** 替换为群集的名称：
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. 在 Jupyter Notebook 中，单击右上角的 " **新建**"，然后单击 " **Spark** " 创建 Scala 笔记本。 HDInsight Spark 群集上的 Jupyter 笔记本还提供 Python2 应用程序的 **PySpark** 内核，以及适用于 Python3 应用程序的 **PySpark3** 内核。 本文将会创建 Scala 笔记本。

    ![Spark 上用于 Jupyter Notebook 的内核](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Spark 上用于 Jupyter Notebook 的内核")

    有关内核的详细信息，请参阅 [在 HDInsight 中将 Jupyter Notebook 内核与 Apache Spark 群集配合使用](apache-spark-jupyter-notebook-kernels.md)。

   > [!NOTE]  
   > 在本文中，我们将使用 Spark (Scala) 内核，因为目前只有 Scala 和 Java 支持从 Spark 到 SQL 数据库的流数据。 尽管可以使用 Python 在 SQL中 读取和写入数据，但为了保持一致，本文将使用 Scala 执行所有三个操作。

1. 将打开默认名称为“无标题”的新笔记本。 单击笔记本名称，然后输入所选的名称。

    ![提供笔记本的名称](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "提供笔记本的名称")

现在可以开始创建应用程序。

## <a name="read-data-from-azure-sql-database"></a>从 Azure SQL 数据库读取数据

在本部分，我们要从 AdventureWorks 数据库中的某个表（例如 **SalesLT.Address**）读取数据。

1. 在新 Jupyter Notebook 的代码单元中，粘贴以下代码片段，并将占位符值替换为数据库的值。

    ```scala
    // Declare the values for your database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    按 **SHIFT + ENTER** 运行代码单元。  

1. 使用以下代码片段生成可传递给 Spark 数据帧 API 的 JDBC URL。 此代码创建一个 `Properties` 对象来保存参数。 粘贴代码单元中的代码片段，然后按 **SHIFT + ENTER** 运行。

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. 使用下面的代码片段，使用数据库中的表中的数据创建数据帧。 此代码片段使用 AdventureWorksLT 数据库中包含的 `SalesLT.Address` 表。 粘贴代码单元中的代码片段，然后按 **SHIFT + ENTER** 运行。

    ```scala
    val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)
    ```

1. 现在，可以对数据帧执行操作，例如，获取数据架构：

    ```scala
    sqlTableDF.printSchema
    ```

    会显示如下图所示的输出：

    ![架构输出](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "架构输出")

1. 还可以执行其他操作，例如检索前 10 行。

    ```scala
    sqlTableDF.show(10)
    ```

1. 或者，从数据集检索特定的列。

    ```scala
    sqlTableDF.select("AddressLine1", "City").show(10)
    ```

## <a name="write-data-into-azure-sql-database"></a>将数据写入 Azure SQL 数据库

在本部分中，我们将使用群集上提供的示例 CSV 文件在数据库中创建一个表，并在其中填充数据。 该示例 CSV 文件 (**HVAC.csv**) 已在所有 HDInsight 群集上提供，路径为 `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`。

1. 在新 Jupyter Notebook 的代码单元中，粘贴以下代码片段，并将占位符值替换为数据库的值。

    ```scala
    // Declare the values for your database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    按 **SHIFT + ENTER** 运行代码单元。  

1. 以下代码片段生成可传递给 Spark 数据帧 API 的 JDBC URL。 此代码创建一个 `Properties` 对象来保存参数。 粘贴代码单元中的代码片段，然后按 **SHIFT + ENTER** 运行。

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. 使用以下代码片段提取 HVAC.csv 中的数据架构，并使用该架构将 CSV 中的数据载入数据帧 `readDf`。 粘贴代码单元中的代码片段，然后按 **SHIFT + ENTER** 运行。

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. 使用 `readDf` 数据帧创建临时表 `temphvactable`。 然后使用该临时表创建 Hive 表 `hvactable_hive`。

    ```scala
    readDf.createOrReplaceTempView("temphvactable")
    spark.sql("create table hvactable_hive as select * from temphvactable")
    ```

1. 最后，使用 hive 表在您的数据库中创建一个表。 以下代码片段在 Azure SQL 数据库中创建 `hvactable`。

    ```scala
    spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)
    ```

1. 使用 SSMS 连接到 Azure SQL 数据库，并确认其中是否显示了 `dbo.hvactable`。

    a. 启动 SSMS，然后提供以下屏幕截图中所示的连接详细信息，以连接到 Azure SQL 数据库。

    ![使用 SSMS1 连接到 SQL 数据库](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "使用 SSMS1 连接到 SQL 数据库")

    b. 在 **对象资源管理器** 中，展开 "数据库" 和 "表" 节点以查看创建的 **hvactable** 。

    ![使用 SSMS2 连接到 SQL 数据库](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "使用 SSMS2 连接到 SQL 数据库")

1. 在 SSMS 中运行查询以查看表中的列。

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>将数据流式传输到 Azure SQL 数据库

在本部分，我们要将数据流式传输到上一部分已创建的 `hvactable`。

1. 首先，请确保 `hvactable` 中没有记录。 使用 SSMS 针对该表运行以下查询。

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. 在 HDInsight Spark 群集上创建新的 Jupyter Notebook。 在代码单元中粘贴以下代码片段，然后按 **SHIFT + ENTER**：

    ```scala
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    import org.apache.spark.sql.streaming._
    import java.sql.{Connection,DriverManager,ResultSet}
    ```

1. 我们要将数据从 HVAC.csv 流式传输到 `hvactable`。 HVAC.csv 文件已在群集上提供，其路径为 `/HdiSamples/HdiSamples/SensorSampleData/HVAC/`。 在以下代码片段中，我们先获取要流式传输的数据的架构。 然后，使用该架构创建流数据帧。 粘贴代码单元中的代码片段，然后按 **SHIFT + ENTER** 运行。

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
    readStreamDf.printSchema
    ```

1. 输出显示 **HVAC.csv** 的架构。 `hvactable` 也有相同的架构。 输出列出表中的列。

    ![HDInsight Apache Spark 架构表](./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png "表的架构")

1. 最后，使用以下代码片段从 HVAC.csv 中读取数据，并将其流式传输到 `hvactable` 数据库中的。 将代码片段粘贴到代码单元中，将占位符值替换为数据库的值，然后按 **SHIFT + enter** 运行。

    ```scala
    val WriteToSQLQuery  = readStreamDf.writeStream.foreach(new ForeachWriter[Row] {
        var connection:java.sql.Connection = _
        var statement:java.sql.Statement = _

        val jdbcUsername = "<SQL DB ADMIN USER>"
        val jdbcPassword = "<SQL DB ADMIN PWD>"
        val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
        val jdbcPort = 1433
        val jdbcDatabase ="<AZURE SQL DB NAME>"
        val driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
        val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"

        def open(partitionId: Long, version: Long):Boolean = {
        Class.forName(driver)
        connection = DriverManager.getConnection(jdbc_url, jdbcUsername, jdbcPassword)
        statement = connection.createStatement
        true
        }

        def process(value: Row): Unit = {
        val Date  = value(0)
        val Time = value(1)
        val TargetTemp = value(2)
        val ActualTemp = value(3)
        val System = value(4)
        val SystemAge = value(5)
        val BuildingID = value(6)  

        val valueStr = "'" + Date + "'," + "'" + Time + "'," + "'" + TargetTemp + "'," + "'" + ActualTemp + "'," + "'" + System + "'," + "'" + SystemAge + "'," + "'" + BuildingID + "'"
        statement.execute("INSERT INTO " + "dbo.hvactable" + " VALUES (" + valueStr + ")")
        }

        def close(errorOrNull: Throwable): Unit = {
        connection.close
        }
        })

    var streamingQuery = WriteToSQLQuery.start()
    ```

1. 通过在 SQL Server Management Studio (SSMS) 中运行以下查询，验证数据是否正在流式传输到 `hvactable`。 每次运行该查询时，它都会显示表中的行数已递增。

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>后续步骤

* [使用 HDInsight Spark 群集分析 Data Lake Storage 中的数据](apache-spark-use-with-data-lake-store.md)
* [在 Azure HDInsight 中的 Apache Spark 群集上加载数据并运行查询](apache-spark-load-data-run-query.md)
* [将 Apache Spark 结构化流式处理与 Apache Kafka on HDInsight 配合使用](../hdinsight-apache-kafka-spark-structured-streaming.md)
