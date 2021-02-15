---
title: Azure Kinect 传感器 SDK 系统要求
description: 了解 Windows 和 Linux 上 Azure Kinect 传感器 SDK 的系统需求。
author: tesych
ms.author: tesych
ms.custom:
- CI 115266
- CSSTroubleshooting
manager: dcscontentpm
ms.prod: kinect-dk
ms.date: 03/12/2020
ms.topic: article
keywords: azure, kinect, 系统要求, CPU, GPU, USB, 设置, 安装, 最低, 要求
ms.openlocfilehash: 5cf313114b62532ee3f2b3d7a5142f79218954c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "79366675"
---
# <a name="azure-kinect-sensor-sdk-system-requirements"></a>Azure Kinect 传感器 SDK 系统要求

本文档详细说明安装传感器 SDK 以及成功部署 Azure Kinect DK 所要满足的系统要求。

## <a name="supported-operating-systems-and-architectures"></a>支持的操作系统和体系结构

- Windows 10 2018 年4月 (版本1803，操作系统版本 17134) 版本 (x64) 或更高版本
- Linux Ubuntu 18.04 (x64) ，其中包含使用 OpenGLv 4.4 或更高版本的 GPU 驱动程序

传感器 SDK 适用于本机 C/C++ Windows 应用程序的 Windows API (Win32)。 该 SDK 目前不适用于 UWP 应用程序。 S 模式的 Windows 10 不支持 Azure Kinect DK。

## <a name="development-environment-requirements"></a>开发环境要求

若要为传感器 SDK 开发供稿，请访问 [GitHub](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK)。

## <a name="minimum-host-pc-hardware-requirements"></a>主机电脑的最低硬件要求

电脑主机的硬件要求取决于在主机电脑上执行的应用程序/算法/传感器帧速率/分辨率。 对 Windows 建议的最低传感器 SDK 配置为：

- 第七个 Gen Intel &reg; CoreTM I3 处理器 (双核 2.4 GHz，含 HD620 GPU 或更快) 
- 4 GB 内存
- 专用 USB3 端口
- 支持 OpenGL 4.4 或 DirectX 11.0 的图形驱动程序

根据具体的用例，低端或早期的 CPU 可能也适用。

此外，性能根据所用的 Windows/Linux 操作系统和图形驱动程序而异。

## <a name="body-tracking-host-pc-hardware-requirements"></a>人体跟踪主机电脑的硬件要求

人体跟踪电脑主机的要求比一般电脑主机的要求更高。 对 Windows 建议的最低人体跟踪 SDK 配置为：

- 第七个 Gen Intel &reg; CoreTM I5 处理器 (四核 2.4 GHz 或更快的) 
- 4 GB 内存
- NVIDIA GEFORCE GTX 1070 或更佳
- 专用 USB3 端口

建议的最低配置假定 K4A_DEPTH_MODE_NFOV_UNBINNED 深度模式以 30fps 的速度跟踪 5 个人。 根据具体的用例，低端或早期的 CPU 和 NVIDIA GPU 可能也适用。

## <a name="usb3"></a>USB3

USB 主控制器存在一些已知的兼容性问题。 可以在[故障排除页](troubleshooting.md#usb3-host-controller-compatibility)上找到详细信息

## <a name="next-steps"></a>后续步骤

- [Azure Kinect DK 概述](about-azure-kinect-dk.md)

- [设置 Azure Kinect DK](set-up-azure-kinect-dk.md)

- [设置 Azure Kinect 人体跟踪](body-sdk-setup.md)
