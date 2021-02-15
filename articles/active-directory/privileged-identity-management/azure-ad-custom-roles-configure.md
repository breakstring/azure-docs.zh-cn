---
title: 配置 Azure AD 自定义角色 - Privileged Identity Management (PIM)
description: 如何在 Privileged Identity Management (PIM) 中配置 Azure AD 自定义角色
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb23e60539c704dac457ab6e8706ec0cfe350ed9
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835316"
---
# <a name="configure-azure-ad-custom-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中配置 Azure AD 自定义角色

特权角色管理员可以更改当用户激活其自定义角色分配时应用到该用户的角色设置，以及适用于分配自定义角色的其他应用程序管理员的角色设置。

> [!NOTE]
> 在预览版中，Azure AD 自定义角色未集成内置的目录角色。 此功能的正式版发布后，可在内置的角色体验中进行角色管理。 如果看到以下横幅，则应[在内置角色体验中](pim-how-to-activate-role.md)管理这些角色，而本文不适用：
>
> [![选择“Azure AD”>“Privileged Identity Management”](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="open-role-settings"></a>打开角色设置

遵循以下步骤打开 Azure AD 角色的设置。

1. 在 Azure 门户中使用分配给特权角色管理员角色的用户帐户登录到 [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart)。
1. 选择“Azure AD 自定义角色(预览版)”。

    ![选择 Azure AD 自定义角色预览版，查看符合条件的角色分配](./media/azure-ad-custom-roles-configure/settings-list.png)

1. 选择“设置”打开“设置”页。 选择要配置其设置的角色。
1. 选择“编辑”打开“角色设置”页。

    ![屏幕截图显示“角色设置详细信息”页，其中选择了“编辑”操作。](./media/azure-ad-custom-roles-configure/edit-settings.png)

## <a name="role-settings"></a>角色设置

可以配置多个设置。

### <a name="assignment-duration"></a>分配持续时间

配置角色的设置时，可以从用于每种分配类型（“符合条件”或“活动”）的两个分配持续时间选项中进行选择·。 在 Privileged Identity Management 中将成员分配到角色时，这些选项将成为默认的最大持续时间。

可以选择其中一个“符合条件”的分配持续时间选项：

- **允许永久性符合条件分配**：管理员可以分配永久性符合条件成员身份。
- **使符合条件分配在以下时间后过期**：管理员可以要求所有符合条件分配都具有指定的开始和结束日期。

此外，可以选择其中一个“活动”分配持续时间选项：

- **允许永久性活动分配**：管理员可以分配永久性活动成员身份。
- **使活动分配在以下时间后过期**：管理员可以要求所有活动分配都具有指定的开始和结束日期。

### <a name="require-azure-ad-multi-factor-authentication"></a>需要 Azure AD 多重身份验证

Privileged Identity Management 为两个不同方案提供了 Azure AD 多重身份验证的可选强制。

- **要求在活动分配时进行多重身份验证**

  如果你只是想要短时间地（例如一天）将某个成员分配到某个角色，要求分配的成员请求激活可能需要花费较长时间。 在这种情况下，当用户激活其角色分配时，Privileged Identity Management 无法强制实施多重身份验证，因为从分配的那一刻，该用户的角色已处于活动状态。 为确保负责分配角色的管理员是其本人，请选中“进行活动分配时要求执行多重身份验证”框。

- **要求在激活时进行多重身份验证**

  你可以要求分配给角色的符合条件的用户注册 Azure AD 多重身份验证才能激活。 此过程可确保请求激活的用户一定就是其本人。 强制执行此选项可以在用户帐户可能已遭入侵的情况下保护关键角色。 若要在激活之前要求符合条件的成员运行 Azure AD 多重身份验证，请选择 " **激活时需要多重身份验证** " 复选框。

有关详细信息，请参阅[多重身份验证和 Privileged Identity Management](pim-how-to-require-mfa.md)。

### <a name="activation-maximum-duration"></a>最长激活持续时间

使用“最长激活持续时间”滑块是角色在过期前保持活动状态的最大时间（以小时为单位）。 此值可以是 1 到 24 个小时。

### <a name="require-justification"></a>需要理由

可以要求成员在活动分配或激活时输入理由。 若需要理由，请选中“在活动分配时需要理由”复选框或“在激活时需要理由”框。

### <a name="require-approval-to-activate"></a>需要批准才能激活

如果要求批准以激活角色，请按照以下步骤操作。

1. 选中“需要批准以激活”复选框。
1. 选择“选择审批者”打开“选择成员或组”列表。

    ![打开 Azure AD 自定义角色以编辑设置](./media/azure-ad-custom-roles-configure/select-approvers.png)

1. 至少选择一个成员或组，然后单击“选择”。 必须至少选择 1 个审批者。 没有默认的审批者。 所选项将出现在所选审批者列表中。
1. 指定角色设置后，选择“更新”以保存更改。

## <a name="next-steps"></a>后续步骤

- [激活 Azure AD 自定义角色](azure-ad-custom-roles-activate.md)
- [分配 Azure AD 自定义角色](azure-ad-custom-roles-assign.md)
- [删除或更新 Azure AD 自定义角色分配](azure-ad-custom-roles-update-remove.md)
- [Azure AD 中的角色定义](../roles/permissions-reference.md)
