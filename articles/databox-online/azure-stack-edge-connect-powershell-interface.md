---
title: 通过 Windows PowerShell 界面连接和管理 Microsoft Azure Stack Edge Pro 设备 |Microsoft Docs
description: 描述如何通过 Windows PowerShell 界面连接到 Azure Stack Edge Pro 并进行管理。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/30/2020
ms.author: alkohli
ms.openlocfilehash: c9c6247f021b7af4cfdd899ffd4b6bd178f2256c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96345587"
---
# <a name="manage-an-azure-stack-edge-pro-fpga-device-via-windows-powershell"></a>通过 Windows PowerShell 管理 Azure Stack Edge Pro FPGA 设备

Azure Stack Edge Pro 解决方案允许处理数据，并通过网络将数据发送到 Azure。 本文介绍 Azure Stack Edge Pro 设备的一些配置和管理任务。 你可以使用 Azure 门户、本地 web UI 或 Windows PowerShell 界面来管理你的设备。

本文重点介绍使用 PowerShell 接口执行的任务。 

本文包括以下过程：

- 连接到 PowerShell 接口
- 创建支持包
- 上传证书
- 重置设备
- 查看设备信息
- 获取计算日志
- 计算模块的监视和故障排除

## <a name="connect-to-the-powershell-interface"></a>连接到 PowerShell 接口

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>创建支持包

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>上传证书

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

还可上传 IoT Edge 证书，在 IoT Edge 设备和可能与其进行连接的下游设备之间启用安全连接。 需要安装以下三个文件 (*pem* 格式) ：

- 根 CA 证书或所有者 CA
- 设备 CA 证书
- 设备私钥 

下面的示例演示如何使用此 cmdlet 安装 IoT Edge 证书：

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-private-key.pem" -Credential "username"
```
运行此 cmdlet 时，系统将提示你提供网络共享的密码。

有关证书的详细信息，请参阅 [Azure IoT Edge 证书](../iot-edge/iot-edge-certs.md) "或 [" 在网关上安装证书 "](../iot-edge/how-to-create-transparent-gateway.md)。

## <a name="view-device-information"></a>查看设备信息
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>重置设备

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>获取计算日志

如果在设备上配置了计算角色，则还可以通过 PowerShell 界面获取计算日志。

1. [连接到 PowerShell 接口](#connect-to-the-powershell-interface)。
2. 使用 `Get-AzureDataBoxEdgeComputeRoleLogs` 获取设备的计算日志。

    以下示例显示了此 cmdlet 的用法：

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    下面是用于 cmdlet 的参数的说明：
    - `Path`：提供要在其中创建计算日志包的共享的网络路径。
    - `Credential`：提供网络共享的用户名。 运行此 cmdlet 时，需要提供共享密码。
    - `FullLogCollection`：此参数可确保日志包将包含所有计算日志。 默认情况下，日志包仅包含一小部分的日志。

## <a name="monitor-and-troubleshoot-compute-modules"></a>计算模块的监视和故障排除

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/azure-stack-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>退出远程会话

若要退出远程 PowerShell 会话，请关闭 PowerShell 窗口。

## <a name="next-steps"></a>后续步骤

- 在 Azure 门户中部署 [Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md)。