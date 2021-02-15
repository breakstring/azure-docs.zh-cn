---
title: 适用于 Linux 的 Azure N 系列 GPU 驱动程序安装
description: 如何为 Azure 中运行 Linux 的 N 系列 VM 安装 NVIDIA GPU 驱动程序
services: virtual-machines-linux
author: vikancha-MSFT
ms.service: virtual-machines-linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: vikancha
ms.openlocfilehash: 22c7a70379649876de4af88080543438e58998a6
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746639"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>在运行 Linux 的 N 系列 VM 上安装 NVIDIA GPU 驱动程序

若要利用 NVIDIA GPU 支持的 Azure N 系列 VM 的 GPU 功能，必须安装 NVIDIA GPU 驱动程序。 [NVIDIA GPU 驱动程序扩展](../extensions/hpccompute-gpu-linux.md)可在 N 系列 VM 上安装适当的 NVIDIA CUDA 或 GRID 驱动程序。 请使用 Azure 门户或工具（例如 Azure CLI 或 Azure 资源管理器模板）安装或管理该扩展。 有关受支持的分发版和部署步骤，请参阅 [NVIDIA GPU 驱动程序扩展文档](../extensions/hpccompute-gpu-linux.md)。

如果选择手动安装 NVIDIA GPU 驱动程序，本文提供受支持的分发版、驱动程序以及安装和验证步骤。 针对 [Windows VM](../windows/n-series-driver-setup.md) 也提供了驱动程序手动安装信息。

有关 N 系列 VM 规格、存储容量和磁盘详细信息，请参阅 [GPU Linux VM 大小](../sizes-gpu.md?toc=/azure/virtual-machines/linux/toc.json)。 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>在 N 系列 VM 上安装 CUDA 驱动程序

从 NVIDIA CUDA 工具包在 N 系列 VM 上安装 CUDA 驱动程序的步骤如下。 


C 和 C++ 开发人员可以选择安装完整的工具包来生成 GPU 加速应用程序。 有关详细信息，请参阅 [CUDA 安装指南](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)。

要安装 CUDA 驱动程序，请建立到每个 VM 的 SSH 连接。 若要验证系统是否具有支持 CUDA 的 GPU，请运行以下命令：

```bash
lspci | grep -i NVIDIA
```
会看到类似于以下示例（显示 NVIDIA Tesla K80 卡）的输出：

![lspci 命令输出](./media/n-series-driver-setup/lspci.png)

然后，运行特定于分发的安装命令。

### <a name="ubuntu"></a>Ubuntu 

1. 从 NVIDIA 网站下载并安装 CUDA 驱动程序。 例如，对于 Ubuntu 16.04 LTS：
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb

   wget -O /tmp/${CUDA_REPO_PKG} https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

   sudo dpkg -i /tmp/${CUDA_REPO_PKG}

   sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo apt-get update

   sudo apt-get install cuda-drivers

   ```

   安装可能需要几分钟。

2. 若要安装完整的 CUDA 工具包，请键入：

   ```bash
   sudo apt-get install cuda
   ```

3. 重新启动 VM，并继续验证安装。

#### <a name="cuda-driver-updates"></a>CUDA 驱动程序更新

在部署后，建议定期更新 CUDA 驱动程序。

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS 或 Red Hat Enterprise Linux

1. 更新内核（建议）。 如果选择不更新内核，请确保 `kernel-devel` 和 `dkms` 的版本适合你的内核。

   ```
   sudo yum install kernel kernel-tools kernel-headers kernel-devel
  
   sudo reboot

