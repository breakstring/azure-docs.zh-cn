---
title: Azure Migrate 服务器评估中的评估最佳实践
description: Azure Migrate Server 评估创建评估的技巧。
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: c1fff5b5b7f6450ad8d1977e55a1f6b255f3d668
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754311"
---
# <a name="best-practices-for-creating-assessments"></a>创建评估的最佳实践

[Azure Migrate](./migrate-services-overview.md) 在一个中心位置提供多种工具，帮助你发现、评估应用、基础结构和工作负荷并将其迁移到 Microsoft Azure。 该中心包含 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 的产品/服务。

本文汇总了使用 Azure Migrate Server 评估工具创建评估时的最佳做法。

## <a name="about-assessments"></a>关于评估

使用 Azure Migrate Server 评估创建的评估是数据的时间点快照。 您可以使用 Azure Migrate：服务器评估来创建两种类型的评估：

**评估类型** | **详细信息**
--- | --- 
**Azure VM** | 将本地服务器迁移到 Azure 虚拟机的评估。 <br/><br/> 可使用此评估类型评估要迁移到 Azure 的本地 [VMware VM](how-to-set-up-appliance-vmware.md)、[Hyper-V VM](how-to-set-up-appliance-hyper-v.md) 和[物理服务器](how-to-set-up-appliance-physical.md)。 [了解详细信息](concepts-assessment-calculation.md)
**Azure VMware 解决方案 (AVS)** | 将本地服务器迁移到 [Azure VMware 解决方案 (AVS)](../azure-vmware/introduction.md) 的评估。 <br/><br/> 可使用此评估类型评估要迁移到 Azure VMware 解决方案 (AVS) 的本地 [VMware VM](how-to-set-up-appliance-vmware.md)。 [了解详细信息](concepts-azure-vmware-solution-assessment-calculation.md)


### <a name="sizing-criteria"></a>调整大小标准
服务器评估提供两个调整大小标准选项：

**调整大小标准** | **详细信息** | **数据**
--- | --- | ---
**基于性能** | 基于收集的性能数据提出建议的评估 | **Azure VM 评估**：VM 大小建议基于 CPU 和内存利用率数据。<br/><br/> 磁盘类型建议（标准 HDD/SSD 或高级托管磁盘）基于本地磁盘的 IOPS 和吞吐量。<br/><br/> **Azure VMware 解决方案 (AVS) 评估**：AVS 节点建议基于 CPU 和内存利用率数据。
**按本地原样** | 不使用性能数据来提出建议的评估。 | **Azure VM 评估**：VM 大小建议基于本地 VM 大小<br/><br> 建议的磁盘类型基于在存储类型设置中选择要评估的内容。<br/><br/> **Azure VMware 解决方案 (AVS) 评估**：AVS 节点建议基于本地 VM 大小。

#### <a name="example"></a>示例
例如，如果你有一个本地 VM，其四个内核的利用率为20%，内存为 8 GB，利用率为10%，Azure VM 评估将如下所示：

- **基于性能的评估**：
    - 基于核心 (4 x 0.20 = 0.8) 和内存 (8 GB x 0.10 = 0.8) 利用率确定有效的内核和内存。
    - 应用评估属性中指定的舒适系数 (假设 1.3 x) 获取用于调整大小的值。 
    - 建议 Azure 中最接近的 VM 大小，该大小可支持 ~ 1.04 核心 (0.8 x 1.3) ，~ 1.04 GB (0.8 x 1.3) 内存。

- **与 (一样，作为本地) 评估**：
    -  建议具有四个核心的 VM;8 GB 内存。


## <a name="best-practices-for-creating-assessments"></a>创建评估的最佳实践

Azure Migrate 设备会持续分析你的本地环境，并将元数据和性能数据发送到 Azure。 遵循以下最佳实践来评估使用设备发现的服务器：

- **创建按配置评估**：你可以在 Azure Migrate 的门户中显示你的计算机后立即创建 "按原样" 评估。
- **创建基于性能的评估**：设置发现后，建议你等待至少一天，然后再运行基于性能的评估：
    - 收集性能数据会耗费时间。 等待至少一天可确保在运行评估之前有足够的性能数据点。
    - 当你运行基于性能的评估时，请确保为评估持续时间分析你的环境。 例如，如果你创建的评估的 "性能持续时间" 设置为 "一周"，则在开始发现后，你需要等待至少一周的时间来收集所有数据点。 如果不这样做，评估将不会获得五星评级。
- **重新计算评估**：由于评估是时间点快照，因此不会自动更新到最新数据。 若要使用最新数据更新评估，需要重新计算。

遵循以下最佳做法，以便评估通过导入到 Azure Migrate 的服务器。CSV 文件：

