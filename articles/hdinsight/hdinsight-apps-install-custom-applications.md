---
title: 在 Azure HDInsight 上安装自定义 Apache Hadoop 应用程序
description: 了解如何在 Azure HDInsight 中为 Apache Hadoop 群集安装 HDInsight 应用程序。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 9591551b7d5657803e770bd71b47acb141b40fb6
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942694"
---
# <a name="install-custom-apache-hadoop-applications-on-azure-hdinsight"></a>在 Azure HDInsight 上安装自定义 Apache Hadoop 应用程序

本文介绍如何在 Azure HDInsight 上安装尚未发布到 Azure 门户的 [Apache Hadoop](https://hadoop.apache.org/) 应用程序。 您将在本文中安装的应用程序是 [色调](https://gethue.com/)。

HDInsight 应用程序是用户可以在 HDInsight 群集上安装的应用程序。  这些应用程序可能是 Microsoft、独立软件供应商 (ISV) 或自己开发的。  

## <a name="prerequisites"></a>先决条件

如果想要在现有的 HDInsight 群集上安装 HDInsight 应用程序，必须有一个 HDInsight 群集。 若要创建群集，请参阅 [创建群集](hadoop/apache-hadoop-linux-tutorial-get-started.md)。 也可以在创建 HDInsight 群集时安装 HDInsight 应用程序。

## <a name="install-hdinsight-applications"></a>Install HDInsight applications

可以在创建群集时安装 HDInsight 应用程序，也可以将它安装到现有的 HDInsight 群集。 若要了解如何定义 Azure 资源管理器模板，请参阅 [MSDN：安装 HDInsight 应用程序](/rest/api/hdinsight/hdinsight-application)。

部署此应用程序 (Hue) 时所需的文件：

* [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json)：用于安装 HDInsight 应用程序的资源管理器模板。 有关如何开发自己的资源管理器模板的信息，请参阅 [MSDN：安装 HDInsight 应用程序](/rest/api/hdinsight/hdinsight-application)。
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh)：资源管理器模板为配置边缘节点而调用的脚本操作。
* [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz)：从 hui-install_v0.sh 调用的 hue 二进制文件。
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz)：从 hui-install_v0.sh 调用的 hue 二进制文件。
* [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz)：从 hui-install_v0.sh 调用的示例 Web 应用程序 (Tomcat)。

### <a name="to-install-hue-to-an-existing-hdinsight-cluster"></a>将 Hue 安装到现有的 HDInsight 群集

1. 选择以下映像以登录到 Azure，然后在 Azure 门户中打开 Resource Manager 模板。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    资源管理器模板位于 [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue)。  若要了解如何编写此资源管理器模板，请参阅 [MSDN：安装 HDInsight 应用程序](/rest/api/hdinsight/hdinsight-application)。

1. 从下拉列表中选择包含你的群集的现有 **资源组**。 必须使用与群集相同的资源组。

1. 输入要安装应用程序的群集的名称。 此群集必须是现有的群集。

1. 选择与“我同意上述条款和条件”相对应的复选框。

1. 选择“购买”。

可以从固定到门户仪表板的磁贴和门户通知查看安装状态（单击门户顶部的铃铛图标）。  安装此应用程序大约需要 10 分钟。

### <a name="to-install-hue-while-creating-a-cluster"></a>在创建群集的同时安装 Hue

1. 选择以下映像以登录到 Azure，然后在 Azure 门户中打开 Resource Manager 模板。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    资源管理器模板位于 [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json)。  若要了解如何编写此资源管理器模板，请参阅 [MSDN：安装 HDInsight 应用程序](/rest/api/hdinsight/hdinsight-application)。

2. 根据说明来创建群集并安装 Hue。 有关创建 HDInsight 群集的详细信息，请参阅 [在 HDInsight 中创建基于 Linux 的 Hadoop 群集](hdinsight-hadoop-provision-linux-clusters.md)。

### <a name="other-installation-methods"></a>其他安装方法

除了 Azure 门户，也可以使用 [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) 和 [Azure CLI](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-azure-cli) 来调用 Resource Manager 模板。

## <a name="validate-the-installation"></a>验证安装

可以在 Azure 门户中检查应用程序状态，以验证应用程序安装。 此外，还可以验证所有 HTTP 终结点是否按预期方式出现，以及网页（如果有）。

对于 **Hue**，可以使用以下步骤：

### <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 选择已在其中安装应用程序的群集。
1. 在“设置”菜单中，选择“应用程序”。 
1. 选择列表中的“hue”以查看属性。  
1. 选择“网页”链接以验证网站。

### <a name="azure-cli"></a>Azure CLI

将 `CLUSTERNAME` 和 `RESOURCEGROUP` 替换为相关值，然后输入以下命令：

* 列出 HDInsight 群集的所有应用程序。

    ```azurecli
    az hdinsight application list --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

* 检索指定应用程序的属性。

    ```azurecli
    az hdinsight application show --name hue --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

## <a name="troubleshoot-the-installation"></a>排查安装问题

可以通过门户通知查看应用程序安装状态（单击门户顶部的铃铛图标）。

如果应用程序安装失败，可以从三个位置查看错误消息和调试信息：

* HDInsight 应用程序：常规错误信息。

    从门户打开群集，并在“设置”中选择“应用程序”：

    ![hdinsight 应用程序安装错误](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-error.png)

* HDInsight 脚本操作：如果 HDInsight 应用程序的错误消息指出脚本操作失败，“脚本操作”窗格会显示有关脚本失败的详细信息。

    从“设置”中选择“脚本操作”。 脚本操作历史记录中显示了错误消息

    ![hdinsight 应用程序脚本操作错误](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-script-action-error.png)

* Apache Ambari Web UI：如果安装脚本是失败的原因，请使用 Ambari Web UI 来检查有关安装脚本的完整日志。

    有关详细信息，请参阅[排查脚本操作问题](./troubleshoot-script-action.md)。

## <a name="remove-hdinsight-applications"></a>删除 HDInsight 应用程序

### <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 选择已在其中安装应用程序的群集。
1. 在“设置”菜单中，选择“应用程序”。 
1. 右键单击想要删除的应用程序，并选择“删除”。
1. 请选择“是”以确认。

### <a name="azure-cli"></a>Azure CLI

将 `NAME`、`CLUSTERNAME` 和 `RESOURCEGROUP` 替换为相关值，然后输入以下命令：

```azurecli
az hdinsight application delete --name NAME --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
```

## <a name="next-steps"></a>后续步骤

* [MSDN：安装 HDInsight 应用程序](/rest/api/hdinsight/hdinsight-application)：了解如何开发用于部署 HDInsight 应用程序的资源管理器模板。
* [安装 HDInsight 应用程序](hdinsight-apps-install-applications.md)：了解如何在群集上安装 HDInsight 应用程序。
* [发布 HDInsight 应用程序](hdinsight-apps-publish-applications.md)：了解如何将自定义 HDInsight 应用程序发布到 Azure 市场。
* [使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)：了解如何使用脚本操作安装其他应用程序。
* [使用资源管理器模板在 HDInsight 中创建基于 Linux 的 Apache Hadoop 群集](hdinsight-hadoop-create-linux-clusters-arm-templates.md)：了解如何调用资源管理器模板创建 HDInsight 群集。
* [在 HDInsight 中使用空边缘节点](hdinsight-apps-use-edge-node.md)：了解如何使用空边缘节点访问 HDInsight 群集、测试 HDInsight 应用程序以及托管 HDInsight 应用程序。
