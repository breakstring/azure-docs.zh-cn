---
title: Windows 7 虚拟机 Windows 虚拟桌面 (经典) -Azure
description: 如何解决 (Vm) Windows 虚拟桌面 (经典) 环境中的 Windows 7 虚拟机问题。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1d71b4472b16372927db56b20eca4fcbde0cc625
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88005408"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop-classic"></a>对 Windows 虚拟机中的 Windows 7 虚拟机进行故障排除 (经典) 

>[!IMPORTANT]
>本教程的内容适用于 Windows 虚拟桌面（经典），后者不支持 Azure 资源管理器 Windows 虚拟桌面对象。

本文介绍了在配置 Windows 虚拟桌面会话主机虚拟机 (Vm) 时遇到的问题。

## <a name="known-issues"></a>已知问题

Windows 虚拟桌面上的 windows 7 不支持以下功能：

- 虚拟化应用程序 (RemoteApps) 
- 时区重定向
- 自动 DPI 缩放

Windows 虚拟桌面只能虚拟化 Windows 7 的整个桌面。

虽然不支持自动 DPI 缩放，但你可以通过右键单击远程桌面客户端中的图标，然后选择 " **解决**方法" 来手动更改虚拟机上的分辨率。

## <a name="error-cant-access-the-remote-desktop-user-group"></a>错误：无法访问远程桌面用户组

如果 Windows 虚拟桌面在远程桌面用户组中找不到你或用户的凭据，你可能会看到以下错误消息之一：

- "此用户不是远程桌面用户组的成员"
- "必须被授予通过远程桌面服务登录的权限"

若要修复此错误，请将用户添加到远程桌面用户组：

1. 打开 Azure 门户。
2. 选择出现错误消息的虚拟机。
3. 选择 " **运行命令**"。
4. 运行以下命令，并将其 `<username>` 替换为要添加的用户的名称：

   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```