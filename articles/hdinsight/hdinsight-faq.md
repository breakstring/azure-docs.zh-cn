---
title: Azure HDInsight 常见问题解答
description: 有关 HDInsight 的常见问题解答
keywords: 常见问题解答, FAQ
author: Ramakoni1
ms.author: ramakoni
ms.service: hdinsight
ms.custom: hdinsightactive,seoapr2020
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: 13282962886d8682b6056d10f8f0cc5a7f626d60
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946014"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight：常见问题

本文提供有关如何运行 [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) 的一些最常见问题的解答。

## <a name="creating-or-deleting-hdinsight-clusters"></a>创建或删除 HDInsight 群集

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>如何预配 HDInsight 群集？

若要查看 HDInsight 群集类型和预配方法，请参阅[使用 Apache Hadoop、Apache Spark、Apache Kafka 及其他组件在 HDInsight 中设置群集](./hdinsight-hadoop-provision-linux-clusters.md)。

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>如何删除现有的 HDInsight 群集？

若要详细了解如何删除不再使用的群集，请参阅[删除 HDInsight 群集](hdinsight-delete-cluster.md)。

尝试在创建群集后，至少等 30 到 60 分钟再删除群集。 否则，操作可能会失败并出现以下错误消息：

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>如何为工作负荷选择适当的核心数或节点数？

适当的核心数和其他配置选项取决于多种因素。

有关详细信息，请参阅 [HDInsight 群集的容量规划](./hdinsight-capacity-planning.md)。

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>HDInsight 群集中有哪些不同类型的节点？

请参阅 [Azure HDInsight 群集中的资源类型](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters)。

### <a name="what-are-the-best-practices-for-creating-large-hdinsight-clusters"></a>创建大型 HDInsight 群集的最佳做法有哪些？

1. 建议使用[自定义 Ambari DB](./hdinsight-custom-ambari-db.md) 设置 HDInsight 群集，以提高群集的可伸缩性。
2. 使用 [Azure Data Lake Storage Gen2](./hdinsight-hadoop-use-data-lake-storage-gen2.md) 创建 HDInsight 群集，以利用更高的带宽和 Azure Data Lake Storage Gen2 的其他性能特点。
3. 头节点应该足够大，能够容纳在这些节点上运行的多个主服务。
4. 某些特定工作负载（如 Interactive Query）也需要更大的 Zookeeper 节点。 请考虑使用至少 8 个核心 VM。
5. 对于 Hive 和 Spark，请使用[外部 Hive 元存储](./hdinsight-use-external-metadata-stores.md)。

## <a name="individual-components"></a>单个组件

### <a name="can-i-install-additional-components-on-my-cluster"></a>是否可以在群集上安装其他组件？

是的。 若要安装其他组件或自定义群集配置，请使用：

- 在创建期间或之后使用脚本。 脚本可通过[脚本操作](./hdinsight-hadoop-customize-cluster-linux.md)调用。 脚本操作是一种配置选项，可通过 Azure 门户、HDInsight Windows PowerShell cmdlet 或 HDInsight .NET SDK 使用。 可通过 Azure 门户、HDInsight Windows PowerShell cmdlet 或 HDInsight .NET SDK 使用此配置选项。

- 使用 [HDInsight 应用程序平台](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/)安装应用程序。

