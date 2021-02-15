---
title: 使用 Azure Data Lake Storage Gen2 URI
description: 了解 abfs 方案标识符的 URI 语法，它代表用于 Azure Data Lake Storage Gen2)  (Hadoop Filesystem 驱动程序的 Azure Blob 文件系统驱动程序。
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: 948b5aa0ad015f9f3c693e13219ec034724687c0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913158"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>使用 Azure Data Lake Storage Gen2 URI

通过方案标识符 `abfs`（Azure Blob 文件系统）可以知道与 Azure Data Lake Storage Gen2 兼容的 [Hadoop 文件系统](https://www.aosabook.org/en/hdfs.html)驱动程序。 与其他 Hadoop 文件系统驱动程序一样，ABFS 驱动程序使用 URI 格式寻址支持 Data Lake Storage Gen2 的帐户中的文件和目录。

## <a name="uri-syntax"></a>URI 语法

Data Lake Storage Gen2 的 URI 语法依赖于存储帐户是否设置为将 Data Lake Storage Gen2 设为默认文件系统。

如果希望寻址的支持 Data Lake Storage Gen2 的帐户在帐户创建期间 **未** 设为默认文件系统，则简写 URI 语法为：

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **方案标识符**：`abfs` 协议用作方案标识符。 你可以选择使用或不是用传输层安全性 (TLS)（旧称为“安全套接字层 (SSL)”）连接进行连接。 使用 `abfss` 通过 TLS 连接进行连接。

2. **文件系统**：保存文件和文件夹的父位置。 这与 Azure 存储 Blob 服务中的“容器”相同。

3. **帐户名称**：创建期间为存储帐户提供的名称。

4. **路径**：目录结构采用正斜杠分隔 (`/`) 表示形式。

5. **文件名**：单个文件的名称。 如果对目录寻址，则此参数是可选的。

但是，如果希望寻址的帐户在帐户创建期间设为默认文件系统，则简写 URI 语法为：

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **路径**：目录结构采用正斜杠分隔 (`/`) 表示形式。

2. **文件名**：单个文件的名称。


## <a name="next-steps"></a>后续步骤

- [将 Azure Data Lake Storage Gen2 用于 Azure HDInsight 群集](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)