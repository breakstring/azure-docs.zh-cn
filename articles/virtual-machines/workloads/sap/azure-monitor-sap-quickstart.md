---
title: 通过 Azure 门户为 SAP 解决方案部署 Azure Monitor
description: 通过 Azure 门户为 SAP 解决方案部署 Azure Monitor
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: workloads
ms.date: 08/17/2020
ms.reviewer: cynthn
ms.openlocfilehash: 0bb0a7833e9ee3b499ae013b665ecf137c667005
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98250989"
---
# <a name="deploy-azure-monitor-for-sap-solutions-with-azure-portal"></a>通过 Azure 门户为 SAP 解决方案部署 Azure Monitor

可以通过 [Azure 门户](https://azure.microsoft.com/features/azure-portal)创建 SAP 解决方案资源的 Azure Monitor。 此方法提供基于浏览器的用户界面，用于部署 SAP 解决方案的 Azure Monitor 和配置提供程序。

## <a name="sign-in-to-azure-portal"></a>登录到 Azure 门户

登录到 Azure 门户 (https://portal.azure.com)

## <a name="create-monitoring-resource"></a>创建监视资源

1. 从 **Azure Marketplace** 中选择 **适用于 SAP 解决方案的 Azure Monitor** 。

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="映像显示了如何从 Azure Marketplace 中选择 SAP 解决方案的 Azure Monitor。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. 在 " **基本** 信息" 选项卡中，提供所需的值。 如果适用，可以使用现有的 Log Analytics 工作区。

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="显示 Azure 门户配置选项。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

3. 选择虚拟网络时，请确保可从该 VNET 中访问要监视的系统。 

   > [!IMPORTANT]
   > 通过 **选择 "** 与 Microsoft 共享数据共享"，我们的支持团队可以提供更多支持。

## <a name="configure-providers"></a>配置提供程序

### <a name="sap-hana-provider"></a>SAP HANA 提供程序 

1. 选择 " **提供程序** " 选项卡以添加要配置的提供程序。 可以逐个添加多个提供程序，也可以在部署监视资源后添加。 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="显示 &quot;提供程序&quot; 选项卡，为 SAP 解决方案添加其他提供程序到 Azure Monitor。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

2. 选择 "**添加提供程序**"，然后从下拉 SAP HANA 中选择 "  "。 

   > [!IMPORTANT]
   > 确保为 SAP HANA "主" 节点配置 SAP HANA 提供程序。

3. 输入 HANA 服务器的专用 IP。

4. 输入要使用的数据库租户的名称。 你可以选择任何租户，但我们建议使用 **SYSTEMDB** ，因为它支持更广泛的监视区域。 

5. 输入与 HANA 数据库关联的 SQL 端口号。 端口号的格式应为 **[3]**  +  **[实例 #]**  +  **[13]**。 例如，30013。 

6. 输入要使用的数据库用户名。 确保数据库用户已分配 " **监视** " 和 " **目录读取** " 角色。 

7. 完成后，选择 " **添加提供程序**"。 继续根据需要添加更多提供程序，或者选择 " **查看 + 创建** " 完成部署。

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="添加提供程序信息时的配置选项图像。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

### <a name="high-availability-cluster-pacemaker-provider"></a>高可用性群集 (Pacemaker) 提供程序

1. 从下拉 (中选择 " **高可用性群集" ")** "。 

   > [!IMPORTANT]
   > 若要配置高可用性群集 (Pacemaker) 提供程序，请确保在每个节点中安装 ha_cluster_provider。 有关详细信息，请参阅[HA 群集导出](https://github.com/ClusterLabs/ha_cluster_exporter#installation)程序

2. 以的形式输入 Prometheus 终结点 http://IP:9664/metrics 。 
 
3. 输入系统 ID (SID) ，主机名和群集名称。

4. 完成后，选择 " **添加提供程序**"。 继续根据需要添加更多提供程序，或者选择 " **查看 + 创建** " 完成部署。

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-5.png" alt-text="Image 显示了与 HA 群集 Pacemaker 提供程序相关的选项。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-5.png":::


### <a name="os-linux-provider"></a>操作系统 (Linux) 提供程序 

1. 从下拉 (中选择 "OS Linux)  

> [!IMPORTANT]
> 若要配置 (Linux) 提供程序的 OS，请确保在每个 BareMetal 实例中安装 Node_Exporter。 有关详细信息，请参阅 [Node_Exporter](https://github.com/prometheus/node_exporter)

2. 输入一个名称，该名称将是 BareMetal 实例的标识符。
3. 以的形式输入节点导出程序终结点 http://IP:9100/metrics 。
4. 完成后，选择 " **添加提供程序**"。 继续根据需要添加更多提供程序，或者选择 " **查看 + 创建**   " 完成部署。 


### <a name="microsoft-sql-server-provider"></a>Microsoft SQL Server 提供程序

1. 添加 Microsoft SQL Server 提供程序之前，应在 SQL Server Management Studio 中运行以下脚本，以创建具有配置提供程序所需的相应权限的用户。

   ```sql
   USE [<Database to monitor>]
   DROP USER [AMS]
   GO
   USE [master]
   DROP USER [AMS]
   DROP LOGIN [AMS]
   GO
   CREATE LOGIN [AMS] WITH PASSWORD=N'<password>', DEFAULT_DATABASE=[<Database to monitor>], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
   CREATE USER AMS FOR LOGIN AMS
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GRANT CONNECT TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW ANY DEFINITION TO AMS
   GRANT EXEC ON xp_readerrorlog TO AMS
   GO
   USE [<Database to monitor>]
   CREATE USER [AMS] FOR LOGIN [AMS]
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GO
   ``` 

2. 选择 "**添加提供程序**"，然后从下拉 Microsoft SQL Server 中选择 "  "。 

3. 使用与 Microsoft SQL Server 相关联的信息填写字段。 

4. 完成后，选择 " **添加提供程序**"。 继续根据需要添加更多提供程序，或者选择 " **查看 + 创建** " 完成部署。

     :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="图像显示与添加 Microsoft SQL Server 提供程序相关的信息。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

## <a name="next-steps"></a>后续步骤

了解有关[SAP 解决方案 Azure Monitor 的](azure-monitor-overview.md)详细信息
