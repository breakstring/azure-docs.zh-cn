---
title: Microsoft Azure Stack 边缘迷你 R 系统要求 |Microsoft Docs
description: 了解 Azure Stack Edge 迷你 R 的软件和网络要求
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: alkohli
ms.openlocfilehash: 6836777c761fa2cce482cb000b6c86919e6b5613
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834062"
---
# <a name="azure-stack-edge-mini-r-system-requirements"></a>Azure Stack 边缘迷你 R 系统要求

本文介绍 Microsoft Azure Stack Edge 迷你 R 解决方案的重要系统要求以及连接到 Azure Stack Edge 迷你 R 的客户端。建议在部署 Azure Stack Edge 迷你 R 之前仔细查看信息。在部署和后续操作过程中，你可以参考此信息。

Azure Stack Edge 迷你 R 的系统要求包括：

- **主机的软件要求** - 介绍支持的平台、本地配置 UI 的浏览器、SMB 客户端以及访问设备的客户端的任何其他要求。
- **设备的网络要求** - 提供有关物理设备运转的网络要求的信息。

## <a name="supported-os-for-clients-connected-to-device"></a>连接到设备的客户端支持的 OS

[!INCLUDE [Supported OS for clients connected to device](../../includes/azure-stack-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>访问设备的客户端支持的协议

[!INCLUDE [Supported protocols for clients accessing device](../../includes/azure-stack-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>支持的存储帐户

[!INCLUDE [Supported storage accounts](../../includes/azure-stack-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-edge-storage-accounts"></a>支持的边缘存储帐户

设备的 REST 接口支持以下边缘存储帐户。 在设备上创建边缘存储帐户。 有关详细信息，请参阅 [边缘存储帐户](azure-stack-edge-j-series-manage-storage-accounts.md#about-edge-storage-accounts)

|类型  |存储帐户  |注释  |
|---------|---------|---------|
|标准     |GPv1：块 Blob         |         |


* 当前不支持页 blob 和 Azure 文件。

## <a name="supported-local-azure-resource-manager-storage-accounts"></a>受支持的本地 Azure 资源管理器存储帐户

当你连接到本地 Azure 资源管理器时，这些存储帐户通过设备本地 Api 进行。 支持以下存储帐户：

|类型  |存储帐户  |注释  |
|---------|---------|---------|
|标准     |GPv1：块 Blob、页 Blob         | SKU 类型为 Standard_LRS        |
|高级   |GPv1：块 Blob、页 Blob         |SKU 类型为 Premium_LRS         |


## <a name="supported-storage-types"></a>受支持的存储类型

[!INCLUDE [Supported storage types](../../includes/azure-stack-edge-gateway-supported-storage-types.md)]


## <a name="supported-browsers-for-local-web-ui"></a>本地 Web UI 支持的浏览器

[!INCLUDE [Supported browsers for local web UI](../../includes/azure-stack-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>网络端口要求

### <a name="port-requirements-for-azure-stack-edge-mini-r"></a>Azure Stack 边缘迷你 R 的端口要求

下表列出了需要在防火墙中打开以允许 SMB、云或管理流量的端口。 在此表中，*入* 或 *入站* 表示传入客户端请求访问设备的方向。 " *Out* " 或 "*出站*" 是指 Azure Stack 边缘迷你 R 设备在外部（例如，在 internet 上出站）发送数据的方向。

[!INCLUDE [Port configuration for device](../../includes/azure-stack-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>IoT Edge 的端口要求

Azure IoT Edge 允许使用支持的 IoT 中心协议从本地 Edge 设备来与 Azure 云进行出站通信。 仅在 Azure IoT 中心需要将消息推送到 Azure IoT Edge 设备（例如，云到设备的消息传送）的特定情况下，才需要进行入站通信。

对托管 Azure IoT Edge 运行时的服务器使用下表中的端口配置：

| 端口号。 | 入或出 | 端口范围 | 必需 | 指南 |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| 出       | WAN        | 是      | 为 IoT Edge 预配打开此出站端口。 使用手动脚本或 Azure IoT 设备预配服务 (DPS) 时，此配置是必需的。|

有关完整信息，请转到 [IoT Edge 部署的防火墙和端口配置规则](../iot-edge/troubleshoot.md)。

## <a name="url-patterns-for-firewall-rules"></a>防火墙规则的 URL 模式

通常，网络管理员可以基于 URL 模式配置高级防火墙规则，以筛选入站和出站流量。 Azure Stack 边缘迷你 R 设备和服务依赖于其他 Microsoft 应用程序，如 Azure 服务总线、Azure Active Directory 访问控制、存储帐户和 Microsoft 更新服务器。 与这些应用程序相关联的 URL 模式可用于配置防火墙规则。 请务必了解可以更改与这些应用程序相关联的 URL 模式。 这些更改要求网络管理员根据需要监视和更新 Azure Stack Edge 迷你 R 的防火墙规则。

建议在大多数情况下，根据 Azure Stack 边缘迷你 R 固定 IP 地址设置防火墙规则，以实现出站流量。 但是，下面的信息可用于设置创建安全环境所需的高级防火墙规则。

> [!NOTE]
> - 设备（源）IP 应始终设置为所有已启用云的网络接口。
> - 目标 Ip 应设置为 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)。

### <a name="url-patterns-for-gateway-feature"></a>网关功能的 URL 模式

[!INCLUDE [URL patterns for firewall](../../includes/azure-stack-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>计算功能的 URL 模式

| URL 模式                      | 组件或功能                     |   
|----------------------------------|---------------------------------------------|
| https： \/ /mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Microsoft 容器注册表（必填）               |
| https://\*.azurecr.io                     | 个人和第三方容器注册表（可选） | 
| https://\*.azure-devices.net              | Iot 中心访问权限（必填）                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>适用于 Azure 政府的网关的 URL 模式

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/azure-stack-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Azure 政府版计算的 URL 模式

| URL 模式                      | 组件或功能                     |  
|----------------------------------|---------------------------------------------|
| https： \/ /mcr.microsoft.com<br></br>https:// \* . cdn.mscr.com | Microsoft 容器注册表（必填）               |
| https:// \* . azure-devices.us              | Iot 中心访问权限（必填）           |
| https:// \* . azurecr.us                    | 个人和第三方容器注册表（可选） | 

## <a name="internet-bandwidth"></a>Internet 带宽

[!INCLUDE [Internet bandwidth](../../includes/azure-stack-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>计算大小注意事项

在开发和测试解决方案时使用体验，以确保 Azure Stack Edge 迷你 R 设备上有足够的容量，并获得设备的最佳性能。

应考虑的因素包括：

- **容器详细信息** -请考虑以下内容。

    - 容器占用量是多少？ 容器消耗多少内存、存储和 CPU？
    - 工作负荷中有多少容器？ 你可能有大量轻型容器，而不是占用大量资源的容器。
    - 分配给这些容器的资源是多少，它们消耗的资源 (占用空间) ？
    - 容器共享多少层？ 容器映像是组织到一系列层中的文件的捆绑。 对于容器映像，确定用于计算资源消耗的层及其各自的大小。
    - 是否存在未使用的容器？ 已停止的容器仍占用磁盘空间。
    - 你的容器在哪种语言中编写？
- 已 **处理的数据大小**-容器处理的数据量是多少？ 此数据是否会占用磁盘空间，否则数据将在内存中进行处理吗？
- **预期的性能** -你的解决方案所需的性能特征是什么？ 

若要了解和优化解决方案的性能，可以使用：

- Azure 门户中可用的计算指标。 中转到 Azure Stack Edge 资源，然后开始 **监视 > 指标**。 查看 **边缘计算内存使用情况** 和 **边缘计算百分比 CPU** ，了解可用资源以及资源的使用情况。
- 可通过设备的 PowerShell 接口使用的监视命令，例如：

    - `dkr` 用于获取容器 (的实时流) 资源使用情况统计信息的统计信息。 命令支持 CPU、内存使用量、内存限制和网络 IO 指标。
    - `dkr system df` 获取有关使用的磁盘空间量的信息。 
    - `dkr image [prune]` 清除未使用的映像并释放空间。
    - `dkr ps --size` 查看正在运行的容器的近似大小。 

    有关可用命令的详细信息，请参阅 [ 调试 Kubernetes 问题](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge)。

最后，在生产环境中部署之前，请确保在数据集上验证解决方案并量化 Azure Stack Edge 迷你 R 上的性能。

## <a name="next-step"></a>后续步骤

- [部署 Azure Stack Edge 迷你 R](azure-stack-edge-placeholder.md)
