---
title: 快速入门：创建 Synapse 工作区
description: 遵循本指南中的步骤创建 Synapse 工作区。
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 09/03/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: fb7672a0d7bdd14415a51f2296c281e92cf5542a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450648"
---
# <a name="quickstart-create-a-synapse-workspace"></a>快速入门：创建 Synapse 工作区
本快速入门介绍了使用 Azure 门户创建 Azure Synapse 工作区的步骤。

## <a name="create-a-synapse-workspace"></a>创建 Synapse 工作区

1. 打开 [Azure 门户](https://portal.azure.com)，然后在顶部搜索“Synapse”。
1. 在“服务”下的搜索结果中，选择“Azure Synapse Analytics” 。
1. 选择“添加”以创建工作区。
1. 在“基础”选项卡中，为工作区指定唯一的名称。 在本文档中，我们将使用 myworkspace
1. 需要 ADLSGEN2 帐户才能创建工作区。 最简单的选择是创建一个新工作区。 如果要重复使用现有工作区，需要执行一些其他配置。 
1. 选项 1 创建新的 ADLSGEN2 帐户 
    1. 在“选择 Data Lake Storage Gen 2”下，单击“新建”并将其命名为 contosolake  。
    1. 在“选择 Data Lake Storage Gen 2”下，单击“文件系统”并将其命名为 users  。
1. 选项 2 参阅本文档底部的“准备存储帐户”说明。
1. Azure Synapse 工作区将此存储帐户用作“主要”存储帐户，并使用容器来存储工作区数据。 工作区将数据存储在 Apache Spark 表中。 它将 Spark 应用程序日志存储在名为 /synapse/workspacename 的文件夹下。
1. 选择“查看 + 创建” > “创建”。 你的工作区将在几分钟内准备就绪。

> [!NOTE]
> 创建 Azure Synapse 工作区后，将无法将工作区移动到另一个 Azure Active Directory 租户。 如果通过订阅迁移或其他操作执行此过程，可能会失去对工作区中项目的访问权限。  

## <a name="open-synapse-studio"></a>打开 Synapse Studio

创建 Azure Synapse 工作区后，可以通过两种方式打开 Synapse Studio：

* 在 [Azure 门户](https://portal.azure.com)中打开 Synapse 工作区。 在“概述”部分的顶部，选择“启动 Synapse Studio” 。
* 转到 `https://web.azuresynapse.net`，然后登录到工作区。

## <a name="prepare-an-existing-storage-account-for-use-with-azure-synapse-analytics"></a>准备与 Azure Synapse Analytics 配合使用的现有存储帐户

1. 打开 [Azure 门户](https://portal.azure.com)。
1. 导航到现有的 ADLSGEN2 存储帐户
1. 在左窗格中，选择“访问控制(IAM)”。 然后，分配以下角色或确保其已经分配：
    * 为自己分配“所有者”角色。
    * 为自己分配“存储 Blob 数据所有者”角色。
1. 在左窗格中选择“容器”并创建容器。
1. 可以为此容器指定任何名称。 在本文档中，我们将此容器命名为“users”。
1. 接受默认设置“公共访问级别”，然后选择“创建” 。

### <a name="configure-access-to-the-storage-account-from-your-workspace"></a>配置从工作区访问存储帐户的权限

Azure Synapse 工作区的托管标识可能已具有对存储帐户的访问权限。 请执行以下步骤，以确保：

1. 打开 [Azure 门户](https://portal.azure.com)和为工作区所选的主存储帐户。
1. 从左窗格中选择“访问控制(标识和访问管理)”。
1. 分配以下角色或确保其已经分配。 我们对工作区标识和工作区使用相同的名称。
    * 对于存储帐户上的“存储 Blob 数据参与者”角色，请将 myworkspace 指定为工作区标识 。
    * 将 myworkspace 指定为工作区名称。

1. 选择“保存”。

## <a name="next-steps"></a>后续步骤

* [创建专用 SQL 池](quickstart-create-sql-pool-studio.md) 
* [创建无服务器 Apache Spark 池](quickstart-create-apache-spark-pool-portal.md)
* [使用无服务器 SQL 池](quickstart-sql-on-demand.md)
