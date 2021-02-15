---
title: 将用户添加到 Azure Data Lake Analytics 帐户
description: 了解如何使用 "添加用户向导" 和 "Azure PowerShell" 将用户正确添加到 Data Lake Analytics 帐户。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 05/24/2018
ms.openlocfilehash: c04b1bbd62e156aeb8d3a0ebb244cfbc753dec52
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020817"
---
# <a name="adding-a-user-in-the-azure-portal"></a>在 Azure 门户中添加用户

## <a name="start-the-add-user-wizard"></a>启动添加用户向导
1. 通过 https://portal.azure.com 打开 Azure Data Lake Analytics。
2. 单击“添加用户向导”。
3. 在“选择用户”步骤中，找到想要添加的用户。 单击“选择”。
4. 在“选择角色”步骤中，选取“Data Lake Analytics 开发人员”。 此角色具有提交/监视/管理 U-SQL 作业所需的最小权限集。 如果组不旨在管理 Azure 服务，则分配给此角色。
5. 在“选择目录权限”步骤中，选择用户需要访问的任何其他数据库。 若要提交作业，必须具有名为 "master" 的默认静态数据库的读取和写入访问权限。 完成后，请单击“确定”  。
6. 在名为“分配所选权限”的最后一步，查看向导将进行的更改。 单击“确定”。


## <a name="configure-acls-for-data-folders"></a>为数据文件夹配置 ACL
根据需要，在包含输入数据和输出数据的文件夹上授予“R-X”或“RWX”。


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>（可选）将用户添加到 Azure Data Lake Storage Gen1 角色“读者”角色。
1.  找到自己的 Azure Data Lake Storage Gen1 帐户。
2.  单击“用户”。
3. 单击“添加”。
4.  选择要分配此组的 Azure 角色。
5.  分配给“读者”角色。 此角色具有浏览/管理存储在 ADLSGen1 中的数据所需的最小权限集。 如果组不旨在管理 Azure 服务，则分配给此角色。
6.  键入组的名称。
7.  单击“确定”。

## <a name="adding-a-user-using-powershell"></a>使用 PowerShell 添加用户

1. 遵循本指南中的说明：[如何安装和配置 Azure PowerShell](/powershell/azure/)。
2. 下载 [Add-AdlaJobUser.ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) PowerShell 脚本。
3. 运行 PowerShell 脚本。 

以下示例命令使用户能够提交作业、查看新的作业元数据和旧的元数据：

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>后续步骤

* [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [使用 Azure 门户实现 Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md)
* [使用 Azure PowerShell 管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-powershell.md)
