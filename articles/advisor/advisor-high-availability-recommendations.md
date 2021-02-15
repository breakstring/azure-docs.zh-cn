---
title: 使用顾问提高应用程序的可靠性
description: 使用 Azure 顾问确保并提高业务关键 Azure 部署的可靠性。
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: 0ced690ae735a281fdf8b1c3a020ff8c63ce469b
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078028"
---
# <a name="improve-the-reliability-of-your-application-by-using-azure-advisor"></a>使用 Azure 顾问提高应用程序的可靠性

Azure 顾问可帮助确保并提高业务关键应用程序的连续性。 可以从顾问中的顾问仪表板的“可靠性”选项卡上获取可靠性建议。

## <a name="check-the-version-of-your-check-point-network-virtual-appliance-image"></a>检查你的 Check Point 网络虚拟设备映像的版本

顾问可以识别你的虚拟机正在运行的是否是已知会在执行平台服务操作期间失去网络连接的 Check Point 映像版本。 顾问建议将帮助你升级到解决了此问题的较新版本的映像。 此检查将通过改进网络连接确保业务连续性。

## <a name="ensure-application-gateway-fault-tolerance"></a>确保应用程序网关容错

此建议可确保由应用程序网关提供支持的任务关键型应用程序的业务连续性。 顾问会识别未配置容错的应用程序网关实例。 然后，它会推荐你可采取的修正操作。 顾问会识别中型或大型单实例应用程序网关，并建议至少再添加一个实例。 它还会识别单实例或多实例小型应用程序网关，并建议将它们迁移到中型或大型 SKU。 顾问建议执行这些操作以确保应用程序网关实例配置为满足这些资源的当前 SLA 要求。

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>防止意外删除虚拟机数据

设置虚拟机备份可确保业务关键型数据的可用性，并防止意外删除或损坏。 顾问会识别未启用备份的虚拟机，并建议启用备份。 

## <a name="ensure-you-have-access-to-azure-experts-when-you-need-it"></a>确保在需要时有权访问 Azure 专家

如果你运行的是业务关键型工作负载，那么在需要时有权访问技术支持至关重要。 顾问会识别在其支持计划中不包含技术支持的潜在业务关键型订阅。 它会建议升级到包含技术支持的选项。

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>创建 Azure 服务运行状况警报，以便在 Azure 问题影响你时收到通知

