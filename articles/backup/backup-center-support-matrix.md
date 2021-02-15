---
title: 备份中心的支持矩阵
description: 本文总结了备份中心为每个工作负荷类型支持的方案
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 672f0e127f173260f25978497198fd7b554aa390
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98893586"
---
# <a name="support-matrix-for-backup-center"></a>备份中心的支持矩阵

备份中心提供单一的玻璃窗格，使企业能够 [大规模控制、监视、操作和分析备份](backup-center-overview.md)。 本文总结了备份中心为每个工作负荷类型支持的方案。

## <a name="supported-scenarios"></a>支持的方案

| **类别** | **方案**  | **支持的工作负荷**  | **限制** |
| -------------| ------------- | ----------------------- |------------|
| 监视   | 查看所有作业 | <li> Azure 虚拟机 <br><br> <li> Azure Database for PostgreSQL 服务器 <br><br> <li> Azure VM 中的 SQL <br><br> <li> Azure VM 中的 SAP HANA <br><br> <li> Azure 文件 | <li> 提供7天的可用作业。 <br> <li> 每个筛选器/下拉最多支持1000项。 因此，可以使用备份中心跨租户监视最多1000个订阅和1000个保管库。 |
| 监视 | 查看所有备份实例 | <li> Azure 虚拟机 <br><br> <li> Azure Database for PostgreSQL 服务器 <br><br> <li> Azure VM 中的 SQL <br><br> <li> Azure VM 中的 SAP HANA <br><br> <li> Azure 文件 | 同上 |
| 监视 | 查看所有备份策略 | <li> Azure 虚拟机 <br><br> <li> Azure Database for PostgreSQL 服务器 <br><br> <li> Azure VM 中的 SQL <br><br> <li> Azure VM 中的 SAP HANA <br><br> <li> Azure 文件 | 同上 |
| 监视 | 查看所有保管库 | <li> Azure 虚拟机 <br><br> <li> Azure Database for PostgreSQL 服务器 <br><br> <li> Azure VM 中的 SQL <br><br> <li> Azure VM 中的 SAP HANA <br><br> <li> Azure 文件 | 同上 |
| 操作 | 配置备份 | <li> Azure 虚拟机 <br><br> <li> Azure Database for PostgreSQL 服务器 <br><br> <li> Azure VM 中的 SQL <br><br> <li> Azure VM 中的 SAP HANA <br><br> <li> Azure 文件 | 请参阅[AZURE VM 备份](./backup-support-matrix-iaas.md)和[Azure Database for PostgreSQL 服务器备份](backup-azure-database-postgresql.md#support-matrix)的支持矩阵 |
| 操作 | 还原备份实例 | <li> Azure 虚拟机 <br><br> <li> Azure Database for PostgreSQL 服务器 <br><br> <li> Azure VM 中的 SQL <br><br> <li> Azure VM 中的 SAP HANA <br><br> <li> Azure 文件 | 请参阅[AZURE VM 备份](./backup-support-matrix-iaas.md)和[Azure Database for PostgreSQL 服务器备份](backup-azure-database-postgresql.md#support-matrix)的支持矩阵 |
| 操作 | 创建保管库 | <li> Azure 虚拟机 <br><br> <li> Azure Database for PostgreSQL 服务器 <br><br> <li> Azure VM 中的 SQL <br><br> <li> Azure VM 中的 SAP HANA <br><br> <li> Azure 文件 | 请参阅[恢复服务保管库](./backup-support-matrix.md#vault-support)的支持矩阵 |
| 操作 | 创建备份策略 | <li> Azure 虚拟机 <br><br> <li> Azure Database for PostgreSQL 服务器 <br><br> <li> Azure VM 中的 SQL <br><br> <li> Azure VM 中的 SAP HANA <br><br> <li> Azure 文件 | 请参阅[恢复服务保管库](./backup-support-matrix.md#vault-support)的支持矩阵 |
| 操作 | 执行备份实例的按需备份 | <li> Azure 虚拟机 <br><br> <li> Azure Database for PostgreSQL 服务器 <br><br> <li> Azure VM 中的 SQL <br><br> <li> Azure VM 中的 SAP HANA <br><br> <li> Azure 文件 | 请参阅[AZURE VM 备份](./backup-support-matrix-iaas.md)和[Azure Database for PostgreSQL 服务器备份](backup-azure-database-postgresql.md#support-matrix)的支持矩阵 |
| 操作 | 停止备份实例备份 | <li> Azure 虚拟机 <br><br> <li> Azure Database for PostgreSQL 服务器 <br><br> <li> Azure VM 中的 SQL <br><br> <li> Azure VM 中的 SAP HANA <br><br> <li> Azure 文件 | 请参阅[AZURE VM 备份](./backup-support-matrix-iaas.md)和[Azure Database for PostgreSQL 服务器备份](backup-azure-database-postgresql.md#support-matrix)的支持矩阵 |
| 洞察力 | 查看备份报表 | <li> Azure 虚拟机 <br><br> <li> Azure 虚拟机中的 SQL <br><br> <li> Azure 虚拟机中的 SAP HANA <br><br> <li> Azure 文件 <br><br> <li> 系统中心数据保护管理器 <br><br> <li> Azure 备份代理 (MARS)  <br><br> <li> Azure 备份服务器 (MABS) | 请参阅 [备份报表支持的方案](./configure-reports.md#supported-scenarios) |
| 调控 | 在 "备份" 类别下查看并分配内置和自定义 Azure 策略 | 不适用 | 不适用 |
| 调控 | 查看未配置备份的数据源 | <li> Azure 虚拟机 <br><br> <li> Azure Database for PostgreSQL 服务器 | 不适用 |

## <a name="unsupported-scenarios"></a>不支持的方案

| **类别** | **方案**  |
|--------------|---------------|
| 监视 | 大规模查看警报 |
| 操作 | 大规模配置保管库设置 |
| 操作 | 从备份中心执行跨区域还原作业 |

## <a name="next-steps"></a>后续步骤

* [查看 Azure 备份的支持矩阵](./backup-support-matrix.md)
* [查看 Azure VM 备份的支持矩阵](./backup-support-matrix-iaas.md)
* [查看 Azure Database for PostgreSQL Server backup 的支持矩阵](backup-azure-database-postgresql.md#support-matrix)