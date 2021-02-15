---
title: 有关从经典部署模型迁移到 Azure Resource Manager 部署模型的常见问题
description: 有关从经典部署模型迁移到 Azure 资源管理器的常见问题。
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: abf4e4621a7e42829032923a67d21c5322f432ec
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98232720"
---
# <a name="frequently-asked-questions-about-classic-to-azure-resource-manager-migration"></a>有关从经典部署模型迁移到 Azure Resource Manager 部署模型的常见问题

> [!IMPORTANT]
> 目前，大约有 90% 的 IaaS VM 在使用 [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)。 自 2020 年 2 月 28 日起，经典 VM 已弃用，并将于 2023 年 3 月 1 日完全停用。 [详细了解]( https://aka.ms/classicvmretirement)此弃用以及[它对你的影响](./classic-vm-deprecation.md#how-does-this-affect-me)。

## <a name="what-is-azure-service-manager-and-what-does-it-mean-by-classic"></a>什么是 Azure Service Manager，“经典”的含义是什么？

IaaS VM（经典）中的“经典”一词是指由 Azure Service Manager (ASM) 托管的 VM。 Azure Service Manager (ASM) 是 Azure 的旧控制平面，负责创建、管理、删除 VM 和执行其他控制平面操作。 

## <a name="what-is-azure-resource-manager"></a>什么是 Azure 资源管理器？

[Azure 资源管理器](../azure-resource-manager/management/overview.md) 是 Azure 的最新控制平面，负责创建、管理、删除 VM 和执行其他控制平面操作。 

## <a name="what-is-the-time-required-for-migration"></a>迁移需要多长时间？

计划迁移和执行迁移很大程度都取决于体系结构的复杂性，这可能需要几个月的时间。  

## <a name="what-is-the-definition-of-a-new-customer-on-iaas-vms-classic"></a>IaaS VM（经典版）新客户的定义是什么？

2020 年 2 月（开始弃用之前的一个月）的订阅中没有 IaaS VM（经典版）的客户则视为新客户。 

## <a name="what-is-the-definition-of-an-existing-customer-on-iaas-virtual-machines-classic"></a>IaaS 虚拟机（经典版）现有客户的定义是什么？

2020 年 2 月在其订阅中有过活跃的或已停止但曾分配过的 IaaS VM（经典版）的客户则视为现有客户。 只有这些客户才会在 2023 年 3 月 1 日之后将其 VM 从 Azure Service Manager 迁移到 Azure 资源管理器。 

## <a name="why-am-i-getting-an-error-stating-newclassicvmcreationnotallowedforsubscription"></a>为什么会收到“NewClassicVMCreationNotAllowedForSubscription”这样的错误信息？

停用过程开始后，IaaS VM（经典版）不再适用于新客户。 我们已将你识别为新客户，因此你无权执行该操作。 强烈建议使用 Azure 资源管理器。 如果无法通过 Azure 资源管理器使用 Azure VM，请联系支持人员，以将订阅加入允许列表。

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>此迁移计划是否影响 Azure 虚拟机上运行的任何现有服务或应用程序？ 

2023 年 3 月 1 日前都是 IaaS VM （经典版），所以不会产生影响。 IaaS VM（经典版）是公开上市的完全受支持的服务。 可以继续使用这些资源拓展你在 Microsoft Azure 上的足迹。 2023 年 3 月 1 日起会全面停用这些 VM，并且会停止或解除分配任何活跃的或分配的 VM。

这不会影响到其他经典的资源，如云服务（经典版）、存储帐户（经典版）等。

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>如果我近期不打算迁移，我的 VM 会发生什么情况？ 

2023 年 3 月 1 日起会全面停用 IaaS VM（经典版），并且会停止或解除分配任何活跃的或分配的 VM。 为避免影响业务，我们强烈建议现在就开始计划迁移并在 2023 年 3 月 1 日之前完成迁移。 我们近期不会淘汰现有的经典 API、云服务和资源模型。 我们想要通过 Resource Manager 部署模型中提供的高级功能，让迁移变得简单。 我们建议现在开始计划将这些资源迁移到 Azure 资源管理器。 

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>对于我现有的工具而言，此迁移计划有何意义？ 

将工具更新为 Resource Manager 部署模型，是必须在迁移计划中考虑的最重要的更改之一。

## <a name="how-long-will-the-management-plane-downtime-be"></a>管理平面的停机时间持续多久？ 

这取决于迁移的资源数量。 对于较小型部署（几十个 VM），整个迁移过程应该不超过一小时。 如果是大规模部署（数百个 VM），迁移过程可能需要花费几个小时。

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>在 Resource Manager 中提交迁移的资源之后，是否还可以回滚？ 

只要资源处于准备就绪状态，就可以中止迁移。 但是，在通过提交操作成功迁移资源之后，就不支持回滚。

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>提交操作失败时，是否可以回滚迁移？ 

如果提交操作失败，就无法中止迁移。 包括提交操作在内的所有迁移操作都是幂等的。 因此，建议在片刻之后重试操作。 如果仍遇到错误，请创建支持票证。

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>如果我必须使用 Resource Manager 下的 IaaS，是否必须购买其他 ExpressRoute 线路？ 

不是。 我们近期实现了 [将 ExpressRoute 线路从经典部署模型转移到 Resource Manager 部署模型](../expressroute/expressroute-move.md)。 如果已有 ExpressRoute 线路，则不需要购买新的线路。

## <a name="what-if-i-had-configured-azure-role-based-access-control-policies-for-my-classic-iaas-resources"></a>如果已经为经典 IaaS 资源配置 Azure 基于角色的访问控制策略，该怎么办？ 

在迁移期间，资源从经典资源转换为 Resource Manager 资源。 因此，建议计划需要在迁移之后进行的 Azure RBAC 策略更新。

## <a name="i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>我已在保管库中备份了经典 VM。 是否可以将 VM 从经典模式迁移到 Resource Manager 模式并在恢复服务保管库中保护它们？

将 VM 从经典模式移到资源管理器模式时，在迁移之前进行的备份不会迁移到新迁移的资源管理器 VM 中。 但是，如果希望保留经典 VM 的备份，请在迁移之前执行以下步骤。 

1. 在恢复服务保管库中，请参阅 " **备份项** " 边栏选项卡并选择 VM。 
2. 单击 "停止备份"。 在下拉菜单中选择 "保留备份数据"。

> [!NOTE]
> 此选项会停止将来所有备份作业对 VM 的保护。 但是，Azure 备份服务将保留已备份的恢复点。  你需要付费才能将恢复点保留在保管库中（有关详细信息，请参阅 [Azure 备份定价](https://azure.microsoft.com/pricing/details/backup/)）。 如果需要，你将能够还原 VM。 如果决定恢复 VM 保护，则可以使用“恢复备份”选项。
>
>

若要将虚拟机迁移到资源管理器模式， 

1. 从 VM 中删除备份/快照扩展。
2. 将虚拟机从经典模式迁移到 Resource Manager 模式。 确保与虚拟机对应的存储和网络信息也已迁移到 Resource Manager 模式。

此外，如果想要备份迁移的 VM，请转至“虚拟机管理”边栏选项卡，以[启用备份](../backup/quick-backup-vm-portal.md#enable-backup-on-a-vm)。

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>我是否可以验证订阅或资源，以查看其是否能够迁移？ 

是的。 在平台支持的迁移选项中，准备迁移的第一个步骤，就是验证资源是否能够进行迁移。 如果验证操作失败，用户会收到包含无法完成迁移的所有原因的消息。

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>如果我在准备要迁移的 IaaS 资源时遇到配额错误，会发生什么情况？ 

建议用户中止迁移，并记录支持请求，以在要迁移 VM 的区域中增加配额。 配额请求经过批准后，可以重新开始执行迁移步骤。

## <a name="how-do-i-report-an-issue"></a>如何报告问题？ 

请使用关键字 ClassicIaaSMigration 将迁移相关的问题发布到[有关 VM 的 Microsoft 问答页](/answers/topics/azure-virtual-machines.html)。 建议将所有问题都发布在此论坛上。 如果有支持协定，也欢迎你记录支持票证。

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>如果我不喜欢平台在迁移期间选择的资源名称，该怎么做？ 

在经典部署模型中为其显式提供名称的所有资源都会在迁移期间得到保留。 在某些情况下，会创建新资源。 例如：为每个 VM 创建网络接口。 目前无法控制迁移期间所创建的这些新资源的名称。 请在 [Azure 反馈论坛](https://feedback.azure.com)上针对此功能进行投票。

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>是否可以使用授权链接迁移跨订阅使用的 ExpressRoute 线路？ 

不停机无法自动迁移使用跨订阅授权链接的 ExpressRoute 线路。 我们提供了有关如何使用手动步骤迁移这些线路的指南。 有关步骤和详细信息，请参阅[将 ExpressRoute 线路和关联的虚拟网络从经典部署模型迁移到 Resource Manager 部署模型](../expressroute/expressroute-migration-classic-resource-manager.md)。

## <a name="i-got-the-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated"></a>我收到消息，指出 *“VM 报告总体代理状态为‘未就绪’。因此，此 VM 无法迁移。请确保 VM 代理报告总体代理状态为‘就绪’”* 或 *“VM 包含未报告其状态的扩展。因此，此 VM 无法迁移。”*

当 VM 未建立到 Internet 的出站连接时，会收到此消息。 VM 代理使用出站连接访问 Azure 存储帐户，每隔五分钟更新一次代理状态。


## <a name="next-steps"></a>后续步骤

对于 Linux：

* [平台支持的从经典部署模型到 Azure Resource Manager 部署模型的 IaaS 资源迁移概述](./migration-classic-resource-manager-overview.md)
* [有关平台支持的从经典部署模型到 Azure Resource Manager 部署模型的迁移的技术深入探讨](migration-classic-resource-manager-deep-dive.md)
* [规划从经典部署模型到 Azure Resource Manager 的 IaaS 资源迁移](migration-classic-resource-manager-plan.md)
* [使用 PowerShell 将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器](migration-classic-resource-manager-ps.md)
* [使用 CLI 将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器](migration-classic-resource-manager-cli.md)
* [用于帮助将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器部署模型的社区工具](migration-classic-resource-manager-community-tools.md)
* [查看最常见的迁移错误](migration-classic-resource-manager-errors.md)

对于 Windows：

* [平台支持的从经典部署模型到 Azure Resource Manager 部署模型的 IaaS 资源迁移概述](migration-classic-resource-manager-overview.md)
* [有关平台支持的从经典部署模型到 Azure Resource Manager 部署模型的迁移的技术深入探讨](migration-classic-resource-manager-deep-dive.md)
* [规划从经典部署模型到 Azure Resource Manager 的 IaaS 资源迁移](migration-classic-resource-manager-plan.md)
* [使用 PowerShell 将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器](migration-classic-resource-manager-ps.md)
* [使用 CLI 将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器](migration-classic-resource-manager-cli.md)
* [用于帮助将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器部署模型的社区工具](migration-classic-resource-manager-community-tools.md)
* [查看最常见的迁移错误](migration-classic-resource-manager-errors.md)