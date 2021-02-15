---
title: 使用 SSH 隧道访问 Azure HDInsight
description: 了解如何使用 SSH 隧道来安全浏览基于 Linux 的 HDInsight 节点上托管的 Web 资源。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: 5bbc770fa6ae5ac69b2aa939f9d2c70bb01f5403
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945289"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-uis"></a>使用 SSH 隧道访问 Apache Ambari Web UI、JobHistory、NameNode、Apache Oozie 和其他 UI

使用 HDInsight 群集可以通过 Internet 访问 Apache Ambari Web UI。 某些功能需要 SSH 隧道。 例如，如果没有 SSH 隧道，则无法通过 Internet 访问 Apache Oozie Web UI。

## <a name="why-use-an-ssh-tunnel"></a>为何使用 SSH 隧道？

Ambari 中的多个菜单只能通过 SSH 隧道工作。 这些菜单依赖于其他节点类型（例如辅助角色节点）上运行的网站和服务。

以下 Web UI 需要 SSH 隧道：

* JobHistory
* NameNode
* 线程堆栈
* Oozie Web UI
* HBase Master 和日志 UI

通过脚本操作安装并公开 Web 服务的服务将需要 SSH 隧道。 通过脚本操作安装的 Hue 需要 SSH 隧道才能访问 Web UI。

> [!IMPORTANT]  
> 如果可以通过虚拟网络直接访问 HDInsight，则不需要使用 SSH 隧道。 有关通过虚拟网络直接访问 HDInsight 的示例，请参阅[将 HDInsight 连接到本地网络](connect-on-premises-network.md)一文。

## <a name="what-is-an-ssh-tunnel"></a>什么是 SSH 隧道

[安全外壳 (SSH) 隧道](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling)将本地计算机上的端口连接到 HDInsight 上的头节点。 发送到本地端口的流量通过 SSH 连接路由到头节点。 请求将得到解析，就如同它源自头节点一样。 然后，通过与工作站建立的隧道将响应路由回去。

## <a name="prerequisites"></a>先决条件

* SSH 客户端。 有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。

* 可配置为使用 SOCKS5 代理的 Web 浏览器。

    > [!WARNING]  
    > 内置于 Windows Internet 设置中的 SOCKS 代理支持不支持 SOCKS5，不适用于此文档中的步骤。 以下浏览器依赖于 Windows 代理设置，当前不适用于此文档中的步骤：
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome 也依赖于 Windows 代理设置。 但是，可以安装支持 SOCKS5 的扩展。 我们建议使用 [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp)。

## <a name="create-a-tunnel-using-the-ssh-command"></a><a name="usessh"></a>使用 SSH 命令创建隧道

使用以下 `ssh` 命令创建 SSH 隧道。 将 `sshuser` 替换为 HDInsight 群集的 SSH 用户，将 `CLUSTERNAME` 替换为 HDInsight 群集的名称：

```cmd
ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
```

此命令创建一个通过 SSH 将流量路由到群集本地端口 9876 的连接。 选项包括：

|选项 |说明 |
|---|---|
|D 9876|通过隧道路由流量的本地端口。|
|C|压缩所有数据，因为 Web 流量大多为文本。|
|2|强制 SSH 仅尝试协议版本 2。|
|q|静默模式。|
|T|禁用 pseudo-tty 分配，因为仅会转发端口。|
|n|防止读取 STDIN，因为仅会转发端口。|
|N|不执行远程命令，因为仅会转发端口。|
|f|在后台运行。|

在命令完成后，发送到本地计算机上的端口 9876 的流量将路由到群集头节点。

## <a name="create-a-tunnel-using-putty"></a><a name="useputty"></a>使用 PuTTY 创建隧道

[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty) 是适用于 Windows 的图形 SSH 客户端。 如果你不熟悉 PuTTY，请参阅 [PuTTY 文档](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html)。 执行以下步骤可使用 PuTTY 创建 SSH 隧道：

### <a name="create-or-load-a-session"></a>创建或加载会话

1. 打开 PuTTY，并确保在左侧菜单中选择“会话”。 如果已保存会话，请从 " **保存的会话** " 列表中选择会话名称，然后选择 " **加载**"。

1. 如果你没有已保存的会话，请输入你的连接信息：

    |属性 |Value |
    |---|---|
    |主机名（或 IP 地址）|HDInsight 群集的 SSH 地址。 例如， **mycluster-ssh.azurehdinsight.net**。|
    |端口|22|
    |连接类型|SSH|

