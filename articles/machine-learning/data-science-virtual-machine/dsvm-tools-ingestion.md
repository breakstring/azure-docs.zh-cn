---
title: 数据引入工具
titleSuffix: Azure Data Science Virtual Machine
description: 了解 Data Science Virtual Machine 中预装的数据引入工具和实用工具。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 21a6efa8108bfd0a317eb955e8b3ffcfba0862a2
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519365"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>数据科学虚拟机数据引入工具

数据科学或 AI 项目中的初始技术步骤之一就是必须识别要使用的数据集并将其引入到分析环境中。 Data Science Virtual Machine (DSVM) 提供工具和库，可将来自不同来源的数据导入 DSVM 上的本地分析数据存储，或者云或本地的数据平台中。

下面是 DSVM 中可用的一些数据移动工具。

## <a name="adlcopy"></a>AdlCopy

| 类别 | 值 |
| ------------- | ------------- |
| 它是什么？   | 可将数据从 Azure Blob 存储复制到 Azure Data Lake Store 的工具。 此外，也可在两个 Azure Data Lake Store 帐户之间复制数据。      |
| 支持的 DSVM 版本      | Windows      |
| 典型用途      | 将多个 blob 从 Azure Blob 存储复制到 Azure Data Lake Store。      |
|  如何使用/运行它？    |   打开命令提示符，键入 `adlcopy` 可获取帮助。    |
| 指向示例的链接      | [使用 AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)      |
| DSVM 上的相关工具      | AzCopy、Azure CLI     |

## <a name="azure-cli"></a>Azure CLI

| 类别 | 值 |
| ------------- | ------------- |
| 它是什么？   | Azure 的管理工具。 它还包含可从 Azure 数据平台（如 Azure Blob 存储和 Azure Data Lake Storage）移动数据的命令谓词。     |
| 支持的 DSVM 版本      | Windows、Linux     |
| 典型用途      | 从 Azure 存储、Azure Data Lake Store 导出数据或将数据导入其中。      |
|  如何使用/运行它？    |   打开命令提示符，键入 `az` 可获取帮助。    |
| 指向示例的链接      | [使用 Azure CLI](/cli/azure)     |
| DSVM 上的相关工具      | AzCopy、AdlCopy      |


## <a name="azcopy"></a>AzCopy

| 类别 | 值 |
| ------------- | ------------- |
| 它是什么？   | 用于从本地文件、Azure BLob 存储、文件和表复制数据以及将数据复制到其中的工具。      |
| 支持的 DSVM 版本      | Windows      |
| 典型用途      | 将文件复制到 Azure Blob 存储以及在帐户之间复制 Blob。      |
|  如何使用/运行它？    |   打开命令提示符，键入 `azcopy` 可获取帮助。    |
| 指向示例的链接      | [AzCopy on Windows](../../storage/common/storage-use-azcopy-v10.md)      |
| DSVM 上的相关工具      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB 数据迁移工具

| 类别 | 值 |
| ------------- | ------------- |
| 它是什么？   | 将不同来源的数据导入 Azure Cosmos DB（一种云中的 NoSQL 数据库）的工具。 这些数据可以来源于 JSON 文件、CSV 文件、SQL、MongoDB、Azure 表存储、Amazon DynamoDB 以及 Azure Cosmos DB SQL API 集合。      |
| 支持的 DSVM 版本      | Windows      |
| 典型用途      | 将文件从虚拟机导入 CosmosDB，将数据从 Azure 表存储导入 CosmosDB 以及将数据从 Microsoft SQL Server 数据库导入到 CosmosDB。     |
|  如何使用/运行它？    |   要使用命令行版本，请打开命令提示符，键入 `dt`。 要使用 GUI 工具，请打开命令提示符，键入 `dtui`。    |
| 指向示例的链接      | [CosmosDB 导入数据](../../cosmos-db/import-data.md)      |
| DSVM 上的相关工具      | AzCopy、AdlCopy      |

## <a name="azure-storage-explorer"></a>Azure 存储资源管理器

| 类别 | 值 |
| ------------- | ------------- |
| 它是什么？   | 用于与 Azure 云中存储的文件进行交互的图形用户界面。 |
| 支持的 DSVM 版本      | Windows      |
| 典型用途      | 从 DSVM 导出数据或将数据导入其中。    |
|  如何使用/运行它？    | 在“开始”菜单中搜索“Azure 存储资源管理器”。 |
| 指向示例的链接      | [Azure 存储资源管理器](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

| 类别 | 值 |
| ------------- | ------------- |
| 它是什么？   | 在 SQL Server 和数据文件之间复制数据的 SQL Server 工具。      |
| 支持的 DSVM 版本      | Windows      |
| 典型用途      | 将 CSV 文件导入到 SQL Server 表中以及将 SQL Server 表导出到文件。      |
|  如何使用/运行它？    |   打开命令提示符，键入 `bcp` 可获取帮助。    |
| 指向示例的链接      | [bcp 实用工具](/sql/tools/bcp-utility)      |
| DSVM 上的相关工具      | SQL Server、sqlcmd      |

## <a name="blobfuse"></a>blobfuse

| 类别 | 值 |
| ------------- | ------------- |
| 它是什么？   | 用于在 Linux 文件系统中装载 Azure Blob 存储容器的工具。      |
| 支持的 DSVM 版本      | Linux      |
| 典型用途      | 读取和写入到容器中的 Blob。      |
|  如何使用和运行它？    |   在终端中运行 _blobfuse_。    |
| 指向示例的链接      | [GitHub 上的 blobfuse](https://github.com/Azure/azure-storage-fuse)      |
| DSVM 上的相关工具      | Azure CLI      |