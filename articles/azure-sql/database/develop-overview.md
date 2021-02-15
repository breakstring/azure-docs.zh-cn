---
title: 应用程序开发概述
description: 了解可用于连接到 SQL 数据库的连接库和最佳实践。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 11/14/2019
ms.custom: sqldbrb=2
ms.openlocfilehash: 7ea3456dbd1d0942cba48c97d70982ae088d73f9
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782971"
---
# <a name="application-development-overview---sql-database--sql-managed-instance"></a>应用程序开发概述 - SQL 数据库和 SQL 托管实例

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

本文逐步讲解开发人员在编写代码以连接到 Azure 数据库时应考虑的基本注意事项。 本文适用于 Azure SQL 数据库和 Azure SQL 托管实例。

## <a name="language-and-platform"></a>语言和平台

可以使用各种[编程语言和平台](connect-query-content-reference-guide.md)连接和查询 Azure SQL 数据库。 可以找到可用于连接到数据库的[示例应用程序](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0)。

你可以利用类似 [cheetah](https://github.com/wunderlist/cheetah)、[sql-cli](https://www.npmjs.com/package/sql-cli)、[VS Code](https://code.visualstudio.com/) 的开源工具。 此外，Azure SQL 数据库可与 Microsoft 工具（如 [Visual Studio](https://www.visualstudio.com/downloads/) 和 [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)）配合使用。 还可以使用 Azure 门户、PowerShell 和 REST API 帮助提高工作效率。

## <a name="authentication"></a>身份验证

对 Azure SQL 数据库的访问使用登录名和防火墙进行保护。 Azure SQL 数据库支持 SQL Server 和 [Azure Active Directory 身份验证](authentication-aad-overview.md)用户和登录名。 Azure Active Directory 登录名仅在 SQL 托管实例中可用。 

详细了解[管理数据库访问和登录名](logins-create-manage.md)。

## <a name="connections"></a>连接

在客户端连接逻辑中，将默认超时替换为 30 秒。 默认值 15 秒对于依赖于 Internet 的连接而言太短。

如果在使用 [连接池](/dotnet/framework/data/adonet/sql-server-connection-pooling)，请确保在程序不活跃地使用连接时将其关闭，而不是准备重用它。

避免长时间运行的事务，因为任何基础结构或连接故障可能会使事务回滚。 如果可能，将事务拆分为多个较小事务，并使用[批处理改进性能](../performance-improve-use-batching.md)。

## <a name="resiliency"></a>复原能力

Azure SQL 数据库是一种云服务，在其中可能会遇到在底层基础结构中或云实体之间的通信中发生的暂时性错误。 尽管 Azure SQL 数据库在发生暂时性基础结构故障时可复原，但这些故障可能会影响连接。 如果在连接到 SQL 数据库时发生暂时性错误，代码应[重试调用](troubleshoot-common-connectivity-issues.md)。 建议让重试逻辑使用退让逻辑，这样就不会因为多个客户端同时重试而对该服务造成混乱。 重试逻辑取决于 [SQL 数据库客户端程序的错误消息](troubleshoot-common-errors-issues.md)。

有关如何为 Azure SQL 数据库上的计划内维护事件做好准备的详细信息，请参阅[规划 Azure SQL 数据库中的 Azure 维护事件](planned-maintenance.md)。

## <a name="network-considerations"></a>网络注意事项

- 在托管客户端程序的计算机上，确保防火墙允许端口 1433 上的传出 TCP 通信。  详细信息：[配置 Azure SQL 数据库防火墙](firewall-configure.md)。
- 客户端在 Azure 虚拟机 (VM) 上运行时，如果客户端程序连接到 SQL 数据库，则必须打开 VM 上的某些端口范围。 详细信息：[用于 ADO.NET 4.5 和 SQL 数据库的非 1433 端口](adonet-v12-develop-direct-route-ports.md)。
- 与 Azure SQL 数据库建立的客户端连接有时会绕过代理直接与数据库交互。 除 1433 以外的端口变得非常重要。 有关详细信息，请参阅 [Azure SQL 数据库连接体系结构](connectivity-architecture.md)和[用于 ADO.NET 4.5 和 SQL 数据库的非 1433 端口](adonet-v12-develop-direct-route-ports.md)。
- 有关 SQL 托管实例的实例网络配置，请参阅 [SQL 托管实例的网络配置](../managed-instance/how-to-content-reference-guide.md#network-configuration)。

## <a name="next-steps"></a>后续步骤

浏览 [SQL 数据库](sql-database-paas-overview.md)和 [SQL 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)的所有功能。

开始之前，请参阅 [Azure SQL 数据库](quickstart-content-reference-guide.md)和 [Azure SQL 托管实例](../managed-instance/quickstart-content-reference-guide.md)的相关指南信息。