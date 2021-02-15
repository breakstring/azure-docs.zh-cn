---
title: PowerShell：将 SQL Server 迁移到 SQL 托管实例脱机
titleSuffix: Azure Database Migration Service
description: 了解如何使用 Azure PowerShell 和 Azure 数据库迁移服务脱机从 SQL Server 迁移到 Azure SQL 托管实例。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit, devx-track-azurepowershell
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 90663b6beb4f1e3f7ade32e603a53c8b9d9158f5
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697775"
---
# <a name="migrate-sql-server-to-sql-managed-instance-offline-with-powershell--azure-database-migration-service"></a>通过 PowerShell & Azure 数据库迁移服务将 SQL Server 迁移到 SQL 托管实例

本文介绍如何使用 Microsoft Azure PowerShell 将还原到 SQL Server 2005 或更高版本的本地实例的 **Adventureworks2016** 数据库脱机迁移到 AZURE sql sql 托管实例。 您可以使用 Microsoft Azure PowerShell 中的模块将数据库从 SQL Server 实例迁移到 SQL 托管实例 `Az.DataMigration` 。

在本文中，学习如何：
> [!div class="checklist"]
>
> * 创建资源组。
> * 创建 Azure 数据库迁移服务的实例。
> * 在 Azure 数据库迁移服务实例中创建迁移项目。
> * 脱机运行迁移。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

本文提供脱机迁移的步骤，但也可以 [联机](howto-sql-server-to-azure-sql-managed-instance-powershell-online.md)迁移。


## <a name="prerequisites"></a>先决条件

若要完成这些步骤，需满足以下条件：

