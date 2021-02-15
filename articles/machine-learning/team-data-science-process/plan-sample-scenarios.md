---
title: 确定 Azure 机器学习场景 - Team Data Science Process
description: 选择适当的方案，用于执行具有团队数据科学过程的高级预测分析。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 2d589d6c3394556499daf033c4c1d528a214b0e3
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93319306"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>用于 Azure 机器学习中高级分析的方案
本文概述了各种示例数据源和可通过[团队数据科学过程 (TDSP)](overview.md) 处理的目标场景。 TDSP 为团队提供了一个系统性方法，可协作构建智能应用程序。 此处介绍的情景说明了数据处理工作流中的可用选项，具体取决于 Azure 中的数据特征、源位置和目标存储库。

“决策树”用于选择适合最后部分中出现的数据和对象的示例情景。

以下各部分都提供了一个示例情景。 每个情景，都列出了可能数据科学或高级分析流和支持 Azure 资源。

> [!NOTE]
> **对于所有以下情况，需要：**
> <br/>
> 
> * [创建存储帐户](../../storage/common/storage-account-create.md)
>   <br/>
> * [创建 Azure 机器学习工作区](../classic/create-workspace.md)
> 
> 

## <a name="scenario-1-small-to-medium-tabular-dataset-in-local-files"></a><a name="smalllocal"></a>场景 \#1：本地文件中的小到中型表格数据集
![小型到中型本地文件][1]

