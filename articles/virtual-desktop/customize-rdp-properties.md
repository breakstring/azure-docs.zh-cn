---
title: 通过 PowerShell 自定义 RDP 属性-Azure
description: 如何通过 PowerShell cmdlet 为 Windows 虚拟桌面自定义 RDP 属性。
author: Heidilohr
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cc3a08f383368b189e41bebd204707f2483e77c0
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018301"
---
# <a name="customize-remote-desktop-protocol-rdp-properties-for-a-host-pool"></a>自定义主机池 (RDP) 属性远程桌面协议

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Windows 虚拟桌面对象的 Windows 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Windows 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/customize-rdp-properties-2019.md)。

通过自定义主机池的远程桌面协议 (RDP) 属性（例如多监视器体验和音频重定向），可以根据用户的需要为用户提供最佳体验。 你可以通过使用 Azure 门户或在 **AzWvdHostPool** cmdlet 中使用 *-CustomRdpProperty* 参数自定义 Windows 虚拟桌面中的 RDP 属性。

有关支持的属性及其默认值的完整列表，请参阅 [支持的 RDP 文件设置](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext) 。

## <a name="default-rdp-file-properties"></a>默认 RDP 文件属性

默认情况下，RDP 文件具有以下属性：

|RDP 属性|在桌面上|作为 RemoteApp|
|---|---|---|
|多监视器模式|已禁用|已启用|
|已启用驱动器重定向|驱动器、剪贴板、打印机、COM 端口和智能卡|驱动器、剪贴板和打印机|
|远程音频模式|本地播放|本地播放|

## <a name="prerequisites"></a>先决条件

在开始之前，请按照[设置 Windows 虚拟桌面 PowerShell 模块](powershell-module.md)中的说明设置 PowerShell 模块并登录到 Azure。

## <a name="configure-rdp-properties-in-the-azure-portal"></a>在 Azure 门户中配置 RDP 属性

若要在 Azure 门户中配置 RDP 属性：

1. 通过 <https://portal.azure.com> 登录到 Azure。
2. 在搜索栏中输入 **windows 虚拟桌面** 。
3. 在 "服务" 下，选择 " **Windows 虚拟桌面**"。
4. 在 Windows 虚拟桌面页面上，在屏幕左侧的菜单中选择 " **主机池** "。
5. 选择要更新的 **主机池的名称** 。
6. 在屏幕左侧的菜单中选择 " **RDP 属性** "。
7. 设置所需的属性。
   - 或者，你可以打开 " **高级** " 选项卡，并以分号分隔的格式添加 RDP 属性，如以下部分中的 PowerShell 示例所示。
8. 完成后，选择 " **保存** " 以保存所做的更改。

后续部分将介绍如何在 PowerShell 中手动编辑自定义 RDP 属性。

## <a name="add-or-edit-a-single-custom-rdp-property"></a>添加或编辑单个自定义 RDP 属性

若要添加或编辑单个自定义 RDP 属性，请运行以下 PowerShell cmdlet：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty <property>
```

若要检查刚刚运行的 cmdlet 是否已更新属性，请运行以下 cmdlet：

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

例如，如果要在名为0301HP 的主机池上检查 "audiocapturemode" 属性，则输入以下 cmdlet：

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;
```

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>添加或编辑多个自定义 RDP 属性

若要添加或编辑多个自定义 RDP 属性，请运行以下 PowerShell cmdlet，方法是将自定义 RDP 属性提供为分号分隔的字符串：

```powershell
$properties="<property1>;<property2>;<property3>"
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty $properties
```

可以通过运行以下 cmdlet 进行检查，确保已添加 RDP 属性：

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

根据前面的 cmdlet 示例，如果在0301HP 主机池上设置了多个 RDP 属性，则 cmdlet 将如下所示：

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;audiomode:i:0;
```

## <a name="reset-all-custom-rdp-properties"></a>重置所有自定义 RDP 属性

可以按照 [添加或编辑单个自定义 rdp 属性](#add-or-edit-a-single-custom-rdp-property)中的说明，将各个自定义 rdp 属性重置为其默认值，也可以通过运行以下 PowerShell cmdlet 来重置主机池的所有自定义 rdp 属性：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty ""
```

若要确保已成功删除设置，请输入以下 cmdlet：

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <CustomRDPpropertystring>
```

## <a name="next-steps"></a>后续步骤

自定义给定主机池的 RDP 属性后，可以登录到 Windows 虚拟桌面客户端，将其作为用户会话的一部分进行测试。 接下来的操作方法指南将告诉你如何使用所选的客户端连接到会话：

- [使用 Windows 桌面客户端进行连接](connect-windows-7-10.md)
- [使用 Web 客户端进行连接](connect-web.md)
- [使用 Android 客户端进行连接](connect-android.md)
- [使用 macOS 客户端进行连接](connect-macos.md)
- [使用 iOS 客户端进行连接](connect-ios.md)