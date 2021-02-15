---
title: 创建虚拟网络 - 快速入门 - Azure 门户
titleSuffix: Azure Virtual Network
description: 快速入门：在 Azure 门户中创建虚拟网络。 这些网络让 Azure 资源（例如 VM）能够安全地相互通信以及与 Internet 通信。
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2020
ms.author: kumud
ms.openlocfilehash: cc3ba3a0519400368e0cbfec7abe2d9bd1731b34
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98217628"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建虚拟网络

本快速入门介绍如何使用 Azure 门户创建虚拟网络。 我们部署两个虚拟机 (VM)。 接下来就可以在 VM 之间安全地通信，并通过 Internet 连接到 VM。 虚拟网络是 Azure 中专用网络的基本构建块。 它能让 Azure 资源（例如 VM）互相安全通信以及与 Internet 通信。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-virtual-network"></a>创建虚拟网络

1. 在 Azure 门户菜单中，选择“创建资源”。 在 Azure 市场中，选择“网络” > “虚拟网络” 。

1. 在“创建虚拟网络”中，输入或选择以下信息：

    | 设置 | “值” |
    | ------- | ----- |
    | 订阅 | 选择订阅。|
    | 资源组 | 选择“新建”，输入 myResourceGroup，然后选择“确定”。 |
    | 名称 | 输入 myVirtualNetwork。 |
    | 位置 | 选择“美国东部”。|

1. 在完成时选择“下一步:IP 地址”，并输入 *10.1.0.0/16* 作为“IPv4 地址空间”。

1. 选择“添加子网”，然后输入 *myVirtualSubnet* 作为“子网名称”，输入 *10.1.0.0/24* 作为“子网地址范围”。

1. 选择“添加”，然后选择“查看 + 创建”。  将其余的设置保留默认值，然后选择“创建”。

1. 在“创建虚拟网络”中，选择“创建”。 

## <a name="create-virtual-machines"></a>创建虚拟机

在虚拟网络中创建两个 VM：

### <a name="create-the-first-vm"></a>创建第一个 VM

1. 在 Azure 门户菜单中，选择“创建资源”。

1. 在 Azure 市场中，选择“计算” > “Windows Server 2019 Datacenter” 。 选择“创建”。

1. 在“创建虚拟机 - 基本信息”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 选择“myResourceGroup”。 我们在上一部分创建了此资源组。 |
    | **实例详细信息** |  |
    | 虚拟机名称 | 输入 myVm1。 |
    | 区域 | 选择“美国东部”。 |
    | 可用性选项 | 默认设置为“无需基础结构冗余”。 |
    | 映像 | 默认设置为“Windows Server 2019 Datacenter”。 |
    | 大小 | 默认设置为“标准 DS1 v2”。 |
    | **管理员帐户** |  |
    | 用户名 | 输入所选用户名。 |
    | 密码 | 输入所选密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
    | 确认密码 | 重新输入密码。 |
    | **入站端口规则** |  |
    | 公共入站端口 | 选择“允许所选端口”  。 |
    | 选择入站端口 | 输入 *HTTP (80)* 和 *RDP (3389)* 。 |
    | **节省资金** |  |
    | 已有 Windows 许可证？ | 默认设置为“否”。 |

1. 在完成时选择“下一步:**磁盘”** 。

1. 在“创建虚拟机 - 磁盘”中保留默认值，然后选择“下一步: **网络”** 。

1. 在“创建虚拟机 - 基本信息”中，选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 虚拟网络 | 默认设置为 **myVirtualNetwork**。 |
    | 子网 | 默认设置为 **myVirtualSubnet (10.1.0.0/24)** 。 |
    | 公共 IP | 默认设置为“(新) myVm-ip”。 |
    | NIC 网络安全组 | 默认设置为“基本”。 |
    | 公共入站端口 | 默认设置为“允许所选端口”。 |
    | 选择入站端口 | 默认设置为“HTTP”和“RDP”。 

1. 在完成时选择“下一步:**管理”** 。

1. 在“创建虚拟机 - 管理”中，为“诊断存储帐户”选择“新建”  。

