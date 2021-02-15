---
title: 软件计划折扣 - Azure | Microsoft Docs
description: 了解如何将软件计划折扣应用于虚拟机上的软件。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: banders
ms.openlocfilehash: 2c17b2f9dc12fa7bff2dae8603bcf95a3a8ddfa0
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350834"
---
# <a name="azure-software-plan-discount"></a>Azure 软件计划折扣

适用于 SUSE 和 RedHat 的 Azure 软件计划是适用于已部署 VM 的预留。 软件计划折扣将应用于与预留匹配的已部署 VM 的软件使用。

关闭 VM 时，折扣会自动应用于另一个匹配的 VM（如果可用）。 软件计划涵盖了在 VM 上运行软件的成本。 计算、存储和网络等其他费用单独收费。

若要购买适当的计划，需要了解 VM 使用情况以及这些 VM 上的 vCPU 数量。 根据使用情况数据，使用以下部分帮助确定要购买的计划。

## <a name="how-reservation-discount-is-applied"></a>如何应用预留折扣

预留折扣的性质是“不用就会失效”  。 因此，如果你在任何小时内没有匹配资源，那么你将丢失该小时的预留数量。 不能结转未使用的预留小时数。

关闭资源时，预留折扣将自动应用于指定范围内的另一个匹配资源。 如果在指定的范围内找不到匹配的资源，则预留小时数将丢失  。

## <a name="review-redhat-vm-usage-before-you-buy"></a>在购买前查看 RedHat VM 使用情况

从使用情况数据获取产品名称，并购买相同类型和大小的 RedHat 计划。

例如，如果使用情况包含产品 **Red Hat Enterprise Linux - 1-4 vCPU VM 许可证**，则应购买适用于 **1-4 vCPU VM** 的 **Red Hat Enterprise Linux**。

<!--ADD RHEL SCREENSHOT -->

## <a name="review-suse-vm-usage-before-you-buy"></a>在购买前查看 SUSE VM 使用情况

从使用情况数据获取产品名称，并购买相同类型和大小的 SUSE 计划。

例如，如果使用的是产品 **SUSE Linux Enterprise Server Priority - 2-4 vCPU VM 支持**，则应购买适用于 **2-4 vCPU** 的 **SUSE Linux Enterprise Server Priority**。

![选择要购买的产品的示例](./media/understand-suse-reservation-charges/select-suse-linux-enterprise-server-priority-2-4-vcpu.png)

## <a name="discount-applies-to-different-vm-sizes-for-suse-plans"></a>折扣适用于 SUSE 计划的不同 VM 大小

与预订的 VM 实例一样，SUSE 计划的购买也提供了实例大小灵活性。 这意味着即使在部署 vCPU 计数不同的 VM 时，折扣也适用。 折扣适用于软件计划内不同的 VM 大小。

折扣金额取决于下表中列出的比率。 比率将比较该组中每个计量的相对占用情况。 比率取决于 VM vCPU。 使用比率值计算可获得 SUSE Linux 计划折扣的 VM 数量。

例如，如果为具有 3 或 4 个 vCPU 的 VM 购买 SUSE Linux Enterprise Server for HPC Priority 计划，此预订的比率则为 2。 折扣涵盖了以下 SUSE 软件成本：

- 2 台具有 1 或 2 个 vCPU 的已部署 VM，
- 1 台具有 3 或 4 个 vCPU 的已部署 VM，
- 或为具有 5 个或更多 vCPU 的 VM 的 0.77 或约 77%。

对于 5 个或更多的 vCPU，此比率为 2.6。 因此，对于具有 5 个或更多 vCPU 的 VM 的 SUSE 预订仅涵盖了软件成本的一部分，即约 77%。

下表显示可为其购买预订的软件计划、其相关的使用情况计量和每个计划的比率。

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>SUSE Linux Enterprise Server for HPC Priority

Azure 门户商城名称：

- SLES 12 SP3 for HPC (Priority)

|SUSE VM | 计量 ID| 比率| 示例 VM 大小|
| -------| ------------------------| --- |--- |
|SLES for HPC 1-2 个 vCPU|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|SLES for HPC 3-4 个 vCPU|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2|D4s_v3|
|SLES for HPC 5个及以上的 vCPU|4edcd5a5-8510-49a8-a9fc-c9721f501913|2.6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server for HPC Standard

Azure 门户商城名称：

- SLES 12 SP3 for HPC

|SUSE VM | 计量 ID | 比率|示例 VM 大小|
| ------- | --- | ------------------------| --- |
|SLES for HPC 1-2 个 vCPU |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|SLES for HPC 3-4 个 vCPU|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1.92308|D4s_v3|
|SLES for HPC 5个及以上的 vCPU |907a85de-024f-4dd6-969c-347d47a1bdff|2.92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-priority"></a>SUSE Linux Enterprise Server for SAP Priority

Azure 门户商城名称：

- SLES for SAP 15 (Priority)
- SLES for SAP 12 SP3 (Priority)
- SLES for SAP 12 SP2 (Priority)

|SUSE VM | 计量 ID | 比率|示例 VM 大小|
| ------- |------------------------| --- | --- |
|SLES for SAP Priority 1-2 个 vCPU|497fe0b6-fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|SLES for SAP Priority 3-4 个 vCPU |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|SLES for SAP Priority 5 个及以上的 vCPU |18ae79cd-dfce-48c9-897b-ebd3053c6058|2.41176|D8s_v3|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server Standard

Azure 门户商城名称：

- SLES 15
- SLES 15 (Standard)
- SLES 12 SP3 (Standard)

|SUSE VM | 计量 ID | 比率|示例 VM 大小|
| ------- |------------------------| --- |--- |
|SLES 1-2 核 vCPU |4b2fecfc-b110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|SLES 3-4 核 vCPU |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1.92308|D4s_v3|
|SLES 5 个及以上的 vCPU |7b349b65-d906-42e5-833f-b2af38513468|2.30769| D8s_v3|

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要了解有关预留的详细信息，请参阅以下文章：

- [什么是 Azure 预订？](save-compute-costs-reservations.md)
- [通过 Azure 预订为 SUSE 软件计划预付费](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [通过 Azure 虚拟机预留实例为虚拟机预付费](../../virtual-machines/prepay-reserved-vm-instances.md)
- [管理 Azure 预留项](manage-reserved-vm-instance.md)
- [了解即用即付订阅的预留使用情况](understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](understand-reserved-instance-usage-ea.md)