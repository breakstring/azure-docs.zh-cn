---
title: include 文件
description: include 文件
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: d610193783d44b86d48cd5ceaa91377b32b7061b
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99580167"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>大小表定义

- 存储容量的单位为 GiB 或 1024^3 字节。 比较以 GB（1000^3 字节）为单位的磁盘与以 GiB（1024^3 字节）为单位的磁盘时，请记住以 GiB 为单位的容量数显得更小。 例如，1023 GiB = 1098.4 GB。
- 磁盘吞吐量的单位为每秒输入/输出操作数 (IOPS) 和 MBps，其中 MBps = 10^6 字节/秒。
- 数据磁盘可以在缓存或非缓存模式下运行。 对于缓存数据磁盘操作，主机缓存模式设置为 **ReadOnly** 或 **ReadWrite**。  对于非缓存数据磁盘操作，主机缓存模式设置为 **None**。
- 若要了解如何为 Vm 获得最佳存储性能，请参阅 [虚拟机和磁盘性能](../articles/virtual-machines/disks-performance.md)。
- **预期的网络带宽** 是指跨所有 NIC 为每个 VM 类型分配的最大聚合带宽，适用于所有目标。 有关详细信息，请参阅[虚拟机网络带宽](../articles/virtual-network/virtual-machine-network-throughput.md)。

  无法保证上限。 限值用于在为目标应用程序选择适当的 VM 类型时提供指导。 实际的网络性能将取决于多种因素，包括网络拥塞、应用程序负载和网络设置。 有关如何优化网络吞吐量的信息，请参阅[为 Azure 虚拟机优化网络吞吐量](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md)。 若要在 Linux 或 Windows 中达到预期的网络性能，可能需要选择特定版本，或者需要优化 VM。 有关详细信息，请参阅[带宽/吞吐量测试 (NTTTCP)](../articles/virtual-network/virtual-network-bandwidth-testing.md)。



