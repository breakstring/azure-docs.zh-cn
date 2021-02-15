---
title: 有关 Azure 中 Windows VM 的常见问题解答
description: 解答通过 Resource Manager 模型创建 Windows 虚拟机的一些常见问题。
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: eaa3f56389e8cc101b57aefce833266fa20f66f7
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197466"
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>有关 Windows 虚拟机的常见问题
本文讨论了在 Azure 中使用 Resource Manager 部署模型创建的 Windows 虚拟机的一些常见问题。 有关本主题的 Linux 版本，请参阅[有关 Linux 虚拟机的常见问题](../linux/faq.md)。

## <a name="what-can-i-run-on-an-azure-vm"></a>我可以在 Azure VM 上运行什么程序？
所有订户都可以在 Azure 虚拟机上运行服务器软件。 有关在 Azure 中运行 Microsoft 服务器软件的支持策略的信息，请参阅 [Microsoft 服务器软件对 Azure 虚拟机的支持](https://support.microsoft.com/kb/2721672)。

MSDN Azure 权益订户以及 MSDN 开发和测试即用即付订户可使用某些版本的 Windows 7、Windows 8.1 和 Windows 10 执行开发和测试任务。 有关详细信息（包括说明和限制），请参阅[适用于 MSDN 订户的 Windows 客户端映像](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/)。 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>使用虚拟机时，我可以使用多少存储？
每个数据磁盘的容量高达 32,767 GiB。 可以使用的数据磁盘数取决于虚拟机大小。 有关详细信息，请参阅[虚拟机大小](../sizes.md)。

Azure 托管磁盘是推荐用于 Azure 虚拟机的磁盘存储产品，方便永久存储数据。 可对每个虚拟机使用多个托管磁盘。 托管磁盘提供两种类型的持久存储选项：高级和标准托管磁盘。 有关定价信息，请参阅[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks)。

Azure 存储帐户还可为操作系统磁盘和任何数据磁盘提供存储空间。 每个磁盘都是一个 .vhd 文件，以页 blob 形式存储。 有关定价详细信息，请参阅 [Storage Pricing Details](https://azure.microsoft.com/pricing/details/storage/)（存储定价详细信息）。

## <a name="how-can-i-access-my-virtual-machine"></a>如何访问我的虚拟机？
使用适用于 Windows VM 的远程桌面连接 (RDP) 建立远程连接。 有关说明，请参阅[如何连接并登录到运行 Windows 的 Azure 虚拟机](connect-logon.md)。 除非将服务器配置为远程桌面服务会话主机，否则最多支持两个并发连接。  

如果在使用远程桌面时遇到问题，请参阅[排查连接到基于 Windows 的 Azure 虚拟机时的远程桌面连接问题](../troubleshooting/troubleshoot-rdp-connection.md?toc=/azure/virtual-machines/windows/toc.json)。 

如果熟悉 Hyper-V，可以查找与 VMConnect 类似的工具。 Azure 不提供类似工具，因为不支持通过控制台访问虚拟机。

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>我是否可以使用临时磁盘（默认为 D: 驱动器）存储数据？
不要使用临时磁盘来存储数据。 它只是临时存储空间，因此存在丢失数据且数据不能恢复的风险。 将虚拟机移到另一主机时，可能会发生数据丢失的情况。 调整虚拟机大小、更新主机、主机硬件故障等都是需要移动虚拟机的原因。

如果有应用程序需要使用 D: 驱动器号，可以重新分配驱动器号以便临时磁盘使用除 D: 以外的位置。 有关说明，请参阅[更改 Windows 临时磁盘的驱动器号](change-drive-letter.md)。


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>如何更改临时磁盘的驱动器号？
可以通过移动页面文件和重新分配驱动器号来更改驱动器号，但需确保按特定顺序执行这些步骤。 有关说明，请参阅[更改 Windows 临时磁盘的驱动器号](change-drive-letter.md)。

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>可否将现有 VM 添加到可用性集？
否。 如果希望将 VM 作为可用性集的一部分，则需要在该可用性集中创建 VM。 目前，不支持在创建 VM 后将其添加到可用性集。

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>可否将虚拟机上传到 Azure？
是的。 有关说明，请参阅[将本地 VM 迁移到 Azure](on-prem-to-azure.md)。

## <a name="can-i-resize-the-os-disk"></a>可否调整 OS 磁盘的大小？
是的。 有关说明，请参阅 [How to expand the OS drive of a Virtual Machine in an Azure Resource Group](expand-os-disk.md)（如何扩展 Azure 资源组中虚拟机的 OS 驱动器）。

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>可否复制或克隆现有的 Azure VM？
是的。 借助托管映像，可创建虚拟机的映像，然后使用该映像生成多个新的 VM。 有关说明，请参阅[创建 VM 的自定义映像](tutorial-custom-images.md)。

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>为什么在 Azure Resource Manager 中看不到加拿大中部和加拿大东部区域？

针对现有 Azure 订阅创建的虚拟机不会自动注册到加拿大中部和加拿大东部这两个新区域。 通过 Azure 门户使用 Azure Resource Manager 将虚拟机部署到其他任何区域时，将自动完成此注册。 将虚拟机部署到其他任何 Azure 区域后，新区域可供后续虚拟机使用。

## <a name="does-azure-support-linux-vms"></a>Azure 可否支持 Linux VM？
是的。 若要快速创建 Linux VM 进行试用，请参阅[使用门户在 Azure 上创建 Linux VM](../linux/quick-create-portal.md)。

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>创建 VM 后能否向 VM 添加 NIC？
能，目前可行。 首先需停止解除分配 VM。 然后便可添加或删除 NIC（除非它是 VM 上的最后一个 NIC）。 

## <a name="are-there-any-computer-name-requirements"></a>是否有任何计算机名称要求？
是的。 计算机名称的最大长度为 15 个字符。 有关命名资源的详细信息，请参阅[命名约定规则和限制](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)。

## <a name="are-there-any-resource-group-name-requirements"></a>是否存在资源组名称要求？
是的。 资源组名称的最大长度为 90 个字符。 有关资源组的详细信息，请参阅[命名约定规则和限制](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)。

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>创建 VM 时，用户名有什么要求？

用户名最长为 20 个字符，不能以句点（“.”）结尾。 

不允许使用以下用户名：

- `1`
- `123`
- `a`
- `actuser`
- `adm`
- `admin`
- `admin1`
- `admin2`
- `administrator`
- `aspnet`
- `backup`
- `console`
- `david`
- `guest`
- `john`
- `owner`
- `root`
- `server`
- `sql`
- `support_388945a0`
- `support`
- `sys`
- `test`
- `test1`
- `test2`
- `test3`
- `user`
- `user1`
- `user2`

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>创建 VM 时，密码有什么要求？

根据所使用的工具，有不同的密码长度要求：
 - 门户 - 12 到 72 个字符之间
 - PowerShell - 8 到 123 个字符之间
 - CLI - 12 到 123 个字符之间

* 具有小写字符
* 包含大写字符
* 包含一个数字
* 具有特殊字符（正则表达式匹配 [\W_]）

不允许使用以下密码：

<table>
    <tr>
        <td>abc@123</td>
        <td>iloveyou!</td>
        <td>P@$$w0rd</td>
        <td>P@ssw0rd</td>
        <td>P@ssword123</td>
    </tr>
    <tr>
        <td>Pa$$word</td>
        <td>pass@word1</td>
        <td>Password!</td>
        <td>Password1</td>
        <td>Password22</td>
    </tr>
</table>