---
title: 将 Windows Server VM 加入 Azure AD 域服务托管域 | Microsoft Docs
description: 在此教程中，会了解如何将 Windows Server 虚拟机加入到 Azure Active Directory 域服务托管域。
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 869c827485d9b7a6baf68d2619af98d4c2ee82b9
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619566"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>教程：将 Windows Server 虚拟机加入到 Azure Active Directory 域服务托管域

Azure Active Directory 域服务 (Azure AD DS) 提供与 Windows Server Active Directory 完全兼容的托管域服务，例如域加入、组策略、LDAP、Kerberos/NTLM 身份验证。 借助 Azure AD DS 托管域，可向 Azure 中的虚拟机 (VM) 提供域加入功能和管理。 本教程演示如何创建 Windows Server VM，然后将其加入托管域。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建 Windows Server VM
> * 将 Windows Server VM 连接到 Azure 虚拟网络
> * 将 VM 加入托管域

如果还没有 Azure 订阅，可以在开始前[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要以下各资源：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请[创建并配置 Azure Active Directory 域服务托管域][create-azure-ad-ds-instance]。
* 属于托管域的用户帐户。
    * 请确保已执行 Azure AD Connect 密码哈希同步或自助式密码重置，以便帐户能够登录到托管域。
* 部署在 Azure AD DS 虚拟网络中的 Azure Bastion 主机。
    * 根据需要[创建 Azure Bastion 主机][azure-bastion]。

如果已有要加入域的 VM，请跳到[将 VM 加入托管域](#join-the-vm-to-the-managed-domain)部分。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

在本教程中，将创建一个 Windows Server VM，以使用 Azure 门户加入托管域。 若要开始操作，请登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-windows-server-virtual-machine"></a>创建 Windows Server 虚拟机

若要查看如何将计算机加入托管域，请创建 Windows Server VM。 此 VM 会连接到 Azure 虚拟网络，该网络提供与托管域的连接。 加入托管域的过程与加入常规本地 Active Directory 域服务域的过程相同。

如果已有要加入域的 VM，请跳到[将 VM 加入托管域](#join-the-vm-to-the-managed-domain)部分。

1. 从 Azure 门户菜单或“主页”页面，选择“创建资源” 。

1. 从“入门”中选择“Windows Server 2016 Datacenter” 。

    ![选择在 Azure 门户中创建 Windows Server 2016 Datacenter VM](./media/join-windows-vm/select-vm-image.png)

1. 在“基本信息”窗口中，配置虚拟机的核心设置。 保留“可用性选项”、“映像”和“大小”的默认值  。

    | 参数            | 建议的值   |
    |----------------------|-------------------|
    | 资源组       | 选择或创建资源组，如 myResourceGroup |
    | 虚拟机名称 | 输入 VM 的名称，如 myVM |
    | 区域               | 选择要在其中创建 VM 的区域，如“美国东部” |
    | 用户名             | 输入要在 VM 上创建的本地管理员帐户的用户名，如 azureuser |
    | 密码             | 输入，然后确认要在 VM 上创建的本地管理员的安全密码。 请不要指定域用户帐户的凭据。 |

1. 默认情况下，可以使用 RDP 从 Internet 访问在 Azure 中创建的 VM。 启用 RDP 后，可能会发生自动登录攻击，这可能会因为多次连续登录尝试失败而禁用具有通用名称（例如“admin”或“administrator”）的帐户 。

    应仅在需要时启用 RDP，并将其限制为一组已授权 IP 范围。 此配置有助于提高 VM 的安全性并减小潜在攻击的范围。 或者，创建并使用 Azure Bastion 主机，以便仅允许在 Azure 门户中通过 TLS 进行访问。 在本教程的下一步，我们使用 Azure Bastion 主机安全地连接到 VM。

    在“公共入站端口”下，选择“无”。

1. 完成后，选择“下一步: **磁盘”** 。
1. 从“OS 磁盘类型”的下拉菜单中，选择“标准 SSD”，然后选择“下一步: 网络”。
1. VM 必须连接到 Azure 虚拟网络子网，该子网可与其中部署托管域的子网通信。 建议将托管域部署到其自己的专用子网中。 请不要将 VM 部署在与托管域相同的子网中。

    可以通过两种主要方法来部署 VM 并连接到相应的虚拟网络子网：
    
    * 在部署托管域的同一虚拟网络中创建子网或选择现有子网。
    * 在 Azure 虚拟网络中的选择一个子网，该子网使用 [Azure 虚拟网络对等互连][vnet-peering]与之连接。
    
    如果为托管域选择一个没有连接到子网的虚拟网络子网，则无法将 VM 加入托管域。 在本教程中，将在 Azure 虚拟网络中创建一个新的子网。

    在“网络”窗格中，选择在其中部署托管域的虚拟网络，如 aaads-vnet
1. 在此示例中，显示了现有的 aaads-subnet，其中显示托管域已连接到该子网。 请不要将 VM 连接到此子网。 若要为 VM 创建子网，请选择“管理子网配置”。

    ![在 Azure 门户中选择管理子网配置](./media/join-windows-vm/manage-subnet.png)

1. 在虚拟网络窗口的左侧菜单中，选择“地址空间”。 随即会创建带有单个地址空间 *10.0.2.0/24*（由默认子网使用）的虚拟网络。 其他子网（例如用于工作负载的子网）或 Azure Bastion 也可能已存在。

    将额外的 IP 地址范围添加到该虚拟网络。 此地址范围的大小以及要使用的实际 IP 地址范围取决于已部署的其他网络资源。 该 IP 地址范围不应与 Azure 或本地环境中的任何现有地址范围重叠。 请确保该 IP 地址范围足够大，能够与要部署到子网中的 VM 数量相适应。

    在以下示例中，添加了额外的 IP 地址范围 *10.0.5.0/24*。 准备就绪后，选择“保存”。

    ![在 Azure 门户中添加额外的虚拟网络 IP 地址范围](./media/join-windows-vm/add-vnet-address-range.png)

1. 接下来，在虚拟网络窗口的左侧菜单中选择“子网”，然后选择“+ 子网”以添加子网。

1. 选择“+ 子网”，然后输入子网名，如 management。 提供“地址范围(CIDR 块)”，如 10.0.5.0/24。 请确保此 IP 地址范围与任何其他现有的 Azure 或本地地址范围不重叠。 将其他选项保留默认值，然后选择“确定”。

    ![在 Azure 门户中创建子网配置](./media/join-windows-vm/create-subnet.png)

1. 创建子网需要几秒钟的时间。 创建后，请选择“X”关闭子网窗口。
1. 返回到“网络”窗格以创建 VM，从下拉菜单中选择所创建的子网，如 management。 同样，请确保选择了正确的子网，并且不要将 VM 部署在与托管域相同的子网中。
1. 对于“公共 IP”，请从下拉菜单中选择“无”。 在本教程中使用 Azure Bastion 连接到管理时，无需为 VM 分配公共 IP 地址。
1. 将其他选项保留默认值，然后选择“管理”。
1. 将“启动诊断”设置为“关”。 将其他选项保留默认值，然后选择“查看 + 创建”。
1. 查看 VM 设置，然后选择“创建”。

创建 VM 需要几分钟时间。 Azure 门户显示部署的状态。 VM 准备就绪后，请选择“转到资源”。

![成功创建后，请转到 Azure 门户中的 VM 资源](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>连接到 Windows Server VM

若要安全地连接到 VM，请使用 Azure Bastion 主机。 使用 Azure Bastion 时，托管主机部署到虚拟网络中，并提供到 VM 的基于 Web 的 RDP 或 SSH 连接。 不需要为 VM 使用公共 IP 地址，也不需要为外部远程流量打开网络安全组规则。 我们在 Web 浏览器中使用 Azure 门户连接到 VM。 根据需要[创建 Azure Bastion 主机][azure-bastion]。

若要使用 Bastion 主机连接到 VM，请完成以下步骤：

1. 在 VM 的“概览”窗格中选择“连接”，然后选择“Bastion”。

    ![在 Azure 门户中使用 Bastion 连接到 Windows 虚拟机](./media/join-windows-vm/connect-to-vm.png)

1. 输入在上一部分指定的 VM 的凭据，然后选择“连接”。

   ![在 Azure 门户中通过 Bastion 主机进行连接](./media/join-windows-vm/connect-to-bastion.png)

在需要的情况下，允许 Web 浏览器打开要显示的 Bastion 连接的弹出窗口。 连接到 VM 需要几秒钟的时间。

## <a name="join-the-vm-to-the-managed-domain"></a>将 VM 加入托管域

使用 Azure Bastion 创建 VM 并建立基于 Web 的 RDP 连接后，接下来将 Windows Server 虚拟机加入托管域。 此过程与连接到常规本地 Active Directory 域服务域的计算机相同。

1. 如果在登录 VM 时服务器管理器默认情况下未打开，请选择“开始”菜单，然后选择“服务器管理器”。
1. 在“服务器管理器”窗口的左窗格中选择“本地服务器”。 在右侧窗格的“属性”下选择“工作组” 。

    ![在 VM 上打开“服务器管理器”，并编辑工作组属性](./media/join-windows-vm/server-manager.png)

1. 在“系统属性”属性页中，选择“更改”以加入托管域 。

    ![选择更改工作组或域属性](./media/join-windows-vm/change-domain.png)

1. 在“域”框中指定托管域的名称（如 aaddscontoso.com），然后选择“确定”。

    ![指定要加入的托管域](./media/join-windows-vm/join-domain.png)

1. 输入域凭据以加入域。 提供属于托管域的用户的凭据。 此帐户必须属于托管域或 Azure AD 租户 - 与 Azure AD 租户关联的外部目录的帐户无法在加入域的过程中正确进行身份验证。

    可以通过以下某种方式指定帐户凭据：

    * **UPN 格式**（推荐）- 输入在 Azure AD 中为用户帐户配置的用户主体名称 (UPN) 后缀。 例如，用户 contosoadmin 的 UPN 后缀为 `contosoadmin@aaddscontoso.onmicrosoft.com`。 有几种常见的用例，可以可靠地使用 UPN 格式登录到域而不是使用 SAMAccountName 格式：
        * 如果用户的 UPN 前缀过长（如 deehasareallylongname），服务可能会自动生成 SAMAccountName 。
        * 如果 Azure AD 租户中有多个用户具有相同的 UPN 前缀（如 dee），服务可能会自动生成其 SAMAccountName 格式 。
    * **SAMAccountName 格式** - 以 SAMAccountName 格式输入帐户名。 例如，用户 contosoadmin 的 SAMAccountName 将为 `AADDSCONTOSO\contosoadmin` 。

1. 加入托管域需要几秒钟时间。 完成后，将出现以下消息欢迎你访问该域：

    ![欢迎加入域](./media/join-windows-vm/join-domain-successful.png)

    选择“确定”以继续。

1. 若要完成加入托管域的过程，请重启 VM。

> [!TIP]
> 可以通过 PowerShell 使用 [Add-Computer][add-computer] cmdlet 将 VM 加入域。 以下示例加入 AADDSCONTOSO 域，然后重启 VM。 出现提示时，输入属于托管域的用户的凭据：
>
> `Add-Computer -DomainName AADDSCONTOSO -Restart`
>
> 若要在不连接到 VM 并手动配置连接的情况下将 VM 加入域，可以使用 [Set-AzVmAdDomainExtension][set-azvmaddomainextension] Azure PowerShell cmdlet。

重启 Windows Server VM 后，托管域中应用的所有策略都将推送到 VM。 现在还可以使用适当的域凭据登录到 Windows Server VM。

## <a name="clean-up-resources"></a>清理资源

在下一个教程中，你将使用此 Windows Server VM 安装管理工具来管理托管域。 如果不想继续学习本系列教程，请查看以下清理步骤，以便[删除 VM](#delete-the-vm)。 否则，[请继续学习下一个教程](#next-steps)。

### <a name="unjoin-the-vm-from-the-managed-domain"></a>从托管域删除 VM

若要从托管域中删除 VM，请再次执行[将 VM 加入域](#join-the-vm-to-the-managed-domain)的步骤。 不要加入托管域，而是选择加入工作组，例如默认的 WORKGROUP。 VM 重新启动后，将从托管域中删除计算机对象。

如果你[删除 VM](#delete-the-vm) 而未取消加入域，则 Azure AD DS 中将保留一个孤立的计算机对象。

### <a name="delete-the-vm"></a>删除 VM

如果不打算使用此 Windows Server VM，请使用以下步骤删除 VM：

1. 从左侧菜单中，选择“资源组”
1. 选择资源组，如 myResourceGroup。
1. 选择 VM（如 myVM），然后选择“删除”。 选择“是”以确认删除资源。 删除 VM 需要几秒钟的时间。
1. 删除 VM 后，选择操作系统磁盘、网络接口卡以及具有 myVM- 前缀的任何其他资源并将其删除。

## <a name="troubleshoot-domain-join-issues"></a>排除域加入问题

Windows Server VM 应成功加入托管域，加入方式与常规本地计算机加入 Active Directory 域服务域的方式相同。 如果 Windows Server VM 无法加入托管域，则表明存在与连接或凭据相关的问题。 请查看以下故障排除部分以成功加入托管域。

### <a name="connectivity-issues"></a>连接问题

如果你没有收到要求使用凭据加入域的提示，则存在连接问题。 VM 无法访问虚拟网络上的托管域。

请尝试执行每个故障排除步骤后，再次将 Windows Server VM 加入托管域。

* 请验证 VM 是否已连接到启用 Azure AD DS 的同一虚拟网络，或者是否具有对等网络连接。
* 请尝试 ping 托管域的 DNS 域名，例如 `ping aaddscontoso.com`。
    * 如果 ping 请求失败，请尝试 ping 托管域的 IP 地址，例如 `ping 10.0.0.4`。 从 Azure 资源列表中选择托管域时，环境的 IP 地址将显示在“属性”页面上。
    * 如果能够 ping 通该 IP 地址，但无法 ping 通域，则表示 DNS 的配置可能不正确。 确认已将托管域的 IP 地址配置为虚拟网络的 DNS 服务器。
* 请尝试使用 `ipconfig /flushdns` 命令刷新虚拟机上的 DNS 解析程序缓存。

### <a name="credentials-related-issues"></a>与凭据相关的问题

如果你收到提示要求使用凭据来加入域，但在输入这些凭据后出现错误，那么 VM 仍可连接到托管域。 你提供的凭据不会让 VM 加入托管域。

请尝试执行每个故障排除步骤后，再次将 Windows Server VM 加入托管域。

* 确保指定的用户帐户属于托管域。
* 确认该帐户属于托管域或 Azure AD 租户。 与 Azure AD 租户关联的外部目录的帐户无法在加入域的过程中正确进行身份验证。
* 请尝试使用 UPN 格式指定凭据。例如 `contosoadmin@aaddscontoso.onmicrosoft.com`。 如果租户中有多个用户具有相同的 UPN 前缀，或者 UPN 前缀过长，系统可能会自动生成帐户的 SAMAccountName。 在这些情况下，帐户的 SAMAccountName 格式可能不同于所需的格式或者在本地域中使用的格式。
* 检查托管域是否已[启用密码同步][password-sync]。 如果没有此配置步骤，托管域中将不会出现所需的密码哈希，因此无法正确验证登录尝试。
* 等待密码同步完成。 更改用户帐户的密码后，Azure AD 的自动后台同步将更新 Azure AD DS 中的密码。 密码需要一段时间才能用于加入域。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 创建 Windows Server VM
> * 连接到 Azure 虚拟网络的 Windows Server VM
> * 将 VM 加入托管域

若要管理托管域，请使用 Active Directory 管理中心 (ADAC) 配置管理 VM。

> [!div class="nextstepaction"]
> [在管理 VM 上安装管理工具](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: ./tutorial-create-instance.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension