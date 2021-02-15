---
title: IoT 即插即用库和 Sdk
description: 有关可用于开发启用 IoT 即插即用解决方案的设备和服务库的信息。
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 3ff01455c56dd259ea64878f912eb3f2431547e7
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2020
ms.locfileid: "95495246"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>用于 IoT 的 Microsoft Sdk 即插即用

IoT 即插即用库和 Sdk 使开发人员能够在多个平台上使用各种编程语言来构建 IoT 解决方案。 下表提供了一些链接，这些链接指向可帮助你入门的示例和快速入门：

## <a name="device-sdks"></a>设备 SDK

| 语言 | 程序包 | 代码存储库 | 示例 | 快速入门 | 参考 |
|---|---|---|---|---|---|
| C-设备 | [vcpkg 1.3。9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [示例](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [连接到 IoT 中心](quickstart-connect-device.md) | [引用](/azure/iot-hub/iot-c-sdk-ref/) |
| .NET-设备 | [NuGet 1.31。0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [示例](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples) | [连接到 IoT 中心](quickstart-connect-device.md) | [引用](/dotnet/api/microsoft.azure.devices.client?preserve-view=true&view=azure-dotnet) |
| Java-设备 | [Maven 1.26。0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [示例](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [连接到 IoT 中心](quickstart-connect-device.md) | [引用](/java/api/com.microsoft.azure.sdk.iot.device?preserve-view=true&view=azure-java-stable) |
| Python-设备 | [pip 2.3。0](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [示例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [连接到 IoT 中心](quickstart-connect-device.md) | [引用](/python/api/azure-iot-device/azure.iot.device?preserve-view=true&view=azure-python) |
| 节点-设备 | [npm 1.17。2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [示例](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [连接到 IoT 中心](quickstart-connect-device.md) | [引用](/javascript/api/azure-iot-device/?preserve-view=true&view=azure-node-latest) |
| Embedded C-设备 | 空值 | [GitHub](https://github.com/Azure/azure-sdk-for-c/)| [示例](howto-use-embedded-c.md#samples) | [如何使用嵌入的 C](howto-use-embedded-c.md) | 不适用

## <a name="service-sdks"></a>服务 SDK

| 平台  | 程序包 | 代码存储库 | 示例 | 快速入门 | 参考 |
|---|---|---|---|---|---|
| .NET-IoT 中心服务 | [NuGet 1.27。1](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [示例](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples) | 不适用 | [引用](/dotnet/api/microsoft.azure.devices?preserve-view=true&view=azure-dotnet) |
| Java-IoT 中心服务 | [Maven 1.26。0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [示例](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/pnp-service-sample) | 不适用 | [引用](/java/api/com.microsoft.azure.sdk.iot.service?preserve-view=true&view=azure-java-stable) |
| 节点-IoT 中心服务 | [npm 1.13。0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [示例](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | 不适用 | [引用](/javascript/api/azure-iothub/?preserve-view=true&view=azure-node-latest) |
| Python-数字孪生服务 | [pip 2.2。3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [示例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [与 IoT 中心数字孪生 API 交互](quickstart-service.md) | 不适用 |
| Node-数字孪生服务 | [npm 1.13。0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [示例](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [与 IoT 中心数字孪生 API 交互](quickstart-service.md) | 不适用 |

## <a name="next-steps"></a>后续步骤

若要尝试 Sdk 和库，请参阅  [开发人员指南](concepts-developer-guide-device.md) 和 [设备快速入门](quickstart-connect-device.md) 和 [服务快速入门](quickstart-service.md)。