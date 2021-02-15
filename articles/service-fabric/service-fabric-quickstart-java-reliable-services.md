---
title: 快速入门：在 Azure Service Fabric 上创建 Java 应用
description: 在本快速入门中，请使用 Service Fabric Reliable Services 示例应用程序创建用于 Azure 的 Java 应用程序。
ms.topic: quickstart
ms.date: 01/29/2019
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 2f1e5f8f73f74d4b427e574b9e6a75aaf84a4211
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91529841"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>快速入门：将 Java 应用部署到 Linux 上的 Azure Service Fabric

在本快速入门中，你将在 Linux 开发人员计算机上使用 Eclipse IDE 将 Java 应用程序部署到 Azure Service Fabric。 完成后，将生成一个带 Java Web 前端的投票应用程序，用于将投票结果保存到群集的有状态后端服务中。

Azure Service Fabric 是一款分布式系统平台，可用于部署和管理微服务和容器。

## <a name="prerequisites"></a>先决条件

- [Java 环境](./service-fabric-get-started-linux.md#set-up-java-development) 和 [Yeoman](./service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Eclipse Neon (4.6)+](https://www.eclipse.org/downloads/packages/) 和[用于 Service Fabric 的 Eclipse 插件](./service-fabric-get-started-linux.md#install-the-eclipse-plug-in-optional)
- [Service Fabric SDK 和命令行接口(CLI)](./service-fabric-get-started-linux.md#installation-methods)
- [Git](https://git-scm.com/downloads)

## <a name="download-the-sample"></a>下载示例

在命令窗口中，运行以下命令，将示例应用程序存储库克隆到本地计算机。

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>在本地运行应用程序

1. 通过运行以下命令来启动本地群集：

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    启动本地群集需要一些时间。 若要确认群集已完全启动，请访问 Service Fabric Explorer（网址：`http://localhost:19080`）。 5 个节点均正常即表示本地群集运行正常。

    ![Azure Service Fabric Explorer 显示运行正常的节点](./media/service-fabric-quickstart-java/service-fabric-explorer-healthy-nodes.png)

2. 打开 Eclipse。
3. 选择“文件”   > “导入”   > “Gradle”   > “现有 Gradle 项目”  ，并按照向导进行操作。
4. 选择“目录”  ，然后在从 GitHub 克隆的 **service-fabric-java-quickstart** 文件夹中选择 **Voting** 目录。 选择“完成”  。

    ![将 Gradle 项目导入 Eclipse](./media/service-fabric-quickstart-java/eclipse-import-gradle-project.png)

5. Eclipse 的包资源管理器中现拥有 `Voting` 项目。
6. 右键单击该项目并选择“Service Fabric”下拉列表中的“发布应用程序”   。 选择“PublishProfiles/Local.json”作为目标配置文件，然后选择“发布”   。

    ![Azure Service Fabric 发布本地 JSON](./media/service-fabric-quickstart-java/service-fabric-publish-local-json.png)

7. 打开喜欢的 Web 浏览器并访问应用程序（网址：`http://localhost:8080`）。

    ![Azure Service Fabric 本地主机](./media/service-fabric-quickstart-java/service-fabric-local-host.png)

现在可以添加一组投票选项，并开始进行投票。 此应用程序可以运行，并将所有数据存储到 Service Fabric 群集中，而无需单独提供数据库。

![Azure Service Fabric 投票示例](./media/service-fabric-quickstart-java/service-fabric-voting-sample.png)

## <a name="scale-applications-and-services-in-a-cluster"></a>在群集中缩放应用程序和服务

可跨群集缩放服务来适应服务负载的变化。 可以通过更改群集中运行的实例数量来缩放服务。 有许多方法可以缩放服务。 例如，可以通过 Service Fabric CLI (`sfctl`) 使用脚本或命令。 以下步骤使用 Service Fabric Explorer。

Service Fabric Explorer 在所有 Service Fabric 群集中运行，并且可以通过浏览到群集的 HTTP 管理端口 (19080) 从浏览器进行访问。 例如，`http://localhost:19080` 。

若要缩放 Web 前端服务，请执行以下操作：

1. 在群集中打开 Service Fabric Explorer。 例如，`https://localhost:19080` 。
2. 在树视图中选择“fabric:/Voting/VotingWeb”节点旁边的省略号 ( **...** )，再选择“缩放服务”   。

    ![在 Azure Service Fabric 中缩放服务](./media/service-fabric-quickstart-java/service-fabric-scale-service.png)

    现在可以缩放 Web 前端服务的实例数量。

3. 将数字更改为 **2**，选择“缩放服务”  。
4. 在树视图中选择“fabric:/Voting/VotingWeb”  节点，再展开分区节点（由 GUID 表示）。

    ![在 Azure Service Fabric 中缩放服务](./media/service-fabric-quickstart-java/service-fabric-explorer-service-scaled.png)

    现在可以看到，服务有两个实例。在树视图中可以查看实例的运行节点。

通过这一简单的管理任务，你已让前端服务用来处理用户负载的资源数量翻了一番。 有必要了解的是，服务无需多个实例便能可靠运行。 如果服务出现故障，Service Fabric 可确保在群集中运行新的服务实例。

## <a name="next-steps"></a>后续步骤

在此快速入门中，读者学习了如何：

* 使用 Eclipse 处理 Service Fabric Java 应用程序
* 将 Java 应用程序部署到本地群集
* 跨多个节点横向扩展应用程序

若要详细了解如何在 Service Fabric 中使用 Java 应用，请继续学习适用于 Java 应用的教程。

> [!div class="nextstepaction"]
> [部署 Java 应用](./service-fabric-tutorial-create-java-app.md)
