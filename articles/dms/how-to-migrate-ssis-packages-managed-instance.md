---
title: 将 SSIS 包迁移到 SQL 托管实例
titleSuffix: Azure Database Migration Service
description: 了解如何使用 Azure 数据库迁移服务或数据迁移助手将 SQL Server Integration Services (SSIS) 包和项目迁移到 Azure SQL 托管实例。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: 01370092c5e272fe64f4ffdad577b69d3a532810
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012146"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-managed-instance"></a>将 SQL Server Integration Services 包迁移到 Azure SQL 托管实例
如果使用 SQL Server Integration Services (SSIS) 并想将 SSIS 项目/包从 SQL Server 托管的源 SSISDB 迁移到 Azure SQL 托管实例托管的目标 SSISDB，可以使用 Azure 数据库迁移服务。

如果使用的 SSIS 版本早于 2012 年版，或者使用非 SSISDB 包存储类型，则在迁移 SSIS 项目/包之前，需要使用 Integration Services 项目转换向导来转换它们，该向导也可从 SSMS 中启动。 有关详细信息，请参阅文章[将项目转换为项目部署模型](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert)。

> [!NOTE]
> Azure 数据库迁移服务 (DMS) 目前不支持将 Azure SQL 数据库用作迁移目标。 若要将 SSIS 项目/包重新部署到 Azure SQL 数据库，请参阅[将 SQL Server Integration Services 包重新部署到 Azure SQL 数据库](./how-to-migrate-ssis-packages.md)。

在本文中，学习如何：
> [!div class="checklist"]
>
> * 评估源 SSIS 项目/包。
> * 将 SSIS 项目/包迁移到 Azure。

## <a name="prerequisites"></a>先决条件

若要完成这些步骤，需满足以下条件：

