---
title: 自动增长存储 - Azure 门户 - Azure Database for MySQL
description: 本文介绍如何使用 Azure 门户允许 Azure Database for MySQL 的存储自动增长
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 9355c2b2c780b6ccd63100e576850fe5566db3cc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998594"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>使用 Azure 门户自动增长 Azure Database for MySQL 的存储
本文介绍如何将 Azure Database for MySQL 服务器存储配置为在不影响工作负荷的情况下增长。

在服务器达到了分配的存储限制时，该服务器将被标记为只读。 但是，如果你启用存储自动增长，则服务器存储会增长，以容纳不断增加的数据。 对于预配的存储大小小于 100 GB 的服务器，可用存储空间一旦小于 1 GB 或预配存储的 10%（以这二者中的较大值为准），预配的存储大小就会立即增加 5 GB。 对于预配的存储大小大于 100 GB 的服务器，可用存储空间小于预配的存储大小的 5% 时，预配的存储大小就会增加 5%。 [此处](./concepts-pricing-tiers.md#storage)所指定的最大存储限制适用。

## <a name="prerequisites"></a>必备条件
若要完成本操作指南，需要：
- [Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>启用存储自动增长 

请按照下列步骤设置 MySQL 服务器存储自动增长：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择现有 Azure Database for MySQL 服务器。

2. 在 MySQL 服务器页上，单击“设置”标题下的“定价层”，以打开“定价层”页。

3. 在“自动增长”部分中，选择“是”以启用存储自动增长。

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Azure Database for MySQL - Settings_Pricing_tier - 自动增长":::

4. 单击“确定”以保存更改  。

5. 此时将显示一则通知，确认自动增长已成功启用。

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-success.png" alt-text="Azure Database for MySQL - 自动增长成功":::

## <a name="next-steps"></a>后续步骤

了解[如何基于指标创建警报](howto-alert-on-metric.md)。