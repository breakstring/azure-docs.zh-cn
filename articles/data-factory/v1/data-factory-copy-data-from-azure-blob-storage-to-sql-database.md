---
title: 将数据从 Blob 存储复制到 SQL 数据库-Azure
description: 本教程演示如何使用 Azure 数据工厂管道中的复制活动将数据从 Blob 存储复制到 SQL 数据库。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: e4035060-93bf-4e8d-bf35-35e2d15c51e0
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 15bce219b96268124729de2f475e33fc386348a8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021208"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>教程：使用数据工厂将数据从 Blob 存储复制到 SQL 数据库
> [!div class="op_single_selector"]
> * [概述与先决条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [复制向导](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager 模板](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> 本文适用于数据工厂版本 1。 如果使用的是数据工厂服务的当前版本，请参阅[复制活动教程](../quickstart-create-data-factory-dot-net.md)。

在本教程中，将创建一个包含管道的数据工厂，用于将数据从 Blob 存储复制到 SQL 数据库。

复制活动在 Azure 数据工厂中执行数据移动。 该活动由全球可用的服务提供支持，能以安全、可靠、可缩放的方式在各种数据存储之间复制数据。 有关复制活动的详细信息，请参阅 [Data Movement Activities](data-factory-data-movement-activities.md) （数据移动活动）。  

> [!NOTE]
> 有关数据工厂服务的详细概述，请参阅 [Azure 数据工厂简介](data-factory-introduction.md)一文。
>
>

## <a name="prerequisites-for-the-tutorial"></a>教程先决条件
开始本教程之前，必须具有以下先决条件：

* **Azure 订阅**。  如果没有订阅，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅[免费使用](https://azure.microsoft.com/pricing/free-trial/)一文。
* **Azure 存储帐户**。 在本教程中，将 Blob 存储用作 **源** 数据存储。 如果没有 Azure 存储帐户，请参阅[创建存储帐户](../../storage/common/storage-account-create.md)一文获取创建步骤。
* **Azure SQL 数据库**。 在本教程中，使用 Azure SQL 数据库作为 **目标** 数据存储。 如果 Azure SQL 数据库中没有可在本教程中使用的数据库，请参阅如何在 [AZURE Sql 数据库中创建和配置数据库](../../azure-sql/database/single-database-create-quickstart.md) 以创建数据库。
* **SQL Server 2012/2014 或 Visual Studio 2013**。 可使用 SQL Server Management Studio 或 Visual Studio 创建示例数据库和查看数据库中的结果数据。  

## <a name="collect-blob-storage-account-name-and-key"></a>收集 Blob 存储帐户名和密钥
要完成本教程，需使用 Azure 存储帐户的帐户名和帐户密钥。 记下 Azure 存储帐户的 **帐户名** 和 **帐户密钥**。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 单击左侧菜单上的“所有服务”，并选择“存储帐户”。

    ![浏览存储帐户](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. 在“存储帐户”边栏选项卡中，选择要在本教程中使用的“Azure 存储帐户”。
4. 在“设置”下，选择“访问密钥”链接。
5. 在“存储帐户名”文本框旁，单击“复制”（图像）按钮，然后将其保存/粘贴到某个位置（例如：文本文件中）。
6. 重复上一步以复制或记下 **key1**。

    ![存储访问密钥](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. 单击“X”关闭所有边栏选项卡。

## <a name="collect-sql-server-database-user-names"></a>收集 SQL Server、数据库和用户的名称
要完成本教程，您需要逻辑 SQL server、数据库和用户的名称。 记下 "Azure SQL 数据库" 的 **服务器**、 **数据库** 和 **用户** 的名称。

1. 在 **Azure 门户** 中，单击左侧的“所有服务”，并选择“SQL 数据库”。
2. 在“SQL 数据库”边栏选项卡中，选择要在本教程中使用的“数据库”。 记下 **数据库名称**。  
3. 在“SQL 数据库”边栏选项卡的“设置”下，单击“属性”。
4. 记下 **服务器名称** 和 **服务器管理员登录名** 的值。
5. 单击“X”关闭所有边栏选项卡。

## <a name="allow-azure-services-to-access-sql-server"></a>允许 Azure 服务访问 SQL Server
确保为服务器启用了 " **允许访问 Azure 服务** **"** 设置，以便数据工厂服务可以访问服务器。 若要验证并启用此设置，请执行以下步骤：

1. 单击左侧的“所有服务”中心，然后单击“SQL Server”。
2. 选择服务器，并单击“设置”下的“防火墙”。
3. 在“防火墙设置”边栏选项卡中，单击“允许访问 Azure 服务”旁边的“打开”。  
4. 单击“X”关闭所有边栏选项卡。

## <a name="prepare-blob-storage-and-sql-database"></a>准备 Blob 存储和 SQL 数据库
现在，通过执行以下步骤，为本教程准备 Azure blob 存储和 Azure SQL 数据库：  

1. 启动记事本。 复制以下文本，将文件命名为 **emp.txt**，然后将其保存到硬盘上的 **C:\ADFGetStarted** 文件夹。

    ```
    John, Doe
    Jane, Doe
    ```
2. 使用 [Azure 存储资源管理器](https://storageexplorer.com/) 等工具创建 **adftutorial** 容器，并将 **emp.txt** 文件上传到该容器。

3. 使用以下 SQL 脚本在 Azure SQL 数据库中创建 **emp** 表。  

    ```SQL
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

    **如果计算机上安装了 SQL Server 2012/2014：** 按照 [使用 SQL SERVER MANAGEMENT STUDIO 管理 Azure SQL 数据库](../../azure-sql/database/single-database-manage.md) 中的说明连接到服务器并运行 SQL 脚本。

    如果不允许客户端访问逻辑 SQL server，则需要为服务器配置防火墙，以允许从计算机 (IP 地址) 进行访问。 有关为服务器配置防火墙的步骤，请参阅 [此文](../../azure-sql/database/firewall-configure.md) 。

## <a name="create-a-data-factory"></a>创建数据工厂
已完成此先决条件。 可使用以下方法之一来创建数据工厂。 单击顶部下拉列表中的其中一个选项或以下链接来执行此教程。     

* [复制向导](data-factory-copy-data-wizard-tutorial.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Azure Resource Manager 模板](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
* [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> 本教程中的数据管道将数据从源数据存储复制到目标数据存储。 该管道并不通过转换输入数据来生成输出数据。 有关如何使用 Azure 数据工厂来转换数据的教程，请参阅[教程：生成第一个使用 Hadoop 群集来转换数据的管道](data-factory-build-your-first-pipeline.md)。
>
> 通过将一个活动的输出数据集设置为另一个活动的输入数据集，可链接两个活动（两个活动先后运行）。 有关详细信息，请参阅[数据工厂中的计划和执行情况](data-factory-scheduling-and-execution.md)。