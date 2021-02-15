---
title: 快速入门：将遥测数据发送到 Azure IoT (Node.js)
description: 本快速入门将运行两个示例 Node.js 应用程序，从而向 IoT 中心发送模拟遥测数据，并读取 IoT 中心的遥测数据，在云中进行处理。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom:
- mvc
- seo-javascript-september2019
- mqtt
- 'Role: Cloud Development'
- devx-track-js
- devx-track-azurecli
ms.date: 06/21/2019
ms.openlocfilehash: 7aa95e2117dc3bb2e837e62ef42e3a770f2266d5
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842118"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-nodejs"></a>快速入门：将遥测数据从设备发送到 IoT 中心并使用后端应用程序读取该数据 (Node.js)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

 本快速入门会将模拟设备应用程序的遥测数据通过 Azure IoT 中心发送到后端应用程序进行处理。 IoT 中心是一项 Azure 服务，用于将大量遥测数据从 IoT 设备引入云中进行存储或处理。 本快速入门使用两个预先编写的 Node.js 应用程序：一个用于发送遥测数据，一个用于读取中心的遥测数据。 运行这两个应用程序前，请先创建 IoT 中心并在中心注册设备。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* [Node.js 10+](https://nodejs.org)。 如果使用的是 Azure Cloud Shell，请勿更新已安装的 Node.js 版本。 Azure Cloud Shell 已安装最新版本的 Node.js。

    可以使用以下命令验证开发计算机上 Node.js 当前的版本：

    ```cmd/sh
    node --version
    ```

* [一个示例 Node.js 项目](https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip)。

* 端口 8883 在防火墙中处于打开状态。 本快速入门中的设备示例使用 MQTT 协议，该协议通过端口 8883 进行通信。 在某些公司和教育网络环境中，此端口可能被阻止。 有关解决此问题的更多信息和方法，请参阅[连接到 IoT 中心(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>注册设备

必须先将设备注册到 IoT 中心，然后该设备才能进行连接。 在本快速入门中，将使用 Azure Cloud Shell 来注册模拟设备。

1. 在 Azure Cloud Shell 中运行以下命令，以创建设备标识。

   **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

   **MyNodeDevice**：这是所注册的设备的名称。 建议使用 **MyNodeDevice**，如图所示。 如果为设备选择不同名称，则可能还需要在本文中从头至尾使用该名称，并在运行示例应用程序之前在其中更新设备名称。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyNodeDevice
    ```

1. 在 Azure Cloud Shell 中运行以下命令，以获取刚注册设备的 _设备连接字符串_：

   **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyNodeDevice --output table
    ```

    记下如下所示的设备连接字符串：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    稍后会在快速入门中用到此值。

1. 还需要使用与事件中心兼容的终结点、与事件中心兼容的路径和 IoT 中心的服务主密钥，确保后端应用程序能连接到 IoT 中心并检索消息  。 以下命令可检索 IoT 中心的这些值：

   **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    记下这三个值，稍后会在快速入门中用到这些值。

## <a name="send-simulated-telemetry"></a>发送模拟遥测数据

模拟设备应用程序会连接到 IoT 中心上特定于设备的终结点，并发送模拟的温度和湿度遥测数据。

1. 打开本地终端窗口，导航到示例 Node.js 项目的根文件夹。 然后导航到 **iot-hub\Quickstarts\simulated-device** 文件夹。

1. 在所选文本编辑器中打开 SimulatedDevice.js 文件。

    将 `connectionString` 变量的值替换为之前记下的设备连接字符串。 然后将更改保存到 **SimulatedDevice.js**。

1. 在本地终端窗口中，运行以下命令以安装所需的库，并运行模拟设备应用程序：

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    以下屏幕截图显示了模拟设备应用程序将遥测数据发送到 IoT 中心后的输出：

    ![运行模拟设备](media/quickstart-send-telemetry-node/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>从中心读取遥测数据

后端应用程序会连接到 IoT 中心上的服务端“事件”终结点。 应用程序会接收模拟设备发送的设备到云的消息。 IoT 中心后端应用程序通常在云中运行，接收和处理设备到云的消息。

1. 打开另一个本地终端窗口，导航到示例 Node.js 项目的根文件夹。 然后导航到 iot-hub\Quickstarts\read-d2c-messages 文件夹。

1. 在所选文本编辑器中打开 ReadDeviceToCloudMessages.js 文件。 更新以下变量并保存对文件所做的更改。

    | 变量 | 值 |
    | -------- | ----------- |
    | `eventHubsCompatibleEndpoint` | 将变量的值替换为之前记下的与事件中心兼容的终结点。 |
    | `eventHubsCompatiblePath`     | 将变量的值替换为之前记下的与事件中心兼容的路径。 |
    | `iotHubSasKey`                | 将变量的值替换为之前记下的服务主密钥。 |

1. 在本地终端窗口中运行以下命令，以安装所需的库并运行后端应用程序：

    ```cmd/sh
    npm install
    node ReadDeviceToCloudMessages.js
    ```

    以下屏幕截图显示了后端应用程序接收模拟设备发送到 IoT 中心的遥测数据后的输出：

    ![运行后端应用程序](media/quickstart-send-telemetry-node/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

本快速入门设置了 IoT 中心、注册了设备、使用 Node.js 应用程序发送了模拟遥测数据到中心，并使用简单的后端应用程序读取中心的遥测数据。

若要了解如何从后端应用程序控制模拟设备，请继续阅读下一快速入门教程。

> [!div class="nextstepaction"]
> [快速入门：控制连接到 IoT 中心的设备](quickstart-control-device-node.md)
