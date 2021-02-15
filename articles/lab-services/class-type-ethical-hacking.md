---
title: 使用 Azure 实验室服务设置道德黑客实验室 |Microsoft Docs
description: 了解如何使用 Azure 实验室服务设置实验室来讲授道德黑客。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 97cdf13f39cc73ee7f35fb402229469195f1456c
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97616416"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>设置实验室来讲授道德攻击类

本文介绍如何设置一个侧重于道德攻击辩论方的类。 渗透测试是道德黑客社区使用的一种做法，当某人试图获得对系统或网络的访问权限以证明恶意攻击者可能利用的漏洞时，就会进行渗透测试。

在道德黑客课程中，学生可以学习抵御漏洞的新式技术。 每个学生都获得一个 Windows Server 主机虚拟机，它包含两个嵌套虚拟机 - 一个是带有 [Metasploitable3](https://github.com/rapid7/metasploitable3) 映像的虚拟机，另一个是带有 [Kali Linux](https://www.kali.org/) 映像的虚拟机。 Metasploitable 虚拟机用于开发目的，Kali 虚拟机提供对执行取证任务所需的工具的访问。

本文包含两个主要部分。 第一部分介绍如何创建教室实验室。 第二部分介绍如何创建启用了嵌套虚拟化的模板计算机以及所需的工具和映像。 在这种情况下，计算机上的 Metasploitable 映像和 Kali Linux 映像已启用 Hyper-v 以承载映像。

## <a name="lab-configuration"></a>实验室配置

若要设置此实验室，你需要具有一个 Azure 订阅才能开始。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 获取 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户或使用现有帐户。 请参阅以下教程，了解如何创建新的实验室帐户： [设置实验室帐户教程](tutorial-setup-lab-account.md)。

按照 [本教程](tutorial-setup-classroom-lab.md) 创建新的实验室，然后应用以下设置：

| 虚拟机大小 | 图像 |
| -------------------- | ----- |
| 中型 (嵌套虚拟化)  | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>模板计算机

创建模板计算机后，启动计算机并连接到该计算机以完成以下三个主要任务。

1. 设置计算机以进行嵌套虚拟化。 它将启用所有适当的 windows 功能（如 Hyper-v），并设置网络，以便 Hyper-v 映像能够彼此通信和 internet。
2. 设置 [Kali](https://www.kali.org/) Linux 映像。 Kali 是一个 Linux 分发版，其中包含用于渗透测试和安全审核的工具。
3. 设置 Metasploitable 映像。 在此示例中，将使用 [Metasploitable3](https://github.com/rapid7/metasploitable3) 图像。 将创建此映像以故意产生安全漏洞。

本文的其余部分将介绍完成上述任务的手动步骤。  或者，你可以运行 [实验室服务 Hyper-v 脚本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) 和 [实验室服务的道德黑客脚本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking)。

### <a name="prepare-template-machine-for-nested-virtualization"></a>准备用于嵌套虚拟化的模板计算机

按照说明 [启用嵌套虚拟化](how-to-enable-nested-virtualization-template-vm.md) ，以便为嵌套虚拟化准备模板虚拟机。

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>使用 Kali Linux 映像设置嵌套虚拟机

Kali 是一个 Linux 分发版，其中包含用于渗透测试和安全审核的工具。

1. 从 [攻击性 Security Kali LINUX VM 映像](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/)下载映像。  请记住 "下载" 页上注明的默认用户名和密码。
    1. 下载适用于 VMware 的 **Kali Linux VMware 64 位 (7z)** 映像。
    1. 提取7z 文件。  如果还没有 7 zip，请从下载 [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html) 。 记住提取文件夹的位置，因为稍后需要用到它。
1. 将提取的 vmdk 文件转换为 vhdx 文件，以便可以将 vhdx 文件与 Hyper-v 配合使用。 有几种工具可用于将 VMware 映像转换为 Hyper-v 映像。  我们将使用 [STARWIND V2V 转换器](https://www.starwindsoftware.com/starwind-v2v-converter)。  若要下载，请参阅 [STARWIND V2V 转换器下载页](https://www.starwindsoftware.com/starwind-v2v-converter#download)。
    1. 启动 **STARWIND V2V 转换器**。
    1. 在 " **选择要转换的图像位置** " 页上，选择 " **本地文件**"。  选择“**下一页**”。
    1. 在 " **源映像** " 页上，导航到并选择在上一步中为 " **文件名** " 设置提取的 Kali Linux vmdk 文件。  该文件的格式将为 Kali-Linux-{version}-vmware-amd64。  选择“**下一页**”。
    1. 在 " **选择目标映像位置**" 中，选择 " **本地文件**"。  选择“**下一页**”。
    1. 在 " **选择目标映像格式** " 页上，选择 " **VHD/VHDX**"。  选择“**下一页**”。
    1. 在 " **为 VHD/VHDX 映像格式选择选项** " 页上，选择 " **VHDX 可扩充映像**"。  选择“**下一页**”。
    1. 在 " **选择目标文件名** " 页上，接受默认文件名。  选择“转换”。
    1. 在 " **转换** " 页上，等待图像转换。  这可能需要几分钟。  完成转换后，选择 " **完成** "。
1. 创建新的 Hyper-v 虚拟机。
    1. 打开 **Hyper-v 管理器**。
    1. 选择 "**操作**" "  ->  **新建**  ->  **虚拟机**"。
    1. 在 **新建虚拟机向导** 的 "**开始之前**" 页上，选择 "**下一步**"。
    1. 在 " **指定名称和位置** " 页上，输入 **Kali-Linux** 作为 **名称**，然后选择 " **下一步**"。
    1. 在 " **指定生成** " 页上，接受默认值，然后选择 " **下一步**"。
    1. 在 "**分配内存**" 页上，为 **启动内存** 输入 **2048 MB** ，然后选择 "**下一步**"。
    1. 在 " **配置网络** " 页上，将 "连接" 保留为 " **未连接**"。 稍后将设置网络适配器。
    1. 在 " **连接虚拟硬盘** " 页上，选择 " **使用现有虚拟硬盘**"。 浏览到在上一步中创建的 **Kali-Linux-{version}-vmware-amd64** 文件的位置，然后选择 " **下一** 步"。
    1. 在 " **正在完成新建虚拟机向导** " 页上，选择 " **完成**"。
    1. 创建虚拟机后，请在 Hyper-v 管理器中选择它。 不要打开计算机。  
    1. 选择 "**操作**  ->  **设置**"。
    1. 在的 " **Kali 的设置** " 对话框中，选择 " **添加硬件**"。
    1. 选择 " **旧版网络适配器**"，然后选择 " **添加**"。
    1. 在 " **旧版网络适配器** " 页上，选择 " **LabServicesSwitch** " 作为 " **虚拟交换机** " 设置，然后选择 **"确定"**。 为 " **嵌套虚拟化的准备模板** " 部分中的 hyper-v 准备模板计算机时创建了 LabServicesSwitch。
    1. Kali-Linux 映像现在已可供使用。 从 **hyper-v 管理器** 中，选择 "**操作**  ->  " "**启动**"，然后选择 "**操作**  ->  **连接**" 以连接到虚拟机。  默认用户名为 **kali** ，密码为 **kali**。

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>使用 Metasploitable 映像设置嵌套 VM  

Rapid7 Metasploitable 映像是特意配置了安全漏洞的映像。 将使用此映像来测试和查找问题。 以下说明介绍了如何使用预先创建的 Metasploitable 映像。 但是，如果需要更新版本的 Metasploitable 映像，请参阅 [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3) 。

1. 下载 Metasploitable 映像。
    1. 导航到 [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html)。 填写表单以下载图像，然后选择 " **提交** " 按钮。
    2. 选择 " **立即下载 Metasploitable** " 按钮。
    3. 下载 zip 文件时，提取 zip 文件，并记住 Metasploitable 文件的位置。
1. 将提取的 vmdk 文件转换为 vhdx 文件，以便可以将 vhdx 文件与 Hyper-v 配合使用。 有几种工具可用于将 VMware 映像转换为 Hyper-v 映像。  我们将再次使用 [STARWIND V2V 转换器](https://www.starwindsoftware.com/starwind-v2v-converter) 。  若要下载，请参阅 [STARWIND V2V 转换器下载页](https://www.starwindsoftware.com/starwind-v2v-converter#download)。
    1. 启动 **STARWIND V2V 转换器**。
    1. 在 " **选择要转换的图像位置** " 页上，选择 " **本地文件**"。  选择“**下一页**”。
    1. 在 " **源映像** " 页上，导航到并选择在上一步中为 " **文件名** " 设置解压缩的 Metasploitable。  选择“**下一页**”。
    1. 在 " **选择目标映像位置**" 中，选择 " **本地文件**"。  选择“**下一页**”。
    1. 在 " **选择目标映像格式** " 页上，选择 " **VHD/VHDX**"。  选择“**下一页**”。
    1. 在 " **为 VHD/VHDX 映像格式选择选项** " 页上，选择 " **VHDX 可扩充映像**"。  选择“**下一页**”。
    1. 在 " **选择目标文件名** " 页上，接受默认文件名。  选择“转换”。
    1. 在 " **转换** " 页上，等待图像转换。  这可能需要几分钟。  完成转换后，选择 " **完成** "。
1. 创建新的 Hyper-v 虚拟机。
    1. 打开 **Hyper-v 管理器**。
    1. 选择 "**操作**" "  ->  **新建**  ->  **虚拟机**"。
    1. 在 **新建虚拟机向导** 的 "**开始之前**" 页上，选择 "**下一步**"。
    1. 在 " **指定名称和位置** " 页上，输入 **Metasploitable** 作为 **名称**，然后选择 " **下一步**"。

        ![新建 VM 映像向导](./media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. 在 " **指定生成** " 页上，接受默认值，然后选择 " **下一步**"。
    1. 在 "**分配内存**" 页上，为 **启动内存** 输入 **512 MB** ，然后选择 "**下一步**"。

        ![“分配内存”页](./media/class-type-ethical-hacking/assign-memory-page.png)
    1. 在 " **配置网络** " 页上，将 "连接" 保留为 " **未连接**"。 稍后将设置网络适配器。
    1. 在 " **连接虚拟硬盘** " 页上，选择 " **使用现有虚拟硬盘**"。 浏览到在上一步中创建的 **metasploitable** 文件的位置，然后选择 " **下一** 步"。

        ![连接虚拟网络磁盘页](./media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. 在 " **正在完成新建虚拟机向导** " 页上，选择 " **完成**"。
    1. 创建虚拟机后，请在 Hyper-v 管理器中选择它。 不要打开计算机。  
    1. 选择 "**操作**  ->  **设置**"。
    1. 在的 " **Metasploitable 设置** " 对话框中，选择 " **添加硬件**"。
    1. 选择 " **旧版网络适配器**"，然后选择 " **添加**"。

        ![网络适配器页](./media/class-type-ethical-hacking/network-adapter-page.png)
    1. 在 " **旧版网络适配器** " 页上，选择 " **LabServicesSwitch** " 作为 " **虚拟交换机** " 设置，然后选择 **"确定"**。 为 " **嵌套虚拟化的准备模板** " 部分中的 hyper-v 准备模板计算机时创建了 LabServicesSwitch。

        ![旧版网络适配器页](./media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. Metasploitable 映像现在已可供使用。 从 **hyper-v 管理器** 中，选择 "**操作**  ->  " "**启动**"，然后选择 "**操作**  ->  **连接**" 以连接到虚拟机。  默认用户名为 **msfadmin** ，密码为 **msfadmin**。

该模板现已更新，并具有符合道德的黑客渗透测试类所需的图像、包含用于执行渗透测试的工具的映像，以及另一个包含安全漏洞的映像。 现在可以将模板映像发布到类。 选择模板页上的“发布”按钮，将模板发布到实验室。

## <a name="cost"></a>成本  

如果要估计此实验室的成本，可以使用以下示例：

如果一个班级有 25 个学生，计划的课堂时间为 20 小时且家庭作业或作业时数配额为 10 小时，则实验室的价格为：

25名学生 \* (20 多个) 小时 \* 55，实验室单位 \* 0.01 美元/小时 = 412.50 美元

>[!IMPORTANT]
>成本估算仅用于示例目的。 有关定价的最新详细信息，请参阅 [Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>结束语

本文指导你完成为道德攻击类创建实验室的步骤。 它包括设置嵌套虚拟化的步骤，用于在主机虚拟机内创建两个虚拟机以进行渗透测试。

## <a name="next-steps"></a>后续步骤

接下来的步骤是设置任何实验室时通用的：

- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置日程安排](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [通过电子邮件将注册链接发送给学生](how-to-configure-student-usage.md#send-invitations-to-users)。
