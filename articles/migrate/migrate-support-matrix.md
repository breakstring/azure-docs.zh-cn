---
title: Azure Migrate 支持矩阵
description: 汇总了 Azure Migrate 服务的支持设置和限制。
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 0e1cb33ca0a5fb9a1c7ddb2c4625dfef4acd3a1e
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627391"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrate 支持矩阵

可以使用 [Azure Migrate 服务](./migrate-services-overview.md)来评估计算机并将其迁移到 Microsoft Azure 云。 本文汇总了 Azure Migrate 方案和部署的一般支持设置和限制。

## <a name="supported-assessmentmigration-scenarios"></a>支持的评估/迁移方案

下表汇总了支持的发现、评估和迁移方案。

**部署** | **详细信息** 
--- | --- 
**发现** | 可以发现计算机元数据和动态性能数据。
**应用程序发现** | 可以发现 VMware VM 上运行的应用、角色和功能。 目前，此功能仅限于发现。 评估目前处于计算机级别。 我们尚未提供应用、角色或基于功能的评估。 
**评估** | 评估 VMware VM、Hyper-V VM 和物理服务器上运行的本地工作负荷和数据。 评估使用 Azure Migrate：服务器评估、Microsoft 数据迁移助手 (DMA) 以及其他工具和 ISV 产品。
**迁移** | 将物理服务器、VMware VM、Hyper-V VM、物理服务器以及基于云的 VM 上运行的工作负荷和数据迁移到 Azure。 使用 Azure Migrate 迁移：服务器评估和 Azure 数据库迁移服务 (DMS) ，以及其他工具和 ISV 产品/服务。

> [!NOTE]
> 目前，ISV 工具无法将数据发送到 Azure 政府中的 Azure Migrate。 可以使用集成的 Microsoft 工具，也可以独立使用合作伙伴工具。

## <a name="supported-tools"></a>支持的工具


表中汇总了特定工具支持。

