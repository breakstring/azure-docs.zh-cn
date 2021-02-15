---
title: 定价层 - Azure Database for PostgreSQL（单一服务器）
description: 本文介绍 Azure Database for PostgreSQL（单一服务器）中的计算和存储选项。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 74d2654c54d1ab2ac5d47aa24449c41c20a90b74
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791132"
---
# <a name="pricing-tiers-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - 单一服务器中的定价层

可以在以下三个不同的定价层之一中创建 Azure Database for PostgreSQL 服务器：“基本”、“常规用途”和“内存优化”。 定价层的差异表现在可以预配的 vCore 中的计算量、每个 vCore 的内存，以及用于存储数据的存储技术。 所有资源都在 PostgreSQL 服务器级别预配。 一个服务器可以有一个或多个数据库。

| 资源/层 | **基本** | **常规用途** | **内存优化** |
|:---|:----------|:--------------------|:---------------------|
| 计算的代 | 第 4 代、第 5 代 | 第 4 代、第 5 代 | 第 5 代 |
| vCore 数 | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| 每个 vCore 的内存 | 2 GB | 5 GB | 10 GB |
| 存储大小 | 5 GB 到 1 TB | 5 GB 到 16 TB | 5 GB 到 16 TB |
| 数据库备份保留期 | 7 到 35 天 | 7 到 35 天 | 7 到 35 天 |

可以从下表着手来选择定价层。

| 定价层 | 目标工作负荷 |
|:-------------|:-----------------|
| 基本 | 需要轻型计算和 I/O 性能的工作负荷。 示例包括用于开发或测试的服务器，或不常使用的小型应用程序。 |
| 常规用途 | 大多数业务工作负荷。此类工作负荷需要均衡的计算和内存以及可缩放的 I/O 吞吐量。 相关示例包括用于托管 Web 和移动应用的服务器，以及其他企业应用程序。|
| 内存优化 | 高性能数据库工作负荷。此类工作负荷需要内存中性能来实现更快的事务处理速度和更高的并发性。 相关示例包括用于处理实时数据的服务器，以及高性能事务性应用或分析应用。|