* 若要使用 Azure 资源管理器部署模型创建 Azure 数据库迁移服务的 Microsoft Azure 虚拟网络，可以使用 [ExpressRoute](../expressroute/expressroute-introduction.md) 或 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)为本地源服务器提供站点到站点连接。 有关详细信息，请参阅[使用 Azure 数据库迁移服务迁移 SQL 托管实例的网络拓扑]( https://aka.ms/dmsnetworkformi)一文。 有关创建虚拟网络的详细信息，请参阅[虚拟网络文档](../virtual-network/index.yml)，尤其是提供了分步详细信息的快速入门文章。
* 确保虚拟网络网络安全组规则未阻止到 Azure 数据库迁移服务的以下入站通信端口：443、53、9354、445、12000。 有关虚拟网络 NSG 流量筛选的更多详细信息，请参阅[使用网络安全组筛选网络流量](../virtual-network/virtual-network-vnet-plan-design-arm.md)一文。
* 配置[针对源数据库引擎访问的 Windows 防火墙](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017)。
* 打开 Windows 防火墙，使 Azure 数据库迁移服务能够访问源 SQL Server（默认情况下为 TCP 端口 1433）。
* 如果使用动态端口运行多个命名 SQL Server 实例，则可能需要启用 SQL Browser 服务并允许通过防火墙访问 UDP 端口 1434，以便 Azure 数据库迁移服务可连接到源服务器上的命名实例。
* 如果在源数据库的前面使用了防火墙设备，可能需要添加防火墙规则以允许 Azure 数据库迁移服务访问要迁移的源数据库，并通过 SMB 端口 445 访问文件。
* 用于托管 SSISDB 的 SQL 托管实例。 如需创建一个，请按[创建 Azure SQL 托管实例](../azure-sql/managed-instance/instance-create-quickstart.md)一文中的详细说明操作。
* 确保用于连接源 SQL Server 和目标托管实例的登录名是 sysadmin 服务器角色的成员。
* 验证是否在包含 Azure-SSIS Integration Runtime (IR) 的 Azure 数据工厂 (ADF) 中预配了 SSIS，并且其目标 SSISDB 由 SQL 托管实例托管（如[在 Azure 数据工厂中创建 Azure-SSIS Integration Runtime](../data-factory/create-azure-ssis-integration-runtime.md) 一文所述）。

## <a name="assess-source-ssis-projectspackages"></a>评估源 SSIS 项目/包

虽然尚未将源 SSISDB 的评估集成到数据库迁移助手 (DMA) 中，但在将 SSIS 项目/包重新部署到 Azure SQL 托管实例上托管的目标 SSISDB 时，将对其进行评估/验证。

## <a name="register-the-microsoftdatamigration-resource-provider"></a>注册 Microsoft.DataMigration 资源提供程序

1. 登录到 Azure 门户，选择“所有服务”，然后选择“订阅”。

    ![显示门户订阅](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. 选择要在其中创建 Azure 数据库迁移服务实例的订阅，再选择“资源提供程序”。

    ![显示资源提供程序](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. 搜索迁移服务，再选择“Microsoft.DataMigration”右侧的“注册” 。

    ![注册资源提供程序](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>创建 Azure 数据库迁移服务实例

1. 在 Azure 门户中，选择“+ 创建资源”，搜索“Azure 数据库迁移服务”，然后从下拉列表选择“Azure 数据库迁移服务”  。

     ![Azure 市场](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. 在“Azure 数据库迁移服务”屏幕上，选择“创建” 。

    ![创建 Azure 数据库迁移服务实例](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. 在“创建迁移服务”屏幕上，为服务、订阅以及新的或现有资源组指定名称。

4. 选择要在其中创建 DMS 实例的位置。

5. 选择现有的虚拟网络或创建一个虚拟网络。

    虚拟网络为 Azure 数据库迁移服务提供源 SQL Server 和目标 Azure SQL 托管实例的访问权限。

    有关如何在 Azure 门户中创建虚拟网络的详细信息，请参阅[使用 Azure 门户创建虚拟网络](../virtual-network/quick-create-portal.md)一文。

    有关更多详细信息，请参阅[使用 Azure 数据库迁移服务迁移 Azure SQL 托管实例的网络拓扑](./resource-network-topologies.md)一文。

6. 选择定价层。

    有关成本和定价层的详细信息，请参阅[价格页](https://aka.ms/dms-pricing)。

    ![创建 DMS 服务](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. 选择“创建”来创建服务。

## <a name="create-a-migration-project"></a>创建迁移项目

创建服务实例后，在 Azure 门户中找到并打开它，然后创建一个新的迁移项目。

1. 在 Azure 门户中，选择“所有服务”，搜索 Azure 数据库迁移服务，然后选择“Azure 数据库迁移服务”。

    ![查找 Azure 数据库迁移服务的所有实例](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. 在“Azure 数据库迁移服务”屏幕上，搜索创建的实例名称，然后选择该实例。

3. 选择“+ 新建迁移项目”。

4. 在“新建迁移项目”屏幕上指定项目名称，在“源服务器类型”文本框中选择“SQL Server”，在“目标服务器类型”文本框中选择“Azure SQL 托管实例”，然后在“选择活动类型”中选择“SSIS 包迁移”。      

   ![创建 DMS 项目](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. 选择“创建”来创建项目。

## <a name="specify-source-details"></a>指定源详细信息

1. 在“迁移源详细信息”屏幕上，指定源 SQL Server 的连接详细信息。

2. 如果尚未在服务器上安装受信任的证书，请选中“信任服务器证书”复选框。

    如果没有安装受信任的证书，SQL Server 会在实例启动时生成自签名证书。 此证书用于加密客户端连接的凭据。

    > [!CAUTION]
    > 使用自签名证书加密的 TLS 连接不提供强安全性。 它们易遭受中间人攻击。 在生产环境中或在连接到 Internet 的服务器上不应依赖使用自签名证书的 TLS。

   ![源详细信息](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. 选择“保存” 。

## <a name="specify-target-details"></a>指定目标详细信息

1. 在“迁移目标详细信息”屏幕上，指定目标的连接详细信息。

     ![目标详细信息](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. 选择“保存” 。

## <a name="review-the-migration-summary"></a>查看迁移摘要

1. 在“迁移摘要”屏幕的“活动名称”文本框中指定迁移活动的名称。 

2. 对于 **SSIS 项目和环境覆盖选项**，请指定是覆盖还是忽略现有的 SSIS 项目和环境。

    ![迁移项目摘要](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. 查看并验证与迁移项目关联的详细信息。

## <a name="run-the-migration"></a>运行迁移

* 选择“运行迁移”。

## <a name="next-steps"></a>后续步骤

* 在 Microsoft [Database Migration Guide](https://datamigration.microsoft.com/)（Microsoft 数据库迁移指南）中查看迁移指南。