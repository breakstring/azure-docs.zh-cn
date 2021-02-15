---
title: Azure Migrate 设备体系结构
description: 概述了服务器评估和迁移期间使用的 Azure Migrate 设备。
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: 42d4a722be25eec4b3e27012350346018fdba0f3
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754107"
---
# <a name="azure-migrate-appliance-architecture"></a>Azure Migrate 设备体系结构

本文介绍 Azure Migrate 设备体系结构和过程。 Azure Migrate 设备是一种轻型设备，部署在本地，用于发现要迁移到 Azure 的 Vm 和物理服务器。 

## <a name="deployment-scenarios"></a>部署方案

Azure Migrate 设备用于以下应用场景。

**方案** | **工具** | **用于** 
--- | --- | ---
**VMware VM 评估** | Azure Migrate：服务器评估 | 发现 VMware Vm。<br/><br/> 发现计算机应用和依赖项。<br/><br/> 收集计算机元数据和性能元数据并将其发送到 Azure。
**VMware VM 迁移 (无代理)** | Azure Migrate：服务器迁移 | 发现 VMware VM<br/><br/>  复制 VMware Vm 和 [无代理迁移](server-migrate-overview.md)。
**Hyper-V VM 评估** | Azure Migrate：服务器评估 | 发现 Hyper-v Vm。<br/><br/> 收集计算机元数据和性能元数据并将其发送到 Azure。
**物理机** |  Azure Migrate：服务器评估 |  发现物理服务器。<br/><br/> 收集计算机元数据和性能元数据并将其发送到 Azure。

## <a name="appliance-components"></a>设备组件

设备有多个组件。

- 管理应用：在设备部署期间用于用户输入的 Web 应用。 在评估要迁移到 Azure 的计算机时会使用它。
- 发现代理：该代理收集计算机配置数据。 在评估要迁移到 Azure 的计算机时会使用它。 
- **收集器代理**：代理收集性能数据。 在评估要迁移到 Azure 的计算机时会使用它。
- DRA 代理：协调 VM 复制，并协调复制计算机与 Azure 之间的通信。 只有在使用无代理迁移将 VMware VM 复制到 Azure 时，才会使用它。
- 网关：将复制的数据发送到 Azure。 只有在使用无代理迁移将 VMware VM 复制到 Azure 时，才会使用它。
- 自动更新服务：更新设备组件（每 24 小时运行一次）。



## <a name="appliance-deployment"></a>设备部署

- 可以使用适用于 [hyper-v](how-to-set-up-appliance-hyper-v.md) 或 [vmware](how-to-set-up-appliance-vmware.md) 的模板设置 Azure Migrate 设备，也可以使用适用于 [vmware/hyper-v](deploy-appliance-script.md)的 PowerShell 脚本安装程序和 [物理服务器](how-to-set-up-appliance-physical.md)。 
- 设备支持 [矩阵](migrate-appliance.md)中概述了设备支持要求和部署先决条件。


## <a name="appliance-registration"></a>设备注册

在设备安装过程中，会将设备注册到 Azure Migrate，并在表中汇总的操作发生。

**操作** | **详细信息** | **权限**
--- | --- | ---
**注册源提供程序** | 这些资源提供程序在设备设置过程中所选的订阅中注册： OffAzure、KeyVault。<br/><br/> 通过注册资源提供程序来配置订阅，以供资源提供程序使用。 | 需要订阅的“参与者”或“所有者”角色才能注册资源提供程序。
**创建 Azure AD 应用程序通信** | Azure Migrate 将在设备上运行的代理和在 Azure 上运行的相应服务之间创建一个 Azure Active Directory (Azure AD) 的身份验证和授权 (应用程序。<br/><br/> 此应用无权对任何资源进行 Azure 资源管理器调用或 Azure RBAC 访问权限。 | 需要 [这些权限](./tutorial-discover-vmware.md#prepare-an-azure-user-account) 才能 Azure Migrate 创建应用。
**创建 Azure AD 应用-密钥保管库** | 仅为 VMware Vm 到 Azure 的无代理迁移创建此应用。<br/><br/> 它以独占方式用于访问在用户订阅中为无代理迁移创建的密钥保管库。<br/><br/> 当从设备启动发现时，它会在客户的租户)  (创建的 Azure 密钥保管库上提供 Azure RBAC 访问权限。 | 需要 [这些权限](./tutorial-discover-vmware.md#prepare-an-azure-user-account) 才能 Azure Migrate 创建应用。



## <a name="collected-data"></a>收集的数据

客户端为所有部署方案收集的数据在 [设备支持矩阵](migrate-appliance.md)中进行了总结。

## <a name="discovery-and-collection-process"></a>发现和收集过程

![体系结构](./media/migrate-appliance-architecture/architecture1.png)

设备使用以下过程与 vCenter 服务器和 Hyper-v 主机/群集通信。

1. **开始发现**：
    - 当你在 Hyper-v 设备上启动发现时，它会与 WinRM 端口5985上的 Hyper-v 主机 (HTTP) 通信。
    - 在 VMware 设备上启动发现时，默认情况下，它会与 TCP 端口443上的 vCenter 服务器通信。 如果 vCenter 服务器侦听其他端口，则可以在设备 web 应用中进行配置。
2. **收集元数据和性能数据**：
    - 设备使用 (CIM) 会话的通用信息模型从端口5985上的 Hyper-v 主机收集 Hyper-v VM 数据。
    - 默认情况下，设备与端口443通信，以便从 vCenter Server 收集 VMware VM 数据。
3. **发送数据**：设备将收集的数据发送到 Azure Migrate 服务器评估，并通过 SSL 端口 443 Azure Migrate 服务器迁移。 设备可以通过 internet 或 ExpressRoute 连接到 Azure (需要 Microsoft 对等互连) 。
    - 对于性能数据，设备会收集实时利用率数据。
        - 对于 VMware，每隔20秒收集一次性能数据，对于每个性能指标，将每30秒收集一次。
        - 收集的数据将汇总到10分钟后创建单个数据点。
        - 峰值利用率值是从所有 20/30 秒数据点中选择的，并发送到 Azure 进行评估计算。
        - 根据在 "评估属性" (第 50/90/95/99%) 中指定的百分位值，每十分钟点按升序排序，并使用适当的百分位值计算评估
    - 对于服务器迁移，设备会开始收集 VM 数据，并将其复制到 Azure。
4. **评估和迁移**：你现在可以使用 Azure Migrate Server 评估从设备收集的元数据中创建评估。 此外，还可以使用 Azure Migrate Server 迁移开始迁移 VMware Vm，以安排无代理 VM 复制。

## <a name="appliance-upgrades"></a>设备升级

当设备上运行的 Azure Migrate 代理更新时，设备也会随之升级。 这会自动发生，因为默认情况下在设备上启用了自动更新。 您可以更改此默认设置以手动更新代理。

通过将 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance "自动更新" 项设置为 0 (DWORD) ，可以关闭注册表中的自动更新。


## <a name="next-steps"></a>后续步骤

[查看](migrate-appliance.md) 设备支持矩阵。