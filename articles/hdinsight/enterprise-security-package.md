---
title: 适用于 Azure HDInsight 的企业安全性套餐
description: 了解 Azure HDInsight 中的企业安全性套餐组件和版本。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 442c21c92ef2124ebef1889f99a8d2b806c8ce10
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943299"
---
# <a name="enterprise-security-package-for-azure-hdinsight"></a>适用于 Azure HDInsight 的企业安全性套餐

Enterprise Security 是一种可选包，可在 HDInsight 群集上作为创建群集工作流的一部分添加。 Enterprise Security 包支持：

* 与 Active Directory 集成进行身份验证。

    在过去，创建了包含本地管理员用户和本地 SSH 用户的 HDInsight 群集。 本地管理员用户可以访问所有文件、文件夹、表和列。  借助企业安全性套餐，可以通过将 HDInsight 与 Azure Active Directory 域服务集成来启用 Azure 基于角色的访问控制。

    有关详细信息，请参阅：

    * [已加入域的 HDInsight 群集的 Apache Hadoop 安全性简介](./domain-joined/hdinsight-security-overview.md)

    * [在 HDInsight 中计划 Azure 已加入域的 Apache Hadoop 群集](./domain-joined/apache-domain-joined-architecture.md)

    * [配置已加入域的沙盒环境](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

    * [使用 Azure Active Directory 域服务设置和配置已加入域的 HDInsight 群集](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

* 数据授权

  * 与 Apache Ranger 集成以对 Hive、Spark SQL 和 Yarn 队列授权。
  * 可以对文件和文件夹设置访问控制。

    有关详细信息，请参阅[在已加入域的 HDInsight 中配置 Apache Hive 策略](./domain-joined/apache-domain-joined-run-hive.md)

* 查看审核日志以监视访问和配置的策略。

## <a name="supported-cluster-types"></a>支持的群集类型

目前只有以下群集类型支持 Enterprise Security 包：

* Hadoop（仅限 HDInsight 3.6）
* Spark
* Kafka
* HBase
* 交互式查询

## <a name="support-for-azure-data-lake-storage"></a>支持 Azure Data Lake Storage

Enterprise Security 包支持使用 Azure Data Lake Storage 作为主存储和附加存储。

## <a name="pricing-and-service-level-agreement-sla"></a>定价和服务级别协议 (SLA)

有关 Enterprise Security 包的定价和 SLA 的信息，请参阅 [HDInsight 定价](https://azure.microsoft.com/pricing/details/hdinsight/)。

## <a name="next-steps"></a>后续步骤

* [为 HDInsight 上的 Apache Hadoop、Spark 和其他组件设置群集](hdinsight-hadoop-provision-linux-clusters.md)
* [使用 Windows 电脑在 HDInsight 上的 Apache Hadoop 中工作](hdinsight-hadoop-windows-tools.md)
* [与 Azure HDInsight 版本相关的 Hortonworks 发行说明](./hortonworks-release-notes.md)
* [HDInsight 上的 Apache 组件](./hdinsight-component-versioning.md)