---
title: Azure HPC 缓存先决条件
description: 使用 Azure HPC 缓存的先决条件
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 11/05/2020
ms.author: v-erkel
ms.openlocfilehash: a31aee3f4548d3137fa1241aaa3a0f6171cf6895
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412504"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Azure HPC 缓存的先决条件

使用 Azure 门户创建新的 Azure HPC 缓存之前，请确保你的环境满足这些要求。

## <a name="video-overviews"></a>视频概述

观看这些视频，快速概述系统组件以及需要协同工作的组件。

 (单击视频图像或观看 ) 的链接。

* [工作原理](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/) -介绍 Azure HPC 缓存如何与存储和客户端交互

  [![视频缩略图： Azure HPC 缓存：工作原理 (单击以访问视频页面) ](media/video-2-components.png)](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/)  

* [先决条件](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/) -介绍 NAS 存储、Azure Blob 存储、网络访问和客户端访问的要求

  [![视频缩略图： Azure HPC 缓存：必备项 (单击以访问视频页面) ](media/video-3-prerequisites.png)](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/)

有关具体建议，请阅读本文的其余部分。

## <a name="azure-subscription"></a>Azure 订阅

建议使用付费订阅。

## <a name="network-infrastructure"></a>网络基础结构

应设置两个与网络相关的先决条件，然后才能使用缓存：

* Azure HPC 缓存实例的专用子网
* DNS 支持，以便缓存可以访问存储和其他资源

### <a name="cache-subnet"></a>缓存子网

Azure HPC 缓存需要具有以下特性的专用子网：

* 子网必须具有至少64的可用 IP 地址。
* 子网不能托管任何其他 Vm，甚至是适用于客户端计算机的相关服务。
* 如果使用多个 Azure HPC 缓存实例，则每个实例都需要其自己的子网。

最佳做法是为每个缓存创建一个新的子网。 创建缓存时，可以创建新的虚拟网络和子网。

### <a name="dns-access"></a>DNS 访问

缓存需要 DNS 来访问其虚拟网络外部的资源。 根据所使用的资源，可能需要设置自定义 DNS 服务器并配置该服务器与 Azure DNS 服务器之间的转发：

* 若要访问 Azure Blob 存储终结点和其他内部资源，需要基于 Azure 的 DNS 服务器。
* 若要访问本地存储，需配置可解析存储主机名的自定义 DNS 服务器。 在创建缓存 **之前** ，必须执行此操作。

如果只需要访问 Blob 存储，则可以使用 Azure 提供的默认 DNS 服务器作为缓存。 但是，如果需要访问其他资源，则应创建自定义 DNS 服务器并将其配置为将任何特定于 Azure 的解析请求转发到 Azure DNS 服务器。

若要使用自定义 DNS 服务器，你需要在创建缓存之前执行以下设置步骤：

* 创建将承载 Azure HPC 缓存的虚拟网络。
* 创建 DNS 服务器。
* 将 DNS 服务器添加到缓存的虚拟网络中。

  按照以下步骤将 DNS 服务器添加到 Azure 门户中的虚拟网络：

  1. 在 Azure 门户中打开虚拟网络。
  1. 从侧栏的 " **设置** " 菜单中选择 " **DNS 服务器** "。
  1. 选择“自定义”
  1. 在字段中输入 DNS 服务器的 IP 地址。

简单的 DNS 服务器还可用于对所有可用缓存装入点中的客户端连接进行负载均衡。

若要详细了解 azure 虚拟网络中的资源，请参阅 [名称解析中的](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)azure 虚拟网络和 DNS 服务器配置。

## <a name="permissions"></a>权限

请先检查与权限相关的先决条件，然后再开始创建缓存。

* 缓存实例需要能够创建 (Nic) 的虚拟网络接口。 创建缓存的用户必须在订阅中具有足够的权限才能创建 Nic。