请参阅[在 HDInsight 中可以使用哪些 Apache Hadoop 组件和版本？](./hdinsight-component-versioning.md#apache-components-available-with-different-hdinsight-versions)了解受支持组件的列表

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>是否可以升级预装在群集上的单个组件？

如果升级内置组件或预安装在群集上的应用程序，Microsoft 将不支持生成的配置。 Microsoft 尚未测试这些系统配置。 请尝试使用不同版本的 HDInsight 群集，该版本可能已预装了组件的升级版本。

例如，不支持将 Hive 升级为单个组件。 HDInsight 是一个托管服务；许多服务已与 Ambari 服务器集成并已经过测试。 单独升级 Hive 会导致其他组件的已编制索引的二进制文件发生更改，并会导致群集上出现组件集成问题。

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Spark 和 Kafka 是否可在同一 HDInsight 群集上运行？

否，无法在同一 HDInsight 群集上运行 Apache Kafka 和 Apache Spark。 单独为 Kafka 和 Spark 创建群集可避免资源争用问题。

### <a name="how-do-i-change-timezone-in-ambari"></a>如何更改 Ambari 中的时区？

1. 通过 `https://CLUSTERNAME.azurehdinsight.net` （其中，CLUSTERNAME 是群集的名称）打开 Ambari Web UI。
2. 在右上角选择“管理”|“设置”。 

   ![Ambari 设置](media/hdinsight-faq/ambari-settings.png)

3. 在“用户设置”窗口中，从“时区”下拉列表中选择新的时区，然后单击“保存”。

   ![Ambari 用户设置](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>元存储

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-database"></a>如何从现有的元存储迁移到 Azure SQL 数据库？ 

若要从 SQL Server 迁移到 Azure SQL Database，请参阅 [教程：使用 DMS 脱机将 SQL Server 迁移到 AZURE Sql 数据库中的单个数据库或池中的数据库](../dms/tutorial-sql-server-to-azure-sql.md)。

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>删除群集时是否会删除 Hive 元存储？

这取决于群集配置为使用的元存储类型。

对于默认元存储：默认元存储是群集生命周期的一部分。 删除群集时，也会删除相应的该元存储和元数据。

对于自定义元存储：元存储的生命周期与群集的生命周期无关。 因此，可以在不丢失元数据的情况下创建和删除群集。 即使在删除和重新创建 HDInsight 群集之后，系统也仍会保留 Hive 架构等元数据。

有关详细信息，请参阅[在 Azure HDInsight 中使用外部元数据存储](hdinsight-use-external-metadata-stores.md)。

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>迁移 Hive 元存储是否也会迁移 Ranger 数据库的默认策略？

否，策略定义位于 Ranger 数据库中，因此，迁移 Ranger 数据库会迁移其策略。

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-the-other-way-around"></a>是否可以将 Hive 元存储从企业安全性套餐 (ESP) 群集迁移到非 ESP 群集，且反之亦然？

是，可以将 Hive 元存储从 ESP 群集迁移到非 ESP 群集。

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>如何估算 Hive 元存储数据库的大小？

Hive 元存储用于存储 Hive 服务器所用数据源的元数据。 其大小要求一定程度上取决于 Hive 数据源的数量和复杂性。 这些项目无法预先估算。 如 [Hive 元存储指南](hdinsight-use-external-metadata-stores.md#hive-metastore-guidelines)中所述，你可以从 S2 层开始。 该层提供 50 DTU 和 250 GB 的存储；如果遇到瓶颈，请纵向扩展数据库。

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>是否支持使用除 Azure SQL 数据库以外的其他任何数据库作为外部元存储？

否，Microsoft 仅支持使用 Azure SQL 数据库作为外部自定义元存储。

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>是否可在多个群集之间共享元存储？

是的，你可以在多个群集之间共享自定义元存储，只要它们使用相同版本的 HDInsight。

## <a name="connectivity-and-virtual-networks"></a>连接和虚拟网络  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>在网络中阻止端口 22 和 23 会造成什么影响？

如果阻止端口22和端口23，则不会有权访问群集。 HDInsight 服务不使用这些端口。

有关详细信息，请参阅以下文档：

- [HDInsight 上的 Apache Hadoop 服务所使用的端口](./hdinsight-hadoop-port-settings-for-services.md)

- [使用专用终结点保护传入虚拟网络中 HDInsight 群集的流量](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [HDInsight 管理 IP 地址](./hdinsight-management-ip-addresses.md)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>是否可以在 HDInsight 群集所在的同一子网中部署额外的虚拟机？

是，可以在 HDInsight 群集所在的同一子网中部署额外的虚拟机。 可使用以下配置：

- 边缘节点：可根据[在 HDInsight 中的 Apache Hadoop 群集上使用空边缘节点](hdinsight-apps-use-edge-node.md)中所述，将另一个边缘节点添加到群集。

- 独立节点：可将独立的虚拟机添加到同一子网，并使用专用终结点 `https://<CLUSTERNAME>-int.azurehdinsight.net` 从该虚拟机访问群集。 有关详细信息，请参阅[控制网络流量](./control-network-traffic.md)。

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>是否应该将数据存储在边缘节点的本地磁盘上？

否，将数据存储在本地磁盘上不是一个好主意。 如果节点出现故障，本地存储的所有数据都将丢失。 我们建议将数据存储在 Azure Data Lake Storage Gen2 或 Azure Blob 存储中，或者通过装载 Azure 文件共享来存储数据。


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>是否可将现有的 HDInsight 群集添加到另一个虚拟网络？

不可以。 应在预配时指定虚拟网络。 如果在预配期间未指定任何虚拟网络，则部署过程将会创建一个无法从外部访问的内部网络。 有关详细信息，请参阅[将 HDInsight 添加到现有的虚拟网络](hdinsight-plan-virtual-network-deployment.md#existingvnet)。

## <a name="security-and-certificates"></a>安全性和证书

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>对于 Azure HDInsight 群集上的恶意软件防护，你们有什么建议？

有关恶意软件防护的详细信息，请参阅[适用于 Azure 云服务和虚拟机的 Microsoft Antimalware](../security/fundamentals/antimalware.md)。

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>如何为 HDInsight ESP 群集创建 keytab？

为域用户名创建 Kerberos keytab。 以后可以使用此 keytab 对已加入远程域的群集进行身份验证，而无需输入密码。 域名采用大写：

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>是否可以使用现有的 Azure Active Directory 租户创建具有 ESP 的 HDInsight 群集？

必须先启用 Azure Active Directory 域服务 (Azure AD DS)，然后才能创建具有 ESP 的 HDInsight 群集。 开源 Hadoop 依赖于使用 Kerberos 进行身份验证（与 OAuth 不同）。

若要将 VM 加入域，必须有一个域控制器。 Azure AD DS 是托管域控制器，可将其视为 Azure Active Directory 的扩展。 Azure AD DS 提供以托管方式生成安全 Hadoop 群集所需满足的所有 Kerberos 要求。 用作托管服务的 HDInsight 可与 Azure AD DS 集成来提供安全性。

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>是否可在 AAD DS 安全 LDAP 设置中使用自签名证书并预配 ESP 群集？

建议使用证书颁发机构颁发的证书。 但在 ESP 上，也支持使用自签名证书。 有关详细信息，请参阅：

- [启用 Azure Active Directory 域服务](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [教程：为 Azure Active Directory 域服务托管域配置安全 LDAP](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>如何拉取 Ranger 中显示的登录活动？

出于审核要求，Microsoft 建议按[使用 Azure Monitor 日志监视 HDInsight 群集](./hdinsight-hadoop-oms-log-analytics-tutorial.md)中的说明启用 Azure Monitor 日志。

### <a name="can-i-disable-clamscan-on-my-cluster"></a>是否可在群集上禁用 `Clamscan`？

`Clamscan` 是在 HDInsight 群集上运行并由 Azure security (azsecd) 使用的防病毒软件，以保护群集免受病毒攻击。 Microsoft 强烈建议用户避免对默认配置进行任何更改 `Clamscan` 。

此过程不会影响其他进程，也不会对其进行任何循环。 它始终屈从于其他进程。 `Clamscan`仅当系统处于空闲状态时，才应显示 CPU 峰值。  

如果必须控制计划，可使用以下步骤：

1. 使用以下命令禁用自动执行：
   
  sudo `usr/local/bin/azsecd config -s clamav -d Disabled` sudo service azsecd 重启 
   
1. 添加一个 Cron 作业，以便以 root 身份运行以下命令：
   
   `/usr/local/bin/azsecd manual -s clamav`

有关如何设置和运行 Cron 作业的详细信息，请参阅[如何设置 Cron 作业](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)？

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>为什么在 Spark ESP 群集上可使用 LLAP？
启用 LLAP 是出于安全（而非性能）原因 (Apache Ranger)。 使用较大的节点 VM 来适应 LLAP 的资源使用（例如最低 D13V2）。 

### <a name="how-can-i-add-additional-aad-groups-after-creating-an-esp-cluster"></a>如何在创建 ESP 群集后添加其他 AAD 组？
可通过两种方式实现此目的：1- 可以重新创建群集，并在创建群集时添加其他组。 如果你使用 AAD-DS 中范围内的同步功能，请确保组 B 包含在范围内的同步中。
2- 将组作为之前用于创建 ESP 群集的组的嵌套子组进行添加。 例如，如果已使用组 `A` 创建了一个 ESP 群集，则稍后可将组 `B` 添加为 `A` 的嵌套子组，在大约一小时后，它将自动同步并在群集中可用。 

## <a name="storage"></a>存储

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>是否可将 Azure Data Lake Storage Gen2 作为附加存储帐户添加到现有的 HDInsight 群集？

否，目前无法将 Azure Data Lake Storage Gen2 存储帐户添加到使用 Blob 存储作为主要存储的群集。 有关详细信息，请参阅[存储选项的比较](hdinsight-hadoop-compare-storage-options.md)。

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>如何查找 Data Lake 存储帐户当前链接的服务主体？

可以在 Azure 门户中群集属性下的“Data Lake Storage Gen1 访问”中找到相关设置。 有关详细信息，请参阅[验证群集设置](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up)。
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>如何计算 HDInsight 群集的存储帐户和 Blob 容器的用量？

执行下列操作之一：

- [使用 PowerShell](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- 使用以下命令行查找 HDInsight 群集上的 */user/hive/.Trash/* 文件夹大小：
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>如何设置 Blob 存储帐户的审核？

若要审核 Blob 存储帐户，请使用[在 Azure 门户中监视存储帐户](../storage/common/storage-monitor-storage-account.md)中的过程配置监视。 HDFS 审核日志仅提供本地 HDFS 文件系统 (hdfs://mycluster) 的审核信息。  它不包括在远程存储上执行的操作。

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>如何在 Blob 容器与 HDInsight 头节点之间传输文件？

在头节点上运行类似于以下 shell 脚本的脚本：

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> 文件 *filenames.txt* 包含 Blob 容器中的文件的绝对路径。
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>是否有任何适用于存储的 Ranger 插件？

目前，Blob 存储和 Azure Data Lake Storage Gen1 或 Gen2 没有任何 Ranger 插件。 对于 ESP 群集，应使用 Azure Data Lake Storage。 至少可以使用 HDFS 工具在文件系统级别手动设置精细权限。 此外，在使用 Azure Data Lake Storage 时，ESP 群集将在群集级别使用 Azure Active Directory 执行某种文件系统访问控制。 

可以使用 Azure 存储资源管理器将数据访问策略分配到用户的安全组。 有关详细信息，请参阅：

- [如何设置 Azure AD 用户的权限，以使用 Hive 或其他服务在 Data Lake Storage Gen2 中查询数据？](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [使用 Azure 存储资源管理器和 Azure Data Lake Storage Gen2 设置文件和目录级别权限](../storage/blobs/data-lake-storage-explorer.md)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>是否可以在不增大工作器节点的磁盘大小的情况下增大群集上的 HDFS 存储？

不是。 不能增加任何工作节点的磁盘大小。 因此，增加磁盘大小的唯一方法是删除群集，并将其重新创建为更大的辅助 Vm。 请不要使用 HDFS 来存储任何 HDInsight 数据，因为删除群集时会删除这些数据。 请改为在 Azure 中存储数据。 纵向扩展群集也可以将更多的容量添加到 HDInsight 群集。

## <a name="edge-nodes"></a>边缘节点

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>创建群集后是否可以添加边缘节点？

请参阅[在 HDInsight 中的 Apache Hadoop 群集上使用空边缘节点](hdinsight-apps-use-edge-node.md)。

### <a name="how-can-i-connect-to-an-edge-node"></a>如何连接到边缘节点？

创建边缘节点后，可以在端口 22 上使用 SSH 连接到该节点。 可以在群集门户中找到边缘节点的名称。 名称通常以 *-ed* 结尾。

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>持久化脚本为何未在新建的边缘节点上自动运行？

持久化脚本用于自定义通过缩放操作添加到群集的新工作器节点。 持久化脚本不适用于边缘节点。

## <a name="rest-api"></a>REST API

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>可使用哪些 REST API 调用从群集提取 Tez 查询视图？

可使用以下 REST 终结点来提取 JSON 格式的所需信息。 使用基本身份验证标头发出请求。

- `Tez Query View`： *https： \/ / \<cluster name> . azurehdinsight.net/ws/v1/timeline/HIVE_QUERY_ID/*
- `Tez Dag View`： *https： \/ / \<cluster name> . azurehdinsight.net/ws/v1/timeline/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>如何以 Azure Active Directory 用户身份从 HDI 群集检索配置详细信息？

若要以 AAD 用户身份协商正确的身份验证令牌，请使用以下格式浏览网关：

* https:// `<cluster dnsname>` . azurehdinsight.net/api/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>如何使用 Ambari Restful API 来监视 YARN 性能？

如果在同一虚拟网络或对等互连的虚拟网络中调用 Curl 命令，则该命令为：

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
如果从虚拟网络外部或者从非对等互连的虚拟网络调用该命令，则命令格式为：

- 对于非 ESP 群集：
  
  ```curl
  curl -u <cluster login username> -sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

- 对于 ESP 群集：
  
  ```curl
  curl -u <cluster login username>-sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

> [!NOTE]
> Curl 将提示输入密码。 必须输入群集登录用户名的有效密码。

## <a name="billing"></a>计费

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>部署一个 HDInsight 群集需要多少费用？

有关定价以及计费相关的常见问题解答的详细信息，请参阅 [Azure HDInsight 定价](https://azure.microsoft.com/pricing/details/hdinsight/)页。

### <a name="when-does-hdinsight-billing-start--stop"></a>HDInsight 计费何时开始和停止？

HDInsight 群集计费在创建群集之后便会开始，删除群集后才会停止。 群集按分钟按比例计费。

### <a name="how-do-i-cancel-my-subscription"></a>如何取消订阅？

有关如何取消订阅的信息，请参阅[取消 Azure 订阅](../cost-management-billing/manage/cancel-azure-subscription.md)。

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>对于即用即付订阅，取消订阅后会发生什么情况？

有关取消订阅后发生的情况的详细信息，请参阅[取消订阅之后会发生什么情况？](../cost-management-billing/manage/cancel-azure-subscription.md)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-im-running-an-hdinsight-36-cluster"></a>即使我在运行 HDInsight 3.6 群集，Hive 版本为何会在 Ambari UI 中显示为1.2.1000 而不是2.1？

尽管 Ambari UI 中仅显示 1.2，但 HDInsight 3.6 同时包含 Hive 1.2 和 Hive 2.1。

## <a name="other-faq"></a>其他常见问题解答

### <a name="what-does-hdinsight-offer-for-real-time-stream-processing-capabilities"></a>HDInsight 提供哪些有关实时流处理的功能？

有关流处理集成功能的信息，请参阅[在 Azure 中选择流处理技术](/azure/architecture/data-guide/technology-choices/stream-processing)。

### <a name="is-there-a-way-to-dynamically-kill-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>当群集空闲特定的时间段后，是否可以动态终止群集的头节点？

无法对 HDInsight 群集执行此操作。 可以使用 Azure 数据工厂实现此类目的。

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>HDInsight 提供哪些符合性产品？

有关符合性信息，请参阅 [Microsoft 信任中心](https://www.microsoft.com/trust-center) 和 [Microsoft Azure 符合性概述](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)。
