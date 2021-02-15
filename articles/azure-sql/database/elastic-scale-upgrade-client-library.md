---
title: 升级到最新的弹性数据库客户端库
description: 使用 NuGet 升级弹性数据库客户端库。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 74aed815d011503cb6caea56cfad5e076bdcbfbd
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793409"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>升级应用以使用最新的弹性数据库客户端库
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

可通过 Visual Studio 中 NuGet 和 NuGet 包管理器界面获取[弹性数据库客户端库](elastic-database-client-library.md)的新版本。 升级包含客户端库的 bug 修复和新功能支持。

**有关最新版本：** 请转到 [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)。

使用新库重新生成应用程序，以及更改存储在 Azure SQL 数据库中数据库的现有分片映射管理器元数据，以支持新的功能。

按顺序执行这些步骤可确保在更新元数据对象时旧版本的客户端库不再存在于环境中，这意味着在升级后不会创建旧版本的元数据对象。

## <a name="upgrade-steps"></a>升级步骤

**1.升级应用程序。** 在 Visual Studio 中，下载最新的客户端库版本并在使用该库的所有开发项目中引用该版本；并重新生成并部署。

* 在 Visual Studio 解决方案中，选择“ **工具** ” --> “ **Nuget 程序包管理器** ” -->  “ **管理解决方案的 Nuget 程序包** ”。
* (Visual Studio 2013) 在左侧面板中，选择“ **更新** ”，并选择窗口中显示的包“ **Azure SQL 数据库弹性扩展客户端库** ”上的“ **更新** ”按钮。
* (Visual Studio 2015) 将“筛选器”框设置为“ **可用升级** ”。 选择要更新的包，并单击“更新”  按钮。
* (Visual Studio 2017) 在对话框顶部，选择“更新”。 选择要更新的包，并单击“更新”  按钮。
* 生成并部署。

**2.升级脚本。** 如果使用 **PowerShell** 脚本来管理分片，请 [下载新的库版本](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)并将其复制到从中执行脚本的目录中。

**3.升级拆分/合并服务。** 如果使用弹性数据库拆分/合并工具来重新组织分片数据，请[下载并部署最新版本的工具](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)。 可在[此处](elastic-scale-overview-split-and-merge.md)找到该服务的详细升级步骤。

**4.升级分片映射管理器数据库** 。 升级 Azure SQL 数据库中支持分片映射的元数据。  有两种方法可以完成此操作：使用 PowerShell 或 C#。 这两个选项在下面说明。

**_选项1：使用 PowerShell 升级元数据_*

1. 在[此处](https://nuget.org/nuget.exe)下载 NuGet 的最新命令行实用工具并将其保存到一个文件夹。
2. 打开命令提示符，导航到同一文件夹，并发出命令： `nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. 导航到包含刚下载的新客户端 DLL 版本的子文件夹，例如： `cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. 从[脚本中心](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)下载弹性数据库客户端升级脚本，并将其保存到包含 DLL 的同一文件夹中。
5. 在命令提示符下从该文件夹运行“PowerShell .\upgrade.ps1”，并按照提示进行操作。

_*_选项 2：使用 C# 升级元数据_*_

或者，创建一个 Visual Studio 应用程序，它打开 ShardMapManager，循环访问所有分片并通过调用 [UpgradeLocalStore](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore) 和 [UpgradeGlobalStore](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore) 方法执行元数据升级，如此示例所示：

```csharp
    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy);
    smm.UpgradeGlobalStore();

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations())
    {
       smm.UpgradeLocalStore(loc);
    }
```

这些元数据升级技术可以应用多次而无危害。 例如，如果在已更新后，旧的客户端版本无意中创建了一个分片，可以对所有分片再次运行升级，以确保最新的元数据版本在整个基础结构中存在。

_ *注意：* * 发布到的客户端库的新版本将继续使用 Azure SQL 数据库上的分片映射管理器元数据的以前版本，反之亦然。   但是，为了利用最新客户端中的一些新功能，需要对元数据进行升级。   请注意，元数据升级不会影响任何用户数据或特定于应用程序的数据，只会影响分片映射管理器创建和使用的对象。  并且应用程序将继续按照前面所述的升级顺序进行操作。

## <a name="elastic-database-client-version-history"></a>弹性数据库客户端版本历史记录

有关版本历史记录，请转到 [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png