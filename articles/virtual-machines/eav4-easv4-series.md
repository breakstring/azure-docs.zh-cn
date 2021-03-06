---
title: Eav4 系列和 Easv4 系列
description: Eav4 和 Easv4 系列 Vm 的规格。
author: migerdes
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: f94e7860bff67218c9629e76b06b7293974e491d
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097517"
---
# <a name="eav4-and-easv4-series"></a>Eav4 和 Easv4 系列

Eav4 系列和 Easv4 系列在多线程配置中利用 AMD 的 2.35 Ghz EPYC<sup>TM</sup> 7452 处理器，最多可使用256Mb 三级缓存，同时增加了运行大多数内存优化工作负荷的选项。 Eav4 系列和 Easv4 系列具有与 Ev3 和 Esv3 系列相同的内存和磁盘配置。

## <a name="eav4-series"></a>Eav4 系列

[ACU](acu.md)： 230-260<br>
[高级存储](premium-storage-performance.md)：不支持<br>
[高级存储缓存](premium-storage-performance.md)：不支持<br>
[实时迁移](maintenance-and-updates.md)：支持<br>
[内存保留更新](maintenance-and-updates.md)：支持<br>
[VM 生成支持](generation-2.md)：第1代和第2代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持<br>
[临时 OS 磁盘](ephemeral-os-disks.md)：不支持 <br>
<br>

Eav4 系列大小基于 2.35 Ghz AMD EPYC<sup>TM</sup> 7452 处理器，可实现 3.35 ghz 的提升最大频率。 Eav4 系列大小适用于内存密集型企业应用程序。 数据磁盘存储与虚拟机分开计费。 若要使用高级 SSD，请使用 Easv4 系列大小。 Easv4 大小的定价和计费标准与 Eav3 系列相同。

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大临时存储吞吐量：IOPS/读取 MBps/写入 MBps | 最大 NIC 数 | 预期的网络带宽 (Mbps) |
| -----|-----|-----|-----|-----|-----|-----|-----|
| 标准 \_ E2a \_ v4|2|16|50|4|3000/46/23|2 | 800 |
| 标准 \_ E4a \_ v4|4|32|100|8|6000/93/46|2 | 1600 |
| 标准 \_ E8a \_ v4|8|64|200|16|12000/187/93|4 | 3200 |
| 标准 \_ E16a \_ v4|16|128|400|32|24000/375/187|8 | 6400 |
| 标准 \_ E20a \_ v4|20|160|500|32|30000/468/234|8 | 8000 |
| 标准 \_ E32a \_ v4|32|256|800|32|48000/750/375|8 | 12800 |
| 标准 \_ E48a \_ v4|48|384|1200|32|96000/1000 (500) |8 | 19200 |
| 标准 \_ E64a \_ v4|64|512|1600|32|96000/1000 (500) |8 | 25600 |
| 标准 \_ E96a \_ v4|96|672|2400|32|96000/1000 (500) |8 | 32000 |

## <a name="easv4-series"></a>Easv4 系列

[ACU](acu.md)： 230-260<br>
[高级存储](premium-storage-performance.md)：支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[实时迁移](maintenance-and-updates.md)：支持<br>
[内存保留更新](maintenance-and-updates.md)：支持<br>
[VM 生成支持](generation-2.md)：第1代和第2代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持<br>
[临时 OS 磁盘](ephemeral-os-disks.md)：不支持 <br>
<br>

Easv4 系列大小基于 2.35 Ghz AMD EPYC<sup>TM</sup> 7452 处理器，可实现 3.35 ghz 的提升最大频率，并使用高级 SSD。 Easv4 系列大小适用于内存密集型企业应用程序。

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 非缓存磁盘最大吞吐量：IOPS / MBps | 最大 NIC 数 | 预期的网络带宽 (Mbps) |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000 / 32 (50)|3200 / 48|2 | 800 |
| Standard_E4as_v4|4|32|64|8|8000 / 64 (100)|6400 / 96|2 | 1600 |
| Standard_E8as_v4|8|64|128|16|16000 / 128 (200)|12800 / 192|4 | 3200 |
| Standard_E16as_v4|16|128|256|32|32000/255 (400) |25600 / 384|8 | 6400 |
| Standard_E20as_v4|20|160|320|32|40000/320 (500) |32000 / 480|8 | 8000 |
| Standard_E32as_v4|32|256|512|32|64000/510 (800) |51200 / 768|8 | 12800 |
| Standard_E48as_v4|48|384|768|32|96000/1020 (1200) |76800/1148|8 | 19200 |
| Standard_E64as_v4|64|512|1024|32|128000/1020 (1600) |80000 / 1200|8 | 25600 |
| Standard_E96as_v4 <sup>1</sup>|96|672|1344|32|192000/1020 (2400) |80000 / 1200|8 | 32000 |

<sup>1</sup> [受约束的可用核心规格](./constrained-vcpu.md)。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>其他大小和信息

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

定价计算器：[定价计算器](https://azure.microsoft.com/pricing/calculator/)

有关磁盘类型的详细信息： [磁盘类型](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
