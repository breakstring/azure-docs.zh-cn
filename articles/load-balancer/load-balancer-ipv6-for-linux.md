---
title: 为 Linux VM 配置 DHCPv6
titleSuffix: Azure Load Balancer
description: 本文介绍如何为 Linux VM 配置 DHCPv6。
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, azure 负载均衡器, 双堆栈, 公共 ip, 本机 ipv6, 移动, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: allensu
ms.openlocfilehash: d8bd62bab627beb70a8fcba276bf8c2eca309c45
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006740"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>为 Linux VM 配置 DHCPv6


Azure 市场中的某些 Linux 虚拟机映像默认未配置动态主机配置协议版本 6 (DHCPv6)。 要支持 IPv6，必须在使用的 Linux OS 分发版中配置 DHCPv6。 各种 Linux 分发会以不同方式配置 DHCPv6，因为它们使用不同的包。

> [!NOTE]
> Azure 市场中最新的 SUSE Linux 和 CoreOS 映像已预先配置 DHCPv6。 使用这些映像不需要进行额外的更改。

本文档介绍如何启用 DHCPv6，使 Linux 虚拟机能够获取 IPv6 地址。

> [!WARNING]
> 不恰当地编辑网络配置文件的操作可能会使你失去 VM 的网络访问权限。 建议先在非生产系统上测试配置更改。 本文中的说明已根据 Azure 市场中的最新版 Linux 映像进行测试。 有关详细说明，请参阅适用于你所用 Linux 版本的文档。

## <a name="ubuntu"></a>Ubuntu

1. 编辑 /etc/dhcp/dhclient6.conf 文件，并添加以下行  ：

    ```config
    timeout 10;
    ```

2. 编辑使用以下配置的 eth0 接口的网络配置：

   * 在 Ubuntu 12.04 和 14.04 上编辑 /etc/network/interfaces.d/eth0.cfg 文件   。 
   * 在 Ubuntu 16.04 上编辑 /etc/network/interfaces.d/50-cloud-init.cfg 文件   。

    ```config
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. 续订 IPv6 地址：

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

从 Ubuntu 17.10 开始，默认网络配置机制是 [NETPLAN]( https://netplan.io)。  在安装/实例化时，NETPLAN 从以下位置的 YAML 配置文件中读取网络配置：/{lib,etc,run}/netplan/*.yaml。

请为配置中的每个以太网接口添加“dhcp6:true”  语句。  例如：

```config
network:
  version: 2
  ethernets:
    eno1:
      dhcp6: true
```

在早期启动过程中，netplan “network renderer”将配置写入“/run”，以将设备控制权移交给指定的网络守护程序。有关 NETPLAN 的参考信息，请参阅 https://netplan.io/reference 。
 
## <a name="debian"></a>Debian

1. 编辑 /etc/dhcp/dhclient6.conf 文件，并添加以下行  ：

    ```config
    timeout 10;
    ```

2. 编辑 /etc/network/interfaces 文件，添加以下配置  ：

    ```config
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. 续订 IPv6 地址：

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL、CentOS 和 Oracle Linux

1. 编辑 /etc/sysconfig/network 文件，添加以下参数  ：

    ```config
    NETWORKING_IPV6=yes
    ```

2. 编辑 /etc/sysconfig/network-scripts/ifcfg-eth0 文件，添加以下两个参数  ：

    ```config
    IPV6INIT=yes
    DHCPV6C=yes
    ```

3. 续订 IPv6 地址：

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 和 openSUSE 13

Azure 中最新的 SUSE Linux Enterprise Server (SLES) 和 openSUSE 映像已预先配置 DHCPv6。 使用这些映像不需要进行额外的更改。 如果 VM 基于旧版或自定义的 SUSE 映像，请执行以下步骤：

1. 根据需要安装 `dhcp-client` 包：

    ```bash
    sudo zypper install dhcp-client
    ```

2. 编辑 /etc/sysconfig/network/ifcfg-eth0 文件，添加以下参数  ：

    ```config
    DHCLIENT6_MODE='managed'
    

3. Renew the IPv6 address:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 和 openSUSE Leap

Azure 中最新的 SLES 和 openSUSE 映像已预先配置 DHCPv6。 使用这些映像不需要进行额外的更改。 如果 VM 基于旧版或自定义的 SUSE 映像，请执行以下步骤：

1. 编辑 /etc/sysconfig/network/ifcfg-eth0 文件，并使用以下值替换 `#BOOTPROTO='dhcp4'` 参数：

    ```config
    BOOTPROTO='dhcp'
    ```

2. 编辑 /etc/sysconfig/network/ifcfg-eth0 文件，添加以下参数：

    ```config
    DHCLIENT6_MODE='managed'
    ```

3. 续订 IPv6 地址：

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Azure 中最新的 CoreOS 映像已预先配置 DHCPv6。 使用这些映像不需要进行额外的更改。 如果 VM 基于旧版或自定义的 CoreOS 映像，请执行以下步骤：

1. 编辑 /etc/systemd/network/10_dhcp.network 文件：

    ```config
    [Match]
    eth0

    [Network]
    DHCP=ipv6
    ```

2. 续订 IPv6 地址：

    ```bash
    sudo systemctl restart systemd-networkd
    ```
