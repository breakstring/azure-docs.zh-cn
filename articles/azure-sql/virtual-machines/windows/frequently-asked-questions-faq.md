---
title: Azure 的 Windows 虚拟机上的 SQL Server 常见问题解答 | Microsoft Docs
description: 本文提供有关运行 Azure VM 中的 SQL Server 时遇到的常见问题的解答。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: b58119ccc1551d12dfc9b09f76f6980618ba6221
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94556282"
---
# <a name="frequently-asked-questions-for-sql-server-on-azure-vms"></a>Azure VM 上的 SQL Server 常见问题解答
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](frequently-asked-questions-faq.md)
> * [Linux](../linux/frequently-asked-questions-faq.md)

本文提供有关[在 Windows Azure 虚拟机 (VM) 上运行 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 时出现的一些最常见问题的解答。

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a>映像

1. **有哪些 SQL Server 虚拟机库映像可用？** 

   Azure 为所有 Windows 和 Linux 版本中的所有受支持 SQL Server 主要发行版维护虚拟机映像。 有关详细信息，请参阅 [Windows VM 映像](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo)和 [Linux VM 映像](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md#create)的完整列表。

1. **现有的 SQL Server 虚拟机库映像是否会更新？**

   每隔两个月，都会使用最新的 Windows 和 Linux 更新对虚拟机库中的 SQL Server 映像进行更新。 对于 Windows 映像，这包括 Windows 更新中标记为重要的任何更新，以及重要的 SQL Server 安全更新和 Service Pack。 对于 Linux 映像，这包括最新的系统更新。 Linux 和 Windows 的 SQL Server 累积更新以不同的方式进行处理。 对于 Linux，SQL Server 累积更新也包含在刷新中。 但目前，Windows VM 不会连同 SQL Server 或 Windows Server 累积更新一起更新。

1. **是否可以从库中删除 SQL Server 虚拟机映像？**

   是的。 Azure 只为每个主要版本维护一个映像。 例如，发布新的 SQL Server Service Pack 时，Azure 会将新映像添加到该 Service Pack 的库。 先前 Service Pack 的 SQL Server 映像将立即从 Azure 门户中删除。 但是，在接下来的三个月，仍可以通过 PowerShell 预配该映像。 三个月之后，先前的 Service Pack 映像不再可用。 如果 SQL Server 版本由于生命周期结束而不受支持，则也会应用此删除策略。


1. **是否可以部署 Azure 门户中不可见的较旧的 SQL Server 映像？**

   是的，使用 PowerShell。 有关使用 PowerShell 部署 SQL Server VM 的详细信息，请参阅[如何使用 Azure PowerShell 预配 SQL Server 虚拟机](create-sql-vm-powershell.md)。
   
1. **是否可以创建 SQL Server SQL Server VM 的通用 Azure Marketplace 映像，并使用它来部署 Vm？**

   是，但你必须随后 [使用 SQL IaaS 代理扩展注册每个 SQL Server VM](sql-agent-extension-manually-register-single-vm.md) ，以便在门户中管理你的 SQL Server VM，并使用自动修补和自动备份等功能。 向扩展注册时，还需要指定每个 SQL Server VM 的许可证类型。

1. 如何使 Azure VM 上的 SQL Server 通用化并使用它部署新 VM？

   可以部署 Windows Server VM（不安装 SQL Server），并使用 [SQL sysprep](/sql/database-engine/install-windows/install-sql-server-using-sysprep) 进程和 SQL Server 安装媒体将 Azure VM (Windows) 上的 SQL Server 通用化。 如果客户有[软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3)，则可以从[批量许可中心](https://www.microsoft.com/Licensing/servicecenter/default.aspx)获取其安装介质。 没有软件保障的客户可以使用具有所需版本的 Azure 市场 SQL Server VM 映像中的安装介质。

   另外，也可使用 Azure 市场中的 SQL Server 映像之一来将 Azure VM 上的 SQL Server 通用化。 请注意，在创建你自己的映像之前，必须在源映像中删除以下注册表项。 如果不这样做，可能会导致 SQL Server 安装程序的启动文件夹扩展以及/或者 SQL IaaS 扩展处于故障状态。

   注册表项路径：  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

   > [!NOTE]
   > Azure Vm 上的 SQL Server （包括从自定义的通用映像部署的映像）应 [注册到 SQL IaaS 代理扩展](./sql-agent-extension-manually-register-single-vm.md?tabs=azure-cli%252cbash) ，以满足符合性要求，并使用可选功能，如自动修补和自动备份。 扩展还允许您指定每个 SQL Server VM [的许可证类型](./licensing-model-azure-hybrid-benefit-ahb-change.md?tabs=azure-portal) 。

1. **是否可以使用我自己的 VHD 来部署 SQL Server VM？**

   是，但你必须随后 [使用 SQL IaaS 代理扩展注册每个 SQL Server VM](sql-agent-extension-manually-register-single-vm.md) ，以便在门户中管理你的 SQL Server VM，并使用自动修补和自动备份等功能。

1. **是否可以设置虚拟机库中未显示的配置（例如 Windows 2008 R2 + SQL Server 2012）？**

   否。 对于包含 SQL Server 的虚拟机图库映像，必须通过 Azure 门户或 [PowerShell](create-sql-vm-powershell.md) 选择提供的某个映像。 但是，可以部署一个 Windows VM 并在其中自行安装 SQL Server。 然后，必须 [使用 SQL IaaS 代理扩展注册 SQL Server VM](sql-agent-extension-manually-register-single-vm.md) ，以在 Azure 门户中管理 SQL Server VM，并使用自动修补和自动备份等功能。 


## <a name="creation"></a>创建

1. **如何创建装有 SQL Server 的 Azure 虚拟机？**

   最简单的方法是创建包含 SQL Server 的虚拟机。 有关注册 Azure 并从门户创建 SQL Server VM 的教程，请参阅[在 Azure 门户中预配 SQL Server 虚拟机](create-sql-vm-portal.md)。 可选择使用按秒付费 SQL Server 许可的虚拟机映像，或者可以使用允许自带 SQL Server 许可证的映像。 此外，你也可以选用免费许可版（开发人员版或速成版），或通过重新使用本地许可证在 VM 上手动安装 SQL Server。 请确保 [使用 SQL IaaS 代理扩展注册 SQL Server VM](sql-agent-extension-manually-register-single-vm.md) ，以便在门户中管理 SQL Server VM，并使用自动修补和自动备份等功能。 如果自带许可，必须[在 Azure 上通过软件保障实现许可证移动性](https://azure.microsoft.com/pricing/license-mobility/)。 有关详细信息，请参阅 [SQL Server Azure VM 定价指南](pricing-guidance.md)。

1. **如何将本地 SQL Server 数据库迁移到云？**

   首先，请创建装有 SQL Server 实例的 Azure 虚拟机。 然后将本地数据库迁转到该实例。 有关数据迁移策略，请参阅 [将 SQL Server 数据库迁移到 Azure VM 中的 SQL Server](migrate-to-vm-from-sql-server.md)。

## <a name="licensing"></a>授权

1. **如何在 Azure VM 上安装 SQL Server 的许可版本？**

   可通过三种方式实现此目的。 企业协议 (EA) 客户可以预配[支持许可证的虚拟机映像](sql-server-on-azure-vm-iaas-what-is-overview.md#BYOL)之一，也称为自带许可 (BYOL)。 如果你有 [软件保障](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)，可以在现有的现用现付 (PAYG) "图像上启用 [Azure 混合权益](licensing-model-azure-hybrid-benefit-ahb-change.md) 。 或者，可将 SQL Server 安装媒体复制到 Windows Server VM，然后在 VM 上安装 SQL Server。 确保为门户管理、自动备份和自动修补等功能的 [扩展](sql-agent-extension-manually-register-single-vm.md) 注册 SQL Server VM。 

1. **如果已通过即用即付库映像之一创建了 VM，是否可以将该 VM 更改为使用自己的 SQL Server 许可证？**

   是的。 通过启用 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)，可以轻松将即用即付 (PAYG) 库映像切换为自带许可 (BYOL)。  有关详细信息，请参阅[如何更改 SQL Server VM 的许可模型](licensing-model-azure-hybrid-benefit-ahb-change.md)。 目前，此功能仅适用于公共和 Azure 政府云客户。

1. **切换许可模型是否需要将 SQL Server 停机？**

   不是。 [更改许可模型](licensing-model-azure-hybrid-benefit-ahb-change.md)不需将 SQL Server 停机，因为更改会立即生效，不需重启 VM。 但是，若要将 SQL Server VM 注册到 SQL IaaS 代理扩展，则需要安装 [Sql iaas 扩展](sql-server-iaas-agent-extension-automate-management.md) ，并在 _完整_ 模式下安装 sql iaas 扩展，以重启 SQL Server 服务。 因此，如果需要安装 SQL IaaS 扩展，可采用一种轻型模式来安装它，获取有限的功能，也可在维护时段期间采用完全模式安装 。 采用轻型模式安装的 SQL IaaS 扩展可以在任何时候升级到完全模式，但需要重启 SQL Server 服务 。 
   
1. **是否可以在使用经典模型部署的 SQL Server VM 上切换许可模型？**

   否。 不支持在经典 VM 上更改许可模型。 可以将 VM 迁移到 Azure 资源管理器型号，并注册到 SQL IaaS 代理扩展。 将 VM 注册到 SQL IaaS 代理扩展后，可在 VM 上使用授权模型更改。

1. **是否可以使用 Azure 门户来管理同一 VM 上的多个实例？**

   不是。 门户管理是 SQL IaaS 代理扩展提供的一项功能，它依赖于 SQL Server IaaS 代理扩展。 同样，此限制也适用于扩展。 只要配置正确，门户就可管理一个默认实例或一个命名实例。 有关这些限制的详细信息，请参阅 [SQL Server IaaS 代理扩展](sql-server-iaas-agent-extension-automate-management.md)。 

1. **CSP 订阅是否能够激活 Azure 混合权益？**

   能，Azure 混合权益适用于 CSP 订阅。 CSP 客户应首先部署即用即付映像，然后[将许可模式更改](licensing-model-azure-hybrid-benefit-ahb-change.md)为自带许可。
   
 
1. **如果 SQL Server 仅用于待机/故障转移，是否必须付费才能在 Azure VM 上为 SQL Server 授予许可？**

   若要获得备用辅助可用性组或故障转移群集实例的免费被动许可证，必须满足[产品许可条款](https://www.microsoft.com/licensing/product-licensing/products)中所述的以下所有条件：

   1. 已通过[软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3)获得[许可移动性](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2)。 
   1. 被动 SQL Server 实例不会为客户端提供 SQL Server 数据，也不会运行活动的 SQL Server 工作负荷。 它只用于与主服务器同步，或者使被动数据库保持热备用状态。 如果它正在提供数据（例如，向运行活动 SQL Server 工作负载的客户端报告，或执行未在产品条款中指定的任何工作），则它必须是付费许可的 SQL Server 实例。 允许对辅助实例执行以下活动：数据库一致性检查或 CheckDB、完整备份、事务日志备份，以及资源使用情况数据监视。 还可以在每隔 90 天运行一次灾难恢复测试的短暂时段内，同时运行主实例和相应的灾难恢复实例。 
   1. 主动 SQL Server 许可证包含在软件保障中，并且允许“一个”被动的次要 SQL Server 实例，最多也只能具有与许可的主动服务器相同的计算量。 
   1. 次要 SQL Server VM 在 Azure 门户中使用[灾难恢复](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)许可证。
   
1. **什么是被动实例？**

   被动 SQL Server 实例不向客户端提供 SQL Server 数据，也不运行主动 SQL Server 工作负载。 它仅用于与主服务器同步，否则将被动数据库保持在热备用状态。 如果它正在为数据提供服务，例如向运行活动 SQL Server 工作负载的客户端提供报告，或者执行产品条款中未指定的任何工作，则必须是付费许可的 SQL Server 实例。 在次要实例上允许进行以下活动：数据库一致性检查或 CheckDB、完整备份、事务日志备份和监视资源使用情况数据。 还可以同时运行主灾难恢复实例和相应灾难恢复实例，以便每 90 天进行一次短暂的灾难恢复测试。
   

1. **哪些方案可以利用灾难恢复 (DR) 权益？**

   [许可指南](https://aka.ms/sql2019licenseguide)提供了可以利用灾难恢复权益的方案。 有关详细信息，请参阅”产品条款”并与许可联系人或客户经理联系。

1. **哪些订阅支持灾难恢复 (DR) 权益？**

   提供软件保障等效订阅权限作为固定权益的综合计划支持 DR 权益。 这包括 但并不限于，开盘值 (OV-ES) 、Open Value 订阅 (OVS-ES) 、企业协议 (EA) 、企业协议订阅 (EAS) 以及服务器和云注册 (SCE) 。 有关详细信息，请参阅[产品条款](https://www.microsoft.com/licensing/product-licensing/products)并与许可联系人或客户经理联系。 

   
 ## <a name="extension"></a>分机

1. **是否会向新的 SQL IaaS 代理扩展注册我的 VM，带来额外的费用？**

   错误。 SQL IaaS 代理扩展在 Azure VM 上启用 SQL Server 的其他可管理性，无需支付额外费用。 

1. **SQL IaaS 代理扩展是否适用于所有客户？**
 
   是的，只要使用资源管理器模型而不是使用经典模型将 SQL Server VM 部署到公共云就行。 所有其他客户都可以注册新的 SQL IaaS 代理扩展。 但是，只有享受[软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3)权益的客户才能通过激活 SQL Server VM 上的 [Azure 混合权益 (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) 来使用自己的许可证。 

1. **如果移动或删除 VM 资源，扩展 ( _SqlVirtualMachine_ ) 资源会发生什么情况？** 

   删除或移动 Microsoft.Compute/VirtualMachine 资源时，会通知关联的 Microsoft.SqlVirtualMachine 资源以异步方式复制此操作。

1. **如果删除 _SqlVirtualMachine_ ) 资源的 (扩展，VM 会发生什么情况？**

    删除 Microsoft.SqlVirtualMachine 资源时，Microsoft.Compute/VirtualMachine 资源不受影响。 但是，许可更改会默认回退到原始的映像源。 

1. **是否可以向 SQL IaaS 代理扩展注册自行部署的 SQL Server Vm？**

    是。 如果你从自己的媒体部署了 SQL Server，并且安装了 SQL IaaS 扩展，则可以向该扩展注册 SQL Server VM，以获取 SQL IaaS 扩展提供的可管理性优势。    


## <a name="administration"></a>管理

1. **是否可以在同一 VM 上安装另一个 SQL Server 实例？是否可以更改默认实例的已安装功能？**

   是的。 SQL Server 安装介质位于 **C** 驱动器上的某个文件夹中。 可从该位置运行 **Setup.exe** 以添加新的 SQL Server 实例，或更改计算机上 SQL Server 的其他已安装功能。 请注意，某些功能（例如自动备份、自动修补和 Azure Key Vault 集成）仅对默认实例或配置正确的命名实例起作用（请参阅问题 3）。 [通过 "Azure 混合权益"](licensing-model-azure-hybrid-benefit-ahb-change.md)或 "即用 **即付** " 许可模式使用软件保障的客户可以在虚拟机上安装 SQL Server 的多个实例，而不会产生额外的许可成本。 如果配置不正确，则其他 SQL Server 实例可能会导致系统资源的负担。 

1. **VM 上的最大实例数是多少？**
   SQL Server 2012 到 SQL Server 2019 可以支持在独立服务器上使用 [50 实例](/sql/sql-server/editions-and-components-of-sql-server-version-15#RDBMSSP) 。 此限制与在本地 Azure 中的情况相同。 请参阅 [最佳实践](performance-guidelines-best-practices.md#multiple-instances) 以了解如何更好地准备环境。 

1. **是否可以卸载 SQL Server 的默认实例？**

   可以，但需注意以下事项。 首先，根据 VM 的许可模型，与 SQL Server 相关的计费可能会继续发生。 其次，如前面的解答中所述，某些功能依赖于 [SQL Server IaaS 代理扩展](sql-server-iaas-agent-extension-automate-management.md)。 如果卸载默认实例但未同时删除 IaaS 扩展，该扩展会继续查找默认实例并可能生成事件日志错误。 这些错误来自以下两个源：Microsoft SQL Server 凭据管理和 Microsoft SQL Server IaaS 代理 。 其中一个错误可能类似于以下内容：

      建立与 SQL Server 的连接时，出现网络相关或特定于实例的错误。 找不到或无法访问服务器。

   如果决定卸载默认实例，还要卸载 [SQL Server IaaS 代理扩展](sql-server-iaas-agent-extension-automate-management.md)。 

1. **是否可以将 SQL Server 的命名实例与 IaaS 扩展一起使用？**
   
   可以，只要指定的实例是 SQL Server 上的唯一实例，且原始默认实例[已正确卸载](sql-server-iaas-agent-extension-automate-management.md#named-instance-support)。 如果没有默认实例，但是单个 SQL Server VM 上有多个命名实例，则 SQL Server IaaS 代理扩展将无法安装。  

1. **是否可以从 SQL Server VM 中删除 SQL Server 和关联的许可证帐单？**

   是的，但你需要采取额外的措施来避免按 [定价指南](pricing-guidance.md)中所述为 SQL Server 实例付费。 如果要完全删除 SQL Server 实例，则可以迁移到其他 Azure VM，而无需 SQL Server 预装 VM 并删除当前 SQL Server VM。 如果要保留 VM，但要停止 SQL Server 计费，请执行以下步骤： 

   1. 如有必要，请备份所有数据，包括系统数据库。 
   1. 完全卸载 SQL Server，包括 SQL IaaS 扩展 (（如果存在）) 。
   1. 安装免费的 [SQL Express edition](https://www.microsoft.com/sql-server/sql-server-downloads)。
   1. 在 [轻型模式下](sql-agent-extension-manually-register-single-vm.md)，注册 SQL IaaS 代理扩展。
   1.  (可选) 禁用服务启动来禁用 Express SQL Server 服务。 

1. **是否可以使用 Azure 门户来管理同一 VM 上的多个实例？**

   否。 门户管理由 SQL IaaS 代理扩展提供，该扩展依赖于 SQL Server IaaS 代理扩展。 同样，此限制也适用于扩展。 只要一个默认实例或一个命名实例的配置正确，门户就只能管理一个默认实例。 有关详细信息，请参阅 [SQL Server IaaS 代理扩展](sql-server-iaas-agent-extension-automate-management.md) 


## <a name="updating-and-patching"></a>更新和修补

1. **如何实现在 Azure VM 中更改为不同版本的 SQL Server？**

   客户可以使用包含所需 SQL Server 版本的安装介质来更改其 SQL Server 版本。 更改版本以后，使用 Azure 门户修改 VM 的版本属性，使之准确反映 VM 的计费。 有关详细信息，请参阅[更改 SQL Server VM 的版本](change-sql-server-edition.md)。 不同版本的 SQL Server 没有计费差异，因此 SQL Server 的版本发生更改后，无需采取进一步操作。

1. **可在何处获取用于更改 SQL Server 版本的安装媒体？**

   如果客户有[软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)，则可以从[批量许可中心](https://www.microsoft.com/Licensing/servicecenter/default.aspx)获取其安装介质。 没有软件保障的客户可以使用 Azure Marketplace 中的安装媒体 SQL Server VM 具有所需版本的映像。
   
1. **如何将更新和服务包应用于 SQL Server VM？**

   虚拟机允许控制主机，包括应用更新的时间与方法。 对于操作系统，可以手动应用 Windows 更新，或者启用名为[自动修补](automated-patching.md)的计划服务。 自动修补将安装任何标记为重要的更新，包括该类别中的 SQL Server 更新。 必须手动安装其他可选的 SQL Server 更新。

1. **在将 SQL Server 2008/2008 R2 实例注册到 SQL IaaS 代理扩展后，是否可以将其升级？**

   如果操作系统为 Windows Server 2008 R2 或更高版本，则是。 可以使用任何安装媒体来升级 SQL Server 的版本，然后可以将 [SQL IaaS 扩展模式](sql-server-iaas-agent-extension-automate-management.md#management-modes)从“无代理”升级到“完整” 。 这样做可以获得 SQL IaaS 扩展的所有优势，如门户可管理性、自动备份和自动修补。 如果操作系统版本为 Windows Server 2008，则仅支持 NoAgent 模式。 

1. **如何获取终止支持的 SQL Server 2008 和 SQL Server 2008 R2 实例的免费扩展安全更新？**

   可以通过将 SQL Server 按原样迁移到 Azure 虚拟机，获取[免费扩展安全更新](sql-server-2008-extend-end-of-support.md)。 有关详细信息，请参阅[终止支持选项](/sql/sql-server/end-of-support/sql-server-end-of-life-overview)。 
  
   

## <a name="general"></a>常规

1. **Azure VM 是否支持 SQL Server 故障转移群集实例 (FCI)？**

   是的。 可以使用存储子系统的[高级文件共享 (PFS)](failover-cluster-instance-premium-file-share-manually-configure.md) 或[存储空间直通 (S2D)](failover-cluster-instance-storage-spaces-direct-manually-configure.md) 安装故障转移群集实例。 高级文件共享提供可满足许多工作负载需求的 IOPS 和吞吐量。 对于 IO 密集型工作负载，请考虑使用存储空间直通（基于托管的高级磁盘或超级磁盘）。 或者，你可以使用第三方群集或存储解决方案，如 [Azure 虚拟机上 SQL Server 的高可用性和灾难恢复](business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions)中所述。

   > [!IMPORTANT]
   > 目前，Azure 上的 SQL Server FCI 不支持 _完整的_ [SQL Server IaaS 代理扩展](sql-server-iaas-agent-extension-automate-management.md)。 我们建议你从参与 FCI 的 VM 中卸载 _完整_ 扩展，并改为在 _轻型_ 模式下安装该扩展。 此扩展支持自动备份和修补等功能，以及 SQL Server 的一些门户功能。 卸载 _完整_ 代理以后，这些功能将不适用于 SQL Server VM。

1. SQL Server VM 与 SQL 数据库服务之间的差别是什么？

   从概念上讲，在 Azure 虚拟机上运行 SQL Server 与在远程数据中心运行 SQL Server 并没什么不同。 相比之下，[Azure SQL 数据库](../../database/sql-database-paas-overview.md) 可提供数据库即服务。 使用 SQL 数据库时，无法访问托管数据库的计算机。 有关完整比较，请参阅[选择云 SQL Server 选项：Azure SQL (PaaS) 数据库或 Azure VM 上的 SQL Server (IaaS)](../../azure-sql-iaas-vs-paas-what-is-overview.md)。

1. **如何在 Azure VM 上安装 SQL Data Tools？**

    从 [Microsoft SQL Server 数据工具 - Visual Studio 2013 商业智能](https://www.microsoft.com/download/details.aspx?id=42313)下载并安装 SQL 数据工具。

1. **SQL Server VM 是否支持使用 MSDTC 的分布式事务？**
   
    是的。 SQL Server 2016 SP2 和更高版本支持本地 DTC。 但是，在使用 Always On 可用性组时必须对应用程序进行测试，因为故障转移期间正在进行的事务将会失败，并且必须重试。 从 Windows Server 2019 开始将会推出群集 DTC。 

## <a name="resources"></a>资源

**Windows VM** ：

* [Windows VM 上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [在 Windows VM 上预配 SQL Server](create-sql-vm-portal.md)
* [将数据库迁移到 Azure VM 上的 SQL Server](migrate-to-vm-from-sql-server.md)
* [Azure 虚拟机中 SQL Server 的高可用性和灾难恢复](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Azure 虚拟机中 SQL Server 的性能最佳做法](performance-guidelines-best-practices.md)
* [Azure 虚拟机中的 SQL Server 的应用程序模式和开发策略](application-patterns-development-strategies.md)

**Linux VM** ：

* [Linux VM 上的 SQL Server 概述](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [在 Linux VM 上预配 SQL Server](../linux/sql-vm-create-portal-quickstart.md)
* [常见问题 (Linux)](../linux/frequently-asked-questions-faq.md)
* [“Linux 上的 SQL Server”文档](/sql/linux/sql-server-linux-overview)
