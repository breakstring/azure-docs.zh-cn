---
title: Azure HDInsight 中的开放源代码软件支持
description: Microsoft Azure 为开源技术提供常规级别的支持。
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: fec4cff974031982c782c9265a7d3186d6bb0233
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942561"
---
# <a name="open-source-software-support-in-azure-hdinsight"></a>Azure HDInsight 中的开放源代码软件支持

Microsoft Azure HDInsight 服务使用围绕 Apache Hadoop 构建的开源技术环境。 Microsoft Azure 为开源技术提供常规级别的支持。 有关详细信息，请参阅 [Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)的“支持范围”部分。 HDInsight 服务为内置组件提供附加的支持级别。

## <a name="components"></a>组件

在 HDInsight 服务中可以使用两种类型的开源组件：

### <a name="built-in-components"></a>内置组件

这些组件预先安装在 HDInsight 群集上，并提供在群集的核心功能。 以下组件属于此类别：

* [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) 资源管理器。
* Hive 查询语言 [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)。
* [Apache Mahout](https://mahout.apache.org/)。

[在 HDInsight 中可以使用哪些 Apache Hadoop 组件和版本？](hdinsight-component-versioning.md)中提供了群集组件的完整列表。

### <a name="custom-components"></a>自定义组件

群集用户可以安装或者在工作负荷中使用由社区提供的或自己创建的任何组件。

> [!WARNING]  
> 完全支持通过 HDInsight 群集提供的组件。 Microsoft 支持部门可帮助找出并解决与这些组件相关的问题。
>
> 自定义组件可获得合理范围的支持，以帮助进一步排查问题。 Microsoft 支持部门也许能够解决问题。 支持人员可能要求你参与可用的开源技术渠道，在该处可以找到该技术的深入专业知识。 可以使用许多社区站点， 示例包括[有关 HDInsight 的 Microsoft 问答页](/answers/topics/azure-hdinsight.html)和[堆栈溢出](https://stackoverflow.com)。
>
> Apache 项目还在 [Apache 网站](https://apache.org)上提供了项目站点， 例如 [Hadoop](https://hadoop.apache.org/)。

## <a name="component-usage"></a>组件使用情况

HDInsight 服务提供多种方式来使用自定义组件。 不论在群集上使用组件或安装组件的方式为何，均适用相同级别的支持。 下表介绍 HDInsight 群集上最常见的自定义组件用法：

|使用情况 |说明 |
|---|---|
|作业提交|Hadoop 或其他类型的作业可以提交到执行或使用自定义组件的群集。|
|群集自定义|在群集创建期间，可以指定其他设置和安装在群集节点上的自定义组件。|
|示例|对于常见的自定义组件，Microsoft 和其他人可能会提供演示如何在 HDInsight 群集上使用这些组件的示例。 我们不针对这些示例提供支持。|

## <a name="next-steps"></a>后续步骤

* [使用脚本操作自定义 Azure HDInsight 群集](./hdinsight-hadoop-customize-cluster-linux.md)
* [为 HDInsight 开发脚本操作脚本](hdinsight-hadoop-script-actions-linux.md)
* [使用脚本操作在 Azure HDInsight 上安全管理 Python 环境](./spark/apache-spark-python-package-installation.md)