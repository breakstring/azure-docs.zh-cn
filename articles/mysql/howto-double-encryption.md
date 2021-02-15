---
title: 基础结构双加密-Azure 门户-Azure Database for MySQL
description: 了解如何为 Azure Database for MySQL 设置和管理基础结构双加密。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 3f9c22a690859b459b6bb748c3b1001c4aa7660d
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241746"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-mysql"></a>Azure Database for MySQL 的基础结构双加密

了解如何使用为你的 Azure Database for MySQL 设置和管理基础结构双加密的方式。

## <a name="prerequisites"></a>先决条件

* 必须有一个 Azure 订阅，并且是该订阅的管理员。

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---portal"></a>使用基础结构双加密创建 Azure Database for MySQL 服务器-门户

按照以下步骤创建一个 Azure Database for MySQL 服务器，其中包含 Azure 门户的基础结构双加密：

1. 在门户左上角选择“创建资源”  (+)。

2. 选择“数据库”   >   “Azure Database for MySQL”。 还可以在搜索框中输入“MySQL”  以查找该服务。

   :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png" alt-text="Azure Database for MySQL 选项&quot;:::

3. 提供服务器的基本信息。 选择 &quot; **其他设置** &quot;，并启用 &quot; **基础结构双加密** " 复选框，以设置参数。

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-selected.png" alt-text="Azure Database for MySQL 选项&quot;:::

3. 提供服务器的基本信息。 选择 &quot; **其他设置** &quot;，并启用 &quot; **基础结构双加密** ":::

4. 选择“查看 + 创建”  以预配服务器。

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-summary.png" alt-text="Azure Database for MySQL 选项&quot;:::

3. 提供服务器的基本信息。 选择 &quot; **其他设置** &quot;，并启用 &quot; **基础结构双加密** " 边栏选项卡中的状态来验证基础结构双加密。

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-validation.png" alt-text="Azure Database for MySQL 选项&quot;:::

3. 提供服务器的基本信息。 选择 &quot; **其他设置** &quot;，并启用 &quot; **基础结构双加密** ":::

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---cli"></a>使用基础结构双加密创建 Azure Database for MySQL 服务器-CLI

按照以下步骤创建一个 Azure Database for MySQL 服务器，其中包含来自 CLI 的基础结构双加密：

此示例在位置创建名为的资源组 `myresourcegroup` `westus` 。

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
下面的示例使用服务器管理员登录名 `myadmin` 在资源组 `myresourcegroup` 中创建位于“美国西部”区域的名为 `mydemoserver` 的 MySQL 5.7 服务器。 这是第 4 代常规用途服务器，带有 2 个 vCore   。 这还会为创建的服务器启用基础结构双加密。 用自己的值替换 `<server_admin_password>`。

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7 --infrastructure-encryption <Enabled/Disabled>
```

## <a name="next-steps"></a>后续步骤

 若要了解有关数据加密的详细信息，请参阅 [Azure Database for MySQL 数据基础结构双加密](concepts-Infrastructure-double-encryption.md)"。
