---
title: 适用于 SQL 的 Azure Defender - 优点和功能
description: 了解适用于 SQL 的 Azure Defender 的优点和功能。
author: memildin
ms.author: memildin
ms.date: 12/13/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 28ec6659430cfdbc81533f05863ccb0ddc560e32
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508026"
---
# <a name="introduction-to-azure-defender-for-sql"></a>适用于 SQL 的 Azure Defender 简介

适用于 SQL 的 Azure Defender 包括两个 Azure Defender 计划，这些计划可扩展 Azure 安全中心的[数据安全包](../azure-sql/database/azure-defender-for-sql.md)以保护任何位置的数据库及其数据。 

> [!VIDEO https://www.youtube.com/embed/V7RdB6RSVpc]

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|**适用于 Azure SQL 数据库服务器的 Azure Defender** - 正式发布 (GA)<br>**适用于计算机上的 SQL Server 的 Azure Defender** - 正式发布 (GA) |
|定价：|适用于 SQL 的 Azure Defender 包含的两个计划按[定价页](security-center-pricing.md)中的定价计费|
|受保护的 SQL 版本：|[Azure 虚拟机上的 SQL](../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)<br>[已启用 Azure Arc 的 SQL Server](/sql/sql-server/azure-arc/overview)<br>不带 Azure Arc 的 Windows 计算机上的本地 SQL Server<br>Azure SQL [单一数据库](../azure-sql/database/single-database-overview.md)和[弹性池](../azure-sql/database/elastic-pool-overview.md)<br>[Azure SQL 托管实例](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)<br>[Azure Synapse Analytics（以前称为 SQL DW）专用 SQL 池](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) US Gov<br>![是](./media/icons/yes-icon.png) China Gov（部分：SQL Server 的警报和漏洞评估的子集。 行为威胁防护功能不可用。）|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>适用于 SQL 的 Azure Defender 保护了哪些项目？

适用于 SQL 的 Azure Defender 包含两个单独的 Azure Defender 计划：

- 适用于 Azure SQL 数据库服务器的 Azure Defender 保护：
    - [Azure SQL 数据库](../azure-sql/database/sql-database-paas-overview.md)
    - [Azure SQL 托管实例](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
    - [Azure Synapse 中的专用 SQL 池](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- 适用于计算机上的 SQL Server 的 Azure Defender 会扩展对 Azure 原生 SQL Server 的保护以完全支持混合环境，并保护在 Azure、其他云环境，甚至本地计算机上托管的 SQL server（所有受支持的版本）：
    - [虚拟机上的 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)
    - 本地 SQL Server：
        - [已启用 Azure Arc 的 SQL Server（预览）](/sql/sql-server/azure-arc/overview)
        - [在不带 Azure Arc 的 Windows 计算机上运行的 SQL Server](../azure-monitor/platform/agent-windows.md)


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>适用于 SQL 的 Azure Defender 有哪些优点？

这两项计划包括用于识别和减少潜在的数据库漏洞的功能，以及用于检测可能表明数据库有威胁的异常活动的功能：

- [漏洞评估](../azure-sql/database/sql-vulnerability-assessment.md) - 一种扫描服务，用于发现、跟踪并帮助修正潜在的数据库漏洞。 评估扫描概述了 SQL 计算机的安全状态以及任何安全发现结果的详细信息。

- [高级威胁防护](../azure-sql/database/threat-detection-overview.md) -一种检测服务，用于持续监视 SQL 服务器的威胁，例如 SQL 注入、暴力攻击和特权滥用。 此服务在 Azure 安全中心提供面向操作的安全警报，其中包括可疑活动的详细信息、有关如何减少威胁的指导以及使用 Azure Sentinel 继续调查的选项。 
    > [!TIP]
    > [在警报参考页中](alerts-reference.md#alerts-sql-db-and-warehouse)查看 SQL Server 的安全警报列表。


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>适用于 SQL 的 Azure Defender 提供哪种类型的警报？

存在以下情况时，会触发具有大量威胁情报的安全警报：

- **潜在的 SQL 注入攻击** - 包括应用程序在数据库中生成错误的 SQL 语句时检测到的漏洞
- **异常的数据库访问和查询模式** - 例如，使用不同的凭据尝试登陆，但登录失败的次数异常多（强制尝试）
- **可疑的数据库活动** - 例如，合法用户从遭到入侵的计算机访问 SQL Server，而此计算机曾与加密挖掘 C&C 服务器通信

警报包含触发警报的事件的详细信息，并提供有关如何调查和消除威胁的建议。



## <a name="next-steps"></a>后续步骤

本文介绍了适用于 SQL 的 Azure Defender。 若要使用所描述的服务：

- 在计算机上使用 Azure Defender for SQL 服务器[扫描 SQL 服务器是否存在漏洞](defender-for-sql-usage.md)