创建服务器后，只需数秒即可增加或减少 vCore 数、硬件生成和定价层（来回调整基本定价层除外）。 也可在不关闭应用程序的情况下，独立调整存储容量（向上调整）和备份保留期（上下调整）。 创建服务器之后，不能更改备份存储类型。 有关详细信息，请参阅[缩放资源](#scale-resources)部分。

## <a name="compute-generations-and-vcores"></a>计算代数和 vCore 数

计算资源以 vCore 的形式提供，代表基础硬件的逻辑 CPU。 中国东部1、中国北部1、US DoD 中部和 US DoD 东部利用基于 Intel E5-2673 v3 的第4代逻辑 Cpu (Haswell) 2.4 GHz 处理器。 所有其他区域均利用基于 Intel E5-2673 v4 (Broadwell) 2.3-GHz 处理器的第 5 代逻辑 CPU。

## <a name="storage"></a>存储

预配的存储是指可供 Azure Database for PostgreSQL 服务器使用的存储容量。 此存储用于数据库文件、临时文件、事务日志和 PostgreSQL 服务器日志。 预配的总存储量也定义了可供服务器使用的 I/O 容量。

| 存储属性 | **基本** | **常规用途** | **内存优化** |
|:---|:----------|:--------------------|:---------------------|
| 存储类型 | 基本存储 | 常规用途存储 | 常规用途存储 |
| 存储大小 | 5 GB 到 1 TB | 5 GB 到 16 TB | 5 GB 到 16 TB |
| 存储增量大小 | 1 GB | 1 GB | 1 GB |
| IOPS | 变量 |3 IOPS/GB<br/>至少 100 IOPS<br/>最大 20,000 IOPS | 3 IOPS/GB<br/>至少 100 IOPS<br/>最大 20,000 IOPS |

> [!NOTE]
> 以下区域支持最大的存储和 20000 IOPS：澳大利亚东部、澳大利亚东南部、巴西南部、加拿大中部、加拿大东部、美国中北部、中国东部2、中国北部2、东亚、美国东部、美国东部、美国东部2、日本东部、日本西部、韩国中部、韩国南部、美国中南部、北欧、美国中南部、东南亚、瑞士北部、瑞士西部、US Gov 东、US Gov SouthCentral、US Gov 西南、英国南部、英国西部、西欧、美国中北部、美国西部和美国西部2。
>
> 所有其他区域支持最高4TB 的存储和 6000 IOPS。
>

在创建服务器的过程中和之后，可以添加更多的存储容量，这样系统就可以根据工作负荷的存储使用情况自动增加存储。

>[!NOTE]
> 存储只能增加，不能减少。

“基本”层不提供 IOPS 保证。 在“常规用途”和“内存优化”定价层中，IOPS 与预配的存储大小按 3:1 的比例缩放。

可以通过 Azure 门户或 Azure CLI 命令监视 I/O 使用情况。 要监视的相关指标是[存储上限、存储百分比、已用存储和 IO 百分比](concepts-monitoring.md)。

### <a name="reaching-the-storage-limit"></a>达到存储限制

如果可用存储小于 512MB 或小于预配存储大小的 5%，则预配存储小于等于 100 GB 的服务器将标记为只读。 对于预配存储超出 100 GB 的服务器，当可用存储不到 5 GB 时，会将该服务器标记为只读。

例如，如果已预配 110 GB 的存储，而实际使用量超过 105 GB，则会将服务器标记为只读。 或者，如果已预配 5 GB 的存储，则当可用存储少于 512 MB 时，服务器会标记为只读。

服务器设置为只读时，所有现有会话都将断开连接，且未提交的事务会回退。 任何后续写入操作和事务提交均会失败。 所有后续读取查询将不间断工作。  

可增加服务器预配存储量，也可在读写模式下启动新会话并删除数据以回收空闲存储。 运行 `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE;` 将当前会话设置为读写模式。 为避免数据损坏，请勿在服务器仍处于只读状态时执行任何写入操作。

我们建议你启用存储自动增长或设置警报，以便在服务器存储接近阈值时通知你，避免进入只读状态。 有关详细信息，请参阅有关[如何设置警报](howto-alert-on-metric.md)的文档。

### <a name="storage-auto-grow"></a>存储自动增长

存储自动增长可防止服务器耗尽存储空间并变为只读。 如果启用了存储自动增长，存储会在不影响工作负荷的情况下自动增长。 对于预配存储小于等于 100 GB 的服务器，可用存储小于 1 GB 或预配存储的 10%（以这二者中的较大值为准）后，预配存储大小会立即增加 5 GB。 对于预配存储大于 100 GB 的服务器，当可用存储空间小于 10 GB 或预配存储大小的 5%（以这二者中的较大值为准）时，预配存储大小会增加 5%。 适用上面指定的最大存储限制。

例如，如果已预配 1000 GB 的存储，而实际使用量超过 950 GB，则服务器存储大小会增加到 1050 GB。 或者，如果已预配 10 GB 的存储，则当可用存储少于 1 GB 时，存储大小会增加到 15 GB。

请记住，存储只能增加，不能减少。

## <a name="backup-storage"></a>备份存储

Azure Database for PostgreSQL 最高可以提供 100% 的已预配服务器存储作为备份存储，不收取任何额外费用。 使用的任何备份存储量超过此数量将按每月 GB 量收费。 例如，如果你预配的服务器的存储空间为 250 GB，则可以免费为服务器备份提供 250 GB 的附加存储。 超过 250GB 的备份存储量按[定价模型](https://azure.microsoft.com/pricing/details/postgresql/)收费。 若要了解影响备份存储使用率的因素、监视和控制备份存储成本，可以参考[备份文档](concepts-backup.md)。

## <a name="scale-resources"></a>缩放资源

创建服务器之后，可以独立地更改 vCore 数、硬件生成、定价层（基本层的操作除外）、存储量和备份保留期。 创建服务器之后，不能更改备份存储类型。 可以向上或向下调整 VCore 数。 备份保留期可以从 7 天到 35 天进行上下调整。 存储大小只能增加。 可以通过门户或 Azure CLI 缩放资源。 有关使用 Azure CLI 进行缩放的示例，请参阅[使用 Azure CLI 监视和缩放 Azure Database for PostgreSQL 服务器](scripts/sample-scale-server-up-or-down.md)。

> [!NOTE]
> 存储大小只能增加。 增加后，将不能返回到更小的存储大小。

更改 vCore 数、硬件生成或定价层时，将会使用新的计算分配创建原始服务器的副本。 启动并运行新服务器后，连接将切换到新服务器。 在系统切换到新服务器的短暂期间，无法建立新的连接，所有未提交的连接将会回退。 此时段不定，但大多数情况下短于一分钟。

缩放存储和更改备份保留期是真正的联机操作。 不会造成停机，应用程序不会受影响。 当 IOPS 随已预配存储的大小缩放时，可以通过扩大存储来增加提供给服务器的 IOPS。

## <a name="pricing"></a>定价

有关最新定价信息，请参阅服务的[定价页](https://azure.microsoft.com/pricing/details/PostgreSQL/)。 若要查看所需配置的具体成本，可以单击 [Azure 门户](https://portal.azure.com/#create/Microsoft.PostgreSQLServer)的“定价层”选项卡，系统就会根据选定的选项显示每月成本。 如果没有 Azure 订阅，可使用 Azure 定价计算器获取估计的价格。 在 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)网站上，选择“添加项”  ，展开“数据库”  类别，选择“Azure Database for PostgreSQL”  自定义选项。

## <a name="next-steps"></a>后续步骤

- 了解如何[在门户中创建 PostgreSQL 服务器](tutorial-design-database-using-azure-portal.md)。
- 了解[服务限制](concepts-limits.md)。
- 了解如何[使用只读副本进行横向扩展](howto-read-replicas-portal.md)。
