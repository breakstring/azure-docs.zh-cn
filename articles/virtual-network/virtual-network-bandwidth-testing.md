---
title: 测试 Azure VM 网络吞吐量
titlesuffix: Azure Virtual Network
description: 使用 NTTTCP 可以将网络作为测试目标，并尽量减少使用可能影响性能的其他资源。
services: virtual-network
documentationcenter: na
author: steveesp
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/06/2020
ms.author: steveesp
ms.openlocfilehash: 27cc55978582468c759603571bd8b0e69f29db5d
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226567"
---
# <a name="bandwidththroughput-testing-ntttcp"></a>带宽/吞吐量测试 (NTTTCP)

在 Azure 中测试网络吞吐量性能时，最好使用以要测试的网络为目标并能最大程度减少其他资源（这些资源可能会对性能产生影响）的使用的工具。 建议使用 NTTTCP。

将此工具复制到大小相同的两个 Azure VM 中。 一个 VM 充当发送方，另一个充当接收方。

#### <a name="deploying-vms-for-testing"></a>部署 VM 以进行测试
为了达到此测试的目的，两个 VM 应位于同一[邻近放置组](../virtual-machines/co-location.md)或同一可用性集中，这样便可使用其内部 IP 并从测试中排除负载均衡器。 也可以使用 VIP 进行测试，但这类测试不在本文档的讨论范围内。

记下接收方的 IP 地址。 暂且将该 IP 称为“a.b.c.r”

记下 VM 上的核心数。 我们将其称为“\#num\_cores”

在发送方 VM 和接收方 VM 上运行 NTTTCP 测试 300 秒（5 分钟）。

提示：第一次设置此测试时，可以尝试更短的测试时间，以更快地获取反馈。 在工具按预期工作后，将测试时间延长到 300 秒，以获取最准确的结果。

> [!NOTE]
> 发送方 **和** 接收方必须指定 **相同的** 测试持续时间参数 (-t)。

测试单个 TCP 流 10 秒：

接收方参数：ntttcp -r -t 10 -P 1

发送方参数：ntttcp -s10.27.33.7 -t 10 -n 1 -P 1

> [!NOTE]
> 以上示例应仅用于确认配置。 本文档稍后会介绍测试的有效示例。

## <a name="testing-vms-running-windows"></a>测试运行 WINDOWS 的 VM：

#### <a name="get-ntttcp-onto-the-vms"></a>在 VM 上安装 NTTTCP。

下载最新版本：https://github.com/microsoft/ntttcp/releases/download/v5.35/NTttcp.exe

或查看顶级 GitHub 页面： <https://github.com/microsoft/ntttcp>\

请考虑将 NTTTCP 放在单独的文件夹中，如 c:\\tools

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>允许 NTTTCP 通过 Windows 防火墙
在接收方上，在 Windows 防火墙上创建允许规则，以允许接收 NTTTCP 流量。 最简单的方法是按名称允许整个 NTTTCP 程序，而不是允许特定的 TCP 端口入站。

允许 ntttcp 通过 Windows 防火墙，如下所示：

netsh advfirewall firewall add rule program=\<PATH\>\\ntttcp.exe name="ntttcp" protocol=any dir=in action=allow enable=yes profile=ANY

例如，如果已将 ntttcp.exe 复制到“c:\\tools”文件夹中，则此命令为： 

netsh advfirewall firewall add rule program=c:\\tools\\ntttcp.exe name="ntttcp" protocol=any dir=in action=allow enable=yes profile=ANY

#### <a name="running-ntttcp-tests"></a>运行 NTTTCP 测试

在接收方上启动 NTTTCP（**从 CMD 运行**，而不是从 PowerShell 运行）：

ntttcp -r –m [2\*\#num\_cores],\*,a.b.c.r -t 300

如果 VM 有四个核心且 IP 地址为 10.0.0.4，则为如下所示：

ntttcp -r –m 8,\*,10.0.0.4 -t 300


在发送方上启动 NTTTCP（**从 CMD 运行**，而不是从 PowerShell 运行）：

ntttcp -s –m 8,\*,10.0.0.4 -t 300 

等待结果。


## <a name="testing-vms-running-linux"></a>测试运行 LINUX 的 VM：

使用 nttcp-for-linux。 可从 <https://github.com/Microsoft/ntttcp-for-linux> 获得它

在 Linux VM上（发送方和接收方），运行以下命令以在 VM 上准备 nttcp-for-linux：

CentOS — 安装 Git：
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu — 安装 Git：
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
获取并安装两者：
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

如 Windows 示例中一样，假设 Linux 接收方的 IP 为 10.0.0.4

在接收方上启动 NTTTCP-for-Linux：

``` bash
ntttcp -r -t 300
```

然后在发送方上运行：

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
如果未给定时间参数，默认的测试持续时间为 60 秒

## <a name="testing-between-vms-running-windows-and-linux"></a>在运行 Windows 和 LINUX 的 VM 之间进行测试：

在此方案中，我们应启用非同步模式，以便可以运行测试。 通过对 Linux 使用“-N 标志”  ，对 Windows 使用“-ns 标志”  完成此操作。

#### <a name="from-linux-to-windows"></a>从 Linux 到 Windows：

接收方 \<Windows>：

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

发送方 \<Linux>：

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>从 Windows 到 Linux：

接收方 \<Linux>：

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

发送方 \<Windows>：

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>测试云服务实例：
需要将以下部分添加到 ServiceDefinition.csdef
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>后续步骤
* 根据得到的结果，也许能够为方案[优化网络吞吐量计算机](virtual-network-optimize-network-bandwidth.md)。
* 阅读有关如何[为虚拟机分配带宽](virtual-machine-network-throughput.md)的信息
* 通过 [Azure 虚拟网络常见问题解答 (FAQ)](virtual-networks-faq.md) 了解详细信息