- **创建按配置评估**：你可以在 Azure Migrate 的门户中显示你的计算机后立即创建 "按原样" 评估。
- **创建基于性能的评估**：这有助于获得更好的成本估算，尤其是在本地过度预配服务器容量时。 但基于性能的评估的准确性取决于你为服务器指定的性能数据。 
- **重新计算评估**：由于评估是时间点快照，因此不会自动更新到最新数据。 若要使用最新导入的数据更新评估，需要对其进行重新计算。
 
### <a name="ftt-sizing-parameters-for-avs-assessments"></a>适用于 AVS 评估的 FTT 大小参数

AVS 中使用的存储引擎为 vSAN。 vSAN 存储策略定义了虚拟机的存储要求。 这些策略保证了 VM 所需的服务级别，因为它们可确定如何将存储分配给 VM。 以下是可用的 FTT-Raid 组合： 

**允许的故障数 (FTT)** | **RAID 配置** | **需要的最少主机数** | **大小调整注意事项**
--- | --- | --- | --- 
1 | RAID-1（镜像） | 3 | 100GB VM 将使用 200GB。
1 | RAID-5（擦除编码） | 4 | 100GB VM 将使用 133.33 GB
2 | RAID-1（镜像） | 5 | 100GB VM 将使用 300GB。
2 | RAID-6（擦除编码） | 6 | 100GB VM 将使用 150GB。
3 | RAID-1（镜像） | 7 | 100GB VM 将使用 400GB。


## <a name="best-practices-for-confidence-ratings"></a>置信度评级的最佳实践

当你运行基于性能的评估时，从1星 (最低) 到5星 (最高) 的置信度将为评估奖励。 有效使用置信度：
- Azure Migrate Server 评估需要 VM CPU/内存的利用率数据。
- 对于附加到本地 VM 的每个磁盘，都需要读/写 IOPS/吞吐量数据。
- 对于附加到 VM 的每个网络适配器，它需要网络输入/输出数据。

根据所选持续时间的可用数据点数百分比，按下表所述提供评估的置信度评级。

   **数据点可用性** | **置信度分级**
   --- | ---
   0%-20% | 1 星
   21%-40% | 2 星
   41%-60% | 3 星
   61%-80% | 4 星
   81%-100% | 5 星


## <a name="common-assessment-issues"></a>常见评估问题

下面介绍了一些影响评估的常见环境问题。

###  <a name="out-of-sync-assessments"></a>不同步评估

如果在创建评估后在组中添加或删除计算机，你创建的评估将标记为 "不 **同步**"。再次运行评估 (**重新计算**) 以反映组更改。

### <a name="outdated-assessments"></a>过时评估

如果对组中已评估的 Vm 进行本地更改，则将评估标记为 **过时**。 由于以下属性中的一个或多个更改，可将评估标记为 "过时"：

- 处理器核心数
- 分配的内存
- 启动类型或固件
- 操作系统名称、版本和体系结构
- 磁盘数目
- 网络适配器的数目
- 磁盘大小更改 (GB 已分配) 
- Nic 属性更新。 示例： Mac 地址更改、IP 地址添加等。

再次运行评估 (**重新计算**) 以反映所做的更改。

### <a name="low-confidence-rating"></a>低置信度分级

评估可能不包含所有数据点的原因有很多：

- 在创建评估的过程中，你没有对环境进行分析。 例如，如果要创建性能持续时间设置为一周的 *基于性能的评估* ，则必须在开始发现所有数据点后至少等待一周。 随时可以单击 " **重新计算** " 查看最新的适用置信度级别。 仅当你创建 *基于性能的* 评估时，置信度才适用。

- 一些 VM 在进行评估计算期间关闭。 如果某些 VM 停机了一段时间，则服务器评估将无法收集该时段的性能数据。

- 启动服务器评估中的发现之后，基本不再创建 VM。 例如，如果要针对最后一个月的性能历史记录创建评估，但仅仅在一周前，在环境中创建了一些 VM， 在这种情况下，新 VM 的性能数据在整个过程中都不可用，并且置信度分级会较低。

### <a name="migration-tool-guidance-for-avs-assessments"></a>适用于 AVS 评估的迁移工具指南

在 Azure VMware 解决方案 (AVS) 评估的 Azure 迁移就绪性报告中，可以看到以下推荐工具： 
- **VMWARE HCX 或 Enterprise**：对于 vmware 计算机，Vmware 混合云扩展 (HCX) 解决方案是将本地工作负荷迁移到 Azure VMware 解决方案 (AVS) 私有云的建议迁移工具。 [了解详细信息](../azure-vmware/tutorial-deploy-vmware-hcx.md)。
- **未知**：对于通过 CSV 文件导入的计算机，默认迁移工具是未知的。 但对于 VMware 计算机，建议使用 VMware 混合云扩展 (HCX) 解决方案。


## <a name="next-steps"></a>后续步骤

- [了解](concepts-assessment-calculation.md) 如何计算评估。
- [了解](how-to-modify-assessment.md) 如何自定义评估。
