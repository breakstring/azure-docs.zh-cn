---
title: Azure Kinect 传感器 SDK 下载
description: 了解如何在 Windows 和 Linux 中下载和安装 Azure Kinect 传感器 SDK。
author: Brent-A
ms.author: brenta
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: azure, kinect, sdk, 下载更新, 最新, 可用, 安装
ms.openlocfilehash: 2fd14781c42192c713d826729f8fab6c698d6321
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505471"
---
# <a name="azure-kinect-sensor-sdk-download"></a>Azure Kinect 传感器 SDK 下载

本页提供各版本 Azure Kinect 传感器 SDK 的下载链接。 安装程序提供了 Azure Kinect 开发所需的所有文件。

## <a name="azure-kinect-sensor-sdk-contents"></a>Azure Kinect 传感器 SDK 的内容

- 标头和库，用于通过 Azure Kinect DK 生成应用程序。
- 使用 Azure Kinect DK 的应用程序所需的可分发 DLL。
- [Azure Kinect 查看器](azure-kinect-viewer.md)。
- [Azure Kinect 录制器](azure-kinect-recorder.md)。
- [Azure Kinect 固件工具](azure-kinect-firmware-tool.md)。

## <a name="windows-installation-instructions"></a>Windows 安装说明

可在 [此处](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md)找到最新版本和以前版本的 Azure KINECT 传感器 SDK 和固件的安装详细信息。

可在[此处](https://github.com/microsoft/Azure-Kinect-Sensor-SDK)找到源代码。

> [!NOTE]
> 安装 SDK 时，请记住要安装到的路径。 例如，“C:\Program Files\Azure Kinect SDK 1.2”。 你将要在此路径中查找文章中参考的工具。

## <a name="linux-installation-instructions"></a>Linux 安装说明

目前，唯一支持的分发版是 Ubuntu 18.04。 若要请求对其他分发版的支持，请参阅[此页](https://aka.ms/azurekinectfeedback)。

首先，需要遵照[此处](/windows-server/administration/linux-package-repository-for-microsoft-software)的说明，配置 [Microsoft 的包存储库](https://packages.microsoft.com/)。

现在，可以安装所需的包。 `k4a-tools` 包中包含 [Azure Kinect 查看器](azure-kinect-viewer.md)、[Azure Kinect 录制器](record-sensor-streams-file.md)和 [Azure Kinect 固件工具](azure-kinect-firmware-tool.md)。 若要安装该包，请运行

 `sudo apt install k4a-tools`

 `libk4a<major>.<minor>-dev` 包中包含针对 `libk4a` 生成的头文件以及 CMake 文件。
`libk4a<major>.<minor>` 包中包含共享对象，运行依赖于 `libk4a` 的可执行文件时需要这些对象。

 基本教程需要 `libk4a<major>.<minor>-dev` 包。 若要安装该包，请运行

 `sudo apt install libk4a1.1-dev`

如果该命令成功，则表示 SDK 可供使用。

## <a name="change-log-and-older-versions"></a>更改日志和早期版本

可在[此处](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/CHANGELOG.md)找到 Azure Kinect 传感器 SDK 的更改日志。

如果需要早期版本的 Azure Kinect 传感器 SDK，可在[此处](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md)找到。

## <a name="next-steps"></a>后续步骤

[设置 Azure Kinect DK](set-up-azure-kinect-dk.md)