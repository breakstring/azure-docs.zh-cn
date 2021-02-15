---
title: 带 Azure DevOps 的 CI/CD 管道入门-Azure IoT Edge |Microsoft Docs
description: 可以通过 Azure DevOps Starter 轻松地完成 Azure 入门。 使用它可以快速启动所选的 Azure IoT Edge 应用。
author: kgremban
ms.author: kgremban
ms.date: 08/25/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 97dc0fe5a3720a41dd63583c222762d832d636ea
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436991"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-starter"></a>使用 Azure DevOps Starter 为 IoT Edge 创建 CI/CD 管道

使用 DevOps Projects 为 IoT Edge 应用程序配置持续集成 (CI) 和持续交付 (CD)。 DevOps Starter 可以简化 Azure Pipelines 中生成和发布管道的初始配置。

如果没有可用的 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

DevOps Starter 在 Azure DevOps 中创建 CI/CD 管道。 可以创建新的 Azure DevOps 组织，或使用现有的组织。 DevOps 入门版还将在所选的 Azure 订阅中创建 Azure 资源。

1. 登录到 [Microsoft Azure 门户](https://portal.azure.com)。

1. 在左窗格中，选择 " **创建资源**"，然后搜索 **DevOps Starter**。  

1. 选择“创建”。

## <a name="create-a-new-application-pipeline"></a>新建应用程序管道

1. 可以用 [C#](tutorial-csharp-module.md)、[Node.js](tutorial-node-module.md)、[Python](tutorial-python-module.md)、[C](tutorial-c-module.md) 和 [Java](tutorial-java-module.md) 编写 Azure IoT Edge 模块。 选择用于启动新应用程序的首选语言：.NET、Node.js、Python、C 或 Java    。 选择“下一步”继续。

   ![选择用于创建新应用程序的语言](./media/how-to-devops-starter/select-language.png)

2. 选择“简单 IoT”作为应用程序框架，然后选择“下一步” 。

   ![选择简单 IoT 框架](media/how-to-devops-starter/select-iot.png)

3. 选择 IoT Edge 作为部署应用程序的 Azure 服务，然后选择“下一步” 。

   ![选择 IoT Edge 服务](media/how-to-devops-starter/select-iot-edge.png)

4. 创建新的免费 Azure DevOps 组织，或选择现有的组织。

   1. 为项目提供名称。

   2. 选择 Azure DevOps 组织。 如果没有现有组织，请选择“其他设置”新建一个组织。

   3. 选择 Azure 订阅。

   4. 使用由项目名称生成的 IoT 中心名称，或者提供自己的名称。

   5. 接受默认位置，或选择接近你的位置。

   6. 选择 " **其他设置** "，为 DevOps Starter 创建的 Azure 资源进行配置。

   7. 选择“完成”结束项目创建。

   ![名称并创建项目](media/how-to-devops-starter/create-project.png)

几分钟后，DevOps Starter 仪表板会显示在 Azure 门户中。 选择项目名称以查看进度。 可能需要刷新页面。 首先会在 Azure DevOps 组织的存储库中设置示例 IoT Edge 应用程序，然后执行生成并将应用程序部署到 IoT Edge 设备。 可以通过此仪表板查看代码存储库、CI/CD 管道以及 Azure 中的应用程序。

   ![查看 Azure 门户中的项目](./media/how-to-devops-starter/portal.png)

## <a name="commit-code-changes-and-execute-cicd"></a>提交代码更改并执行 CI/CD

DevOps Starter 在 Azure Repos 中为项目创建了 Git 存储库。 在本部分中，查看存储库并对应用程序进行代码更改。

1. 若要导航到为项目创建的存储库，请在项目仪表板的菜单中选择“存储库”。 此链接将打开新项目的浏览器选项卡和 Azure DevOps 存储库。

   ![查看 Azure Repos 中生成的存储库](./media/how-to-devops-starter/view-repositories.png)

   > [!NOTE]
   > 以下步骤演示如何使用 Web 浏览器进行代码更改。 如果要在本地克隆存储库，请从窗口的右上角选择“克隆”。 使用提供的 URL 在 Visual Studio Code 或首选开发工具中克隆 Git 存储库。

2. 基于在创建过程中选择的应用程序语言，存储库已包含名为 FilterModule 的模块的代码。 打开 modules/FilterModule/module.json 文件。

   ![打开 Azure Repos 中的 module.json 文件](./media/how-to-devops-starter/open-module-json.png)

3. 请注意，此文件在 version 参数中使用 [Azure DevOps 生成变量](/azure/devops/pipelines/build/variables#build-variables)。 此配置可确保在每次运行新生成时都创建模块的新版本。

## <a name="examine-the-cicd-pipeline"></a>检查 CI/CD 管道

在前面的部分中，Azure DevOps Starter 自动为你的 IoT Edge 应用程序配置了完整的 CI/CD 管道。 现在，根据需要探索和自定义管道。 按照以下步骤，自行熟悉 Azure DevOps 的生成和发布管道。

1. 若要查看 DevOps 项目中的生成管道，请在项目仪表板的菜单中选择“生成管道”。 此链接会打开一个浏览器标签页，并打开新项目的 Azure DevOps 生成管道。

   ![查看 Azure Pipelines 中的生成管道](./media/how-to-devops-starter/view-build-pipelines.png)

2. 打开自动生成的生成管道，然后选择右上方的 " **编辑** "。

    ![编辑生成管道](media/how-to-devops-starter/click-edit-button.png)

3. 在打开的面板中，可以检查生成管道运行时出现的任务。 该生成管道会执行各种任务，例如，从 Git 存储库提取源、生成 IoT Edge 模块映像、将 IoT Edge 模块推送到容器注册表以及发布用于部署的输出。 若要详细了解 Azure DevOps 中的 Azure IoT Edge 任务，请参阅[配置 Azure 管道以进行持续集成](how-to-continuous-integration-continuous-deployment-classic.md#create-a-build-pipeline-for-continuous-integration)。

4. 选择生成管道顶部的“管道”标头，打开管道详细信息。 将生成管道的名称更改为更具说明性的名称。

   ![编辑管道详细信息](./media/how-to-devops-starter/edit-build-pipeline.png)

5. 选择“保存和队列”，然后选择“保存” 。 对于注释，它是可选的。

6. 从生成管道菜单中选择“触发器”。 DevOps Starter 将自动创建一个 CI 触发器，每次向存储库提交内容都会启动新的生成。  可以选择在 CI 过程中包括或排除分库。

7. 选择“保留期”。 单击链接可将你重定向到保留策略所在的项目设置。 可以根据方案指定策略，以保留或删除特定数目的生成。

8. 选择“历史记录”。 “历史记录”面板包含最近针对生成所做的更改的审核线索。 Azure Pipelines 会跟踪对生成管道所做的任何更改，并允许进行版本比较。

9. 完成对生成管道的探索之后，请导航到相应的发布管道。 选择“管道”下的“发布”，然后选择“编辑”查看管道详细信息  。

    ![查看发布管道](media/how-to-devops-starter/release-pipeline.png)

10. 在“项目”下选择“删除” 。 此项目监视的源是上述步骤中所检查的生成管道的输出。

11. 在“删除”图标的旁边，请选择闪电状的“持续部署触发器” 。 此发布管道已启用触发器，每次有新的生成项目可用时，此触发器就会运行部署。 （可选）可以禁用此触发器，这样就需要手动执行部署。  

12. 在发布管道的菜单中，请选择“任务”，然后从下拉列表选择“开发”阶段 。 DevOps Projects 创建了发布阶段，该阶段创建 IoT 中心、在该中心创建 IoT Edge 设备、从生成管道部署示例模块，并将虚拟机预配为作为 IoT Edge 设备运行。 若要了解有关 CD 的 Azure IoT Edge 任务的详细信息，请参阅[配置 Azure 管道以进行持续部署](how-to-continuous-integration-continuous-deployment-classic.md#create-a-release-pipeline-for-continuous-deployment)。

    ![查看持续部署任务](media/how-to-devops-starter/choose-release.png)

13. 在右侧，选择“查看发布”。 此视图显示发布历史记录。

14. 选择发布的名称，查看关于它的更多信息。

## <a name="clean-up-resources"></a>清理资源

不再需要创建的 Azure 应用服务和其他相关资源时，可将其删除。 请使用 DevOps 入门版仪表板上的“删除”功能。

## <a name="next-steps"></a>后续步骤

* 在[向 Azure IoT Edge 进行持续集成和持续部署](how-to-continuous-integration-continuous-deployment.md)中详细了解 Azure DevOps 中的 Azure IoT Edge 任务
* 在[了解单设备 IoT Edge 部署或大规模 IoT Edge 部署](module-deployment-monitoring.md)中了解 IoT Edge 部署
* 阅读[大规模地部署和监视 IoT Edge 模块](how-to-deploy-at-scale.md)，详细了解创建、更新或删除部署的步骤。
