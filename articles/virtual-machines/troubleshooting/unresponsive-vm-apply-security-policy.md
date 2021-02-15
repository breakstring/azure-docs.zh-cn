---
title: Azure VM 在将安全策略应用于系统时没有响应
description: 本文提供了一些步骤来解决在将安全策略应用于 Azure VM 中的系统时，VM 没有响应且加载屏幕卡住的问题。
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5629
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.author: v-mibufo
ms.openlocfilehash: 047c8afbfe7b489e5c3ac0ccb677f6fc021443a8
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632633"
---
# <a name="azure-vm-is-unresponsive-while-applying-security-policy-to-the-system"></a>Azure VM 在将安全策略应用于系统时没有响应

本文提供了一些步骤来解决在 Azure VM 中应用安全策略时，操作系统挂起且无响应的问题。

## <a name="symptoms"></a>症状

使用[启动诊断](boot-diagnostics.md)查看 VM 的屏幕截图时，你将看到屏幕截图显示启动时操作系统卡住，并显示以下消息：

> “将安全策略应用于系统”。

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy.png" alt-text="Windows Server 2012 R2 启动屏幕卡住的屏幕截图。":::

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy-2.png" alt-text="操作系统启动屏幕卡住的屏幕截图。":::

## <a name="cause"></a>原因

此问题的可能原因有很多。 在执行内存转储分析之前，你将无法知道该源。

## <a name="resolution"></a>解决方法

### <a name="process-overview"></a>流程概述

> [!TIP]
> 如果你有 VM 的最新备份，则可以尝试 [从备份还原 vm](../../backup/backup-azure-arm-restore-vms.md) ，以解决启动问题。

1. [创建和访问修复 VM](#create-and-access-a-repair-vm)
2. [启用串行控制台和内存转储收集](#enable-serial-console-and-memory-dump-collection)
3. [重新生成 VM](#rebuild-the-vm)
4. [收集内存转储文件](#collect-the-memory-dump-file)

### <a name="create-and-access-a-repair-vm"></a>创建并访问修复 VM

1. 使用 [VM 修复命令的步骤 1-3](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 来准备一个修复 VM。
2. 使用远程桌面连接来连接到修复 VM。

### <a name="enable-serial-console-and-memory-dump-collection"></a>启用串行控制台和内存转储收集

若要启用内存转储集合和串行控制台，请运行以下脚本：

1. 打开提升的命令提示符会话（以管理员身份运行）。
2. 列出 BCD 存储数据，并确定要在下一步中使用的引导加载程序标识符。

     1. 对于第 1 代 VM，请输入以下命令，并记下列出的标识符：

        ```console
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```

        在该命令中，将 \<BOOT PARTITON> 替换为附加磁盘中包含引导文件夹的分区驱动器号。

        :::image type="content" source="media/unresponsive-vm-apply-security-policy/store-data.png" alt-text="关系图显示在第1代虚拟机中列出 BCD 存储的输出，该 VM 在 Windows 启动加载程序中列出了标识符号。":::

     2. 对于第 2 代 VM，请输入以下命令，并记下列出的标识符：

        ```console
        bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum
        ```

        - 在该命令中，将 \<LETTER OF THE EFI SYSTEM PARTITION> 替换为 EFI 系统分区的驱动器号。
        - 启动 "磁盘管理" 控制台来确定标记为 "EFI 系统分区" 的适当系统分区可能会很有帮助。
        - 标识符可以是唯一的 GUID，也可以是默认值 "bootmgr"。
3. 运行以下命令以启用串行控制台：

    ```console
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```console
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

    - 在该命令中，将 \<VOLUME LETTER WHERE THE BCD FOLDER IS> 替换为 BCD 文件夹的驱动器号。
    - 在该命令中，将 \<BOOT LOADER IDENTIFIER> 替换为在上一步骤中找到的标识符。
4. 验证 OS 磁盘上的可用空间是否大于 VM 上的内存大小 (RAM)。

    1. 如果 OS 磁盘上没有足够的空间，则应更改将在其中创建内存转储文件的位置。 不要在 OS 磁盘上创建文件，可以将其指向附加到 VM 且具有足够可用空间的任何其他数据磁盘。 若要更改位置，请将 "% SystemRoot%" 替换为驱动器号 (例如，在下面列出的命令中输入数据磁盘的 "F：" ) 。
    2. 输入以下命令（建议的转储配置）：

        加载损坏的 OS 磁盘：

        ```console
        REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
        ```

        在 ControlSet001 上启用：

        ```console
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
        ```

        在 ControlSet002 上启用：

        ```console
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
        ```

        卸载损坏的 OS 磁盘：

        ```console
        REG UNLOAD HKLM\BROKENSYSTEM
        ```

### <a name="rebuild-the-vm"></a>重建 VM

使用 [VM 修复命令的步骤 5](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 重新装配 VM。

### <a name="collect-the-memory-dump-file"></a>收集内存转储文件

若要解决此问题，需先收集故障的内存转储文件，然后使用此内存转储文件联系支持部门。 若要收集转储文件，请执行以下步骤：

1. 将 OS 磁盘附加到新的修复 VM：

    - 使用 [VM 修复命令的步骤 1-3](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 来准备一个新的修复 VM。
    - 使用远程桌面连接来连接到修复 VM。

2. 找到转储文件并提交支持票证：

    - 在修复 VM 上，转到附加的 OS 磁盘中的 Windows 文件夹。 如果分配给附加 OS 磁盘的驱动器号为 `F`，则需转到 `F:\Windows`。
    - 找到 memory.dmp 文件，然后使用该内存转储文件[提交支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
    - 如果在查找 memory.dmp 文件时遇到问题，可以改为[在串行控制台中使用不可屏蔽的中断 (NMI) 调用](serial-console-windows.md#use-the-serial-console-for-nmi-calls)。 可以按照指南 [使用 NMI 调用生成故障转储文件](/windows/client-management/generate-kernel-or-complete-crash-dump)。

## <a name="next-steps"></a>后续步骤

如果在应用本地用户和组策略时遇到问题，请参阅[应用组策略本地用户和组策略时，VM 无响应](unresponsive-vm-apply-group-policy.md)