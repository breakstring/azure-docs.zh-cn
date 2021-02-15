---
title: 选择并部署安全代理
description: 了解如何在 IoT 设备上为 IoT 安全代理选择和部署 Defender。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 2634ed819b8818632c58b9e471b0f26190e5f16b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96339959"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>在 IoT 设备上选择并部署安全代理

Defender for IoT 提供监视和收集 IoT 设备数据的安全代理的参考体系结构。
若要了解详细信息，请参阅 [安全代理参考体系结构](security-agent-architecture.md)。

代理作为开放源代码项目开发，提供两种风格： <br> [C](https://aka.ms/iot-security-github-c)和 [c #](https://aka.ms/iot-security-github-cs)。

在本文中，学习如何：

> [!div class="checklist"]
> * 比较安全代理风格
> * 发现支持的代理平台
> * 为解决方案选择正确的代理风格

## <a name="understand-security-agent-options"></a>了解安全代理选项

每个 IoT security agent 风格都提供一组相同的功能，并支持类似的配置选项。

基于 C 的安全代理具有更低的内存占用量，并且对于具有较少可用资源的设备是理想选择。

|     | 基于 C 的安全代理 | 基于 c # 的安全代理 |
| --- | ----------- | --------- |
| **开源** | [GitHub](https://aka.ms/iot-security-github-c)中的[MIT 许可证](https://en.wikipedia.org/wiki/MIT_License)下提供 | [GitHub](https://aka.ms/iot-security-github-cs)中的[MIT 许可证](https://en.wikipedia.org/wiki/MIT_License)下提供 |
| **开发语言**    | C | C# |
| **支持的 Windows 平台？** | 否 | 是 |
| **Windows 先决条件** | --- | [WMI](/windows/desktop/wmisdk/) |
| **支持的 Linux 平台？** | 是、x64 和 x86 | 是，仅 x64 |
| **Linux 先决条件** | libunwind8、libcurl3、uuid-runtime、审核、audispd-插件 | libunwind8，libcurl3，uuid-runtime，审核，audispd-插件，sudo，netstat，iptables |
| **磁盘占用量** | 10.5 MB | 90 MB |
| **平均) 内存占用量 (** | 5.5 MB | 33 MB |
| **向 IoT 中心进行 [身份验证](concept-security-agent-authentication-methods.md)** | 是 | 是 |
| **安全数据 [收集](how-to-agent-configuration.md#supported-security-events)** | 是 | 是 |
| **事件聚合** | 是 | 是 |
| **通过 [安全模块](concept-security-module.md)克隆远程配置** | 是 | 是 |

## <a name="security-agent-installation-guidelines"></a>安全代理安装指南

对于 **Windows**：必须从管理员 PowerShell 窗口执行安装 SecurityAgent.ps1 脚本。

对于 **Linux**： InstallSecurityAgent.sh 必须以超级用户身份运行。 建议在安装命令前面加上 "sudo"。

## <a name="choose-an-agent-flavor"></a>选择代理风格

回答以下有关 IoT 设备的问题，以便选择正确的代理：

- 你使用的是 _Windows Server_ 还是 _windows IoT Core_？

    [为 Windows 部署基于 c # 的安全代理](how-to-deploy-windows-cs.md)。

- 是否在使用 Linux 分发和 x86 体系结构？

    [部署基于 C 的安全代理（适用于 Linux）](how-to-deploy-linux-c.md)。

- 你是否正在使用带有 x64 体系结构的 Linux 分发版？

    这两种代理风格都可以使用。 <br>
    [部署适用于 linux 的基于 c 的安全代理](how-to-deploy-linux-c.md) 和/或 [部署基于 c # 的安全代理（适用于 linux）](how-to-deploy-linux-cs.md)。

两种代理风格都提供了相同的功能集，并支持类似的配置选项。
有关详细信息，请参阅 [安全代理比较](how-to-deploy-agent.md#understand-security-agent-options) 。

## <a name="supported-platforms"></a>受支持的平台

以下列表包含当前支持的所有平台。

|用于 IoT 代理的 Defender |操作系统 |体系结构 |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |    X64|
|C|Ubuntu 18.04 |    x64、ARMv7|
|C|Debian 9 |    x64、x86|
|C#|Ubuntu 16.04     |X64|
|C#|Ubuntu 18.04    |x64、ARMv7|
|C#|Debian 9    |X64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT 核心版 17763    |X64|
|

## <a name="next-steps"></a>后续步骤

若要了解有关配置选项的详细信息，请继续阅读有关代理配置的操作方法指南。
> [!div class="nextstepaction"]
> [代理配置操作指南](./how-to-agent-configuration.md)