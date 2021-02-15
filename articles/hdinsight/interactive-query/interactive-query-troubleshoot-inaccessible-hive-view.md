---
title: Apache Hive 连接到 Apache Zookeeper-Azure HDInsight
description: 由于 Azure HDInsight 中的 Apache Zookeeper 问题，因此无法访问 Apache Hive 视图
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 42fb9f5389cbc31e772dc9cf36b6a975c5e18d3c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939298"
---
# <a name="scenario-apache-hive-fails-to-establish-a-connection-to-apache-zookeeper-in-azure-hdinsight"></a>方案：Apache Hive 无法与 Azure HDInsight 中的 Apache Zookeeper 建立连接

本文介绍在 Azure HDInsight 群集中使用交互式查询组件时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

Hive 视图不可访问，`/var/log/hive` 中的日志显示类似于以下内容的错误：

```
ERROR [Curator-Framework-0]: curator.ConnectionState (ConnectionState.java:checkTimeouts(200)) - Connection timed out for connection string (zk0-cluster.cloud.wbmi.com:2181,zk1-cluster.cloud.wbmi.com:2181,zk2-cluster.cloud.wbmi.com:2181) and timeout (15000) / elapsed (21852)
```

## <a name="cause"></a>原因

Hive 可能无法建立与 Zookeeper 的连接，这会阻止 Hive 视图启动。

## <a name="resolution"></a>解决方法

1. 检查 Zookeeper 服务是否正常运行。

1. 检查 Zookeeper 服务是否具有 Hive Server2 的 ZNode 条目。 该值会缺失或不正确。

    ```
    /usr/hdp/2.6.2.25-1/zookeeper/bin/zkCli.sh -server zk1-wbwdhs
    [zk: zk0-cluster(CONNECTED) 0] ls /hiveserver2-hive2
    ```

1. 若要重新建立连接，请重新启动 Zookeeper 节点，并重新启动 HiveServer2。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]