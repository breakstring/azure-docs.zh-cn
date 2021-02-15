---
title: 支持在具有 Azure 资源移动器的区域之间移动 Azure SQL 资源。
description: 查看支持以通过 Azure 资源移动器在区域之间移动 Azure SQL 资源。
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 22a7738c2d4d3cc02c03c233e0821f07b459dd94
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452081"
---
# <a name="support-for-moving-azure-sql-resources-between-azure-regions"></a>支持在 Azure 区域之间移动 Azure SQL 资源

本文总结了在 Azure 区域与 Azure 资源移动器之间移动 Azure SQL 资源的支持和先决条件。

## <a name="requirements"></a>要求

下表总结了要求。

**功能** | **支持/不支持** | **详细信息**
--- | --- | ---
**Azure SQL 数据库超大规模** | 不支持 | 无法将 Azure SQL 超大规模服务层中的数据库移动到资源移动器。
**区域冗余** | 支持 |  支持的移动选项：<br/><br/> -在支持区域冗余的区域之间。<br/><br/> -在不支持区域冗余的区域之间。<br/><br/> -在支持区域冗余的区域与不支持区域冗余的区域之间。<br/><br/> -在不支持区域冗余的区域之间，不支持区域冗余。 
**数据同步** | 中心/同步数据库：不支持<br/><br/> 同步成员：支持。 | 如果移动了同步成员，则需要设置到新目标数据库的数据同步。
**现有异地复制** | 支持 | 现有异地副本将重新映射到目标区域中的新主副本。<br/><br/> 必须在移动后初始化种子设定。 [了解详细信息](../azure-sql/database/active-geo-replication-configure-portal.md)
**透明数据加密 (TDE) 与创建自己的密钥 (BYOK)** | 支持 | [了解](../key-vault/general/move-region.md) 有关跨区域移动密钥保管库的详细信息。
**具有服务托管密钥的 TDE** | 。 |  [了解](../key-vault/general/move-region.md) 有关跨区域移动密钥保管库的详细信息。
**动态数据掩码规则** | 。 | 在移动过程中，规则将自动复制到目标区域。 [了解详细信息](../azure-sql/database/dynamic-data-masking-configure-portal.md)。
**高级数据安全性** | 不支持。 | 解决方法：在目标区域中的 SQL Server 级别设置。 [了解详细信息](../azure-sql/database/azure-defender-for-sql.md)。
**防火墙规则** | 不支持。 | 解决方法：设置目标区域中 SQL Server 的防火墙规则。 数据库级防火墙规则从源服务器复制到目标服务器。 [了解详细信息](../azure-sql/database/firewall-create-server-level-portal-quickstart.md)。
**审核策略** | 不支持。 | 移动之后，策略将重置为默认值。 [了解](../azure-sql/database/auditing-overview.md) 如何重置。
**备份保留** | 。 | 源数据库的备份保留策略将转移到目标数据库。 [了解](../azure-sql/database/long-term-backup-retention-configure.md) 如何在移动之后修改设置。
**自动优化** | 不支持。 | 解决方法：移动后设置自动调整设置。 [了解详细信息](../azure-sql/database/automatic-tuning-enable.md)。
**数据库警报** | 不支持。 | 解决方法：在移动后设置警报。 [了解详细信息](../azure-sql/database/alerts-insights-configure-portal.md)。
**Azure SQL Server stretch database** | 不支持 | 不能通过资源移动器移动 SQL server stretch database。
**Azure Synapse Analytics** | 不支持 | 无法将 Azure Synapse 分析与资源移动器一起移动。
## <a name="next-steps"></a>后续步骤

通过资源移动器试用 [AZURE SQL 资源](tutorial-move-region-sql.md) 到其他区域。