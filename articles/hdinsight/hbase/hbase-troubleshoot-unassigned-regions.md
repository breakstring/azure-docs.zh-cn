---
title: Azure HDInsight 中的区域服务器问题
description: Azure HDInsight 中的区域服务器问题
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: 968a0c6e1717245171bf84821a58cad4e440046e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936610"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Azure HDInsight 中的区域服务器问题

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。

## <a name="scenario-unassigned-regions"></a>方案：未分配的区域

### <a name="issue"></a>问题

运行 `hbase hbck` 命令时看到如下所示的错误消息：

```
multiple regions being unassigned or holes in the chain of regions
```

在 Apache HBase Master UI 中，可以看到所有区域服务器中非均衡区域的数目。 然后，可以运行 `hbase hbck` 命令查看区域链中的漏洞。

### <a name="cause"></a>原因

问题可能是区域脱机造成的。

### <a name="resolution"></a>解决方法

修复分配。 执行以下步骤，让未分配区域重新回到正常状态：

1. 使用 SSH 登录到 HDInsight HBase 群集。

1. 运行 `hbase zkcli` 命令以连接到 ZooKeeper shell。

1. 运行 `rmr /hbase/regions-in-transition` 或 `rmr /hbase-unsecure/regions-in-transition` 命令。

1. 使用 `exit` 命令退出 Zookeeper shell。

1. 打开 Apache Ambari UI，并重启 Active HBase Master 服务。

1. 再次运行 `hbase hbck` 命令（不带任何其他选项）。 检查输出并确保正在分配所有区域。

---

## <a name="scenario-dead-region-servers"></a>方案：区域服务器死机

### <a name="issue"></a>问题

区域服务器无法启动。

### <a name="cause"></a>原因

存在多个 WAL 拆分目录。

1. 获取当前 WAL 列表：`hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`。

1. 检查 `wals.out` 文件。 如果拆分目录（以 *-splitting 开头）过多，可能会导致区域服务器出现故障。

### <a name="resolution"></a>解决方法

1. 从 Ambari 门户停止 HBase。

1. 执行 `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` 获取 WAL 的最新列表。

1. 将 *-splitting 目录移到临时文件夹 `splitWAL`，并删除 *-splitting 目录。

1. 执行 `hbase zkcli` 命令连接 Zookeeper shell。

1. 执行 `rmr /hbase-unsecure/splitWAL`。

1. 重启 HBase 服务。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 联系 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 它可以将 Azure 社区成员连接到适当的资源，为他们提供解答、支持和专家建议。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”  ，或打开“帮助 + 支持”  中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 Microsoft Azure 订阅中带有对订阅管理和计费支持的访问权限，技术支持通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供。