我们建议设置 Azure 服务运行状况警报，以便在 Azure 服务问题影响你时收到通知。 [Azure 服务运行状况](https://azure.microsoft.com/features/service-health/)是一项免费服务，可在你受到 Azure 服务问题影响时提供个性化指导和支持。 顾问会识别未配置警报的订阅，并建议对其进行配置。

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>配置流量管理器终结点以便进行复原

如果任何给定的终结点出现故障，则包含多个终结点的 Azure 流量管理器配置文件将体验到更高的可用性。 将终结点放在不同区域中可进一步提高服务可靠性。 顾问会识别仅有一个终结点的流量管理器配置文件，并建议在另一个区域中至少再添加一个终结点。

如果为邻近路由配置的流量管理器配置文件中的所有终结点都位于同一区域，则来自其他区域的用户可能会遇到连接延迟。 如果一个区域中的所有终结点都出现故障，则将一个终结点添加或移动到另一个区域会提高整体性能并提供更好的可用性。 顾问会识别为邻近路由配置的流量管理器配置文件（其中所有终结点都位于同一区域中）。 它会建议将一个终结点添加或移动到另一个 Azure 区域。

如果为地理路由配置了流量管理器配置文件，则会根据已定义的区域将流量路由到终结点。 如果某个区域出现故障，则不会有任何预定义的故障转移。 如果你的某一终结点的“区域分组”配置为“所有(全球)”，则可避免流量被丢弃，并可提高服务可用性。 顾问会识别已为地理路由配置的、其中没有任何终结点的“区域分组”配置为“所有(全球)”的流量管理器配置文件。 它会建议更改该配置以将一个终结点设为“所有(全球)”。

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>对 Azure 存储帐户使用软删除可在意外覆盖或删除之后保存和恢复数据

为存储帐户启用[软删除](../storage/blobs/soft-delete-blob-overview.md)，以便将删除的 blob 转换为软删除状态，而不是永久删除。 覆盖数据时，会生成软删除快照以保存被覆盖数据的状态。 使用软删除可以在意外删除或覆盖时进行恢复。 顾问会识别未启用软删除的 Azure 存储帐户，并建议你启用软删除。

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>配置 VPN 网关为主动-主动以进行连接复原

在主动-主动配置中，VPN 网关的两个实例将建立连接到本地 VPN 设备的 S2S VPN 隧道。 当一个网关实例上发生计划内维护事件或计划外事件时，流量将自动切换到其他活动 IPsec 隧道。 Azure 顾问将识别未配置为主动-主动模式的 VPN 网关，并建议配置它们以提供高可用性。

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>使用生产 VPN 网关运行生产工作负荷

Azure 顾问会检查是否有任何使用基本 SKU 的 VPN 网关，并建议你改用生产 SKU。 基本 SKU 设计用于开发和测试。 生产 SKU 提供：
- 更多隧道。 
- BGP 支持。 
- 主动-主动配置选项。 
- 自定义 IPsec/IKE 策略。 
- 更高的稳定性和可用性。

## <a name="ensure-virtual-machine-fault-tolerance-temporarily-disabled"></a>确保虚拟机容错（暂时禁用）

要为应用程序提供冗余，建议将两个或更多虚拟机分组到一个可用性集中。 顾问会识别不属于可用性集的虚拟机，并建议将它们移动到可用性集中。 这种配置可以确保在发生计划内或计划外维护期间，至少有一个虚拟机可用，并且它满足 Azure 虚拟机 SLA 要求。 可以选择为虚拟机创建可用性集，或将虚拟机添加到现有可用性集。

> [!NOTE]
> 如果选择创建可用性集，则需要至少向其中再添加一个虚拟机。 建议在可用性集中对两个或更多虚拟机进行分组，确保其中一台虚拟机在出现故障期间可用。

## <a name="ensure-availability-set-fault-tolerance-temporarily-disabled"></a>确保可用性集容错（暂时禁用）

要为应用程序提供冗余，建议将两个或更多虚拟机分组到一个可用性集中。 顾问标识包含单个虚拟机的可用性集，并建议向其中添加一个或多个虚拟机。 这种配置可以确保在发生计划内或计划外维护期间，至少有一个虚拟机可用，并且它满足 Azure 虚拟机 SLA 要求。可以选择创建虚拟机，或将现有的虚拟机添加到可用性集。  

## <a name="use-managed-disks-to-improve-data-reliability-temporarily-disabled"></a>使用托管磁盘提高数据可靠性（暂时禁用）

如果虚拟机所在的可用性集带有共享存储帐户或存储缩放单元的磁盘，那么这些虚拟机在服务中断期间不可从单个存储规模单元的故障中复原。 顾问将识别这些可用性集，并建议迁移到 Azure 托管磁盘。 此迁移将确保可用性集中的虚拟机的磁盘彼此完全独立，以避免单点故障。 

## <a name="repair-invalid-log-alert-rules"></a>修复无效的日志警报规则

Azure 顾问检测到在 "条件" 部分中指定了无效查询的日志警报规则。 Azure Monitor 日志警报规则按指定的频率运行查询，并基于结果激发警报。 由于引用的资源、表或命令发生了变化，查询可能会因时间而变得无效。 顾问建议对警报查询进行更正，以防止自动禁用规则并确保监视范围。 有关详细信息，请参阅 [警报规则疑难解答](../azure-monitor/platform/alerts-troubleshoot-log.md#query-used-in-a-log-alert-isnt-valid)

## <a name="configure-consistent-indexing-mode-on-your-azure-cosmos-db-collection"></a>在 Azure Cosmos DB 集合上配置“一致”索引模式

为 Azure Cosmos DB 容器配置延迟索引模式可能会影响查询结果的新鲜度。 顾问会检测到以此方式配置的容器，并建议切换到“一致”模式。 [详细了解 Azure Cosmos DB 中的索引编制策略。](../cosmos-db/how-to-manage-indexing-policy.md)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>为 Azure Cosmos DB 容器配置分区键

Azure 顾问将识别即将达到其预配存储配额的 Azure Cosmos DB 非分区集合。 它会建议将这些集合迁移到具有分区键定义的新集合，使服务能够自动横向扩展它们。 [详细了解如何选择分区键。](../cosmos-db/partitioning-overview.md)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>将 Azure Cosmos DB .NET SDK 升级到 NuGet 提供的最新版本

Azure 顾问会识别使用旧版本的 .NET SDK 的 Azure Cosmos DB 帐户。 它会建议升级到 NuGet 提供的最新版本，以获取最新的修补程序、性能改进和功能。 [详细了解 Azure Cosmos DB .NET SDK。](../cosmos-db/sql-api-sdk-dotnet-standard.md)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>将 Azure Cosmos DB Java SDK 升级到 Maven 提供的最新版本

Azure 顾问会识别使用旧版本的 Java SDK 的 Azure Cosmos DB 帐户。 它会建议升级到 Maven 提供的最新版本，以获取最新的修补程序、性能改进和功能。 [详细了解 Azure Cosmos DB Java SDK。](../cosmos-db/sql-api-sdk-java-v4.md)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>将 Azure Cosmos DB Spark 连接器升级到 Maven 提供的最新版本

Azure 顾问会识别使用旧版本的 Azure Cosmos DB Spark 连接器的 Azure Cosmos DB 帐户。 它会建议升级到 Maven 提供的最新版本，以获取最新的修补程序、性能改进和功能。 [详细了解 Azure Cosmos DB Spark 连接器。](../cosmos-db/spark-connector.md)

## <a name="consider-moving-to-kafka-21-on-hdinsight-40"></a>考虑迁移到 HDInsight 4.0 上的 Kafka 2.1

从 2020 年 7 月 1 日开始，你将无法使用 Azure HDInsight 4.0 上的 Kafka 1.1 创建新的 Kafka 群集。 现有群集将在没有 Microsoft 支持的情况下按原样运行。 请考虑在 2020 年 6 月 30 日之前迁移到 HDInsight 4.0 上的 Kafka 2.1，避免出现潜在的系统/支持中断。

## <a name="consider-upgrading-older-spark-versions-in-hdinsight-spark-clusters"></a>考虑升级 HDInsight Spark 群集中较旧的 Spark 版本

从 2020 年 7 月 1 日开始，你将无法使用 HDInsight 3.6 上的 Spark 2.1 或 2.2 创建新的 Spark 群集。 你将无法使用 HDInsight 4.0 上的 Spark 2.3 创建新的 Spark 群集。 现有群集将在没有 Microsoft 支持的情况下按原样运行。 

## <a name="enable-virtual-machine-replication"></a>启用虚拟机复制
因未启用复制而不能复制到另一区域的虚拟机在出现区域性服务中断时无法复原。 复制虚拟机可在 Azure 区域服务中断期间减少任何不利的业务影响。 顾问会检测到未启用复制的 VM，并建议启用复制。 启用了复制时，如果发生服务中断，可以使虚拟机快速在远程 Azure 区域中启动。 [详细了解虚拟机复制。](../site-recovery/azure-to-azure-quickstart.md)

## <a name="upgrade-to-the-latest-version-of-the-azure-connected-machine-agent"></a>升级到最新版的 Azure Connected Machine Agent
[Azure 连接的计算机代理](../azure-arc/servers/manage-agent.md)会定期更新，其中包含 bug 修复、稳定性增强和新功能。 我们已识别出不能使用最新版本的计算机代理的资源，此顾问建议会建议将代理升级到最新版本，以获得最佳的 Azure Arc 体验。

## <a name="do-not-override-hostname-to-ensure-website-integrity"></a>请勿替代主机名以确保网站的完整性
在配置应用程序网关时，顾问建议尽量避免替代主机名。 如果在应用程序网关前端使用的域不同于访问后端所使用的域，可能会导致 cookie 或重定向 URL 被破坏。 请注意，并非所有情况都是如此，某种类别的后端（如 REST API 的后端）一般对此不太敏感。 请确保后端能够应对此情况或更新应用程序网关配置，以便不需要在后端替代主机名。 与应用服务一起使用时，将自定义域名附加到 Web 应用，并避免使用 *azurewebsites.net 主机名。* [详细了解自定义域](../application-gateway/troubleshoot-app-service-redirection-app-service-url.md)。

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>如何访问顾问中的高可用性建议

1. 登录 [Azure 门户](https://portal.azure.com)，并打开[顾问](https://aka.ms/azureadvisordashboard)。

2.  在顾问仪表板中，选择“高可用性”选项卡。

## <a name="next-steps"></a>后续步骤

有关顾问建议的详细信息，请参阅以下资源：
* [顾问简介](advisor-overview.md)
* [顾问入门](advisor-get-started.md)
* [顾问分数](azure-advisor-score.md)
* [顾问成本建议](advisor-cost-recommendations.md)
* [顾问性能建议](advisor-performance-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)
* [顾问卓越运营建议](advisor-operational-excellence-recommendations.md)