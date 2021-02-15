---
title: 本地 HDFS 在 Azure HDInsight 群集上的安全模式下停止响应
description: 排查本地 Apache HDFS 在 Azure HDInsight Apache 群集上的安全模式下停止响应的问题
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: d34bf8d82aee14f5ba835f68a061555d24ee2621
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944444"
---
# <a name="scenario-local-hdfs-stuck-in-safe-mode-on-azure-hdinsight-cluster"></a>方案：本地 HDFS 在 Azure HDInsight 群集上的安全模式下停止响应

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

本地 Apache Hadoop 分布式文件系统 (HDFS) 在 HDInsight 群集上的安全模式下停止响应。 收到如下错误消息：

```output
hdiuser@spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
...
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

## <a name="cause"></a>原因

HDInsight 群集已纵向缩减为以下非常少的节点，或者节点数接近 HDFS 复制因子。

## <a name="resolution"></a>解决方法

1. 使用以下命令报告 HDInsight 群集上的 HDFS 状态：

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
    ```

1. 使用以下命令检查 HDInsight 群集上的 HDFS 完整性：

    ```bash
    hdiuser@spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
    ```

1. 如果确定没有块处于缺失、损坏或复制状态，或者确定可以忽略这些块，请运行以下命令，使指定节点脱离安全模式：

    ```bash
    hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
    ```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]