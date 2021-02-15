---
title: 'Azure Database for PostgreSQL 灵活的服务器 (预览版中的服务器) '
description: 本文提供了有关配置和管理 Azure Database for PostgreSQL 灵活服务器的注意事项和指南。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 5cd4454d5b77d18940f6693a25a691a01f4fedec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934756"
---
# <a name="servers---azure-database-for-postgresql---flexible-server"></a>服务器-Azure Database for PostgreSQL-灵活服务器

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

本文提供了有关使用 Azure Database for PostgreSQL 灵活服务器的注意事项和指南。

## <a name="what-is-an-azure-database-for-postgresql-server"></a>什么是 Azure Database for PostgreSQL 服务器？

Azure Database for PostgreSQL-灵活服务器部署选项中的服务器是多个数据库的中心管理点。 它的 PostgreSQL 服务器构造与本地环境中用户可能比较熟悉的构造相同。 具体而言，PostgreSQL 服务是托管的服务，它提供性能保证、公开服务器级访问权限和功能。

Azure Database for PostgreSQL 服务器：

- 在 Azure 订阅中创建。
- 是数据库的父资源。
- 为数据库提供了一个命名空间。
- 是具有强生存期语义的容器 - 删除服务器时会删除所包含的数据库。
- 并置区域中的资源。
- 为服务器和数据库访问提供连接终结点
- 提供应用于其数据库的管理策略的作用域：登录名、防火墙、用户、角色、配置等。
- 在多个版本内可用。 有关详细信息，请参阅 [支持的 PostgreSQL 数据库版本](concepts-supported-versions.md)。
- 用户可将其进行扩展。 有关详细信息，请参阅 [PostgreSQL 扩展](concepts-extensions.md)。

在 Azure Database for PostgreSQL 数据库中，可创建一个或多个数据库。 可以选择为每个服务器创建单一数据库来使用所有资源，还可以创建多个数据库来共享资源。 按服务器根据定价层、vCore 和存储 (GB) 的配置采用结构化定价。 有关详细信息，请参阅 [计算和存储选项](concepts-compute-storage.md)。

## <a name="how-do-i-connect-and-authenticate-to-the-database-server"></a>如何实现连接到数据库服务器并向其进行身份验证？

以下元素有助于确保安全地访问数据库：

|||
|:--|:--|
| **身份验证和授权** | Azure Database for PostgreSQL 服务器支持本机 PostgreSQL 身份验证。 可使用服务器的管理员登录名连接到服务器并进行身份验证。 |
| **协议** | 该服务支持 PostgreSQL 使用的基于消息的协议。 |
| **TCP/IP** | 通过 TCP/IP 和 Unix 域套接字支持该协议。 |
| **Firewall** | 为帮助保护数据，在用户指定具有访问权限的计算机之前，防火墙规则将禁止所有对服务器及其数据库的访问。 请参阅 [Azure Database for PostgreSQL 服务器防火墙规则](how-to-manage-firewall-portal.md)。 |

## <a name="managing-your-server"></a>管理服务器

您可以通过使用 [Azure 门户](https://portal.azure.com) 或 [Azure CLI](/cli/azure/postgres)来管理 Azure Database for PostgreSQL 服务器。

在创建服务器时，设置管理员用户的凭据。 管理员用户是在服务器上具有最高特权的用户。 它属于角色 azure_pg_admin。 此角色没有完整的超级用户权限。 

PostgreSQL 超级用户属性分配给属于托管服务的 azure_superuser。 你无权访问此角色。

Azure Database for PostgreSQL 服务器具有默认数据库： 

- postgres**** - 可以在创建服务器后连接到的默认数据库。
- azure_maintenance**** - 此数据库用于将提供托管服务的进程与用户操作分开。 你无权访问此数据库。

## <a name="server-parameters"></a>服务器参数

PostgreSQL 服务器参数可确定服务器的配置。 在 Azure Database for PostgreSQL 中，可使用 Azure 门户或 Azure CLI 查看和编辑参数列表。

由于是 Postgres 的托管服务，Azure Database for PostgreSQL 中的可配置参数是本地 Postgres 实例中的参数的子集（有关 Postgres 参数的详细信息，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/12/static/runtime-config.html)）。 在创建时为每个参数使用了默认值，启用了 Azure Database for PostgreSQL 服务器。 用户不能配置某些需要服务器重启或超级用户访问权限才能使更改生效的参数。

## <a name="next-steps"></a>后续步骤

- 有关该服务的概述，请参阅 [Azure Database for PostgreSQL 概述](overview.md)。
- 有关基于 **配置**的特定资源配额和限制的信息，请参阅 [计算和存储选项](concepts-compute-storage.md)。
- 通过 [Azure 门户](howto-configure-server-parameters-using-portal.md)或 [Azure CLI](howto-configure-server-parameters-using-cli.md) 查看和编辑服务器参数。