1. 在“创建存储帐户”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入 myvmstorageaccount。 如果此名称已被使用，请创建唯一的名称。|
    | 帐户类型 | 默认设置为“存储(常规用途 v1)”。 |
    | 性能 | 默认设置为“标准”。 |
    | 复制 | 默认设置为“本地冗余存储(LRS)”。 |

1. 选择“确定”，然后选择“查看 + 创建”。  随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置。

1. 看到“验证通过”消息时，选择“创建” 。

### <a name="create-the-second-vm"></a>创建第二个 VM

重复上一部分的过程，创建另一个虚拟机。

> [!IMPORTANT]
> 输入 *myVm2* 作为“虚拟机名称”。
>
> 确保选择 **myvmstorageaccount** 作为“诊断存储帐户”，而不是创建一个。

## <a name="connect-to-a-vm-from-the-internet"></a>从 Internet 连接到 VM

创建 myVm1 后，连接到 Internet。

1. 在 Azure 门户中，搜索并选择“myVm1”。

1. 选择“连接”，然后选择“RDP”。

    ![连接到虚拟机](./media/quick-create-portal/connect-to-virtual-machine.png)

    此时会打开“连接”页。

1. 选择“下载 RDP 文件”。 Azure 会创建远程桌面协议 ( *.rdp*) 文件，并将其下载到计算机。

1. 打开该 RDP 文件。 出现提示时，选择“连接”。

1. 输入在创建 VM 时指定的用户名和密码。

    > [!NOTE]
    > 可能需要选择“更多选择” > “使用其他帐户”，以指定在创建 VM 时输入的凭据 。

1. 选择“确定”。

1. 可能会在登录时收到证书警告。 如果收到证书警告，请选择“确定”或“继续” 。

1. VM 桌面出现后，将其最小化以返回到本地桌面。

## <a name="communicate-between-vms"></a>VM 之间进行通信

1. 在 myVm1 远程桌面中，打开 PowerShell。

1. 输入 `ping myVm2`。

    会收到类似于以下输出的消息：

    ```output
    Pinging myVm2.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.clouda
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    由于 `ping` 使用 Internet 控制消息协议 (ICMP)，`ping` 失败。 默认情况下，不允许 ICMP 通过 Windows 防火墙。

1. 要允许 myVm2 在后面的步骤中对 myVm1 执行 ping 操作，请输入以下命令：

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    该命令允许 ICMP 通过 Windows 防火墙入站：

1. 关闭与 *myVm1* 的远程桌面连接。

1. 再次完成 [从 Internet 连接到 VM](#connect-to-a-vm-from-the-internet) 中的步骤，但这次连接到 *myVm2*。

1. 从命令提示符输入 `ping myvm1`。

    你将看到类似于以下信息的内容：

    ```output
    Pinging myVm1.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 1ms, Average = 0ms
    ```

    将从 myVm1 收到答复，因为在第 3 步中已经允许 ICMP 通过 myVm1 VM 上的 Windows 防火墙 。

1. 关闭与 *myVm2* 的远程桌面连接。

## <a name="clean-up-resources"></a>清理资源

在本快速入门中，你创建了默认的虚拟网络和两个 VM。 从 Internet 连接到了其中一个 VM，并在两个 VM 之间安全地进行了通信。

使用虚拟网络和 VM 之后，请删除资源组和其包含的所有资源：

1. 搜索并选择“myResourceGroup”。

1. 选择“删除资源组”。

1. 对于“键入资源组名称”，请输入“myResourceGroup”，然后选择“删除” 。

## <a name="next-steps"></a>后续步骤

若要详细了解虚拟网络设置，请参阅[创建、更改或删除虚拟网络](manage-virtual-network.md)。

默认情况下，Azure 允许在 VM 之间进行安全通信。 Azure 只允许从 Internet 到 Windows VM 的入站远程桌面连接。 若要详细了解 VM 网络通信类型，请参阅[筛选网络流量](tutorial-filter-network-traffic.md)。

> [!NOTE] 
> Azure 服务是要花钱的。 Azure 成本管理有助于你设置预算并配置警报，使支出保持在控制范围之内。 使用成本管理分析、管理和优化 Azure 成本。 要了解详细信息，请参阅[分析成本快速入门](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。