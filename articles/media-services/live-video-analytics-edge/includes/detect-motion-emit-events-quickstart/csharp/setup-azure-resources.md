---
ms.openlocfilehash: 40d2f957ce115b43a1dcc138b86e05ec9cc47384
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060554"
---
本教程需要以下 Azure 资源：

* IoT 中心
* 存储帐户
* Azure 媒体服务帐户
* Azure 中的 Linux VM，已安装 [IoT Edge 运行时](../../../../../iot-edge/how-to-install-iot-edge.md)

在本快速入门中，我们建议你使用[实时视频分析资源设置脚本](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)在 Azure 订阅中部署所需资源。 为此，请执行下列步骤：

1. 打开 [Azure Cloud Shell](https://shell.azure.com)。
1. 如果你是第一次使用 Cloud Shell，系统将提示你选择一个订阅以创建存储帐户和 Microsoft Azure 文件存储共享。 选择“创建存储”，创建用于存储 Cloud Shell 会话信息的存储帐户。 此存储帐户不同于脚本将要创建的与 Azure 媒体服务帐户配合使用的帐户。
1. 在 Cloud Shell 窗口左侧的下拉菜单中，选择“Bash”作为环境。

    ![环境选择器](../../../media/quickstarts/env-selector.png)
1. 运行以下命令。

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    在脚本成功完成后，你应可在订阅中看到所有所需资源。
1. 脚本完成后，选择大括号即可公开文件夹结构。 你可在 ~/clouddrive/lva-sample 目录中看到几个文件。 与本快速入门相关的文件如下：

     * * **~/clouddrive/lva-sample/edge-deployment/.env** - 此文件包含 Visual Studio Code 用来将模块部署到边缘设备的属性。
     * **~/clouddrive/lva-sample/appsetting.json** - Visual Studio Code 使用此文件来运行示例代码。
     
    在下一节的 Visual Studio Code 中设置开发环境时，你需要这些文件。 现在需要将它们复制到本地文件中。
    
    ![应用设置](../../../media/quickstarts/clouddrive.png)

> [!TIP]
> 如果在创建 Azure 资源时遇到问题，请查看[故障排除指南](../../../troubleshoot-how-to.md#common-error-resolutions)来解决一些常见问题。_*