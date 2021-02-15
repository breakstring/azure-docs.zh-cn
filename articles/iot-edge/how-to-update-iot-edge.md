---
title: 更新设备上的 IoT Edge 版本 - Azure IoT Edge | Microsoft Docs
description: 如何将 IoT Edge 设备更新为运行最新版本的安全守护程序和 IoT Edge 运行时
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/20/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0adcbf49ff2128fdbe623121838058c5ed89dce2
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378020"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>更新 IoT Edge 安全守护程序和运行时

当 IoT Edge 服务发布新版本时，可能需要更新 IoT Edge 设备，使其获得最新功能并改善安全性。 本文提供有关在新版本推出时如何更新 IoT Edge 设备的信息。

若要转移到较新的版本，需要更新 IoT Edge 设备的两个组件。 第一个组件是安全守护程序，它在设备上运行并在设备启动时启动运行时模块。 目前，只能从设备本身更新安全守护程序。 第二个组件是由 IoT Edge 中心和 IoT Edge 代理模块组成的运行时。 根据部署的构造方式，可以从设备或者远程更新运行时。

若要查找最新版本的 Azure IoT Edge，请参阅 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

## <a name="update-the-security-daemon"></a>更新安全守护程序

IoT Edge 安全守护程序是一个本机组件，需要使用 IoT Edge 设备上的包管理器进行更新。

使用命令 `iotedge version` 检查设备上运行的安全守护程序的版本。

# <a name="linux"></a>[Linux](#tab/linux)

在 Linux x64 设备上，请使用 apt-get 或相应的包管理器将安全守护程序更新到最新版本。

从 Microsoft 获取最新的存储库配置：

* **Ubuntu Server 18.04**：

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspberry Pi OS Stretch**：

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

复制生成的列表。

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

安装 Microsoft GPG 公钥。

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

更新 apt。

   ```bash
   sudo apt-get update
   ```

查看哪些 IoT Edge 版本可用。

   ```bash
   apt list -a iotedge
   ```

如果要更新到最新版本的安全守护程序，请使用以下命令，该命令还会将 **libiothsm-std** 更新到最新版本：

   ```bash
   sudo apt-get install iotedge
   ```

如果要更新到特定版本的安全守护程序，请从 apt 列表输出中指定该版本。 每当更新 **iotedge** 时，它都会自动尝试将 **libiothsm-std** 包更新到其最新版本，这可能会导致依赖项冲突。 如果不打算使用最新版本，请确保两个包都是针对同一版本。 例如，以下命令安装 1.0.9 发行版的特定版本：

   ```bash
   sudo apt-get install iotedge=1.0.9-1 libiothsm-std=1.0.9-1
   ```

