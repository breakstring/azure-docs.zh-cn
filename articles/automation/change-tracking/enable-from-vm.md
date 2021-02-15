---
title: 从 Azure VM 启用 Azure 自动化更改跟踪和库存
description: 本文介绍如何从 Azure VM 启用更改跟踪和库存。
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 61b45d6f6414b1e8e1f48f6d46957b21b9b8c58b
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052492"
---
# <a name="enable-change-tracking-and-inventory-from-an-azure-vm"></a>从 Azure VM 启用更改跟踪和清单

本文介绍如何使用 Azure VM 在其他计算机上启用 [更改跟踪和清单](overview.md) 。 若要大规模启用 Azure VM，必须使用更改跟踪和库存启用现有 VM。

> [!NOTE]
> 在启用更改跟踪和库存时，只有某些区域支持链接 Log Analytics 工作区和自动化帐户。 有关支持的映射对的列表，请参阅[自动化帐户和 Log Analytics 工作区的区域映射](../how-to/region-mappings.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 用于管理计算机的[自动化帐户](../automation-security-overview.md)。
* [虚拟机](../../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="enable-change-tracking-and-inventory"></a>启用更改跟踪和库存

1. 在 [Azure 门户](https://portal.azure.com)中，选择“虚拟机”，或在“主页”页上搜索并选择“虚拟机” 。

2. 选择要为其启用更改跟踪和库存的 VM。 VM 可以位于任何区域，无论自动化帐户的位置如何。

3. 在 VM 页上，选择“配置管理”下的“库存”或“更改跟踪”  。

4. 必须拥有 `Microsoft.OperationalInsights/workspaces/read` 权限才能确定是否为工作区启用了 VM。 若要了解所需的其他权限，请参阅[功能设置权限](../automation-role-based-access-control.md#feature-setup-permissions)。 若要了解如何一次启用多台计算机，请参阅[从自动化帐户启用更改跟踪和库存](enable-from-automation-account.md)。

5. 选择 Log Analytics 工作区和自动化帐户，然后单击“启用”为 VM 启用更改跟踪和库存。 安装最多需要 15 分钟才能完成。

## <a name="next-steps"></a>后续步骤

* 有关使用此功能的详细信息，请参阅 [管理更改跟踪](manage-change-tracking.md) 和 [管理清单](manage-inventory-vms.md)。

* 若要排查该功能的常见问题，请参阅[排查更改跟踪和清单问题](../troubleshoot/change-tracking.md)。