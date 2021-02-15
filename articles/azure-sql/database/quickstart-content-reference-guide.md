---
title: 单一数据库快速入门内容参考
description: 查找所有快速入门的内容参考，这有助于快速学习 Azure SQL 数据库中的单一数据库。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/29/2019
ms.openlocfilehash: 392f361f6fe487a15bdd63c2f3281ccc78b2f86d
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184410"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>开始使用 Azure SQL 数据库中的单一数据库
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[单一数据库](../index.yml)是完全托管的平台即服务 (PaaS) 数据库即服务 (DbaaS)，也是新式云原生应用程序的理想存储引擎。 本部分介绍如何在 Azure SQL 数据库中快速配置和创建单一数据库。

## <a name="quickstart-overview"></a>快速入门概述

本部分提供了可帮助你快速开始使用单一数据库的可用文章的概述。 以下快速入门可帮助你快速创建单一数据库、配置服务器级防火墙规则，然后使用 `.bacpac` 文件将数据库导入新的单一数据库：

- [使用 Azure 门户创建单一数据库](single-database-create-quickstart.md)。
- 创建数据库后，需要[通过配置防火墙规则来保护该数据库](firewall-create-server-level-portal-quickstart.md)。
- 若要将 SQL Server 上的现有数据库迁移到 Azure SQL 数据库，应该安装[数据迁移助手 (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)，用于分析 SQL Server 上的数据库，并找出可能会阻止迁移的任何问题。 如果找不到任何问题，可将数据库导出为 `.bacpac` 文件，然后[使用 Azure 门户或 SqlPackage 导入](database-import.md)该文件。


## <a name="automating-management-operations"></a>自动化管理操作

可以使用 PowerShell 或 Azure CLI 创建、配置和缩放数据库。

- [使用 PowerShell](scripts/create-and-configure-database-powershell.md) 或 [Azure CLI](scripts/create-and-configure-database-cli.md) 创建和配置单一数据库
- [使用 PowerShell](scripts/monitor-and-scale-database-powershell.md) 或 [Azure CLI](scripts/monitor-and-scale-database-cli.md) 更新单一数据库和缩放资源

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>在尽量缩短停机时间的情况下迁移到单一数据库

参考这些快速入门中的文章可以使用 `.bacpac` 文件快速创建数据库或将其导入 Azure。 但是，使用 `.bacpac` 和 `.dacpac` 文件可以快速在 Azure SQL 中以及不同 SQL Server 版本之间转移数据库，或者在 DevOps 管道中实现持续集成。 但是，此方法不适合用于在尽量缩短停机时间的前提下迁移生产数据库，因为需要停止添加新数据，等待将源数据库导出到 `.bacpac` 文件，然后等待将其导入 Azure SQL 数据库。 所有这些操作会导致应用程序出现一段停机时间，尤其是数据库较大时。 若要转移生产数据库，需要采用更好的方式进行迁移，以确保尽量缩短迁移所导致的停机时间。 为此，请使用[数据迁移服务 (DMS)](../../dms/tutorial-sql-server-to-azure-sql.md?toc=%2fazure%2fsql-database%2ftoc.json)，它可以在尽量缩短停机时间的情况下迁移数据库。 为实现这种迁移，DMS 会以增量方式将源数据库中发生的更改推送到所要还原的单一数据库。 这样，便可以在尽量缩短停机时间的前提下，快速将应用程序从源数据库切换到目标数据库。

## <a name="hands-on-learning-modules"></a>动手学习模块

以下 Microsoft Learn 模块可帮助你免费了解 Azure SQL 数据库。

- [预配 SQL 数据库中的数据库，用于存储应用程序数据](/learn/modules/provision-azure-sql-db/)
- [开发和配置可在 Azure SQL 数据库中查询数据库的 ASP.NET 应用程序](/learn/modules/develop-app-that-queries-azure-sql/)
- [保护 Azure SQL 数据库中的数据库](/learn/modules/secure-your-azure-sql-database/)

## <a name="next-steps"></a>后续步骤

- 查看 [Azure SQL 数据库支持的功能的概要列表](features-comparison.md)。
- 了解如何[使数据库变得更安全](secure-database-tutorial.md)。
- 在[如何在 Azure SQL 数据库中使用单一数据库](how-to-content-reference-guide.md)中查看更深入的操作指南。
- 查找在 [PowerShell](powershell-script-content-guide.md) 和 [Azure CLI](az-cli-script-samples-content-guide.md) 中编写的其他示例脚本。
- 详细了解可用于配置数据库的[管理 API](single-database-manage.md)。
- [确定适合本地数据库的 Azure SQL 数据库或 Azure SQL 托管实例 SKU](/sql/dma/dma-sku-recommend-sql-db/)。