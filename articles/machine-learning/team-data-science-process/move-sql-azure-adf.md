---
title: 通过 Azure 数据工厂将数据 SQL Server 到 SQL 数据库-团队数据科学流程
description: 设置一个 ADF 管道，它由两个数据迁移活动组成，这两个活动每天共同在本地和云中的数据库之间移动数据。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/03/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 02fd6c1d4cbd1c2db287a38e086045042b5f220a
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93309548"
---
# <a name="move-data-from-a-sql-server-database-to-sql-database-with-azure-data-factory"></a>使用 Azure 数据工厂将数据从 SQL Server 数据库移到 SQL 数据库

本文介绍如何使用 Azure 数据工厂 (ADF) 通过 Azure Blob 存储将数据从 SQL Server 数据库移到 Azure SQL 数据库：此方法是一种受支持的旧方法，具有复制临时副本的优势，尽管[我们建议查看“数据迁移”页以了解最新选项](https://datamigration.microsoft.com/scenario/sql-to-azuresqldb?step=1)。

有关汇总了用于将数据移到 Azure SQL 数据库的各种选项的表格，请参阅[将数据移到 Azure SQL 数据库进行 Azure 机器学习](move-sql-azure.md)。

## <a name="introduction-what-is-adf-and-when-should-it-be-used-to-migrate-data"></a><a name="intro"></a>简介：什么是 ADF 以及它应何时用于迁移数据？
Azure 数据工厂是一项完全托管、基于云的数据集成服务，可安排并自动化处理数据的移动和转换。 ADF 模型中的关键概念是管道。 管道是活动的逻辑分组，其中每个活动定义对包含在数据集中的数据所执行的操作。 链接服务用于定义数据工厂连接到数据资源所需的信息。

凭借 ADF，可将现有的数据处理服务整合到数据管道中，该数据管道具有高可用性且托管在云中。 可安排这些数据管道用于引入、准备、转换、分析和发布数据，ADF 可管理并安排复杂数据和处理依赖关系。 可在云中快速生成并部署解决方案，连接越来越多的本地和云数据源。

在以下情况中，请考虑使用 ADF：

* 在同时访问本地和云资源的混合方案中需要不断迁移数据时
* 数据需要转换或在迁移过程中向其添加了业务逻辑时。

ADF 允许使用简单的 JSON 脚本计划和监视作业，JSON 脚本可定期管理数据移动。 ADF 还具有其他功能，例如支持复杂操作。 有关 ADF 的详细信息，请参阅 [Azure 数据工厂 (ADF)](https://azure.microsoft.com/services/data-factory/) 中的文档。

## <a name="the-scenario"></a><a name="scenario"></a>方案
我们设置了一个由两个数据迁移活动组成的 ADF 管道。 它们每天共同在 SQL Server 数据库和 Azure SQL 数据库之间移动数据。 这两个活动是：

* 将数据从 SQL Server 数据库复制到 Azure Blob 存储帐户
* 从 Azure Blob 存储帐户将数据复制到 Azure SQL 数据库。

> [!NOTE]
> 此处所示的步骤改编自 ADF 团队提供的更详细教程：[将数据从 SQL Server 数据库复制到 Azure Blob 存储](../../data-factory/tutorial-hybrid-copy-portal.md) 适时提供对该主题相关部分的引用。
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>先决条件
本教程假设你拥有：

* 一个 **Azure 订阅** 。 如果尚无订阅，可注册[免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* 一个 **Azure 存储帐户** 。 在本教程中，将使用 Azure 存储帐户存储数据。 如果还没有 Azure 存储帐户，请参阅[创建存储帐户](../../storage/common/storage-account-create.md)一文。 创建存储帐户后，需要获取用于访问存储的帐户密钥。 请参阅[管理存储帐户访问密钥](../../storage/common/storage-account-keys-manage.md)。
* 访问 **Azure SQL 数据库** 。 如果必须设置 Azure SQL 数据库，可在主题 [Microsoft Azure SQL 数据库入门](../../azure-sql/database/single-database-create-quickstart.md)中找到相关信息，了解如何预配 Azure SQL 数据库的新实例。
* 已在本地安装和配置 **Azure PowerShell** 。 有关说明，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/)。

> [!NOTE]
> 此过程将使用 [Azure 门户](https://portal.azure.com/)。
>
>

## <a name="upload-the-data-to-your-sql-server-instance"></a><a name="upload-data"></a> 将数据上传到 SQL Server 实例
将使用 [NYC 出租车数据集](https://chriswhong.com/open-data/foil_nyc_taxi/)来演示迁移过程。 该文章所述的 NYC 出租车数据集在 Azure Blob 存储 [NYC 出租车数据](https://www.andresmh.com/nyctaxitrips/)上可用。 该数据具有两个文件，trip_data.csv 文件（包含行程详情）和 trip_far.csv 文件（包含每次行程的费用详情）。 [NYC 出租车行程数据集说明](sql-walkthrough.md#dataset)中介绍了这些文件的示例和说明。

可将此处提供的流程调整为自己的一组数据，或者使用 NYC 出租车数据集遵循所述的步骤进行操作。 若要将 NYC 出租车数据集上传到 SQL Server 数据库，请按照[将数据批量导入 SQL Server 数据库](sql-walkthrough.md#dbload)中概述的过程进行操作。

## <a name="create-an-azure-data-factory"></a><a name="create-adf"></a>创建 Azure 数据工厂
[创建 Azure 数据工厂](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory)中提供了在 [Azure 门户](https://portal.azure.com/)中创建新的 Azure 数据工厂和资源组的相关说明。 将新的 ADF 实例命名为 *adfdsp* ，将创建的资源组命名为 *adfdsprg* 。

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>安装和配置 Azure 数据工厂集成运行时
Integration Runtime 是由 Azure 数据工厂用于在不同的网络环境之间提供数据集成功能的客户托管的数据集成基础结构。 此运行时以前称为“数据管理网关”。

若要进行设置，请[按照有关创建管道的说明进行操作](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)

## <a name="create-linked-services-to-connect-to-the-data-resources"></a><a name="adflinkedservices"></a>创建链接服务以连接到数据资源
链接服务定义 Azure 数据工厂连接到数据资源所需的信息。 在此方案中，我们有三个需要链接服务的资源：

1. 本地 SQL Server
2. Azure Blob 存储
3. Azure SQL Database

[创建链接服务](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)中提供了创建链接服务的分步过程。


## <a name="define-and-create-tables-to-specify-how-to-access-the-datasets"></a><a name="adf-tables"></a>定义和创建表以指定访问数据集的方式
使用以下基于脚本的过程，创建指定数据集的结构、位置和可用性的表。 可使用 JSON 文件定义表。 若要深入了解这些文件的结构，请参阅[数据集](../../data-factory/concepts-datasets-linked-services.md)。

> [!NOTE]
> 在执行 [New-AzureDataFactoryTable](/previous-versions/azure/dn835096(v=azure.100)) cmdlet 之前，应先执行 `Add-AzureAccount` cmdlet，以确认是否为命令执行选择了正确的 Azure 订阅。 有关此 cmdlet 的文档，请参阅 [Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount?view=azuresmps-3.7.0)。
>
>

表中基于 JSON 的定义使用以下名称：

* SQL Server 中的表名为“nyctaxi_data”
* Azure Blob 存储帐户中的 **容器名** 为 containername

此 ADF 管道所需的表定义有 3 个：

1. [SQL 本地表](#adf-table-onprem-sql)
2. [Blob 表](#adf-table-blob-store)
3. [SQL Azure 表](#adf-table-azure-sql)

> [!NOTE]
> 这些过程使用 Azure PowerShell 来定义和创建 ADF 活动。 但是，也可使用 Azure 门户来完成这些任务。 有关详细信息，请参阅[创建数据集](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)。
>
>

### <a name="sql-on-premises-table"></a><a name="adf-table-onprem-sql"></a>SQL 本地表
SQL Server 的表定义在以下 JSON 文件中指定：

```json
{
    "name": "OnPremSQLTable",
    "properties":
    {
        "location":
        {
            "type": "OnPremisesSqlServerTableLocation",
            "tableName": "nyctaxi_data",
            "linkedServiceName": "adfonpremsql"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1,
            "waitOnExternal":
            {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

此处不包括列名称。 可以通过在列名称中包含它们来进行选择， (有关详细信息，请参阅 [ADF 文档](../../data-factory/copy-activity-overview.md) 主题。

将表的 JSON 定义复制到名为 onpremtabledef.json 的文件中，并将其保存到已知位置（此处假定为 *C:\temp\onpremtabledef.json* ）。 使用以下 Azure PowerShell cmdlet 在 ADF 中创建表：

```azurepowershell
New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json
```


### <a name="blob-table"></a><a name="adf-table-blob-store"></a>Blob 表
以下是适用于输出 blob 位置的表的定义（这将引入的数据从本地映射到 Azure blob）：

```json
{
    "name": "OutputBlobTable",
    "properties":
    {
        "location":
        {
            "type": "AzureBlobLocation",
            "folderPath": "containername",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": "\t"
            },
            "linkedServiceName": "adfds"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

将表的 JSON 定义复制到名为 bloboutputtabledef.json 的文件中，并将其保存到已知位置（此处假定为 *C:\temp\bloboutputtabledef.json* ）。 使用以下 Azure PowerShell cmdlet 在 ADF 中创建表：

```azurepowershell
New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json
```

### <a name="sql-azure-table"></a><a name="adf-table-azure-sql"></a>SQL Azure 表
以下是适用于 SQL Azure 输出的表的定义（此架构将映射来自 blob 的数据）：

```json
{
    "name": "OutputSQLAzureTable",
    "properties":
    {
        "structure":
        [
            { "name": "column1", "type": "String"},
            { "name": "column2", "type": "String"}
        ],
        "location":
        {
            "type": "AzureSqlTableLocation",
            "tableName": "your_db_name",
            "linkedServiceName": "adfdssqlazure_linked_servicename"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

将表的 JSON 定义复制到名为 AzureSqlTable.json 的文件中，并将其保存到已知位置（此处假定为 *C:\temp\AzureSqlTable.json* ）。 使用以下 Azure PowerShell cmdlet 在 ADF 中创建表：

```azurepowershell
New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json
```


## <a name="define-and-create-the-pipeline"></a><a name="adf-pipeline"></a>定义和创建管道
使用以下基于脚本的过程，指定属于管道的活动并创建管道。 可使用 JSON 文件定义管道属性。

* 该脚本假设管道名称是 AMLDSProcessPipeline。
* 另请注意：我们将管道的周期设置为每天执行，并且为作业使用默认的执行时间（UTC 的凌晨 12 点）。

> [!NOTE]
> 以下过程使用 Azure PowerShell 来定义和创建 ADF 管道。 但是，也可使用 Azure 门户来完成此任务。 有关详细信息，请参阅[创建管道](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)。
>
>

使用前面提供的表定义，按以下步骤指定适用于 ADF 的管道定义：

```json
{
    "name": "AMLDSProcessPipeline",
    "properties":
    {
        "description" : "This pipeline has two activities: the first one copies data from SQL Server to Azure Blob, and the second one copies from Azure Blob to Azure Database Table",
        "activities":
        [
            {
                "name": "CopyFromSQLtoBlob",
                "description": "Copy data from SQL Server to blob",
                "type": "CopyActivity",
                "inputs": [ {"name": "OnPremSQLTable"} ],
                "outputs": [ {"name": "OutputBlobTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from nyctaxi_data"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            },
            {
                "name": "CopyFromBlobtoSQLAzure",
                "description": "Push data to Sql Azure",
                "type": "CopyActivity",
                "inputs": [ {"name": "OutputBlobTable"} ],
                "outputs": [ {"name": "OutputSQLAzureTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "BlobSource"
                    },
                    "sink":
                    {
                        "type": "SqlSink",
                        "WriteBatchTimeout": "00:5:00",
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 2,
                    "timeout": "02:00:00"
                }
            }
        ]
    }
}
```

将管道的 JSON 定义复制到名为 *pipelinedef.json* 的文件中，并将其保存到已知位置（此处假定为 *C:\temp\pipelinedef.json* ）。 使用以下 Azure PowerShell cmdlet 在 ADF 中创建管道：

```azurepowershell
New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json
```


## <a name="start-the-pipeline"></a><a name="adf-pipeline-start"></a>启动管道
现在可使用以下命令来运行管道：

```azurepowershell
Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline
```

需要将 *startdate* 和 *enddate* 参数值替换为想要在此期间运行管道的实际日期。

在管道执行后，应能看到数据显示在为 blob 选择的容器中，每天一个文件。

我们并未使用 ADF 提供的功能以增量方式对数据进行管道传输。 若要深入了解如何执行此操作以及 ADF 提供的其他功能，请参阅 [ADF 文档](https://azure.microsoft.com/services/data-factory/)。