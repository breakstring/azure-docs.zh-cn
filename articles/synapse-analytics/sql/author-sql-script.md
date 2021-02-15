---
title: Synapse Studio 中的 SQL 脚本
description: Azure Synapse Analytics 中的 Synapse Studio SQL 脚本简介。
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 4ed02901aa0d6948e9c6443e5bbcf4ebfbc872f7
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118687"
---
# <a name="synapse-studio-sql-scripts-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中的 Synapse Studio SQL 脚本 

Synapse Studio 提供了一个用于编写 SQL 查询的 SQL 脚本 web 界面。 

## <a name="begin-authoring-in-sql-script"></a>开始在 SQL 脚本中创作 

可以通过多种方法在 SQL 脚本中启动创作体验。 可以通过以下方法之一创建新的 SQL 脚本。

1. 从 "开发" 菜单中，选择 **"+"** 图标，然后选择 " **SQL 脚本**"。

2. 从 " **操作** " 菜单中，选择 " **新建 SQL 脚本**"。

3. 从 "开发 SQL 脚本" 下的 "**操作**" 菜单中选择 "**导入**"。 从本地存储中选择现有的 SQL 脚本。
![新的 sql 脚本3操作](media/author-sql-script/new-sql-script-3-actions.png)

## <a name="create-your-sql-script"></a>创建 SQL 脚本

1. 选择 " **属性** " 按钮并替换分配给 sql 脚本的默认名称，以选择 sql 脚本的名称。 
![新的 sql 脚本重命名](media/author-sql-script/new-sql-script-rename.png)

2. 从 " **连接到** " 下拉菜单中选择特定专用 sql 池或无服务器 SQL 池。 如果需要，请选择 " **使用数据库**" 数据库。 
![新建 sql 选择池](media/author-sql-script/new-sql-choose-pool.png)

3. 开始使用 intellisense 功能创作你的 SQL 脚本。

## <a name="run-your-sql-script"></a>运行 SQL 脚本

选择 " **运行** " 按钮以执行您的 SQL 脚本。 默认情况下，表中会显示结果。

![新的 sql 脚本结果表](media/author-sql-script/new-sql-script-results-table.png)

## <a name="export-your-results"></a>导出结果

可以通过选择 "导出结果" 并选择相应的扩展，将结果以不同的格式导出到本地存储 (包括 CSV、Excel、JSON、XML) 。

还可以通过选择 " **图表** " 按钮，将图表中的 SQL 脚本结果可视化。 选择 "图表类型" 和 " **类别" 列**。 可以通过选择 " **另存为图像**" 将图表导出到图片中。 

![新的 sql 脚本结果图表](media/author-sql-script/new-sql-script-results-chart.png)

## <a name="explore-data-from-a-parquet-file"></a>浏览 Parquet 文件中的数据

可以使用 SQL 脚本浏览存储帐户中的 Parquet 文件，以预览文件内容。

![新脚本 sqlod parquet](media/author-sql-script/new-script-sqlod-parquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL 表、外部表、视图

通过选择 "数据" 下的 " **操作** " 菜单，可以选择多个操作，例如：

- 新的 SQL 脚本
- 选择前1000行
- CREATE
- DROP 和 CREATE 
 
通过右键单击 SQL 数据库的节点来浏览可用的手势。
 
![新建脚本数据库](media/author-sql-script/new-script-database.png)

## <a name="create-folders-and-move-sql-scripts-into-a-folder"></a>创建文件夹并将 SQL 脚本移动到文件夹中

从 "开发 SQL 脚本" 下的 "操作" 菜单中，从 "开发 SQL 脚本" 下的 "操作" 菜单中选择 "新建文件夹"。 并在弹出窗口中键入新文件夹的名称。 

> [!div class="mx-imgBorder"] 
> ![显示选定了 "新文件夹" 的 SQL 脚本示例的屏幕截图。](./media/author-sql-script/new-sql-script-create-folder.png)

若要将 SQL 脚本移动到文件夹中，您可以选择该 sql 脚本，然后从 "操作" 菜单中选择 "移到"。 然后在新窗口中查找目标文件夹，并将 sql 脚本移动到选定文件夹中。你还可以快速地将 sql 脚本拖放到文件夹中。  

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/new-sql-script-move-folder.png)

## <a name="next-steps"></a>后续步骤

有关编写 SQL 脚本的详细信息，请参阅 [Azure Synapse Analytics](../index.yml)。