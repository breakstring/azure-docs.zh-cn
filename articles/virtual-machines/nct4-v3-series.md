---
title: NCas T4 v3 系列
description: NCas T4 v3 系列 Vm 的规格。
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: vikancha
ms.openlocfilehash: ac1147435bee60db0f3028289a83026e1f9c1c15
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091674"
---
# <a name="ncast4_v3-series"></a>NCasT4_v3 系列 

NCasT4_v3 系列虚拟机由 [Nvidia Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) GPU 和 AMD EPYC 7V12 提供支持 (罗马) cpu。 Vm 的功能最多支持4个 NVIDIA T4 Gpu，每个内存有 16 GB 内存，最多64个非多线程 AMD EPYC 7V12 (罗马) 处理器核心，440 GiB 的系统内存。 这些虚拟机非常适合用于部署 AI 服务，例如用户生成的请求的实时推断，或使用 NVIDIA 网格驱动程序和虚拟 GPU 技术的交互式图形和可视化工作负荷。 基于 CUDA、TensorRT、Caffe、ONNX 和其他框架的标准 GPU 计算工作负荷，或基于 GPU 加速图形应用程序的 OpenGL 和 DirectX，可以在 NCasT4_v3 系列上以经济实惠的方式部署到用户。

<br>

[ACU](acu.md)：230-260<br>
[高级存储](premium-storage-performance.md)：支持<br>
[高级存储缓存](premium-storage-performance.md)：支持<br>
[实时迁移](maintenance-and-updates.md)：不支持<br>
[内存保留更新](maintenance-and-updates.md)：不支持<br>
[VM 代系支持](generation-2.md)：第 1 代和第 2 代<br>
[加速网络](../virtual-network/create-vm-accelerated-networking-cli.md)：支持<br>
[临时 OS 磁盘](ephemeral-os-disks.md)：不支持 <br>
Nvidia NVLink 互连：不支持<br>
<br>

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 最大 NIC 数/预期网络带宽 (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 / 8000 |
| Standard_NC8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4 / 8000  |
| Standard_NC16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8 / 8000  |
| Standard_NC64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8 / 32000  |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

若要利用运行 Windows 或 Linux 的 Azure NCasT4_v3 系列 Vm 的 GPU 功能，必须安装 Nvidia GPU 驱动程序。

若要手动安装 Nvidia GPU 驱动程序，请参阅适用于 [Windows 的 N 系列 gpu 驱动程序设置](./windows/n-series-driver-setup.md) 以获取支持的操作系统、驱动程序、安装和验证步骤。

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
