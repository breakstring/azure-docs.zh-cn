---
title: 快速入门：在 Azure Service Fabric 上创建 Spring Boot 应用
description: 在本快速入门中，请使用 Spring Boot 示例应用程序为 Azure Service Fabric 部署 Spring Boot 应用程序。
ms.topic: quickstart
ms.date: 01/29/2019
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 84ce5920af95113801f468e3149421f3b9bd8901
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91529994"
---
# <a name="quickstart-deploy-a-java-spring-boot-app-on-azure-service-fabric"></a>快速入门：在 Azure Service Fabric 上部署 Java Spring Boot 应用

在本快速入门中，你将在 Linux 或 MacOS 上使用熟悉的命令行工具将 Java Spring Boot 应用程序部署到 Azure Service Fabric。 Azure Service Fabric 是一款分布式系统平台，可用于部署和管理微服务和容器。 

## <a name="prerequisites"></a>先决条件

#### <a name="linux"></a>[Linux](#tab/linux)

- [Java 环境](./service-fabric-get-started-linux.md#set-up-java-development)和 [Yeoman](./service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Service Fabric SDK 和 Service Fabric 命令行接口 (CLI)](./service-fabric-get-started-linux.md#installation-methods)
- [Git](https://git-scm.com/downloads)

#### <a name="macos"></a>[MacOS](#tab/macos)

- [Java 环境和 Yeoman](./service-fabric-get-started-mac.md#create-your-application-on-your-mac-by-using-yeoman)
- [Service Fabric SDK 和 Service Fabric 命令行接口 (CLI)](./service-fabric-cli.md#cli-mac)
- [Git](https://git-scm.com/downloads)

--- 

## <a name="download-the-sample"></a>下载示例

在终端窗口中运行以下命令，将 Spring Boot [入门](https://github.com/spring-guides/gs-spring-boot)示例应用克隆到本地计算机。

```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>生成 Spring Boot 应用程序 
在 *gs-spring-boot/complete* 目录中，运行以下命令以生成此应用程序。 

```bash
./gradlew build
``` 

## <a name="package-the-spring-boot-application"></a>打包 Spring Boot 应用程序 
1. 在克隆中的 *gs-spring-boot* 目录中运行 `yo azuresfguest` 命令。 

1. 每次出现提示时，请输入以下详细信息。

    ![Spring Boot Yeoman 条目](./media/service-fabric-quickstart-java-spring-boot/yeoman-entries-spring-boot.png)

1. 在 *SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code* 文件夹中，创建一个名为 *entryPoint.sh* 的文件。在 *entryPoint.sh* 文件中添加以下代码。 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar *spring-boot*.jar
    ```

1. 在 gs-spring-boot/SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/ServiceManifest.xml  文件中添加**终结点**资源

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    *ServiceManifest.xml* 现在如下所示： 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceManifest Name="SpringGettingStartedPkg" Version="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" >

       <ServiceTypes>
          <StatelessServiceType ServiceTypeName="SpringGettingStartedType" UseImplicitHost="true">
       </StatelessServiceType>
       </ServiceTypes>

       <CodePackage Name="code" Version="1.0.0">
          <EntryPoint>
             <ExeHost>
                <Program>entryPoint.sh</Program>
                <Arguments></Arguments>
                <WorkingFolder>CodePackage</WorkingFolder>
             </ExeHost>
          </EntryPoint>
       </CodePackage>
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
     </ServiceManifest>
    ```

现已创建 Spring Boot 入门示例的 Service Fabric 应用程序，可将它部署到 Service Fabric。

## <a name="run-the-application-locally"></a>在本地运行应用程序

1. 通过运行以下命令来启动 Ubuntu 计算机上的本地群集：

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    如果使用 Mac，请从 Docker 映像启动本地群集（此处假定你已按照[先决条件](./service-fabric-get-started-mac.md#create-a-local-container-and-set-up-service-fabric)设置适用于 Mac 的本地群集）。 

    ```bash
    docker run --name sftestcluster -d -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 -p 8080:8080 mysfcluster
    ```

    启动本地群集需要一些时间。 若要确认群集已完全启动，请访问 Service Fabric Explorer（网址：`http://localhost:19080`）。 5 个节点均正常即表示本地群集运行正常。 
    
    ![Service Fabric Explorer 显示运行正常的节点](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-healthy-nodes.png)

1. 打开 *gs-spring-boot/SpringServiceFabric* 文件夹。
1. 运行以下命令连接到本地群集。

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
1. 运行 *install.sh* 脚本。

    ```bash
    ./install.sh
    ```

1. 打开喜欢的 Web 浏览器并访问应用程序（网址：`http://localhost:8080`）。

    ![Spring Boot Service Fabric 示例](./media/service-fabric-quickstart-java-spring-boot/spring-boot-service-fabric-sample.png)

现在可以访问已部署到 Service Fabric 群集的 Spring Boot 应用程序。

有关详细信息，请参阅 Spring 网站上的 Spring Boot [入门](https://spring.io/guides/gs/spring-boot/)示例。

## <a name="scale-applications-and-services-in-a-cluster"></a>在群集中缩放应用程序和服务

可跨群集缩放服务来适应服务负载的变化。 可以通过更改群集中运行的实例数量来缩放服务。 存在多种服务缩放方式，例如，可使用 Service Fabric CLI (sfctl) 脚本/命令。 以下步骤使用 Service Fabric Explorer。

Service Fabric Explorer 在所有 Service Fabric 群集中运行，并能通过浏览器进行访问，访问方法是转到群集的 HTTP 管理端口 (19080)，例如，`http://localhost:19080`。

若要缩放 Web 前端服务，请执行以下操作：

1. 在群集中打开 Service Fabric Explorer - 例如 `http://localhost:19080`。
1. 在树视图中选择“fabric:/SpringServiceFabric/SpringGettingStarted”节点旁边的省略号 ( **...** )，选择“缩放服务”   。

    ![Service Fabric Explorer 缩放服务示例](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-scale-sample.png)

    现在可以选择缩放服务的实例数量。

1. 将数字更改为 **3**，选择“缩放服务”  。

    下面显示了使用命令行缩放服务的另一种方法。

    ```bash
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted' --instance-count 3 --stateless 
    ``` 

1. 在树视图中选择“fabric:/SpringServiceFabric/SpringGettingStarted”  节点，展开分区节点（由 GUID 表示）。

    ![Service Fabric Explorer 缩放服务完成](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-partition-node.png)

    此服务有三个实例。树状视图显示实例在哪些节点上运行。

通过这一简单的管理任务，你已让前端服务用来处理用户负载的资源数量翻了一番。 有必要了解的是，服务无需多个实例便能可靠运行。 如果服务出现故障，Service Fabric 可确保在群集中运行新的服务实例。

## <a name="fail-over-services-in-a-cluster"></a>故障转移群集中的服务

为了演示服务故障转移，可以使用 Service Fabric Explorer 来模拟节点重启。 请确保只有一个服务实例在运行。

1. 在群集中打开 Service Fabric Explorer - 例如 `http://localhost:19080`。
1. 选择运行服务实例的节点旁边的省略号 ( **...** )，并重启节点。

    ![Service Fabric Explorer 重启节点](./media/service-fabric-quickstart-java-spring-boot/service=fabric-explorer-restart=node.png)
1. 服务实例已转移到另一个节点，且应用程序并未关闭。

    ![Service Fabric Explorer 重启节点成功](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-service-moved.png)

## <a name="next-steps"></a>后续步骤

在此快速入门中，读者学习了如何：

* 将 Spring Boot 应用程序部署到 Service Fabric
* 将应用程序部署到本地群集
* 跨多个节点横向扩展应用程序
* 在不影响可用性的情况下执行服务故障转移

若要详细了解如何在 Service Fabric 中使用 Java 应用，请继续学习适用于 Java 应用的教程。

> [!div class="nextstepaction"]
> [部署 Java 应用](./service-fabric-tutorial-create-java-app.md)