如果要安装的版本无法通过 apt-get 获取，则可使用 curl 将 [IoT Edge 发行版](https://github.com/Azure/azure-iotedge/releases)存储库中的任何版本作为目标。 不管要安装哪个版本，请找到设备的相应 **libiothsm-std** 和 **iotedge** 文件。 右键单击每个文件对应的链接，并复制链接地址。 使用链接地址安装这些组件的特定版本：

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

# <a name="windows"></a>[Windows](#tab/windows)

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

借助 IoT Edge for Linux on Windows，IoT Edge 可以在 Windows 设备上托管的 Linux 虚拟机中运行。 此虚拟机已预安装 IoT Edge，并通过 Microsoft 更新进行管理，以使组件保持最新。 目前没有可用的更新。

::: moniker-end

借助适用于 Windows 的 IoT Edge，IoT Edge 可直接在 Windows 设备上运行。 有关使用 PowerShell 脚本的更新说明，请参阅[安装和管理适用于 Windows 的 Azure IoT Edge](how-to-install-iot-edge-windows-on-windows.md)。

---

## <a name="update-the-runtime-containers"></a>更新运行时容器

更新 IoT Edge 代理和 IoT Edge 中心容器的方式取决于在部署中使用的是滚动标记（如 1.0）还是特定标记（如 1.0.7）。

使用 `iotedge logs edgeAgent` 或 `iotedge logs edgeHub` 命令检查设备上目前安装的 IoT Edge 代理和 IoT Edge 中心模块的版本。

  ![在日志中查找容器版本](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>了解 IoT Edge 标记

IoT Edge 代理和 IoT Edge 中心映像使用与之关联的 IoT Edge 版本进行标记。 可通过两种不同的方法对运行时映像使用标记：

* **滚动更新标记** - 仅使用版本号的前两个值来获取匹配这些数字的最新映像。 例如，每当有新版本指向最新的 1.0.x 版时，就更新 1.0。 如果 IoT Edge 设备的容器运行时重新提取映像，则运行时模块会更新到最新版本。 建议在开发时使用此方法。 Azure 门户中的部署默认使用滚动更新标记。

* **特定标记** - 使用版本号的所有三个值，以显式设置映像版本。 例如，1.0.7 在其初始版本发布后不会更改。 准备好更新时，可以在部署清单中声明新的版本号。 建议在生产环境中使用此方法。

### <a name="update-a-rolling-tag-image"></a>更新滚动更新标记映像

如果在部署中使用滚动更新标记（例如 mcr.microsoft.com/azureiotedge-hub:**1.0**），则需要在设备上强制实施容器运行时，以提取最新版本的映像。

从 IoT Edge 设备中删除本地版本的映像。 在 Windows 计算机上，卸载安全守护程序时也会删除运行时映像，因此不需再次执行此步骤。

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

可能需要使用 `-f`（强制）标志来删除映像。

IoT Edge 服务将提取最新版本的运行时映像，并自动在设备上将其重新启动。

### <a name="update-a-specific-tag-image"></a>更新特定标记映像

如果在部署中使用特定标记（例如 mcr.microsoft.com/azureiotedge-hub:**1.0.8**），则只需更新部署清单中的标记，并将更改应用到设备即可。

1. 在 Azure 门户的 IoT 中心，选择 IoT Edge 设备，然后选择“设置模块”  。

1. 在“IoT Edge 模块”部分中，选择“运行时设置”   。

   ![配置运行时设置](./media/how-to-update-iot-edge/configure-runtime.png)

1. 在”运行时设置”中，将“Edge 中心”的“映像”值更新为所需的版本    。 暂时不要选择“保存”。

   ![更新 Edge 中心的映像版本](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. 折叠“Edge 中心”设置，或向下滚动，将“Edge 代理”的“映像”值更新为所需的相同版本    。

   ![更新 Edge 中心的代理版本](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. 选择“保存”  。

1. 选择“查看 + 创建”，检查部署，然后选择“创建”   。

## <a name="update-to-a-release-candidate-version"></a>更新到候选发布版本

Azure IoT Edge 定期发布新版 IoT Edge 服务。 在发布每个稳定版本之前，会有一个或多个候选发布 (RC) 版本。 RC 版本包括发布版的所有计划内功能，但仍需进行测试和验证。 若要提前测试某项新功能，可以安装 RC 版本，然后通过 GitHub 提供反馈。

候选发布版本遵循相同的版本编号约定，但会在末尾追加 **-rc** 和一个增量数字。 可以在与稳定版本相同的 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)列表中查看候选发布版本。 例如，可以找到 **1.0.9-rc5** 和 **1.0.9-rc6** 这两个在 **1.0.9** 之前发布的候选发布版本。 还可以看到 RC 版本带有 **预发行版** 标签。

IoT Edge 代理和中心模块包含根据相同约定标记的 RC 版本。 例如 **mcr.microsoft.com/azureiotedge-hub:1.0.9-rc6**。

充当预览版的候选发布版本不会包括在常规安装程序所针对的最新版本中。 需要手动将要测试的 RC 版资产设为目标。 大多数情况下，安装或更新到 RC 版本的过程与将目标设为任何其他特定版本的 IoT Edge 相同。

使用本文中的部分了解如何将 IoT Edge 设备更新到特定版本的安全守护程序或运行时模块。

如果要安装 IoT Edge 而不是升级现有安装，请使用[脱机或特定版本安装](how-to-install-iot-edge.md#offline-or-specific-version-installation-optional)中的步骤。

## <a name="next-steps"></a>后续步骤

查看最新的 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

持续关注[物联网博客](https://azure.microsoft.com/blog/topics/internet-of-things/)中的最新更新和通告