#### <a name="additional-azure-resources-none"></a>其他 Azure 资源：无
1. 登录到 [Azure 机器学习工作室](https://studio.azureml.net/)。
1. 上传数据集。
1. 开始使用上传的数据集生成 Azure 机器学习实验流。

## <a name="scenario-2-small-to-medium-dataset-of-local-files-that-require-processing"></a><a name="smalllocalprocess"></a>场景 \#2：需要处理的本地文件的小型到中型数据集
![需要处理的小型到中型本地文件][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>其他 Azure 资源：Azure 虚拟机（IPython Notebook 服务器）
1. 创建运行 IPython Notebook 的 Azure 虚拟机。
1. 将数据上传到 Azure 存储容器。
1. 在 IPython 笔记本中预处理和清理数据，访问 Azure 存储容器中的数据。
1. 将数据转换为已清除的表格格式。
1. 将已转换的数据保存在 Azure blob 中。
1. 登录到 [Azure 机器学习工作室](https://studio.azureml.net/)。
1. 使用[导入数据][import-data]模块从 Azure blob 中读取数据。
1. 开始使用引入的数据集生成 Azure 机器学习实验流。

## <a name="scenario-3-large-dataset-of-local-files-targeting-azure-blobs"></a><a name="largelocal"></a>场景 \#3：本地文件针对 Azure Blob 的大型数据集
![大型本地文件][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>其他 Azure 资源：Azure 虚拟机（IPython Notebook 服务器）
1. 创建运行 IPython Notebook 的 Azure 虚拟机。
1. 将数据上传到 Azure 存储容器。
1. 访问 Azure blob 中的数据，预处理并清理 IPython Notebook 中的数据。
1. 将数据转换为已清除的表格格式（如果需要）。
1. 浏览数据，并根据需要创建功能。
1. 提取小到中型数据示例。
1. 将采样的数据保存在 Azure blob 中。
1. 登录到 [Azure 机器学习工作室](https://studio.azureml.net/)。
1. 使用[导入数据][import-data]模块从 Azure blob 中读取数据。
1. 开始使用引入的数据集生成 Azure 机器学习实验流。

## <a name="scenario-4-small-to-medium-dataset-of-local-files-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="smalllocaltodb"></a>场景 \#4：本地文件中针对 Azure 虚拟机中 SQL Server 的小型到中型数据集
![Azure 中 SQL DB 的小型到中型本地文件][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>其他 Azure 资源：Azure 虚拟机（SQL Server/IPython Notebook 服务器）
1. 创建运行 SQL Server + IPython Notebook 的 Azure 虚拟机。
1. 将数据上传到 Azure 存储容器。
1. 使用 IPython 笔记本预处理和清理 Azure 存储容器中的数据。
1. 将数据转换为已清除的表格格式（如果需要）。
1. 将数据保存到本地 VM 文件（IPython Notebook 正在 VM 上运行，本地驱动器是指 VM 驱动器）。
1. 将数据加载到在 Azure VM 上运行的 SQL Server 数据库。
   
   选项 \#1：使用 SQL Server Management Studio。
   
   * 登录到 SQL Server VM
   * 运行 SQL Server Management Studio。
   * 创建数据库和目标表。
   * 使用某个大容量导入方法从本地 VM 文件加载数据。
   
   选项 \#2：使用 IPython Notebook - 不建议将其用于中型和较大型数据集
   
   <!-- -->    
   * 使用 ODBC 连接字符串访问 VM 上的 SQL Server。
   * 创建数据库和目标表。
   * 使用某个大容量导入方法从本地 VM 文件加载数据。
1. 浏览数据，根据需要创建功能。 这些功能不需要在数据库表中具体化。 仅请注意创建它们所需的查询。
1. 如果需要和/或想要，请确定数据示例大小。
1. 登录到 [Azure 机器学习工作室](https://studio.azureml.net/)。
1. 使用[导入数据][import-data]模块直接从 SQL Server 中读取数据。 直接在[导入数据][import-data]查询中粘贴必要的查询，该查询可提取字段、创建特征并根据需要对数据进行采样。
1. 开始使用引入的数据集生成 Azure 机器学习实验流。

## <a name="scenario-5-large-dataset-in-local-files-target-sql-server-in-azure-vm"></a><a name="largelocaltodb"></a>场景 \#5：本地文件中的大型数据集、Azure VM 中的目标 SQL Server
![Azure 中 SQL DB 的大型本地文件][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>其他 Azure 资源：Azure 虚拟机（SQL Server/IPython Notebook 服务器）
1. 创建运行 SQL Server 和 IPython Notebook 服务器的 Azure 虚拟机。
1. 将数据上传到 Azure 存储容器。
1. （可选）预处理并清理数据。
   
    a.  访问 Azure blob 中的数据，预处理并清理 IPython Notebook 中的数据。
   
    b.  将数据转换为已清除的表格格式（如果需要）。
   
    c.  将数据保存到本地 VM 文件（IPython Notebook 正在 VM 上运行，本地驱动器是指 VM 驱动器）。
1. 将数据加载到在 Azure VM 上运行的 SQL Server 数据库。
   
    a.  登录到 SQL Server VM。
   
    b.  如果数据尚未保存，请从 Azure 存储容器将数据文件下载到本地 VM 文件夹。
   
    c.  运行 SQL Server Management Studio。
   
    d.  创建数据库和目标表。
   
    e.  使用某个大容量导入方法加载数据。
   
    f.  如果需要表联接，可创建索引以加快联接。
   
   > [!NOTE]
   > 为了加快较大数据大小的加载速度，建议创建分区表，并大容量导入并行数据。 有关详细信息，请参阅[将并行数据导入到 SQL 分区表](parallel-load-sql-partitioned-tables.md)。
   > 
   > 
1. 浏览数据，根据需要创建功能。 这些功能不需要在数据库表中具体化。 仅请注意创建它们所需的查询。
1. 如果需要和/或想要，请确定数据示例大小。
1. 登录到 [Azure 机器学习工作室](https://studio.azureml.net/)。
1. 使用[导入数据][import-data]模块直接从 SQL Server 中读取数据。 直接在[导入数据][import-data]查询中粘贴必要的查询，该查询可提取字段、创建特征并根据需要对数据进行采样。
1. 开始使用上传的数据集对 Azure 机器学习实验流进行采样

## <a name="scenario-6-large-dataset-in-a-sql-server-database-on-premises-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="largedbtodb"></a>场景 \#6：本地 SQL Server 数据库中针对 Azure 虚拟机中 SQL Server 的大型数据集
![Azure 中 SQL DB 的本地大型 SQL DB][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>其他 Azure 资源：Azure 虚拟机（SQL Server/IPython Notebook 服务器）
1. 创建运行 SQL Server 和 IPython Notebook 服务器的 Azure 虚拟机。
1. 使用一种数据导出方法将数据从 SQL Server 导出到转储文件。
   
   > [!NOTE]
   > 如果决定移动本地数据库中的所有数据，另一个方法（更快）是将完整数据库移到 Azure 中的 SQL Server 实例。 跳过导出数据、创建数据库和将数据加载/导入到目标数据库的步骤，使用另一个方法。
   > 
   > 
1. 将转储文件上传到 Azure 存储容器。
1. 将数据加载到在 Azure 虚拟机上运行的 SQL Server 数据库。
   
   a.  登录到 SQL Server VM。
   
   b.  从 Azure 存储容器将数据文件下载到本地 VM 文件夹。
   
   c.  运行 SQL Server Management Studio。
   
   d.  创建数据库和目标表。
   
   e.  使用某个大容量导入方法加载数据。
   
   f.  如果需要表联接，可创建索引以加快联接。
   
   > [!NOTE]
   > 为了加快较大数据大小的加载速度，可创建分区表，并大容量导入并行数据。 有关详细信息，请参阅[将并行数据导入到 SQL 分区表](parallel-load-sql-partitioned-tables.md)。
   > 
   > 
1. 浏览数据，根据需要创建功能。 这些功能不需要在数据库表中具体化。 仅请注意创建它们所需的查询。
1. 如果需要和/或想要，请确定数据示例大小。
1. 登录到 [Azure 机器学习工作室](https://studio.azureml.net/)。
1. 使用[导入数据][import-data]模块直接从 SQL Server 中读取数据。 直接在[导入数据][import-data]查询中粘贴必要的查询，该查询可提取字段、创建特征并根据需要对数据进行采样。
1. 开始使用上传的数据集对 Azure 机器学习实验流进行采样。

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>另一个方法是将完整的数据库从本地 SQL Server 复制到 Azure SQL 数据库
![分离本地 DB，并附加到 Azure 中的 SQL DB][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>其他 Azure 资源：Azure 虚拟机（SQL Server/IPython Notebook 服务器）
要复制 SQL Server VM 中的完整 SQL Server 数据库，假定数据库可暂时处于脱机状态，应将数据库从一个位置/服务器复制到另一个。 可使用 SQL Server Management Studio 对象资源管理器，或使用等效的 Transact-SQL 命令。

1. 在源位置分离数据库。 有关详细信息，请参阅[分离数据库](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx)。
1. 在 Windows Explorer 或“Windows 命令提示”窗口中，将分离的一个或多个数据库文件和日志文件复制到 Azure 中 SQL Server VM 上的目标位置。
1. 将复制的文件附加到目标 SQL Server 实例。 有关详细信息，请参阅 [Attach a Database](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx)。

[通过分离和附加来移动数据库 (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="scenario-7-big-data-in-local-files-target-hive-database-in-azure-hdinsight-hadoop-clusters"></a><a name="largedbtohive"></a>场景 \#7：本地文件中针对 Azure HDInsight Hadoop 群集中 Hive 数据库的大数据
![本地目标 Hive 中的大数据][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>其他 Azure 资源：Azure HDInsight Hadoop 群集和 Azure 虚拟机（IPython Notebook 服务器）
1. 创建运行 IPython Notebook 的 Azure 虚拟机服务器。
1. 创建 Azure HDInsight Hadoop 群集。
1. （可选）预处理并清理数据。
   
   a.  访问 Azure blob 中的数据，预处理并清理 IPython Notebook 中的数据。
   
   b.  将数据转换为已清除的表格格式（如果需要）。
   
   c.  将数据保存到本地 VM 文件（IPython Notebook 正在 VM 上运行，本地驱动器是指 VM 驱动器）。
1. 将数据上传到步骤 2 中选择的 Hadoop 群集的默认容器。
1. 将数据加载到 Azure HDInsight Hadoop 群集中的 Hive 数据库。
   
   a.  登录到 Hadoop 群集的头节点
   
   b.  打开 Hadoop 命令行。
   
   c.  按照 Hadoop 命令行中的命令 `cd %hive_home%\bin` 输入 Hive 根目录。
   
   d.  运行 Hive 查询以创建数据库和表，并将数据从 Blob 存储加载到 Hive 表。
   
   > [!NOTE]
   > 如果数据太大，用户可以使用分区创建 Hive 表。 然后，用户可以使用头节点上 Hadoop 命令行中的 `for` 循环，将数据加载到由分区进行分区的 Hive 表中。
   > 
   > 
1. 浏览数据，并根据需要在 Hadoop 命令行中创建功能。 这些功能不需要在数据库表中具体化。 仅请注意创建它们所需的查询。
   
   a.  登录到 Hadoop 群集的头节点
   
   b.  打开 Hadoop 命令行。
   
   c.  按照 Hadoop 命令行中的命令 `cd %hive_home%\bin` 输入 Hive 根目录。
   
   d.  在 Hadoop 群集的头节点上的 Hadoop 命令行中运行 Hive 查询，可浏览数据并根据需要创建功能。
1. 如果需要和/或想要，对数据进行采样以满足 Azure 机器学习工作室。
1. 登录到 [Azure 机器学习工作室](https://studio.azureml.net/)。
1. 使用[导入数据][import-data]模块直接从 `Hive Queries` 中读取数据。 直接在[导入数据][import-data]查询中粘贴必要的查询，该查询可提取字段、创建特征并根据需要对数据进行采样。
1. 开始使用上传的数据集对 Azure 机器学习实验流进行采样。

## <a name="decision-tree-for-scenario-selection"></a><a name="decisiontree"></a>用于方案选择的决策树
---
下面的图表总结了上述的方案以及让你采用每个详细方案的高级分析流程和技术选择。 数据处理、浏览、特征工程和采样可能会出现在一个或多个方法/环境中（在源、中间和/或目标环境中），并且可能会根据需要以迭代方式进行。 此图只用于列举某些可能流，并不提供详尽的枚举。

![示例 DS 流程演练方案][8]

### <a name="advanced-analytics-in-action-examples"></a>操作示例中的高级分析
关于利用使用公共数据集的高级分析流程和技术的端到端 Azure 机器学习演练，请参阅：

* [运行中的团队数据科学过程：使用 SQL Server](sql-walkthrough.md)。
* [运行中的团队数据科学过程：使用 HDInsight Hadoop 群集](hive-walkthrough.md)。

[1]: ./media/plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data