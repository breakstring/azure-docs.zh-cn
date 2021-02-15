---
title: 使用 Azure Active Directory 凭据登录到 Linux VM
description: 了解如何创建和配置 Linux VM，以使用 Azure Active Directory 身份验证进行登录。
author: SanDeo-MSFT
ms.service: virtual-machines-linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: sandeo
ms.openlocfilehash: 3e50b6209c7790853158a1d2be2f42d625b6753b
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882121"
---
# <a name="preview-log-in-to-a-linux-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>预览：使用 Azure Active Directory 身份验证登录到 Azure 中的 Linux 虚拟机

若要改进 Azure 中 Linux 虚拟机 (VM) 的安全性，可以与 Azure Active Directory (AD) 身份验证集成。 将 Azure AD 身份验证用于 Linux VM 时，可以通过集中控制和强制实施策略来允许或拒绝对 VM 的访问。 本文介绍如何创建和配置 Linux VM，以便使用 Azure AD 身份验证。


> [!IMPORTANT]
> Azure Active Directory authentication 目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
> 请在测试后就丢弃的测试性虚拟机上使用此功能。
>


在 Azure 中使用 Azure AD 身份验证登录到 Linux VM 有许多好处，其中包括：

- **提高了安全性：**
  - 可以使用公司 AD 凭据登录到 Azure Linux VM。 不需创建本地管理员帐户并管理凭据生存期。
  - 减少对本地管理员帐户的依赖以后，就不需要担心凭据丢失/失窃、用户配置的凭据过弱，等等。
  - 针对 Azure AD 目录配置的密码复杂性和密码生存期策略也有助于维护 Linux VM 安全性。
  - 若要进一步确保 Azure 虚拟机的登录安全性，可以配置多重身份验证。
  - 使用 Azure Active Directory 登录 Linux VM 这一功能也适用于使用[联合身份验证服务](../../active-directory/hybrid/how-to-connect-fed-whatis.md)的客户。

- **无缝协作：** 使用基于 Azure 角色的访问控制 (Azure RBAC) ，你可以指定谁可以作为常规用户或使用管理员权限登录到给定的 VM。 当用户加入或离开团队时，可以更新 VM 的 Azure RBAC 策略，以根据需要授予访问权限。 与必须通过清理 VM 来删除不必要的 SSH 公钥相比，这种体验要简单得多。 员工在离开你的组织时，其用户帐户会被禁用或从 Azure AD 中删除，然后他们就再也不能访问你的资源。

## <a name="supported-azure-regions-and-linux-distributions"></a>受支持的 Azure 区域和 Linux 发行版

下面是目前（此功能的预览期间）受支持的 Linux 发行版：

| 分发 | 版本 |
| --- | --- |
| CentOS | CentOS 6，CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE Leap 42.3 |
| RedHat Enterprise Linux 7 | RHEL 6 和 RHEL 7 | 
| SUSE Linux 企业服务器 | SLES 12 |
| Ubuntu Server | Ubuntu 14.04 LTS、Ubuntu Server 16.04 和 Ubuntu Server 18.04 |


下面是目前（此功能的预览期间）受支持的 Azure 区域：

- 所有全局 Azure 区域

>[!IMPORTANT]
> 若要使用此预览版功能，请只部署受支持的 Linux 发行版，并且只在受支持的 Azure 区域部署。 此功能在 Azure 政府版或主权云中不受支持。
>
> 不支持在 Azure Kubernetes Service (AKS) 群集上使用此扩展。 有关详细信息，请参阅 [AKS 的支持策略](../../aks/support-policies.md)。


如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.31 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="network-requirements"></a>网络要求

若要为 Azure 中的 Linux Vm 启用 Azure AD 身份验证，需要确保 Vm 网络配置允许通过 TCP 端口443对以下终结点进行出站访问：

* https:\//login.microsoftonline.com
* https:\//login.windows.net
* https： \/ /device.login.microsoftonline.com
* https： \/ /pas.windows.net
* https:\//management.azure.com
* https： \/ /packages.microsoft.com

> [!NOTE]
> 目前，不能为启用了 Azure AD 身份验证的 Vm 配置 Azure 网络安全组。

## <a name="create-a-linux-virtual-machine"></a>创建 Linux 虚拟机