2. Install the latest [Linux Integration Services for Hyper-V and Azure](https://www.microsoft.com/download/details.aspx?id=55106). Check if LIS is required by verifying the results of lspci. If all GPU devices are listed as expected, installing LIS is not required.

Skip this step if you plan to use CentOS 7.8(or higher) as LIS is no longer required for these versions.

Please note that LIS is applicable to Red Hat Enterprise Linux, CentOS, and the Oracle Linux Red Hat Compatible Kernel 5.2-5.11, 6.0-6.10, and 7.0-7.7. Please refer to the [Linux Integration Services documentation] (https://www.microsoft.com/en-us/download/details.aspx?id=55106) for more details. 

Skip this step if you are not using the Kernel versions listed above.

   ```bash
   wget https://aka.ms/lis
 
   tar xvzf lis
 
   cd LISISO
 
   sudo ./install.sh
 
   sudo reboot
   ```
 
3. 重新连接到 VM 并使用以下命令继续安装：

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

   sudo yum install dkms

   CUDA_REPO_PKG=cuda-repo-rhel7-10.0.130-1.x86_64.rpm

   wget https://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

   安装可能需要几分钟。 

4. 若要安装完整的 CUDA 工具包，请键入：

   ```bash
   sudo yum install cuda
   ```

5. 重新启动 VM，并继续验证安装。

### <a name="verify-driver-installation"></a>验证驱动程序安装

要查询 GPU 设备状态，请建立到 VM 的 SSH 连接，并运行与驱动程序一起安装的 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) 命令行实用工具。 

如果安装了驱动程序，将看到如下输出。 请注意，除非当前正在 VM 上运行 GPU 工作负荷，否则 GPU-Util 将显示 0%。 驱动程序版本和 GPU 详细信息可能与所示的内容不同。

![NVIDIA 设备状态](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>RDMA 网络连接

可以在支持 RDMA 的 N 系列 VM（例如 NC24r）上启用 RDMA 网络连接，这些 VM 部署在同一可用性集中或虚拟机 (VM) 规模集的单个放置组中。 对于使用 Intel MPI 5.x 或更高版本运行的应用程序，RDMA 网络支持消息传递接口 (MPI) 流量。 其他要求如下：

### <a name="distributions"></a>分发

在 N 系列 VM 上，在支持 RDMA 连接的 Azure 市场中，从以下映像之一部署支持 RDMA 的 N 系列 VM：
  
* **Ubuntu 16.04 LTS** - 在 VM 上配置 RDMA 驱动程序，并注册 Intel 下载 Intel MPI：

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **基于 CentOS 的 7.4 HPC** - 在 VM 上安装 RDMA 驱动程序和 Intel MPI 5.1。

* **基于 CentOS 的 HPC** - CentOS-HPC 7.6 及更高版本（适用于通过 SR-IOV 支持 InfiniBand 的 SKU）。 这些映像预安装了 Mellanox OFED 和 MPI 库。

> [!NOTE]
> 仅 Mellanox OFED 的 LTS 版本支持 CX3-Pro 卡。 在带有 ConnectX3-Pro 卡的 N 系列 VM 上使用 LTS Mellanox OFED 版本 (4.9-0.1.7.0)。 有关详细信息，请参阅 [Linux 驱动程序](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed)。
>
> 另外，某些最新的 Azure 市场 HPC 映像具有 Mellanox OFED 5.1 及更高版本，这些版本不支持 ConnectX3-Pro 卡。 请先检查 HPC 映像中的 Mellanox OFED 版本，然后再将其用于带有 ConnectX3-Pro 卡的 VM。
>
> 以下映像是支持 ConnectX3-Pro 卡的最新 CentOS-HPC 映像：
>
> - OpenLogic:CentOS-HPC:7.6:7.6.2020062900
> - OpenLogic:CentOS-HPC:7_6gen2:7.6.2020062901
> - OpenLogic:CentOS-HPC:7.7:7.7.2020062600
> - OpenLogic:CentOS-HPC:7_7-gen2:7.7.2020062601
> - OpenLogic:CentOS-HPC:8_1:8.1.2020062400
> - OpenLogic:CentOS-HPC:8_1-gen2:8.1.2020062401
>

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>在 NV 或 NVv3 系列 VM 上安装 GRID 驱动程序

若要在 NV 或 NVv3 系列 VM 上安装 NVIDIA GRID 驱动程序，请通过 SSH 连接到每个 VM，并执行 Linux 发行版的步骤。 

### <a name="ubuntu"></a>Ubuntu 

1. 运行 `lspci` 命令。 验证 NVIDIA M60 卡是否显示为 PCI 设备。

2. 安装更新。

   ```bash
   sudo apt-get update

   sudo apt-get upgrade -y

   sudo apt-get dist-upgrade -y

   sudo apt-get install build-essential ubuntu-desktop -y
   
   sudo apt-get install linux-azure -y
   ```
3. 禁用 Nouveau 内核驱动程序，该驱动程序与 NVIDIA 驱动程序不兼容。 （只能在 NV 或 NVv2 VM 上使用 NVIDIA 驱动程序。）若要执行此操作，请在 `/etc/modprobe.d` 中创建一个名为 `nouveau.conf` 的文件，其中包含以下内容：

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```


4. 重新启动 VM，并重新连接。 退出 X 服务器：

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. 下载并安装 GRID 驱动程序：

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. 当系统询问你是否要运行 nvidia-xconfig 实用程序以更新 X 配置文件时，请选择“是”。

7. 完成安装后，将 /etc/nvidia/gridd.conf.template 复制到位于 /etc/nvidia/ 的新文件 gridd.conf

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. 将下列内容添加到 `/etc/nvidia/gridd.conf`：
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE
   ```
   
9. 将以下内容从 `/etc/nvidia/gridd.conf` 中删除（如果其存在）：
 
   ```
   FeatureType=0
   ```
10. 重新启动 VM，并继续验证安装。


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS 或 Red Hat Enterprise Linux 

1. 更新内核和 DKMS（建议）。 如果选择不更新内核，请确保 `kernel-devel` 和 `dkms` 的版本适合你的内核。
 
   ```bash  
   sudo yum update
 
   sudo yum install kernel-devel
 
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
   sudo yum install dkms
   
   sudo yum install hyperv-daemons
   ```

2. 禁用 Nouveau 内核驱动程序，该驱动程序与 NVIDIA 驱动程序不兼容。  (仅使用 NV 或 NV3 Vm 上的 NVIDIA 驱动程序。 ) 为此，请使用 `/etc/modprobe.d` 以下内容在名为的文件中创建一个文件 `nouveau.conf` ：

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```
 
3. 重新启动 VM、重新进行连接并安装最新[适用于 Hyper-V 和 Azure 的 Linux 集成服务](https://www.microsoft.com/download/details.aspx?id=55106)。 通过验证 lspci 的结果来检查是否需要 LIS。 如果所有 GPU 设备都按预期列出，则不需要安装 .LIS。 

跳过此步骤是使用 CentOS/RHEL 7.8 和更高版本。
 
   ```bash
   wget https://aka.ms/lis

   tar xvzf lis

   cd LISISO

   sudo ./install.sh

   sudo reboot

   ```
 
4. 重新连接到 VM 并运行 `lspci` 命令。 验证 NVIDIA M60 卡是否显示为 PCI 设备。
 
5. 下载并安装 GRID 驱动程序：

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. 当系统询问你是否要运行 nvidia-xconfig 实用程序以更新 X 配置文件时，请选择“是”。

7. 完成安装后，将 /etc/nvidia/gridd.conf.template 复制到位于 /etc/nvidia/ 的新文件 gridd.conf
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. 将下列内容添加到 `/etc/nvidia/gridd.conf`：
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE 
   ```
9. 将以下内容从 `/etc/nvidia/gridd.conf` 中删除（如果其存在）：
 
   ```
   FeatureType=0
   ```
10. 重新启动 VM，并继续验证安装。


### <a name="verify-driver-installation"></a>验证驱动程序安装


要查询 GPU 设备状态，请建立到 VM 的 SSH 连接，并运行与驱动程序一起安装的 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) 命令行实用工具。 

如果安装了驱动程序，将看到如下输出。 请注意，除非当前正在 VM 上运行 GPU 工作负荷，否则 GPU-Util 将显示 0%。 驱动程序版本和 GPU 详细信息可能与所示的内容不同。

![屏幕截图，显示在查询 GPU 设备状态时的输出。](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11 服务器
如果需要使用 X11 服务器远程连接到 NV 或 NVv2 VM，建议使用 [x11vnc](http://www.karlrunge.com/x11vnc/)，因为它允许硬件图形加速。 必须手动将 M60 设备的 BusID 添加到 X11 配置文件（通常为 `etc/X11/xorg.conf`）中。 添加 `"Device"` 节，如下所示：
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
此外，更新 `"Screen"` 节以使用此设备。
 
通过运行以下命令可找到十进制 BusID

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
重新分配或重新启动 VM 后，BusID 可能会更改。 因此，重新启动 VM 后，可能需要创建脚本来更新 X11 配置中的 BusID。 例如，创建名为 `busidupdate.sh`（或所选的其他名称）的脚本，其内容如下所示：

```bash 
#!/bin/bash
XCONFIG="/etc/X11/xorg.conf"
OLDBUSID=`awk '/BusID/{gsub(/"/, "", $2); print $2}' ${XCONFIG}`
NEWBUSID=`nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'`

if [[ "${OLDBUSID}" == "${NEWBUSID}" ]] ; then
        echo "NVIDIA BUSID not changed - nothing to do"
else
        echo "NVIDIA BUSID changed from \"${OLDBUSID}\" to \"${NEWBUSID}\": Updating ${XCONFIG}" 
        sed -e 's|BusID.*|BusID          '\"${NEWBUSID}\"'|' -i ${XCONFIG}
fi
```

然后，在 `/etc/rc.d/rc3.d` 中为更新脚本创建一个条目，以便在启动时以 root 身份调用该脚本。

## <a name="troubleshooting"></a>疑难解答

* 可以使用 `nvidia-smi` 设置持久性模式，以便在需要查询卡时该命令的输出更快。 若要设置持久性模式，请执行 `nvidia-smi -pm 1`。 请注意，如果重启 VM，此模式设置将消失。 你可以始终将该模式设置编写为在启动时执行。
* 如果已将 NVIDIA CUDA 驱动程序更新到最新版本，并且发现 RDMA 连接不再工作，请[重新安装 RDMA 驱动程序](#rdma-network-connectivity)以重新建立该连接。 
* 如果某个 CentOS/RHEL OS 版本 (或内核) 不支持用于 .LIS，则会引发错误 "不支持的内核版本"。 请报告此错误以及 OS 和内核版本。

## <a name="next-steps"></a>后续步骤

* 若要捕获安装了 NVIDIA 驱动程序的 Linux VM 映像，请参阅[如何通用化和捕获 Linux 虚拟机](capture-image.md)。
