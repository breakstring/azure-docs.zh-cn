---
title: 使用虚拟网络规则 - Azure CLI - Azure Database for PostgreSQL - 单一服务器
description: 本文介绍如何使用 Azure CLI 命令行为 Azure Database for PostgreSQL 创建和管理 VNet 服务终结点和规则。
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 07e0f7cf2834b3984d9207fa18f3b0e32340e216
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660876"
---
# <a name="create-and-manage-vnet-service-endpoints-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>使用 Azure CLI 为 Azure Database for PostgreSQL 创建和管理 VNet 服务终结点 - 单一服务器
虚拟网络 (VNet) 服务终结点和规则将虚拟网络的专用地址空间扩展到你的 Azure Database for PostgreSQL 服务器。 使用便捷的 Azure 命令行接口 (CLI) 命令，可创建、更新、删除、列出和显示 VNet 服务终结点和规则，用于管理服务器。 有关 Azure Database for PostgreSQL VNet 服务终结点（包括限制）的概述，请参阅 [Azure Database for PostgreSQL Server VNet 服务终结点](concepts-data-access-and-security-vnet.md)。 在 Azure Database for PostgreSQL 的所有支持区域中，VNet 服务终结点均可用。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南：
- 安装 [Azure CLI](/cli/azure/install-azure-cli)，或在浏览器中使用 Azure Cloud Shell。
- 创建 [Azure Database for PostgreSQL 服务器和数据库](quickstart-create-server-database-azure-cli.md)。

    > [!NOTE]
    > 只有常规用途和内存优化服务器才支持 VNet 服务终结点。
    > 在 VNet 对等互连的情况下，如果流量通过具有服务终结点的公共 VNet 网关流动，并且应该流向对等机，请创建 ACL/VNet 规则，以便网关 VNet 中的 Azure 虚拟机能够访问 Azure Database for PostgreSQL 服务器。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 本文需要 Azure CLI 版本2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>配置 Azure Database for PostgreSQL Vnet 服务终结点
[az network vnet](/cli/azure/network/vnet) 命令用于配置虚拟网络。

如果有多个订阅，请选择应计费的资源所在的相应订阅。 使用 [az account set](/cli/azure/account#az-account-set) 命令选择帐户下的特定订阅 ID。 用订阅的 **az login** 输出中的 **id** 属性代替订阅 id 占位符。

- 该帐户必须拥有创建虚拟网络和服务终结点所需的必要权限。

对虚拟网络拥有写入访问权限的用户可在虚拟网络上单独配置服务终结点。

若要在 VNet 中保护 Azure 服务资源，用户必须对所添加的子网拥有“Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/”权限。 此权限默认包含在内置的服务管理员角色中，可以通过创建自定义角色进行修改。

详细了解[内置角色](../role-based-access-control/built-in-roles.md)以及将特定的权限分配到[自定义角色](../role-based-access-control/custom-roles.md)。

VNet 和 Azure 服务资源可以位于相同或不同的订阅中。 如果 VNet 和 Azure 服务资源位于不同的订阅中，资源应在相同的 Active Directory (AD) 租户下。 确保两个订阅都注册了 Microsoft.Sql 资源提供程序。 有关详细信息，请参阅 [资源管理器注册][resource-manager-portal]。

> [!IMPORTANT]
> 强烈建议在运行下面的示例脚本或配置服务终结点前先阅读本文有关服务终结点配置和注意事项的内容。 **虚拟网络服务终结点：** [虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)是一个子网，其属性值包括一个或多个正式的 Azure 服务类型名称。 VNet 服务终结点使用服务类型名称 Microsoft.Sql，可引用名为“SQL 数据库”的 Azure 服务。 此服务标记也适用于 Azure SQL 数据库、Azure Database for PostgreSQL 和 MySQL 服务。 请务必要注意，对 VNet 服务终结点应用 Microsoft.Sql 服务标记时，它会为所有 Azure 数据库服务配置服务终结点流量，其中包括 Azure SQL 数据库、Azure Database for PostgreSQL 和子网上的 Azure Database for MySQL 服务器。 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>示例脚本演示了如何创建 Azure Database for PostgreSQL 数据库，创建 VNet 和 VNet 服务终结点，以及如何使用 VNet 规则在子网中保护服务器
在此示例脚本中，更改突出显示的行，以自定义管理员用户名和密码。 将 `az account set --subscription` 命令中使用的 SubscriptionID 替换为你自己的订阅标识符。
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>清理部署
运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