使用 [az group create](/cli/azure/group#az-group-create) 创建资源组，然后使用 [az vm create](/cli/azure/vm#az-vm-create) 创建 VM。这两项操作都是使用支持的发行版在支持的区域进行。 以下示例将使用 *Ubuntu 16.04 LTS* 的名为 *myVM* 的 VM 部署到 *southcentralus* 区域的名为 *myResourceGroup* 的资源组中。 在以下示例中，可以根据需要提供自己的资源组和 VM 名称。

```azurecli-interactive
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

创建 VM 和支持资源需要几分钟时间。

## <a name="install-the-azure-ad-login-vm-extension"></a>安装 Azure AD 登录 VM 扩展

> [!NOTE]
> 如果将此扩展部署到以前创建的 VM，请确保计算机至少分配了1GB 的内存，否则将无法安装扩展

若要使用 Azure AD 凭据登录到 Linux VM，请安装 Azure Active Directory 登录 VM 扩展。 VM 扩展是小型应用程序，可在 Azure 虚拟机上提供部署后配置和自动化任务。 请使用 [az vm extension set](/cli/azure/vm/extension#az-vm-extension-set) 在 *myResourceGroup* 资源组中名为 *myVM* 的 VM 上安装 *AADLoginForLinux* 扩展：

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

成功将扩展安装到 VM 后，会显示 "*成功*" 的 *provisioningState* 。 VM 需要运行的 VM 代理来安装扩展。 有关详细信息，请参阅 [VM 代理概述](../extensions/agent-windows.md)。

## <a name="configure-role-assignments-for-the-vm"></a>为 VM 配置角色分配

Azure RBAC) 策略的 azure 基于角色的访问控制 (确定可登录到 VM 的用户。 使用两个 Azure 角色来授权 VM 登录：

- **虚拟机管理员登录名**：分配了此角色的用户可以使用 Windows 管理员或 Linux root 用户权限登录到 Azure 虚拟机。
- **虚拟机用户登录名**：分配了此角色的用户可以使用常规用户权限登录到 Azure 虚拟机。

> [!NOTE]
> 若要允许用户通过 SSH 登录到 VM，必须分配“虚拟机管理员登录名”或“虚拟机用户登录名”角色。 “虚拟机管理员登录”和“虚拟机用户登录”角色使用 dataActions，因此无法在管理组范围内进行分配。 目前只能在订阅、资源组或资源范围中分配这些角色。 分配了 VM“所有者”或“参与者”角色的 Azure 用户不会自动获得通过 SSH 登录到 VM 的权限。 

下面的示例使用 [az role 赋值 create](/cli/azure/role/assignment#az-role-assignment-create) 为当前 Azure 用户将 *虚拟机管理员登录* 角色分配给 VM。 使用 [az account show](/cli/azure/account#az-account-show)获取活动 Azure 帐户的用户名，并使用 [az VM show](/cli/azure/vm#az-vm-show)将 *作用域* 设置为上一步中创建的 VM。 也可在资源组或订阅级别设置范围，这种情况下会应用正常的 Azure RBAC 继承权限。 有关详细信息，请参阅 [AZURE RBAC](../../role-based-access-control/overview.md)

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> 如果 AAD 域和登录用户名域不匹配，则必须使用 *--assignee-object-id* 指定用户帐户的对象 ID，而不是仅指定 *--assignee* 的用户名。 可以使用 [az ad user list](/cli/azure/ad/user#az-ad-user-list) 获取用户帐户的对象 ID。

有关如何使用 Azure RBAC 管理对 Azure 订阅资源的访问的详细信息，请参阅使用 [Azure CLI](../../role-based-access-control/role-assignments-cli.md)、 [Azure 门户](../../role-based-access-control/role-assignments-portal.md)或 [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)。

## <a name="using-conditional-access"></a>使用条件访问

在授权访问通过 Azure AD 登录启用的 Azure 中的 Linux Vm 之前，可以强制实施条件访问策略（如多重身份验证或用户登录风险检查）。 若要应用条件性访问策略，你必须从 "云应用" 或 "操作" 分配选项中选择 "Azure Linux VM 登录" 应用，然后将登录风险用作条件，并/或需要多重身份验证作为授权访问控制。 

> [!WARNING]
> VM 登录不支持每用户启用/强制执行的 Azure AD 多重身份验证。

## <a name="log-in-to-the-linux-virtual-machine"></a>登录到 Linux 虚拟机

首先，使用 [az vm show](/cli/azure/vm#az-vm-show) 查看 VM 的 公共 IP 地址：

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

使用 Azure AD 凭据登录到 Azure Linux 虚拟机。 可以通过 `-l` 参数指定自己的 Azure AD 帐户地址。 将示例帐户替换为自己的帐户。 应以全小写的形式输入帐户地址。 将示例 IP 地址替换为上一命令中 VM 的公共 IP 地址。

```console
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

系统会提示使用中的一次性代码登录到 Azure AD [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) 。 将一次性使用代码复制并粘贴到 "设备登录" 页。

出现提示时，请在登录页中输入 Azure AD 登录凭据。 

成功通过身份验证后，web 浏览器中会显示以下消息： `You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.`

关闭浏览器窗口，返回到 SSH 提示符窗口，然后按 **Enter** 键。 

现在，你已使用分配的角色权限（例如“VM 用户”或“VM 管理员”）登录到 Azure Linux 虚拟机。 如果为用户帐户分配了 *虚拟机管理员登录* 角色，则可以使用 `sudo` 来运行需要 root 权限的命令。

## <a name="sudo-and-aad-login"></a>Sudo 和 AAD 登录名

第一次运行 sudo 时，系统会要求你进行第二次身份验证。 如果不想再次进行身份验证就运行 sudo，则可以编辑 sudoers文件 `/etc/sudoers.d/aad_admins` 并将此行：

```bash
%aad_admins ALL=(ALL) ALL
```

替换为以下行：

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```


## <a name="troubleshoot-sign-in-issues"></a>排查登录问题

尝试将 SSH 与 Azure AD 凭据结合时，某些常见错误包括未分配任何 Azure 角色，并重复提示登录。 请根据以下部分的说明来纠正这些问题。

### <a name="access-denied-azure-role-not-assigned"></a>拒绝访问：未分配 Azure 角色

如果在 SSH 提示符下出现以下错误，请验证是否已为 VM 配置了 Azure RBAC 策略，该策略向用户授予 " *虚拟机管理员登录名* " 或 " *虚拟机用户登录* " 角色：

```output
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```
> [!NOTE]
> 如果在 Azure 角色分配方面遇到问题，请参阅[排查 Azure RBAC 的问题](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit)。

### <a name="continued-ssh-sign-in-prompts"></a>持续的 SSH 登录提示

如果你在 Web 浏览器中成功完成了身份验证步骤，系统会立即提示你使用新的代码再次登录。 出现此错误通常是由于在 SSH 提示符窗口中指定的登录名称与登录到 Azure AD 时使用的帐户不符。 若要纠正此问题，请执行以下操作：

- 验证在 SSH 提示符窗口中指定的登录名是否正确。 登录名拼写错误可能导致在 SSH 提示符窗口中指定的登录名称与登录到 Azure AD 时使用的帐户不符。 例如，你键入了 *azuresuer \@ contoso.onmicrosoft.com* 而不是 *azureuser \@ contoso.onmicrosoft.com*。
- 如果有多个用户帐户，请确保登录到 Azure AD 时在浏览器窗口中提供的用户帐户是相同的。
- Linux 是区分大小写的操作系统。 “Azureuser@contoso.onmicrosoft.com”和“azureuser@contoso.onmicrosoft.com”是不同的，会导致不匹配。 请确保在 SSH 提示符窗口中使用正确的大小写指定 UPN。

### <a name="other-limitations"></a>其他限制

当前不支持通过嵌套组或角色分配继承访问权限的用户。 必须直接向用户或组分配 [所需的角色分配](#configure-role-assignments-for-the-vm)。 例如，使用管理组或嵌套组角色分配不会授予允许用户登录的正确权限。

## <a name="preview-feedback"></a>预览功能反馈

在[Azure AD 反馈论坛](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上，分享有关此预览功能的反馈或使用此功能的报告问题

## <a name="next-steps"></a>后续步骤

有关 Azure Active Directory 的详细信息，请参阅[什么是 Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)