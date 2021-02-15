---
title: 什么是 Privileged Identity Management？ - Azure AD | Microsoft Docs
description: 概述 Azure AD Privileged Identity Management (PIM)。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 09/29/2020
ms.author: curtand
ms.custom: pim ; azuread-video-2020
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3c7003ce3e8c65f6fdb7f3d847947114aee251f
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370179"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>什么是 Azure AD Privileged Identity Management？

 Privileged Identity Management (PIM) 是 Azure Active Directory (Azure AD) 中用于管理、控制和监视对组织中重要资源的访问的服务。 这些资源包括 Azure AD、Azure 和其他 Microsoft Online Services（例如 Microsoft 365 或 Microsoft Intune）中的资源。 以下视频介绍了重要的 PIM 概念和功能。
<br><br>

> [!VIDEO https://www.youtube.com/embed/f-0K7mRUPpQ]

## <a name="reasons-to-use"></a>使用原因

组织希望尽量减少拥有访问权限的人员以保护信息或资源安全，因为这样可以减少恶意行动者获得相应访问权限，或者已授权用户无意中影响敏感资源的可能性。 但是，用户仍需在 Azure AD、Azure、Microsoft 365 或 SaaS 应用中执行特权操作。 组织可以授予用户对 Azure 资源和 Azure AD 的实时特许访问权限。 在这种情况下，需要监督这些用户使用其管理员特权执行了哪些操作。

## <a name="what-does-it-do"></a>它有什么作用？

Privileged Identity Management 提供基于时间和基于审批的角色激活，用于缓解所关注资源上出现的访问权限过度、不必要或滥用的风险。 下面是 Privileged Identity Management 的一些重要功能：

- 提供对 Azure AD 和 Azure 资源的 **实时** 特权访问权限
- 使用开始和结束日期分配对资源的 **限时** 访问权限
- 要求获得 **批准** 才能激活特权角色
- 强制要求在激活任何角色时执行 **多重身份验证**
- 使用 **理由** 来了解用户激活角色的原因
- 激活特权角色时获取 **通知**
- 开展 **访问评审** ，以确保用户仍然需要角色
- 下载 **审核历史记录** 进行内部或外部审核

## <a name="what-can-i-do-with-it"></a>它的作用是什么？

设置 Privileged Identity Management 时，左侧导航菜单中会显示“任务”、“管理”和“活动”选项。    作为管理员，你将在管理 Azure AD 角色，管理 Azure 资源角色或特权访问组等选项之间进行选择 。 选择要管理的选项时，将看到该选项的相应选项集。

![Azure 门户中 Privileged Identity Management 的屏幕截图](./media/pim-configure/pim-quickstart.png)

## <a name="who-can-do-what"></a>用户及其权限

对于 Privileged Identity Management 中的 Azure AD 角色，只有充当“特权角色管理员”或“全局管理员”角色的用户可以管理其他管理员分配。 可以[授予其他管理员访问权限以管理 Privileged Identity Management](pim-how-to-give-access-to-pim.md)。 全局管理员、安全管理员、全局读取者和安全读取者也可在 Privileged Identity Management 中查看 Azure AD 角色的分配。

对于 Privileged Identity Management 中的 Azure 资源角色，只有订阅管理员、资源所有者或资源用户访问管理员可以管理其他管理员的分配。 默认情况下，充当特权角色管理员、安全管理员或安全读取者的用户无权在 Privileged Identity Management 中查看 Azure 资源角色的分配。

## <a name="scenarios"></a>方案

Privileged Identity Management 支持以下方案：

### <a name="privileged-role-administrator-permissions"></a>特权角色管理员权限

- 启用对特定角色的审批
- 指定要审批请求的审批者用户或组
- 查看所有特权角色的请求和审批历史记录

### <a name="approver-permissions"></a>审批者权限

- 查看挂起的审批（请求）
- 批准或拒绝角色提升请求（单个和批量）
- 提供批准或拒绝的理由

### <a name="eligible-role-user-permissions"></a>有资格的角色用户权限

- 请求激活需要审批的角色
- 查看要激活的请求的状态
- 批准激活后，在 Azure AD 中完成任务

## <a name="terminology"></a>术语

为了更好地理解 Privileged Identity Management 及其文档，应查看以下术语。

| 术语或概念 | 角色分配类别 | 说明 |
| --- | --- | --- |
| 符合条件 | 类型 | 要求用户在使用角色之前执行一项或多项操作的角色分配。 如果用户符合某个角色的条件，则意味着他们在需要执行特权任务时可以激活该角色。 用户无论具有永久角色分配还是合格角色分配，获得的访问权限并无差异。 唯一的差异在于，有些用户并不是一直需要该访问权限。 |
| 活动 | 类型 | 不要求用户在使用角色之前执行任何操作的角色分配。 分配为“活动”的用户拥有分配给该角色的特权。 |
| 激活 |  | 合格用户在使用角色之前执行一项或多项操作的过程。 操作可能包括执行多重身份验证 (MFA) 检查、提供业务理由或请求获得指定审批者的批准。 |
| 已分配 | 状态 | 具有活动角色分配的用户。 |
| 已激活 | 状态 | 具有符合条件的角色分配、已执行激活角色的操作且现在处于活动状态的用户。  激活后，用户即可使用该角色，使用期限是预先配置的，过期之后需重新激活。 |
| 永久符合条件 | Duration | 使用户始终有资格激活该角色的角色分配。 |
| 永久活动 | Duration | 使用户无需执行任何操作，始终可以使用该角色的角色分配。 |
| 在过期之前符合条件 | Duration | 使用户在指定的开始和结束日期范围内有资格激活该角色的角色分配。 |
| 在过期之前处于活动状态 | Duration | 使用户无需执行任何操作，可在指定的开始和结束日期范围内使用该角色的角色分配。 |
| 实时 (JIT) 访问 |  | 一种访问模式。在此模式下，用户会收到执行特权任务的临时权限，防止恶意用户或未授权用户在权限过期后获得访问权限。 只有在用户需要的情况下，才会授予访问权限。 |
| 最低访问权限原则 |  | 一种建议的安全做法，仅为每个用户提供所需的最低权限，以便完成有权执行的任务。 此做法会尽量减少全局管理员的数目，并使用适合特定方案的特定管理员角色。 |

## <a name="license-requirements"></a>许可要求

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

有关用户许可证的信息，请参阅[使用 Privileged Identity Management 所要满足的许可证要求](subscription-requirements.md)。

## <a name="next-steps"></a>后续步骤

- [使用 Privileged Identity Management 的许可要求](subscription-requirements.md)
- [确保 Azure AD 中混合部署和云部署的特权访问安全性](../roles/security-planning.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [部署 Privileged Identity Management](pim-deployment-plan.md)