**工具** | 评估 | **迁移** 
--- | --- | ---
Azure Migrate：服务器评估 | 评估 [VMware VM](./tutorial-discover-vmware.md)、[Hyper-V VM](./tutorial-discover-hyper-v.md) 和[物理服务器](./tutorial-discover-physical.md)。 |  不可用 (N/A) 
Azure Migrate:服务器迁移 | 不适用 | 迁移 [VMware VM](tutorial-migrate-vmware.md)、[Hyper-V VM](tutorial-migrate-hyper-v.md) 和[物理服务器](tutorial-migrate-physical-virtual-machines.md)。
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | 不适用 | 迁移 VMware VM、Hyper-V VM、物理服务器、公有云工作负荷。 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| 评估 VMware VM、Hyper-V VM、物理服务器、公有云工作负荷。 | 不适用
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | 评估和迁移 VMware VM、Hyper-V VM、物理服务器、公有云工作负荷。 |  迁移 VMware VM、Hyper-V VM、物理服务器、公有云工作负荷。
[设备 42](https://go.microsoft.com/fwlink/?linkid=2097158) | 评估 VMware VM、Hyper-V VM、物理服务器、公有云工作负荷。| 不适用
[DMA](/sql/dma/dma-overview?view=sql-server-2017) | 评估 SQL Server 的数据库。 | 不适用
[DMS](../dms/dms-overview.md) | 不适用 | 迁移 SQL Server、Oracle、MySQL、PostgreSQL、MongoDB。 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | 评估虚拟桌面基础结构 (VDI) | 不适用
[Movere](https://www.movere.io/) | 评估 VMware Vm、Hyper-v Vm、Xen Vm、物理计算机、工作站 (包括 VDI) 、公有云工作负荷 | 不适用
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | 不适用 | 迁移 VMware VM、Hyper-V VM、Xen VM、KVM VM、物理计算机、公有云工作负荷 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | 评估 VMware VM、Hyper-V VM、物理服务器、公有云工作负荷。 | 不适用
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | 评估 VMware VM、Hyper-V VM、物理服务器、公有云工作负荷和 SQL Server 数据库。 | 不适用
[Webapp 迁移助手](https://appmigration.microsoft.com/) | 评估 Web 应用 | 迁移 Web 应用。


## <a name="azure-migrate-projects"></a>Azure Migrate 项目

**支持** | **详细信息**
--- | ---
订阅 | 一个订阅中可以有多个 Azure Migrate 项目。
Azure 权限 | 你需要订阅中的“参与者”或“所有者”权限才能创建 Azure Migrate 项目。
VMware VM  | 在单个项目中最多评估 35,000 个 VMware VM。
Hyper-V VM    | 在单个项目中最多评估 35,000 个 Hyper-V VM。

一个项目可以同时包含 VMware VM 和 Hyper-V VM，只要不超过评估限制即可。

## <a name="azure-permissions"></a>Azure 权限

若要让 Azure Migrate 与 Azure 配合使用，需要以下权限才能开始评估和迁移计算机。

**任务** | **权限** | **详细信息**
--- | --- | ---
创建 Azure Migrate 项目 | Azure 帐户需要创建项目的权限。 | 针对 [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account)、[Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) 或[物理服务器](./tutorial-discover-physical.md#prepare-an-azure-user-account)进行设置。
注册 Azure Migrate 设备| Azure Migrate 使用轻型 [Azure Migrate 设备](migrate-appliance.md) 来评估具有 Azure Migrate 的计算机：服务器评估，并使用 Azure Migrate： server 迁移运行 VMware vm 的 [无代理迁移](server-migrate-overview.md) 。 此设备可发现计算机，并将元数据和性能数据发送到 Azure Migrate。<br/><br/> 在注册过程中，注册提供程序（Microsoft.OffAzure、Microsoft.Migrate 和 Microsoft.KeyVault）将注册到在设备中选择的订阅，以便订阅可使用资源提供程序。 若要注册，你需要订阅的“参与者”或“所有者”访问权限。<br/><br/> VMware - 在载入过程中，Azure Migrate 将创建两个 Azure Active Directory (Azure AD) 应用。 第一个应用在设备代理与 Azure Migrate 服务之间通信。 应用无权进行 Azure 资源管理调用，或具有 Azure RBAC 访问资源的权限。 第二个应用可访问在用户订阅中创建的仅用于无代理 VMware 迁移的 Azure Key Vault。 在无代理迁移中，Azure Migrate 需要创建一个 Key Vault，用于管理订阅中复制存储帐户的访问密钥。 当从设备发起发现时，它会) 客户租户中的 Azure Key Vault (提供 Azure RBAC 访问权限。<br/><br/> Hyper-V - 在载入过程中。 Azure Migrate 创建一个 Azure AD 应用。 该应用在设备代理与 Azure Migrate 服务之间通信。 应用无权进行 Azure 资源管理调用，或具有 Azure RBAC 访问资源的权限。 | 针对 [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account)、[Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) 或[物理服务器](./tutorial-discover-physical.md#prepare-an-azure-user-account)进行设置。
创建用于 VMware 无代理迁移的密钥保管库 | 若要将 VMware Vm 迁移到无代理 Azure Migrate：服务器迁移，Azure Migrate 创建 Key Vault，以管理订阅中复制存储帐户的访问密钥。 若要创建保管库，需要在 Azure Migrate 项目所在的资源组中设置权限（所有者或参与者和用户访问管理员）。 | [设置](./tutorial-discover-vmware.md#prepare-an-azure-user-account)权限。

## <a name="supported-geographies-public-cloud"></a>支持的地理区域（公有云）

可以在公有云的多个地理位置中创建 Azure Migrate 项目。

- 尽管只能在这些地理区域中创建项目，但可以针对其他目标位置评估或迁移计算机。
- 项目地域仅用于存储已发现的元数据。
- 创建项目时，请选择 "地理"。 项目和相关资源是在地理区域中的某个区域创建的。 区域由 Azure Migrate 服务分配。

**地域** | **元数据存储位置**
--- | ---
亚太区 | 东亚或东南亚
澳大利亚 | 澳大利亚东部或澳大利亚东南部
巴西 | 巴西南部
Canada | 加拿大中部或加拿大东部
欧洲 | 欧洲北部或欧洲西部
法国 | 法国中部
印度 | 印度中部或印度南部
日本 |  日本东部或日本西部
韩国 | 韩国中部或韩国南部
瑞士 | 瑞士北部
United Kingdom | 英国南部或英国西部
United States | 美国中部或美国西部 2

> [!NOTE]
> 对于瑞士地域，瑞士西部仅适用于 REST API 用户并且需要批准的订阅。

## <a name="supported-geographies-azure-government"></a>支持的地理区域（Azure 政府）

**任务** | **地域** | **详细信息**
--- | --- | ---
创建项目 | United States | 元数据存储在 US Gov 亚利桑那州、US Gov 弗吉尼亚州
目标评估 | United States | 目标区域：US Gov 亚利桑那州、US Gov 弗吉尼亚州、US Gov 德克萨斯州
目标复制 | United States | 目标区域：US DoD 中部、US DoD 东部、US Gov 亚利桑那州、US Gov 爱荷华州、US Gov 德克萨斯州、US Gov 弗吉尼亚州


## <a name="vmware-assessment-and-migration"></a>VMware 评估和迁移

[查看](migrate-support-matrix-vmware.md) Azure Migrate：服务器评估和 Azure Migrate：适用于 VMware Vm 的服务器迁移支持矩阵。

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V 评估和迁移

[查看](migrate-support-matrix-hyper-v.md) Azure Migrate：服务器评估和 Azure Migrate：适用于 hyper-v Vm 的服务器迁移支持矩阵。



## <a name="azure-migrate-versions"></a>Azure Migrate 版本

有两个版本的 Azure Migrate 服务：

- **当前版本**：使用此版本可以创建新的 Azure Migrate 项目，发现本地评估，并安排评估和迁移。 [了解详细信息](whats-new.md)。
- **以前版本**：如果客户使用的是以前版本的 Azure Migrate（仅支持对本地 VMware VM 的评估），则现在应使用当前版本。 在以前的版本中，已无法创建新的 Azure Migrate 项目，也无法执行新发现。

## <a name="next-steps"></a>后续步骤

- [评估 VMware VM](./tutorial-assess-vmware-azure-vm.md) 以进行迁移。
- [评估 HYPER-V VM](tutorial-assess-hyper-v.md) 以进行迁移。
