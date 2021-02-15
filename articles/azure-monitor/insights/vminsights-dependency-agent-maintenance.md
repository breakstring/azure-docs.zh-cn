---
title: 如何升级用于 VM 的 Azure Monitor 依赖关系代理
description: 本文介绍如何使用命令行、安装向导和其他方法升级用于 VM 的 Azure Monitor 依赖关系代理。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/16/2020
ms.openlocfilehash: 01dd8422658aa0c8982733e48782efd27c1bf5be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "81617855"
---
# <a name="how-to-upgrade-the-azure-monitor-for-vms-dependency-agent"></a>如何升级用于 VM 的 Azure Monitor 依赖关系代理

初始部署用于 VM 的 Azure Monitor 依赖关系代理后，将发布更新，其中包括 bug 修补程序或新功能的支持。  本文将帮助你了解可用的方法以及如何手动或通过自动化执行升级。

## <a name="upgrade-options"></a>升级选项 

适用于 Windows 和 Linux 的依赖关系代理可以手动或自动升级到最新版本，具体取决于计算机运行所在的部署方案和环境。 可以使用以下方法来升级代理。

|环境 |安装方法 |升级方法 |
|------------|--------------------|---------------|
|Azure VM | 适用于[Windows](../../virtual-machines/extensions/agent-dependency-windows.md)和[Linux](../../virtual-machines/extensions/agent-dependency-linux.md)的依赖关系代理 VM 扩展 | 默认情况下代理会自动升级，除非你通过将 *autoUpgradeMinorVersion* 属性设置为 **false** 来将 Azure 资源管理器模板配置为选择退出。 已禁用自动升级的次要版本升级，并且主要版本升级遵循相同的方法-卸载并重新安装该扩展。 |
| 自定义 Azure VM 映像 | 为 Windows/Linux 手动安装依赖关系代理 | 将 VM 更新到最新版本的代理需要从命令行运行 Windows 安装程序包或 Linux 自解压和可安装的 shell 脚本包。|
| 非 Azure VM | 为 Windows/Linux 手动安装依赖关系代理 | 将 VM 更新到最新版本的代理需要从命令行运行 Windows 安装程序包或 Linux 自解压和可安装的 shell 脚本包。 |

## <a name="upgrade-windows-agent"></a>升级 Windows 代理 

若要将 Windows VM 上的代理更新为不使用依赖关系代理 VM 扩展安装的最新版本，您可以从命令提示符、脚本或其他自动化解决方案中运行，也可以使用 InstallDependencyAgent-Windows.exe 安装向导运行。  

可从 [此处](https://aka.ms/dependencyagentwindows)下载最新版本的 Windows 代理。

### <a name="using-the-setup-wizard"></a>使用安装向导

1. 使用具有管理权限的帐户登录到计算机。

2. 执行 **InstallDependencyAgent-Windows.exe** 以启动安装向导。
   
3. 按照 **Dependency Agent 安装** 向导卸载以前版本的依赖关系代理，然后安装最新版本。


### <a name="from-the-command-line"></a>从命令行

1. 使用具有管理权限的帐户登录到计算机。

2. 运行以下命令。

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    `/RebootMode=manual`如果某些进程使用以前版本中的文件并对其进行锁定，则该参数可防止升级自动重新启动计算机。 

3. 若要确认升级是否成功，请查看 `install.log` 以了解详细的设置信息。 日志目录为 *%Programfiles%\Microsoft Dependency Agent\logs*。

## <a name="upgrade-linux-agent"></a>升级 Linux 代理 

支持从 Linux 上先前版本的依赖关系代理升级，并执行与新安装相同的命令。

可从 [此处](https://aka.ms/dependencyagentlinux)下载最新版本的 Linux 代理。

1. 使用具有管理权限的帐户登录到计算机。

2. 以 root 身份运行以下命令 `sh InstallDependencyAgent-Linux64.bin -s` 。 

如果 Dependency Agent 无法启动，请检查日志以获取详细的错误信息。 在 Linux 代理上，日志目录是 */var/opt/microsoft/dependency-agent/log*。 

## <a name="next-steps"></a>后续步骤

如果要停止监视 Vm 一段时间或完全删除用于 VM 的 Azure Monitor，请参阅 [在用于 VM 的 Azure Monitor 中禁用 vm 监视](vminsights-optout.md)。
