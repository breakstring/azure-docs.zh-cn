---
title: 弹性数据库工具入门
description: 大致介绍 Azure SQL 数据库的弹性数据库工具功能，包括易于使用的示例应用。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: anumjs
ms.author: anjangsh
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 74343b2f05bb4a59e475449c87524ff66cdd605d
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919538"
---
# <a name="get-started-with-elastic-database-tools"></a>弹性数据库工具入门
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本文档通过帮助你运行示例应用来介绍[弹性数据库客户端库](elastic-database-client-library.md)的开发人员体验。 此示例应用将创建一个简单的分片应用程序，并探讨 Azure SQL 数据库的弹性数据库工具特性的主要功能。 它重点介绍[分片映射管理](elastic-scale-shard-map-management.md)、[数据依赖型路由](elastic-scale-data-dependent-routing.md)和[多分片查询](elastic-scale-multishard-querying.md)的用例。 该客户端库可用于 Java 和 .NET。

## <a name="elastic-database-tools-for-java"></a>适用于 Java 的弹性数据库工具

### <a name="prerequisites"></a>先决条件

* Java 开发人员工具包 (JDK) 1.8 或更高版本
* [Maven](https://maven.apache.org/download.cgi)
* SQL 数据库或本地 SQL Server 实例

### <a name="download-and-run-the-sample-app"></a>下载并运行示例应用

若要生成 JAR 文件并开始使用示例项目，请执行以下操作：

1. 克隆包含客户端库以及示例应用的 [GitHub 存储库](https://github.com/Microsoft/elastic-db-tools-for-java)。

2. 编辑 _./sample/src/main/resources/resource.properties_ 文件以设置以下内容：
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. 若要生成示例项目，请在 _./sample_ 目录中运行以下命令：

    ```
    mvn install
    ```

4. 若要启动示例项目，请在 _./sample_ 目录中运行以下命令：

    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```

5. 若要详细了解客户端库功能，请试验各种选项。 随意浏览代码以了解示例应用的实现方法。

    ![Progress-java][5]

祝贺！ 你已成功使用弹性数据库工具在 Azure SQL 数据库上生成并运行了第一个分片应用程序。 使用 Visual Studio 或 SQL Server Management Studio 连接到数据库，并快速查看一下样本创建的分片。 会看到该示例创建的新示例分片数据库和分片映射管理器数据库。

若要将客户端库添加到自己的 Maven 项目，请在 POM 文件中添加以下依赖项：

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>elastic-db-tools</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="elastic-database-tools-for-net"></a>适用于 .NET 的弹性数据库工具

### <a name="prerequisites"></a>先决条件

* 使用 C# 的 Visual Studio 2012 或更高版本。 可以从 [Visual Studio 下载页面](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)下载免费版本。
* NuGet 2.7 或更高版本。 若要获取最新版本，请参阅[安装 NuGet](https://docs.nuget.org/docs/start-here/installing-nuget)。

### <a name="download-and-run-the-sample-app"></a>下载并运行示例应用

若要安装该库，请转到 [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)。 该库和以下部分中描述的示例应用一起安装。

若要下载并运行该示例，请遵循以下步骤：

1. 下载 [Elastic DB Tools for Azure SQL - Getting Started sample](https://github.com/Azure/elastic-db-tools)（Azure SQL 弹性数据库工具 - 入门示例）。 将示例解压缩到所选位置。

2. 若要创建项目，请从 *弹性-db-master* 目录打开 *ElasticDatabaseTools* 解决方案。 

3. 将 *elasticscalestarterkit.sln* 项目设置为启动项目。

4. 在 *elasticscalestarterkit.sln* 项目中，打开 *App.config* 文件。 然后遵循该文件中的说明，添加服务器名称和登录信息（用户名和密码）。

5. 生成并运行应用程序。 出现提示时，请允许 Visual Studio 还原该解决方案的 NuGet 包。 此操作从 NuGet 下载最新版本的弹性数据库客户端库。

6. 若要详细了解客户端库功能，请试验各种选项。 请注意应用程序在控制台输出中执行的步骤，并随意浏览后台代码。

   ![进度][4]

祝贺！ 你已成功使用弹性数据库工具在 SQL 数据库上生成并运行了第一个分片应用程序。 使用 Visual Studio 或 SQL Server Management Studio 连接到数据库，并快速查看一下样本创建的分片。 会看到该示例创建的新示例分片数据库和分片映射管理器数据库。

> [!IMPORTANT]
> 建议始终使用最新版本的 Management Studio，以便与 Azure 和 SQL 数据库的更新保持同步。 [更新 SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)。

## <a name="key-pieces-of-the-code-sample"></a>重要的代码示例片段

* **管理分片和分片映射**：该代码演示如何在“ShardManagementUtils.cs”文件中处理分片、范围和映射。 有关详细信息，请参阅[使用分片映射管理器扩大数据库](https://go.microsoft.com/?linkid=9862595)。  

* **数据依赖型路由**：“DataDependentRoutingSample.cs”文件演示了如何向正确的分片路由事务。 有关详细信息，请参阅[数据依赖型路由](https://go.microsoft.com/?linkid=9862596)。

* **跨多个分片进行查询**：“MultiShardQuerySample.cs”文件演示了如何跨多个分片进行查询。 有关详细信息，请参阅[多分片查询](https://go.microsoft.com/?linkid=9862597)。

* **添加空分片**：“CreateShardSample.cs”文件中的代码以迭代方式添加新的空分片。 有关详细信息，请参阅[使用分片映射管理器扩大数据库](https://go.microsoft.com/?linkid=9862595)。

## <a name="other-elastic-scale-operations"></a>其他弹性缩放操作

* **拆分现有分片**：拆分分片的功能由拆分/合并工具提供。 有关详细信息，请参阅[在横向扩展的云数据库之间移动数据](elastic-scale-overview-split-and-merge.md)。

* **合并现有分片**：分片合并也是使用拆分/合并工具执行的。 有关详细信息，请参阅[在横向扩展的云数据库之间移动数据](elastic-scale-overview-split-and-merge.md)。

## <a name="cost"></a>成本

弹性数据库工具库免费。 使用弹性数据库工具时，除 Azure 使用费用外，不需支付任何额外费用。

例如，示例应用程序会创建新的数据库。 此功能的费用取决于所选的 SQL 数据库版本以及应用程序的 Azure 使用情况。

有关定价信息，请参阅 [SQL 数据库定价详细信息](https://azure.microsoft.com/pricing/details/sql-database/)。

## <a name="next-steps"></a>后续步骤

有关弹性数据库工具的详细信息，请参阅以下文章：

* 代码示例：
  * 弹性数据库工具（[.NET](https://github.com/Azure/elastic-db-tools)、[Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22)）
  * [Elastic Database Tools for Azure SQL - Entity Framework Integration](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)（Azure SQL 的弹性数据库工具 - 实体框架集成）
  * [脚本中心上的分片弹性](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* 博客：[弹性缩放公告](https://azure.microsoft.com/blog/20../../introducing-elastic-scale-preview-for-azure-sql-database/)
* 第 9 频道：[弹性缩放概述视频](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* 讨论论坛： [Microsoft Q&适用于 AZURE SQL 数据库的问题页面](/answers/topics/azure-sql-database.html)
* 衡量性能：[分片映射管理器的性能计数器](elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/elastic-scale-get-started/newProject.png
[2]: ./media/elastic-scale-get-started/click-online.png
[3]: ./media/elastic-scale-get-started/click-CSharp.png
[4]: ./media/elastic-scale-get-started/output2.png
[5]: ./media/elastic-scale-get-started/java-client-library.PNG