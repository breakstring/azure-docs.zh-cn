---
title: HC 系列-Azure 虚拟机
description: HC 系列 Vm 的规格。
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 38020376f9d86fb074bd6d9c09e3d16e899c4a6f
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2021
ms.locfileid: "99096892"
---
# <a name="hc-series"></a>HC 系列

HC 系列 Vm 针对密集计算驱动的应用程序进行了优化，如隐式有限元素分析、分子 dynamics 和计算化学。 HC Vm 功能 44 Intel 至强白金8168处理器核心，每 CPU 内核 8 GB RAM，无超线程。 Intel 强白金平台支持 Intel 丰富的软件工具生态系统，如 Intel 数学内核库和高级向量处理功能（例如 AVX-512）。

HC 系列 Vm 功能 100 Gb/秒 Mellanox EDR。 这些 Vm 连接在一个非阻止 fat 树中，用于实现优化和一致的 RDMA 性能。 这些 Vm 支持自适应路由，而动态连接传输 (DCT，) 的附加到标准 RC 和 UD 传输。 这些功能增强了应用程序的性能、可伸缩性和一致性，建议使用这些功能。

[ACU](acu.md)：297-315<br>
[高级存储](premium-storage-performance.md)：支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[实时迁移](maintenance-and-updates.md)：不支持<br>
[内存保留更新](maintenance-and-updates.md)：不支持<br>
[VM 代系支持](generation-2.md)：第 1 代和第 2 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持<br>
[临时 OS 磁盘](ephemeral-os-disks.md)：不支持 <br>
<br>

| 大小 | vCPU | 处理器 | 内存 (GiB) | 内存带宽 GB/秒 | 基本 CPU 频率 (GHz)  | 所有核心频率 (GHz，峰值)  | 单核频率 (GHz，峰值)  | RDMA 性能 (Gb/s)  | MPI 支持 | 临时存储 (GiB) | 最大数据磁盘数 | 最大以太网 Vnic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel 强白金8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | All | 700 | 4 | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

- 详细了解如何[配置 vm](./workloads/hpc/configure.md)、在[hpc 工作负荷](./workloads/hpc/overview.md)下为 Azure 设置不[允许](./workloads/hpc/enable-infiniband.md)的[设置](./workloads/hpc/setup-mpi.md)和优化 hpc 应用程序。
- 在 [Azure 计算技术社区博客](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)上阅读最新公告以及一些 HPC 示例和结果。
- 有关运行 HPC 工作负荷的高级体系结构视图，请参阅 [Azure 上的高性能计算 (HPC) ](/azure/architecture/topics/high-performance-computing/)。
- 了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