* [SQL Server 2016 或更高版本](https://www.microsoft.com/sql-server/sql-server-downloads)（任意版本）。
* **AdventureWorks2016** 数据库的本地副本，可从 [此处](/sql/samples/adventureworks-install-configure)下载。
* 启用 TCP / IP 协议，使用 SQL Server Express 安装的情况下会默认禁用该协议。 遵循[启用或禁用服务器网络协议](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)一文启用 TCP/IP 协议。
* 配置[针对数据库引擎访问的 Windows 防火墙](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
* Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/)。
* SQL 托管实例。 可按照[创建 SQL 托管实例](../azure-sql/managed-instance/instance-create-quickstart.md)一文中的详述创建 SQL 托管实例。
* 下载并安装[数据迁移助手](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 或更高版本。
* Microsoft Azure 虚拟网络使用 Azure 资源管理器部署模型创建的，该模型通过使用 [ExpressRoute](../expressroute/expressroute-introduction.md) 或 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)向 azure 数据库迁移服务提供与本地源服务器的站点到站点连接。
* 已使用[执行 SQL Server 迁移评估](/sql/dma/dma-assesssqlonprem)一文中所述的数据迁移助手完成对本地数据库和架构迁移的评估。
* 使用 [Install-Module PowerShell cmdlet](/powershell/module/powershellget/Install-Module) 从 PowerShell 库下载并安装 `Az.DataMigration` 模块（0.7.2 或更高版本）。
* 确保用于连接到源 SQL Server 实例的凭据具有 [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql) 权限。
* 确保用于连接到目标 SQL 托管实例的凭据具有目标 SQL 托管实例数据库的 CONTROL DATABASE 权限。


## <a name="sign-in-to-your-microsoft-azure-subscription"></a>登录到 Microsoft Azure 订阅

使用 PowerShell 登录到 Azure 订阅。 有关详细信息，请参阅[使用 Azure PowerShell 登录](/powershell/azure/authenticate-azureps)一文。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

使用 [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) 命令创建资源组。

以下示例在 "*美国东部*" 区域创建名为 " *myResourceGroup* " 的资源组。

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>创建 Azure 数据库迁移服务的实例

可以通过 `New-AzDataMigrationService` cmdlet 创建 Azure 数据库迁移服务的新实例。
此 cmdlet 需要以下必需参数：

* Azure 资源组名称。 可以使用 [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) 命令创建前述 Azure 资源组，并提供其名称作为参数。
* 服务名称。 与 Azure 数据库迁移服务的所需唯一服务名称相对应的字符串。
* *位置*。 指定服务的位置。 指定 Azure 数据中心位置，例如 "美国西部" 或 "东南亚"。
* Sku。 此参数对应于 DMS Sku 名称。 目前支持的 SKU 名称为 *Basic_1vCore*、*Basic_2vCores* 和 *GeneralPurpose_4vCores*。
* 虚拟子网标识符。 可以使用 cmdlet [`New-AzVirtualNetworkSubnetConfig`](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) 创建子网。

以下示例使用名为 *MyVNET* 的虚拟网络和名为 *MySubnet* 的子网，在 *美国东部* 区域的资源组 *MyDMSResourceGroup* 中创建名为 *MyDMS* 的服务。

```powershell
$vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>创建迁移项目

在创建 Azure 数据库迁移服务实例以后，创建迁移项目。 Azure 数据库迁移服务项目需要源和目标实例的连接信息，以及要在项目中迁移的数据库的列表。

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>创建源和目标连接的数据库连接信息对象

可以使用 `New-AzDmsConnInfo` cmdlet 创建数据库连接信息对象，这需要使用以下参数：

* ServerType。 请求的数据库连接的类型，例如 SQL、Oracle 或 MySQL。 将 SQL 用于 SQL Server 和 Azure SQL。
* DataSource。 SQL Server 实例或 Azure SQL 数据库实例的名称或 IP。
* AuthType。 连接的身份验证类型，可以为 SqlAuthentication 或 WindowsAuthentication。
* *TrustServerCertificate*。 此参数设置的值用于指示在绕过验证信任的证书链时，是否对通道加密。 值可为 `$true` 或 `$false`。

以下示例使用 SQL 身份验证为名为 *MySourceSQLServer* 的源 SQL Server 创建连接信息对象：

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

下一个示例演示如何创建名为 "targetmanagedinstance" 的 Azure SQL 托管实例的连接信息：

```powershell
$targetResourceId = (Get-AzSqlInstance -Name "targetmanagedinstance").Id
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI -MiResourceId $targetResourceId
```

### <a name="provide-databases-for-the-migration-project"></a>为迁移项目提供数据库

创建 `AzDataMigrationDatabaseInfo` 对象的列表，以便指定 Azure 数据库迁移服务项目中的数据库（可以作为创建项目所需的参数提供）。 可以使用 cmdlet `New-AzDataMigrationDatabaseInfo` 创建 `AzDataMigrationDatabaseInfo`。

以下示例为 **AdventureWorks2016** 数据库创建 `AzDataMigrationDatabaseInfo` 项目，并将其添加到列表，以便在创建项目时作为参数提供。

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>创建项目对象

最后，你 *可以使用创建名为* *MyDMSProject* 的 Azure 数据库迁移服务项目 `New-AzDataMigrationProject` ，并添加以前创建的源和目标连接以及要迁移的数据库的列表。

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>创建并启动迁移任务

接下来，创建并启动 Azure 数据库迁移服务任务。 此任务需要源和目标的连接凭据信息、要迁移的数据库表的列表，以及在作为先决条件创建的项目中提供的信息。

### <a name="create-credential-parameters-for-source-and-target"></a>创建源和目标的凭据参数

创建连接安全凭据作为 [PSCredential](/dotnet/api/system.management.automation.pscredential) 对象。

以下示例显示了如何为源和目标连接创建 *PSCredential* 对象，将密码作为字符串变量 *$sourcePassword* 和 *$targetPassword* 提供。

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>创建备份 FileShare 对象

现在，使用 `New-AzDmsFileShare` cmdlet 创建 FileShare 对象，用于 Azure 数据库迁移服务可将源数据库备份迁移到的本地 SMB 网络共享。

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>创建选定的数据库对象

下一步是使用 `New-AzDmsSelectedDB` cmdlet 选择源和目标数据库。

以下示例用于将 SQL Server 中的单一数据库迁移到 Azure SQL 托管实例：

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

如果整个 SQL Server 实例需要直接迁移到 Azure SQL 托管实例，可以使用下面提供的从源提取所有数据库的循环。 在以下示例中，请为 $Server、$SourceUserName 和 $SourcePassword 提供源 SQL Server 详细信息。

```powershell
$Query = "(select name as Database_Name from master.sys.databases where Database_id>4)";
$Databases= (Invoke-Sqlcmd -ServerInstance "$Server" -Username $SourceUserName
-Password $SourcePassword -database master -Query $Query)
$selectedDbs=@()
foreach($DataBase in $Databases.Database_Name)
    {
      $SourceDB=$DataBase
      $TargetDB=$DataBase
      
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
                                              -Name $SourceDB `
                                              -TargetDatabaseName $TargetDB `
                                              -BackupFileShare $backupFileShare
      }
```

### <a name="sas-uri-for-azure-storage-container"></a>Azure 存储容器的 SAS URI

创建包含 SAS URI 的变量，使 Azure 数据库迁移服务能够访问它要将备份文件上传到的存储帐户容器。

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

> [!NOTE]
> Azure 数据库迁移服务不支持使用帐户级别 SAS 令牌。 必须使用存储帐户容器的 SAS URI。 [了解如何获取 Blob 容器的 SAS URI](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)。

### <a name="additional-configuration-requirements"></a>其他配置要求

需要满足一些其他要求：


* **选择登录名**。 按以下示例中所示，创建要迁移的登录名列表：

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > 目前，Azure 数据库迁移服务仅支持迁移 SQL 登录名。

* **选择代理作业**。 按以下示例中所示，创建要迁移的代理作业列表：

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > 目前，Azure 数据库迁移服务仅支持包含 T-SQL 子系统作业步骤的作业。



### <a name="create-and-start-the-migration-task"></a>创建并启动迁移任务

使用 `New-AzDataMigrationTask` cmdlet 创建并启动迁移任务。

#### <a name="specify-parameters"></a>指定参数

此 `New-AzDataMigrationTask` cmdlet 需要以下参数：

* TaskType。 要创建的迁移任务的类型。对于从 SQL Server 到 Azure SQL 托管实例的迁移类型，此项应为“MigrateSqlServerSqlDbMi”。 
* ResourceGroupName。 要在其中创建任务的 Azure 资源组的名称。
* ServiceName。 要在其中创建任务的 Azure 数据库迁移服务实例。
* ProjectName。 要在其中创建任务的 Azure 数据库迁移服务项目的名称。 
* TaskName。 要创建的任务的名称。 
* *SourceConnection*。 表示源 SQL Server 连接的 AzDmsConnInfo 对象。
* *TargetConnection*。 AzDmsConnInfo 对象，表示目标 Azure SQL 托管实例连接。
* SourceCred。 [PSCredential](/dotnet/api/system.management.automation.pscredential) 对象，用于连接到源服务器。
* TargetCred。 [PSCredential](/dotnet/api/system.management.automation.pscredential) 对象，用于连接到目标服务器。
* SelectedDatabase。 表示源和目标数据库映射的 AzDataMigrationSelectedDB 对象。
* *BackupFileShare*。 表示 Azure 数据库迁移服务可在其上创建源数据库备份的本地网络共享的 FileShare 对象。
* *BackupBlobSasUri*。 为 Azure 数据库迁移服务提供存储帐户容器访问权限的 SAS URI，该服务将备份文件上传到该容器。 了解如何获取 Blob 容器的 SAS URI。
* *SelectedLogins*。 要迁移的选定登录名列表。
* *SelectedAgentJobs*。 要迁移的选定代理作业列表。
* *SelectedLogins*。 要迁移的选定登录名列表。
* *SelectedAgentJobs*。 要迁移的选定代理作业列表。



#### <a name="create-and-start-a-migration-task"></a>创建并启动迁移任务

以下示例创建并启动名为 **myDMSTask** 的脱机迁移任务：

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```


## <a name="monitor-the-migration"></a>监视迁移

若要监视迁移，请执行以下任务。

1. 将所有迁移详细信息合并到名为 $CheckTask 的变量中。

    若要合并迁移详细信息（例如，与迁移相关的属性、状态和数据库信息），请使用以下代码片段：

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. 使用 `$CheckTask` 变量获取迁移任务的当前状态。

    可以使用 `$CheckTask` 变量获取迁移任务的当前状态，并可以通过查询任务的状态属性来监视正在运行的迁移任务，如以下示例所示：

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      Write-Host "migration task running"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      Write-Host "Migration task is completed Successfully"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation" -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      Write-Host "Migration Task Failed"
    }
    ```


## <a name="delete-the-instance-of-azure-database-migration-service"></a>删除 Azure 数据库迁移服务的实例

完成迁移后，可以删除 Azure 数据库迁移服务实例：

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```


## <a name="next-steps"></a>后续步骤

在[什么是 Azure 数据库迁移服务？](./dms-overview.md)一文中详细了解 Azure 数据库迁移服务。

有关其他迁移方案 (源/目标对) 的信息，请参阅 Microsoft [数据库迁移指南](https://datamigration.microsoft.com/)。