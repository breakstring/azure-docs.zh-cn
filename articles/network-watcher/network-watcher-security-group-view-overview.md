---
title: Azure 网络观察程序中的有效安全规则视图简介 |Microsoft Docs
description: 此页概述了网络观察程序中有效安全规则视图功能
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: damendo
ms.openlocfilehash: d172ba7353b7ac4c490584cd7c84871c6244f214
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961815"
---
# <a name="introduction-to-effective-security-rules-view-in-azure-network-watcher"></a>Azure 网络观察程序中的有效安全规则视图简介

网络安全组可以在子网级别或 NIC 级别关联。 在子网级别关联时，网络安全组将应用于子网中的所有 VM 实例。 有效安全规则视图针对虚拟机返回在 NIC 和子网级别关联的所有已配置 NSG 和规则，以供深入了解配置。 此外，VM 中的每个 NIC 将返回有效的安全规则。 使用有效安全规则视图，可以评估网络漏洞的 VM，如打开端口。 还可以基于[已配置安全规则和已批准安全规则之间的比较](network-watcher-nsg-auditing-powershell.md)，验证网络安全组是否按预期方式工作。

更多扩展用例在安全合规性和审核方面。 在组织中可以定义一组规范性安全规则作为安全监管模型。 通过将规范性规则与网络中每个 VM 的有效规则相比较，可以编程方式实现定期合规性审核。

在门户中，会为每个网络接口显示规则，并按入站和出站对规则分组。 这会提供一个应用于虚拟机的规则的简单视图。 无论哪个标签页，提供的下载按钮均可将所有安全规则轻松地转换为 CSV 文件。

![安全组视图][1]

可以选择规则并打开一个新的边栏选项卡以显示网络安全组以及源和目标前缀。 从此边栏选项卡可以直接导航到网络安全组资源。

![明细][2]

### <a name="next-steps"></a>后续步骤

还可以通过下面列出的其他方法，使用有效安全组功能：
* [REST API](/rest/api/virtualnetwork/NetworkInterfaces/ListEffectiveNetworkSecurityGroups)
* [PowerShell](/powershell/module/az.network/get-azeffectivenetworksecuritygroup?view=azps-4.4.0)
* [Azure CLI](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-list-effective-nsg)

通过访问[使用 PowerShell 审核网络安全组设置](network-watcher-nsg-auditing-powershell.md)，了解如何审核网络安全组设置

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png