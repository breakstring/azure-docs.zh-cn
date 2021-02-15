---
title: '还原现有的专用 SQL 池 (以前的 SQL DW) '
description: 关于如何在 Azure Synapse Analytics 中还原现有专用 SQL 池的操作指南。
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2ce552a13592c9d26ef70575f98b0b76ecc454ff
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591984"
---
# <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>还原现有的专用 SQL 池 (以前的 SQL DW) 

本文介绍如何使用 Azure 门户和 PowerShell 将现有专用 SQL 池 (以前的 SQL DW) 还原。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**验证 DTU 容量。** 每个池由 [逻辑 SQL server](../../azure-sql/database/logical-servers.md) (（例如，具有默认 DTU 配额的 myserver.database.windows.net) 。 验证该服务器的剩余 DTU 配额是否足够进行数据库还原。 若要了解如何计算所需 DTU 或请求更多的 DTU，请参阅[请求 DTU 配额更改](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="before-you-begin"></a>准备阶段

1. 确保[安装 Azure PowerShell](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。
2. 有一个现有的还原点，需要从该点进行还原。 若要创建新的还原，请参阅[教程：新建用户定义的还原点](sql-data-warehouse-restore-points.md)。

## <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw-through-powershell"></a>通过 PowerShell (以前的 SQL DW) 还原现有专用 SQL 池

若要从还原点还原现有的专用 SQL 池 (以前的 SQL DW) ，请使用 [AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell cmdlet。

1. 打开 PowerShell。

2. 连接到 Azure 帐户，并列出与帐户关联的所有订阅。

3. 选择包含要还原的数据库的订阅。

4. 列出专用 SQL 池 (以前的 SQL DW) 的还原点。

5. 使用 RestorePointCreationDate 选取所需的还原点。

6. 使用 [AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell cmdlet，将专用 sql DW (以前的 sql DW) 还原到所需的还原点。

    1. 若要将专用 SQL DW)  (以前的 SQL DW 还原到其他服务器，请确保指定其他服务器名称。  该服务器也可以位于另一资源组和区域中。
    2. 若要还原到其他订阅，请使用 "移动" 按钮将服务器移到另一个订阅。

7. 验证还原的专用 SQL 池 (以前的 SQL DW) 是否处于联机状态。

8. 还原完成后，可以按照 [在恢复后配置数据库](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery) (以前的 sql DW) 来配置恢复的专用 sql 池。

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Or list all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different server
#$TargetResourceGroupName = $Database.ResourceGroupName # for restoring to different server in same resourcegroup 
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-dedicated-sql-pool-formerly-sql-dw-through-the-azure-portal"></a>通过 Azure 门户 (以前的 SQL DW) 还原现有的专用 SQL 池

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 导航到要从中还原的专用。
3. 在“概览”边栏选项卡顶部，选择“还原”。

    ![ 还原概述](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. 选择“自动还原点”或“用户定义的还原点”。  如果专用 SQL 池 (以前的 SQL DW) 没有任何自动还原点，请在还原前等待几小时或创建用户定义的还原点。 对于用户定义的还原点，请选择一个现有的，或者创建一个新的。 对于“服务器”，可以选取另一资源组和区域中的服务器，也可以创建一个新服务器。 在提供所有参数后，请单击“查看 + 还原”。

    ![自动还原点](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>后续步骤

- [还原已删除的专用 SQL 池 (以前的 SQL DW) ](sql-data-warehouse-restore-deleted-dw.md)
- [从异地备份专用 SQL 池还原 (以前的 SQL DW) ](sql-data-warehouse-restore-from-geo-backup.md)