* 如果使用 Blob 存储，Azure HPC 缓存需要授权才能访问存储帐户。 使用 azure RBAC)  (Azure 基于角色的访问控制，以授予缓存对 Blob 存储的访问权限。 需要两个角色：存储帐户参与者和存储 Blob 数据参与者。

  按照 [添加存储目标](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) 中的说明添加角色。

## <a name="storage-infrastructure"></a>存储基础结构

缓存支持 Azure Blob 容器或 NFS 硬件存储导出。 创建缓存后，添加存储目标。

每个存储类型都具有特定的先决条件。

### <a name="blob-storage-requirements"></a>Blob 存储要求

如果要将 Azure Blob 存储与缓存一起使用，则需要一个兼容的存储帐户，以及一个使用 Azure HPC 缓存格式化数据填充的空 Blob 容器或容器，如 [将数据移动到 Azure Blob 存储](hpc-cache-ingest.md)中所述。

尝试添加存储目标之前，请先创建帐户。 添加目标时，可以创建新的容器。

若要创建兼容的存储帐户，请使用以下设置：

* 性能： **标准**
* 帐户类型： **StorageV2 (常规用途 v2)**
* 复制： **本地冗余存储 (LRS)**
*  (默认) 的访问层： **热**

最好使用与缓存位于同一位置的存储帐户。

还必须为缓存应用程序授予对 Azure 存储帐户的访问 [权限](#permissions)，如上文所述。 按照 [添加存储目标](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) 中的过程为缓存提供所需的访问角色。 如果你不是存储帐户所有者，请让所有者执行此步骤。

### <a name="nfs-storage-requirements"></a>NFS 存储要求
<!-- linked from configuration.md -->

如果使用的是 NFS 存储系统 (例如，本地硬件 NAS 系统) ，请确保它满足这些要求。 你可能需要与存储系统 (或数据中心) 的网络管理员或防火墙管理器合作来验证这些设置。

> [!NOTE]
> 如果缓存对 NFS 存储系统的访问权限不足，则存储目标创建将失败。

有关 [解决 NAS 配置和 NFS 存储目标问题](troubleshoot-nas.md)的详细信息。

* **网络连接：** Azure HPC 缓存需要在缓存子网和 NFS 系统的数据中心之间进行高带宽的网络访问。 建议使用[ExpressRoute](../expressroute/index.yml)或类似的访问。 如果使用的是 VPN，你可能需要将其配置为将 TCP MSS 固定在1350，以确保不会阻止较大的数据包。 阅读 [vpn 数据包大小限制](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) ，了解有关 vpn 设置疑难解答的详细信息。

* **端口访问：** 缓存需要访问存储系统上的特定 TCP/UDP 端口。 不同类型的存储具有不同的端口要求。

  若要检查存储系统的设置，请按照此过程操作。

  * `rpcinfo`向存储系统发出命令以检查所需的端口。 以下命令列出了这些端口，并在表中设置相关结果的格式。  (使用系统的 IP 地址来代替 *<storage_IP>* 术语。 ) 

    你可以从安装了 NFS 基础结构的任何 Linux 客户端发出此命令。 如果在群集子网中使用客户端，它还可以帮助验证子网和存储系统之间的连接。

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  请确保查询返回的所有端口都 ``rpcinfo`` 允许来自 AZURE HPC 缓存的子网的无限制流量。

  * 如果无法使用 `rpcinfo` 命令，请确保这些常用端口允许入站和出站流量：

    | 协议 | 端口  | 服务  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | mountd   |
    | TCP/UDP  | 4047  | status   |

    某些系统对这些服务使用不同的端口号，请参阅存储系统的文档以确保。

  * 检查防火墙设置，以确保它们允许所有这些所需端口上的流量。 请确保检查 Azure 中使用的防火墙以及数据中心内的本地防火墙。

* **目录访问：**`showmount`在存储系统中启用命令。 Azure HPC 缓存使用此命令来检查存储目标配置是否指向有效导出，并确保多个装载不会访问相同的子目录 () 发生文件冲突的风险。

  > [!NOTE]
  > 如果你的 NFS 存储系统使用 NetApp 的 ONTAP 9.2 操作系统，请 **不要启用 `showmount`** 。 [请与 Microsoft 服务和支持部门联系](hpc-cache-support-ticket.md) 以获取帮助。

  有关目录列表访问权限的详细信息，请参阅 NFS 存储目标 [故障排除一文](troubleshoot-nas.md#enable-export-listing)。

* **根访问** (读/写) ：缓存以用户 ID 0 的形式连接到后端系统。 检查存储系统上的这些设置：
  
  * 启用 `no_root_squash`。 此选项可确保远程根用户可以访问 root 所拥有的文件。

  * 选中 "导出策略"，确保它们不包括缓存子网中的根访问限制。

  * 如果你的存储具有作为另一个导出的子目录的任何导出，请确保缓存对路径的最小段具有根访问权限。 有关详细信息，请参阅 NFS 存储目标故障排除一文中 [目录路径上的根访问权限](troubleshoot-nas.md#allow-root-access-on-directory-paths) 。

* NFS 后端存储必须是兼容的硬件/软件平台。 有关详细信息，请联系 Azure HPC 缓存团队。

## <a name="set-up-azure-cli-access-optional"></a>设置 Azure CLI 访问 (可选) 

如果要从 Azure 命令行接口创建或管理 Azure HPC 缓存 (Azure CLI) ，则需要安装 CLI 软件和 HPC 缓存扩展。 按照 [为 AZURE HPC 缓存设置 Azure CLI](az-cli-prerequisites.md)中的说明进行操作。

## <a name="next-steps"></a>后续步骤

* 从 Azure 门户[创建 AZURE HPC 缓存实例](hpc-cache-create.md)