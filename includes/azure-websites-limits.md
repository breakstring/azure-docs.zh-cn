---
author: rothja
ms.service: app-service
ms.topic: include
ms.date: 03/04/2020
ms.author: jroth
ms.openlocfilehash: 71782661fd960cae3248005a8b90944eff9bef45
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024223"
---
| 资源 | 免费 | 共享 | 基本 | Standard | 高级 (v1-v3) | 隔离 </th> |
| --- | --- | --- | --- | --- | --- | --- |
| 每个 [Azure 应用服务计划](../articles/app-service/overview-hosting-plans.md)的 [Web 应用、移动应用或 API 应用数](https://azure.microsoft.com/services/app-service/)<sup>1</sup> |10 |100 |无限制<sup>2</sup> |无限制<sup>2</sup> |无限制<sup>2</sup> |无限制<sup>2</sup>|
| [应用服务计划](../articles/app-service/overview-hosting-plans.md) |每个区域仅 10 个 |每个资源组 10 个 |每个资源组 100 个 |每个资源组 100 个 |每个资源组 100 个 |每个资源组 100 个|
| 计算实例类型 |共享 |共享 |专用<sup>3</sup> |专用<sup>3</sup> |专用<sup>3</sup></p> |专用<sup>3</sup>|
| [横向扩展](../articles/app-service/manage-scale-up.md)（最大实例数） |1 个共享 |1 个共享 |3 个专用<sup>3</sup> |10 个专用<sup>3</sup> | 20 个专用于 v1 和 v2；30 个专用于 v3。<sup>3</sup>|100 个专用<sup>4</sup>|
| 存储<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup> <br/><br/> 如果超过 250 GB，请提交支持请求。 |1 TB<sup>5</sup> <br/><br/> 可用存储配额为 999 GB。 |
| CPU 时间（5 分钟）<sup>6</sup> |3 分钟 |3 分钟 |无限制，按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)</a>付费 |无限制，按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)</a>付费 |无限制，按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)</a>付费 |无限制，按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)</a>付费|
| CPU 时间（天）<sup>6</sup> |60 分钟 |240 分钟 |无限制，按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)</a>付费 |无限制，按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)</a>付费 |无限制，按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)</a>付费 |无限制，按标准[费率](https://azure.microsoft.com/pricing/details/app-service/)</a>付费 |
| 内存（1 小时） |每个应用服务计划 1024 MB |每个应用 1024 MB |空值 |空值 |空值 |空值 |
| 带宽 |165 MB |无限制，收取[数据传输费](https://azure.microsoft.com/pricing/details/data-transfers/) |无限制，收取[数据传输费](https://azure.microsoft.com/pricing/details/data-transfers/) |无限制，收取[数据传输费](https://azure.microsoft.com/pricing/details/data-transfers/) |无限制，收取[数据传输费](https://azure.microsoft.com/pricing/details/data-transfers/) |无限制，收取[数据传输费](https://azure.microsoft.com/pricing/details/data-transfers/) |
| 应用程序体系结构 |32 位 |32 位 |32 位/64 位 |32 位/64 位 |32 位/64 位 |32 位/64 位 |
| 每个实例的 Web 套接字数<sup>7</sup> |5 |35 |350 |无限制 |无限制 |无限制 |
| IP 连接数 | 600 | 600 | 取决于实例大小<sup>8</sup> | 取决于实例大小<sup>8</sup> | 取决于实例大小<sup>8</sup> | 16,000 |
| 每个应用程序的并发[调试器连接数](../articles/app-service/troubleshoot-dotnet-visual-studio.md) |1 |1 |1 |5 |5 |5 |
| 每个订阅的应用服务证书数<sup>9</sup>| 不支持 | 不支持 |10 |10 |10 |10 |
| 每个应用的自定义域数</a> |0（仅 azurewebsites.net 子域）|500 |500 |500 |500 |500 |
| 自定义域 [SSL 支持](../articles/app-service/configure-ssl-certificate.md) |不支持，默认情况下 \*.azurewebsites.net 的通配符证书可用|不支持，默认情况下 \*.azurewebsites.net 的通配符证书可用|无限制的 SNI SSL 连接 |包含无限制的 SNI SSL 连接和 1 个 IP SSL 连接 |包含无限制的 SNI SSL 连接和 1 个 IP SSL 连接 | 包含无限制的 SNI SSL 连接和 1 个 IP SSL 连接|
| 混合连接 | | | 每个计划 5 个 | 每个计划 25 个 | 每个应用 200 个 | 每个应用 200 个 |
| [虚拟网络集成](../articles/app-service/web-sites-integrate-with-vnet.md) | | |   |  X |  X  |  X  |
| [终结点](../articles/app-service/networking/private-endpoint.md) | | |   |   |  每个应用 100 个  |    |
| 集成负载均衡器 | |X |X |X |X |X<sup>10</sup> |
| [访问限制](../articles/app-service/networking-features.md#access-restrictions) | 每个应用 512 条规则 | 每个应用 512 条规则 | 每个应用 512 条规则 | 每个应用 512 条规则 | 每个应用 512 条规则 | 每个应用 512 条规则 |
| [Always On](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [计划备份](../articles/app-service/manage-backup.md) | | | | 计划备份每 2 小时一次，每天最多 12 次备份（手动 + 计划） | 计划备份每小时一次，每天最多 50 次备份（手动 + 计划） | 计划备份每小时一次，每天最多 50 次备份（手动 + 计划） |
| [自动缩放](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJob](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [终结点监视](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| 每个应用的[过渡槽](../articles/app-service/deploy-staging-slots.md)数| | | |5 |20 |20 |
| [在生产环境中测试](../articles/app-service/deploy-staging-slots.md#route-traffic)| | | |X |X |X |
| [诊断日志](../articles/app-service/troubleshoot-diagnostic-logs.md) | X | X | X | X | X | X |
| Kudu | X | X | X | X | X | X |
| [身份验证和授权](../articles/app-service/overview-authentication-authorization.md) | X | X | X | X | X | X |
| [应用服务托管证书（公共预览版）](https://azure.microsoft.com/updates/secure-your-custom-domains-at-no-cost-with-app-service-managed-certificates-preview/)<sup>12</sup> | |  | X | X | X | X |
| SLA | |  |99.95%|99.95%|99.95%|99.95%|  

<sup>1</sup>除非特别说明，否则应用和存储配额依每个应用服务计划为准。  
<sup>2</sup>可以在这些计算机上托管的应用的实际数目取决于应用的活动、计算机实例的大小和相应的资源利用率。  
<sup>3</sup>专用实例可有不同的大小。 有关详细信息，请参阅[应用服务定价](https://azure.microsoft.com/pricing/details/app-service/)。  
<sup>4</sup>收到请求时允许更多。  
<sup>5</sup>该存储限制是针对同一应用服务计划中所有应用的内容总大小的限制。 单个资源组和区域内所有应用服务计划中的所有应用的总内容大小不能超过 500GB。  
<sup>6</sup>这些资源受到专用实例上的物理资源（实例大小和实例数）的限制。  
<sup>7</sup>如果将基本层的某个应用扩展为两个实例，则其中每个实例有 350 个并发连接。 对于标准层和更高层，Web 套接字没有理论上的限制，但其他因素可能会限制 Web 套接的数量。 例如，允许的最大并发请求数（由 `maxConcurrentRequestsPerCpu` 定义）为：每个小型 VM 7500 个、每个中型 VM 15000 个（7500 x 2 个核心）以及每个大型 VM 75000 个（18750 x 4 个核心）。  
<sup>8</sup>最大 IP 连接数指的是每个实例的最大 IP 连接数，它取决于实例大小：每个 B1/S1/P1V3 实例 1,920 个，每个 B2/S2/P2V3 实例 3,968 个，每个 B3/S3/P3V3 实例 8,064 个。  
<sup>9</sup>每个订阅的应用服务证书配额限制可以通过支持请求增加到最大限制 200。  
<sup>10</sup>独立应用服务 SKU 能够通过使用 Azure 负载均衡器实现内部负载均衡 (ILB)，因此没有来自 Internet 的公共连接。 因此，必须从能够直接访问 ILB 网络终结点的计算机使用 ILB 独立应用服务的某些功能。  
<sup>11</sup>按需、按计划或持续作为应用服务实例内的后台任务运行自定义可执行文件和/或脚本。 连续执行 WebJob 需要使用“始终打开”。 对于可以在某个应用服务实例中运行的 Webjob 的数量，没有预定义的限制。 但存在实际上的限制，具体取决于应用程序代码尝试执行的操作。

<sup>12</sup>不支持裸域。 仅颁发标准证书（通配符证书不可用）。 每个自定义域仅限一个免费证书。
