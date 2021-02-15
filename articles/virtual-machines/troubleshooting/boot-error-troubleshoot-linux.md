---
title: 排查 Azure Linux 虚拟机中的启动错误 |Microsoft Docs
description: 本文提供的链接指向关于对 Azure Linux 虚拟机启动错误进行故障排除的文章。
services: virtual-machines-linux
documentationCenter: ''
author: vilibert
manager: spogge
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2019
ms.author: vilibert
ms.openlocfilehash: 37cb201751f72918838efe5837aa0e357d483f24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "74408737"
---
# <a name="troubleshoot-azure-linux-virtual-machines-boot-errors"></a>对 Azure Linux 虚拟机启动错误进行故障排除

本文列出了在 Microsoft Azure 中启动 Linux 虚拟机 (VM) 时可能会收到的常见启动错误。 有关这些错误的详细信息，请参阅“启动错误和解决方案”部分中的文章  。

## <a name="boot-errors-and-solutions"></a>启动错误和解决方案

* [GRUB 救援](troubleshoot-vm-boot-error.md)

## <a name="next-steps"></a>后续步骤

- [VM 串行控制台](serial-console-linux.md)

通过使用 Azure 将 OS 磁盘附加到恢复 VM 来对 Linux VM 进行故障排除：

- [Azure VM 修复](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

 磁盘交换–可以使用以下任一方法自动执行此操作：
- [Power Shell 恢复脚本](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
- [Bash 恢复脚本](https://github.com/sribs/azure-support-scripts)

- [CLI](troubleshoot-recovery-disks-linux.md)
- [Azure 门户](troubleshoot-recovery-disks-portal-linux.md)


## <a name="disk-swap-video"></a>磁盘交换视频：

如果你无法访问 GRUB，请观看[此](https://youtu.be/m5t0GZ5oGAc)视频，了解如何轻松地自动执行磁盘交换过程以恢复 VM

## <a name="unofficial-solution"></a>非正式解决方案

也可尝试使用不受支持的 BETA 脚本 [ALAR](https://github.com/malachma/azure-auto-recover) 来恢复 VM