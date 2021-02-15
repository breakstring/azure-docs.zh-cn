---
title: CAF 迁移登陆区域蓝图示例概述
description: 适用于 Azure 的云采用框架 (CAF) 迁移登陆区域蓝图示例的概述和体系结构。
ms.date: 09/14/2020
ms.topic: sample
ms.openlocfilehash: 6b8e3484690d263a43d3824c054b28344ea07fb1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531672"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-migration-landing-zone-blueprint-sample"></a>适用于 Azure 的 Microsoft 云采用框架迁移登陆区域蓝图示例概述

适用于 Azure 的 Microsoft 云采用框架 (CAF) 迁移登陆区域蓝图是一组基础结构，用于帮助你安排迁移你的第一个工作负荷并管理你的云资产，使其与 CAF 相符合。

[CAF 基础](../caf-foundation/index.md)蓝图示例扩展了此示例。

## <a name="architecture"></a>体系结构

CAF 迁移登陆区域蓝图示例在 Azure 中部署基础结构资源，这些资源可供组织用来准备订阅，以便将虚拟机迁移到其中。 它还可帮助将管理云资产所需的治理控制实施到位。 此示例将部署并强制实施资源、策略和模板，从而使组织能够自信地开始使用 Azure。

:::image type="complex" source="../../media/caf-blueprints/caf-migration-landing-zone-architecture.png" alt-text="C A F 迁移登陆区域，图像说明了作为 C A F 指南的一部分为初始登陆区域安装的内容。" border="false":::
   介绍 Azure 体系结构，该体系结构是通过部署 C A F 迁移蓝图来实现的。  它适用于具有资源组的订阅，该订阅包含 Azure 虚拟网络、用于存储日志的存储帐户、Log analytics 配置为在存储帐户中存储。  它还说明已配置的 Azure Key Vault 和已创建的 Azure Migrate 初始安装程序。  所有这些核心基础结构均使用 Azure Active Directory 进行访问。     
:::image-end:::

此环境包括多项 Azure 服务，这些服务用于根据 ISO 27001 标准提供安全的、全面受监视的、面向企业的治理。 此环境包括：

- 一个 [Azure Key Vault](../../../../key-vault/general/overview.md) 实例，用于托管对共享服务环境中部署的证书、密钥和机密使用的机密
- 部署 [Log Analytics](../../../../azure-monitor/overview.md)，以便确保从开始迁移起所有操作和服务都记录到一个中心位置
- 部署 [Azure 虚拟网络](../../../../virtual-network/virtual-networks-overview.md)，以便为虚拟机提供隔离的网络和子网。
- 部署 [Azure Migrate 项目](../../../../migrate/migrate-services-overview.md)以用于发现和评估。 我们正在添加用于服务器评估、服务器迁移、数据库评估和数据库迁移的工具。  


所有这些元素遵守 [Azure 体系结构中心 - 参考体系结构](/azure/architecture/reference-architectures/)中发布的行之有效的做法。

> [!NOTE]
> CAF 迁移蓝图为你的工作负荷布设了登陆区域。 你仍需要基于此基础架构执行虚拟机/数据库的评估和迁移。

有关详细信息，请参阅[适用于 Azure 的 Microsoft 云采用框架 - 迁移](/azure/architecture/cloud-adoption/migrate/)。

## <a name="next-steps"></a>后续步骤

你已查看了 CAF 迁移登陆区域蓝图示例的概述和体系结构。

> [!div class="nextstepaction"]
> [CAF 迁移登陆区域蓝图 - 部署步骤](./deploy.md)

有关蓝图及其使用方式的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。
