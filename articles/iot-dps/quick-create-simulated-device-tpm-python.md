---
title: 快速入门 - 使用 Python 将模拟的 TPM 设备预配到 Azure IoT 中心
description: 快速入门 - 使用适用于 IoT 中心设备预配服务 (DPS) 的 Java 设备 SDK 创建和预配模拟的 TPM 设备。 本快速入门使用单独注册。
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 23a2ca3cae92f65786a3d2fb978b1727e18857e8
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966630"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>快速入门：使用适用于 IoT 中心设备预配服务的 Python 设备 SDK 创建和预配模拟的 TPM 设备

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

在本快速入门中，我们在 Windows 计算机上创建一台模拟 IoT 设备。 此模拟设备包含用作硬件安全模块 (HSM) 的 TPM 模拟器。 我们使用设备预配服务 (DPS) 的单个注册，通过设备示例 Python 代码将此模拟设备连接到 IoT 中心。

## <a name="prerequisites"></a>先决条件

- 熟悉[预配](about-iot-dps.md#provisioning-process)概念。
- 完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)。
- 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Visual Studio 2015+](https://visualstudio.microsoft.com/vs/)，结合使用通过 C++ 进行的桌面开发。
- [CMake 生成系统](https://cmake.org/download/)。
- [Git](https://git-scm.com/download/)。

> [!IMPORTANT]
> 本文仅适用于已弃用的 V1 Python SDK。 V2 中尚不提供用于 IoT 中心设备预配服务的设备和服务客户端。 该团队目前正在努力使 V2 具有功能奇偶一致性。

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>准备环境 

1. 确保你已安装了 [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 或更高版本，并为你的 Visual Studio 安装启用“使用 C++ 的桌面开发”。

1. 下载并安装 [CMake 生成系统](https://cmake.org/download/)。

1. 确保在计算机上安装 `git` 并将其添加到可供命令窗口访问的环境变量。 请参阅[软件自由保护组织提供的 Git 客户端工具](https://git-scm.com/download/)，了解要安装的最新版 `git` 工具，其中包括  Git Bash，这是一个命令行应用，可以用来与本地 Git 存储库交互。 

1. 打开命令提示符或 Git Bash。 为设备模拟代码示例克隆 GitHub 存储库：
    
    ```cmd/sh
    git clone --single-branch --branch v1-deprecated https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

1. 在该 GitHub 存储库的本地副本中创建一个用于 CMake 生成过程的文件夹。 

    ```cmd/sh
    cd azure-iot-sdk-python/c
    mkdir cmake
    cd cmake
    ```

1. 代码示例使用 Windows TPM 模拟器。 运行以下命令，启用 SAS 令牌身份验证。 该命令还会为模拟设备生成 Visual Studio 解决方案。

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

1. 在单独的命令提示符处，导航到 TPM 模拟器文件夹，然后运行将要成为模拟设备的 [HSM](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) 的 [TPM](/windows/device-security/tpm/trusted-platform-module-overview) 模拟器。 单击“允许访问”。  该模拟器通过套接字在端口 2321 和 2322 上进行侦听。 请勿关闭此命令窗口；本快速入门指南自始至终都需让该模拟器保持运行状态。 

    ```cmd/sh
    .\azure-iot-sdk-python\c\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

    ![TPM 模拟器](./media/python-quick-create-simulated-device/tpm-simulator.png)


## <a name="create-a-device-enrollment-entry"></a>创建设备注册项

Azure IoT 设备预配服务支持两类注册：

- [注册组](concepts-service.md#enrollment-group)：用于注册多个相关设备。
- [单个注册](concepts-service.md#individual-enrollment)：用于注册单个设备。

本文演示单个注册。

1. 打开在  cmake 文件夹中生成的名为 `azure_iot_sdks.sln` 的解决方案，将其内置到 Visual Studio 中。

1. 右键单击“tpm_device_provision”项目，  然后选择“设为启动项目”。  运行解决方案。 输出窗口会显示进行设备注册所需的“认可密钥”和“注册 ID”   。 记下这些值。 

    ![TPM 安装程序](./media/python-quick-create-simulated-device/tpm-setup.png)

1. 登录到 Azure 门户，选择左侧菜单上的“所有资源”按钮，打开设备预配服务  。

1. 在“设备预配服务”菜单中，选择“管理注册”  。 选择“个人注册”选项卡，然后选择顶部的“添加个人注册”按钮   。 

1. 在“添加注册”面板中，输入以下信息  ：
   - 选择“TPM”  作为标识证明 *机制*。
   - 使用你之前记下的值输入 TPM 设备的“注册 ID”和“认可密钥”   。
   - 选择与预配服务链接的 IoT 中心。
   - （可选）可以提供以下信息：
       - 输入唯一“设备 ID”  。 为设备命名时，请确保避免使用敏感数据。 如果选择不提供此项，则系统将改用注册 ID 来标识设备。
       - 使用设备所需的初始配置更新“初始设备孪生状态”  。
   - 完成后，按“保存”按钮  。 

     ![在门户边栏选项卡中输入设备注册信息](./media/python-quick-create-simulated-device/enterdevice-enrollment.png)  

   成功注册以后，设备的“注册 ID”会显示在“单个注册”选项卡下的列表中。   


## <a name="simulate-the-device"></a>模拟设备

1. 下载并安装 [Python 2.x 或 3.x](https://www.python.org/downloads/)。 请确保根据安装程序的要求，使用 32 位或 64 位安装。 在安装过程中出现提示时，请确保将 Python 添加到特定于平台的环境变量中。
    - 如果使用 Windows OS，则请安装 [Visual C++ 可再发行组件包](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)，以便使用 Python 中的本机 DLL。

1. 按照[这些说明](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md)生成 Python 包。

   > [!NOTE]
   > 如果运行 `build_client.cmd`，请确保使用 `--use-tpm-simulator` 标志。
   > 
   > [!NOTE]
   > 如果使用 `pip`，请确保也安装 `azure-iot-provisioning-device-client` 包。 请注意，发布的 PIP 包使用真实的 TPM 而非模拟器。 若要使用模拟器，需使用 `--use-tpm-simulator` 标志从源进行编译。

1. 导航到示例文件夹。

    ```cmd/sh
    cd azure-iot-sdk-python/provisioning_device_client/samples
    ```

1. 使用 Python IDE，编辑名为 **provisioning\_device\_client\_sample.py** 的 Python 脚本。 将 *GLOBAL\_PROV\_URI* 和 *ID\_SCOPE* 变量修改先前记下的值。 此外，请确保 *SECURITY\_DEVICE\_TYPE* 设置为 `ProvisioningSecurityDeviceType.TPM`

    ```python
    GLOBAL_PROV_URI = "{globalServiceEndpoint}"
    ID_SCOPE = "{idScope}"
    SECURITY_DEVICE_TYPE = ProvisioningSecurityDeviceType.TPM
    PROTOCOL = ProvisioningTransportProvider.HTTP
    ```

    ![服务信息](./media/python-quick-create-simulated-device/extract-dps-endpoints.png)

1. 运行该示例。 

    ```cmd/sh
    python provisioning_device_client_sample.py
    ```

1. 请注意相关消息，这些消息模拟设备启动后连接到设备预配服务以获取 IoT 中心信息的情况。 

    ![注册成功](./media/python-quick-create-simulated-device/registration-success.png)

1. 将模拟设备成功预配到与预配服务链接的 IoT 中心以后，设备 ID 会显示在该中心的“IoT 设备”边栏选项卡上  。

    ![设备注册到 IoT 中心](./media/python-quick-create-simulated-device/hubregistration.png) 

    如果从设备的注册项中的默认值更改了“初始设备孪生状态”  ，则它会从中心拉取所需的孪生状态，并执行相应的操作。 有关详细信息，请参阅[了解并在 IoT 中心内使用设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>清理资源

如果打算继续使用和探索设备客户端示例，请勿清理在本快速入门中创建的资源。 如果不打算继续学习，请按以下步骤删除本快速入门中创建的所有资源。

1. 关闭计算机上的设备客户端示例输出窗口。
1. 关闭计算机上的 TPM 模拟器窗口。
1. 在 Azure 门户的左侧菜单中选择“所有资源”，然后选择设备预配服务  。 打开服务的“管理注册”边栏选项卡，然后选择“单个注册”选项卡   。选中在本快速入门中注册的设备的“注册 ID”旁边的复选框，然后按窗格顶部的“删除”按钮   。 
1. 在 Azure 门户的左侧菜单中选择“所有资源”，然后选择 IoT 中心  。 打开中心的“IoT 设备”边栏选项卡，选中在本快速入门中注册的设备的“设备 ID”旁边的复选框，然后按窗格顶部的“删除”按钮    。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已在计算机上创建 TPM 模拟设备，并已使用 IoT 中心设备预配服务将其预配到 IoT 中心。 若要了解如何以编程方式注册 TPM 设备，请继续阅读快速入门中关于 TPM 设备的编程注册内容。 

> [!div class="nextstepaction"]
> [Azure 快速入门 - 将 TPM 设备注册到 Azure IoT 中心设备预配服务](quick-enroll-device-tpm-python.md)