---
title: 快速入门 - 通过 Azure IoT 中心设备流与采用 C# 的设备应用进行通信
description: 在本快速入门中，我们将运行两个使用通过 IoT 中心建立的设备流通信的示例 C# 应用程序。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 3eb65db27e5b96f4b12973154bc860a2ab3df020
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624602"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>快速入门：通过 IoT 中心设备流（预览版）使用 C# 与设备应用程序进行通信

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT 中心目前支持设备流作为[预览版功能](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

服务和设备应用程序可以使用 [IoT 中心设备流](./iot-hub-device-streams-overview.md)以安全且防火墙友好的方式进行通信。 本快速入门涉及两个 C# 应用程序，它们利用设备流来回发送数据（回显）。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* 目前仅以下区域中创建的 IoT 中心支持设备流预览：
  * 美国中部
  * 美国中部 EUAP
  * 北欧
  * 东南亚

* 本快速入门中运行的两个示例应用程序是使用 C# 编写的。 开发计算机上需要有 .NET Core SDK 2.1.0 或更高版本。

    [从 .NET 下载适用于多个平台的 .NET Core SDK](https://www.microsoft.com/net/download/all)。

    使用以下命令验证开发计算机上 C# 的当前版本：

    ```
    dotnet --version
    ```

* [下载 Azure IoT C# 示例](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)，并提取 ZIP 存档。 需要在设备端和服务端执行此操作。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>注册设备

必须先将设备注册到 IoT 中心，然后该设备才能进行连接。 在本部分，我们将使用 Azure Cloud Shell 来注册模拟设备。

1. 若要创建设备标识，请在 Cloud Shell 中运行以下命令：

   > [!NOTE]
   > * 请将 *YourIoTHubName* 占位符替换为你为 IoT 中心选择的名称。
   > * 对于正在注册的设备的名称，建议使用 *MyDevice*，如下所示。 如果为设备选择其他名称，请在本文中从头至尾使用该名称，并在运行示例应用程序之前在其中更新设备名称。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. 若要获取刚刚注册的设备的 *设备连接字符串*，请在 Cloud Shell 中运行以下命令：

   > [!NOTE]
   > 请将 *YourIoTHubName* 占位符替换为你为 IoT 中心选择的名称。

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    请记下返回的设备连接字符串，以便稍后在此快速入门中使用。 如以下示例所示：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. 此外，需要使用 IoT 中心的服务连接字符串才能让服务端应用程序连接到 IoT 中心并建立设备流。 以下命令检索 IoT 中心的此值：

   > [!NOTE]
   > 请将 *YourIoTHubName* 占位符替换为你为 IoT 中心选择的名称。

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    请记下返回的服务连接字符串，以便稍后在此快速入门中使用。 如以下示例所示：

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>通过设备流在设备与服务之间通信

在本部分中，你将运行设备端应用程序和服务器端应用程序并在两者之间进行通信。

### <a name="run-the-service-side-application"></a>运行服务端应用程序

在本地终端窗口中，导航到解压缩项目文件夹中的 `iot-hub/Quickstarts/device-streams-echo/service` 目录。 请保留以下信息：

| 参数名称 | 参数值 |
|----------------|-----------------|
| `ServiceConnectionString` | IoT 中心的服务连接字符串。 |
| `MyDevice` | 前面创建的设备标识符。 |

使用以下命令编译并运行代码：

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{ServiceConnectionString}" "MyDevice"

# In Windows
dotnet run {ServiceConnectionString} MyDevice
```
应用程序将等待设备应用程序变为可用。

> [!NOTE]
> 如果设备端应用程序未及时响应，则会发生超时。

### <a name="run-the-device-side-application"></a>运行设备端应用程序

在另一个本地终端窗口中，导航到解压缩项目文件夹中的 `iot-hub/Quickstarts/device-streams-echo/device` 目录。 请保留以下信息：

| 参数名称 | 参数值 |
|----------------|-----------------|
| `DeviceConnectionString` | IoT 中心的设备连接字符串。 |

使用以下命令编译并运行代码：

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{DeviceConnectionString}"

# In Windows
dotnet run {DeviceConnectionString}
```

最后一个步骤结束时，服务端应用程序会向设备发起流。 建立流后，应用程序会通过该流将一个字符串缓冲区发送到服务。 在此示例中，服务端应用程序直接将相同的数据回显到设备，表明已成功地在两个应用程序之间建立双向通信。

设备端的控制台输出：

![设备端的控制台输出](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

服务端的控制台输出：

![服务端的控制台输出](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

通过该流发送的流量将通过 IoT 中心以隧道方式进行传输，而不是直接发送。 [设备流优势](./iot-hub-device-streams-overview.md#benefits)中详细介绍了所提供的优势。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们设置了一个 IoT 中心、注册了一个设备、在设备和服务端的 C# 应用程序之间建立了一个设备流，并通过该流在应用程序之间来回发送数据。

若要详细了解设备流，请参阅：

> [!div class="nextstepaction"]
> [设备流概述](./iot-hub-device-streams-overview.md)