1. 选择“保存”

    ![HDInsight 创建 putty 会话](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png)

1. 在对话框左侧的“类别”部分中，依次展开“连接”和“SSH”，并选择“隧道”。

1. 提供以下有关“用于控制 SSH 端口转发的选项”窗体的信息：

    |属性 |Value |
    |---|---|
    |Source Port|客户端上要转发的端口。 例如 **9876**。|
    |目标|HDInsight 群集的 SSH 地址。 例如， **mycluster-ssh.azurehdinsight.net**。|
    |动态|启用动态 SOCKS 代理路由。|

    ![PuTTY 配置隧道选项](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-putty-tunnel.png)

1. 选择“添加”以添加设置，然后选择“打开”以打开 SSH 连接。

1. 出现提示时，登录到服务器。

## <a name="use-the-tunnel-from-your-browser"></a>从浏览器使用隧道

> [!IMPORTANT]  
> 本部分中的步骤使用 Mozilla FireFox 浏览器，因为它在所有平台中提供相同的代理设置。 对于其他新式浏览器（如 Google Chrome），可能需要 FoxyProxy 等扩展才能使用隧道。

1. 将浏览器配置为使用 **localhost**，并将创建隧道时使用的端口配置为 **SOCKS v5** 代理。 Firefox 中的设置如下所示。 如果使用的端口不是 9876，请将端口更改为所用的端口：

    ![firefox 浏览器代理设置](./media/hdinsight-linux-ambari-ssh-tunnel/firefox-proxy-settings.png)

   > [!NOTE]  
   > 通过选择“远程 DNS”，可使用 HDInsight 群集解析域名系统 (DNS) 请求。 此设置使用群集的头节点解析 DNS。

2. 通过访问 [https://www.whatismyip.com/](https://www.whatismyip.com/) 等网站验证隧道是否正常工作。 返回的 IP 应是 Microsoft Azure 数据中心使用的 IP。

## <a name="verify-with-ambari-web-ui"></a>Ambari Web UI 访问验证

建立群集后，请通过以下步骤验证是否可以从 Ambari Web 访问服务 Web UI：

1. 在浏览器中转到 `http://headnodehost:8080`。 `headnodehost` 地址通过隧道发送到群集，并解析为运行 Ambari 的头节点。 出现提示时，请输入群集的管理员用户名 (admin) 和密码。 Ambari Web UI 可能会再次出现提示。 如果出现，请重新输入信息。

   > [!NOTE]  
   > 如果使用 `http://headnodehost:8080` 地址连接到群集，则将通过隧道进行连接。 通信是使用 SSH 隧道而不是 HTTPS 保护的。 若要使用 HTTPS 通过 Internet 进行连接，请使用 `https://clustername.azurehdinsight.net`，其中 `clustername` 是群集的名称。

2. 在 Ambari Web UI 中，请选择页面左侧列表中的“HDFS”。

    ![已选择 Apache Ambari hdfs 服务](./media/hdinsight-linux-ambari-ssh-tunnel/hdfs-service-selected.png)

3. 显示 HDFS 服务信息时，请选择“快速链接”。 会显示群集头节点列表。 选择其中一个头节点，并选择“NameNode UI”。

    ![已展开“快速链接”菜单的截图](./media/hdinsight-linux-ambari-ssh-tunnel/namenode-drop-down-menu.png)

    > [!NOTE]  
    > 选择“快速链接”时，可能会看到等待指示器。 如果 Internet 连接速度慢，则可能会出现此情况。 请等待一两分钟，让系统从服务器接收数据，然后再次尝试列出节点列表。
    >
    > “快速链接”菜单中的某些项可能在屏幕右侧截断。 如果是这样，请使用鼠标展开菜单，然后使用向右键向右滚动屏幕，查看菜单的余下内容。

4. 随后将显示类似于下图的页面：

    ![Hadoop NameNode UI 的图像](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-namenode-ui.png)

    > [!NOTE]  
    > 请注意此页的 URL；它应类似于 `http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster`。 此 URI 使用节点的内部完全限定域名 (FQDN)，只能在使用 SSH 隧道的情况下访问它。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何创建和使用 SSH 隧道，请参阅以下文档，了解使用 Ambari 的其他方法：

* [使用 Apache Ambari 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)
