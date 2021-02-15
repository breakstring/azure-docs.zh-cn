---
title: 在 PIM 中分配 Azure AD 角色 - Azure Active Directory | Microsoft Docs
description: 了解如何在 Azure AD Privileged Identity Management (PIM) 中分配 Azure AD 角色。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 09/16/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0e230a975748fe2f737c4b8fe8491887351d387
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004664"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中分配 Azure AD 角色

使用 Azure Active Directory (Azure AD)，全局管理员可以完成 **永久性的** Azure AD 管理员角色分配。 可以使用 [Azure 门户](../roles/permissions-reference.md)或 [PowerShell 命令](/powershell/module/azuread#directory_roles)创建这些角色分配。

Azure AD Privileged Identity Management (PIM) 服务还允许特权角色管理员进行永久管理员角色分配。 此外，特权角色管理员可将用户设置为 Azure AD 管理员角色的 **合格** 用户。 符合条件的管理员可在需要时激活角色，在完成任务后，其权限随即失效。

## <a name="determine-your-version-of-pim"></a>确定 PIM 版本

从 2019 年 11 月开始，Privileged Identity Management 的 Azure AD 角色部分将更新为与 Azure 资源角色的体验相匹配的新版本。 这将创建附加功能以及[对现有 API 的更改](azure-ad-roles-features.md#api-changes)。 在推出新版本时，本文中遵循的过程取决于当前拥有的 Privileged Identity Management 版本。 按照本部分中的步骤确定所拥有的 Privileged Identity Management 的版本。 了解 Privileged Identity Management 版本之后，可以选择本文中与该版本匹配的过程。

1. 以具有[特权角色管理员](../roles/permissions-reference.md#privileged-role-administrator)角色的用户身份登录到 [Azure 门户](https://portal.azure.com/)。
1. 打开“Azure AD Privileged Identity Management”。  如果在概述页的顶部有横幅，请按照本文“新版本”选项卡中的说明进行操作  。 否则，请按照“先前版本”选项卡中的说明操作  。

  [![选择“Azure AD”>“Privileged Identity Management”。](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[新版本](#tab/new)

## <a name="assign-a-role"></a>分配角色

遵循以下步骤可使用户符合 Azure AD 管理员角色的条件。

1. 使用“[特权角色管理员](../roles/permissions-reference.md#privileged-role-administrator)”角色成员的用户身份登录到 [Azure 门户](https://portal.azure.com/)。

    有关如何授予其他管理员访问权限以管理 Privileged Identity Management 的信息，请参阅[授予其他管理员访问权限以管理 Privileged Identity Management](pim-how-to-give-access-to-pim.md)。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择“Azure AD 角色”。

1. 选择“角色”，查看 Azure AD 权限的角色列表。

    ![已选择“添加分配”操作的“角色”页的屏幕截图。](./media/pim-how-to-add-role-to-user/roles-list.png)

1. 选择“添加分配”以打开“添加分配”页。 

1. 选择“选择角色”以打开“选择角色”页 。

    ![“新建分配”窗格](./media/pim-how-to-add-role-to-user/select-role.png)

1. 依次选择要分配的角色、要向角色分配的成员、“下一步”。

1. 在“成员身份设置”窗格的“分配类型”列表中，选择“合格”或“活动”。

    - “合格”  分配要求该角色的成员执行某个操作才能使用该角色。 操作可能包括执行多重身份验证 (MFA) 检查、提供业务理由或请求获得指定审批者的批准。

    - “活动”  分配不要求成员执行任何操作便可使用该角色。 分配为“活动”的成员始终具有分配给该角色的权限。

1. 若要指定特定分配持续时间，请添加开始和结束日期与时间框。 完成后，选择“分配”以创建新的角色分配。

    ![成员身份设置 - 日期和时间](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. 分配角色后，会显示分配状态通知。

    ![新建分配 - 通知](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="assign-a-role-with-restricted-scope"></a>向角色分配受限范围

对于某些角色，所授予权限的范围可以限制为单个管理单元、服务主体或应用程序。 若要分配范围为一个管理单元的角色，则可参考此过程。 有关支持管理单元范围的角色列表，请参阅[分配范围限定为一个管理单元的角色](../roles/admin-units-assign-roles.md)。 此功能目前正在向 Azure AD 组织推出。

1. 使用特权角色管理员权限登录到 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。

1. 选择“Azure Active Directory” > “角色和管理员”。 。

1. 选择“用户管理员”。

    ![在门户中打开角色时，可以使用“添加分配”命令](./media/pim-how-to-add-role-to-user/add-assignment.png)

1. 选择“添加分配”。

    ![如果角色支持范围，可以选择一个范围](./media/pim-how-to-add-role-to-user/add-scope.png)

1. 在“添加分配”页上，你可以：

   - 选择要分配给角色的用户或组
   - 选择角色范围（本例中为管理单元）
   - 对于范围，选择一个管理单元

有关创建管理单元的详细信息，请参阅[添加和删除管理单元](../roles/admin-units-manage.md)。

## <a name="update-or-remove-an-existing-role-assignment"></a>更新或删除现有的角色分配

按照以下步骤更新或删除现有的角色分配。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择“Azure AD 角色”。

1. 选择“角色”以查看 Azure AD 的角色列表。

1. 选择要更新或删除的角色。

1. 在“合格角色”或“活动角色”选项卡上查找角色分配。

    ![更新或删除角色分配](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. 选择“更新”或“删除”以更新或删除角色分配。

# <a name="previous-version"></a>[先前版本](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>使用户符合角色的条件

遵循以下步骤可使用户符合 Azure AD 管理员角色的条件。

1. 选择“角色”或“成员”。

    ![打开 Azure AD 角色](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. 选择“添加成员”打开“添加受管理成员”。

1. 依次选择“选择角色”、要管理的角色、“选择”。

    ![选择角色](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. 依次选择“选择成员”、要分配给角色的用户、“选择”。

    ![选择要分配的用户或组](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. 在“添加受管理成员”中，选择“确定”，将该用户添加到角色。

1. 在角色列表中，选择刚刚分配的角色以查看成员列表。

     分配角色后，选择的用户将显示在 **符合** 该角色条件的成员列表中。

    ![符合角色条件的用户](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. 用户符合角色的条件后，请告诉他们，可以按照[在 Privileged Identity Management 中激活 Azure AD 角色](pim-how-to-activate-role.md)中的说明来激活该角色。

    要求符合条件的管理员在激活过程中注册 Azure AD 多重身份验证。 如果用户无法注册 MFA 或使用 Microsoft 帐户（例如 @outlook.com），则需要将其设置为永久充当其角色。

## <a name="make-a-role-assignment-permanent"></a>将角色分配设为永久

默认情况下，新用户只符合 Azure AD 管理员角色的条件。 若要将某个角色分配设为永久，请执行以下步骤。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择“Azure AD 角色”。

1. 选择“**成员**”。

    ![成员列表](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 选择要设为永久的 **符合条件** 的角色。

1. 依次选择“更多”、“永久保留”。

    ![将角色分配设为永久](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    该角色现在会列为 **永久** 角色。

    ![已做出永久性更改的成员列表](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>从角色中删除用户

可将用户从角色分配中删除，但始终必须至少保留一个永久的全局管理员用户。 如果不确定哪些用户仍然需要其角色分配，可以[启动角色的访问权限评审](pim-how-to-start-security-review.md)。

按以下步骤从 Azure AD 管理员角色中删除特定的用户。

1. 打开“Azure AD Privileged Identity Management”。

1. 选择“Azure AD 角色”。

1. 选择“**成员**”。

    ![成员列表](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. 选择要删除的角色分配。

1. 依次选择“更多”、“删除”。

    ![删除角色](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. 在要求确认的消息中，选择“是”。

    ![确认删除](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    随即会删除该角色分配。

## <a name="authorization-error-when-assigning-roles"></a>分配角色时出现授权错误

如果你最近为某个订阅启用了 Privileged Identity Management 并且在尝试使某个用户获得某个 Azure AD 管理员角色时出现授权错误，则可能是因为 MS-PIM 服务主体尚不具有合适的权限。 MS-PIM 服务主体必须具有[用户访问管理员](../../role-based-access-control/built-in-roles.md#user-access-administrator)角色才能向其他人分配角色。 不需要等待 MS-PIM 被分配“用户访问管理员”角色，你可以手动分配该角色。

请按照以下步骤向订阅的 MS-PIM 服务主体分配“用户访问管理员”角色。

1. 以全局管理员身份登录到 Azure 门户。

1. 选择“所有服务”，然后选择“订阅”。

1. 选择订阅。

1. 选择“访问控制(IAM)”。

1. 选择“角色分配”，以在订阅范围查看角色分配的当前列表。

   ![订阅的“访问控制(IAM)”边栏选项卡](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. 检查 **MS-PIM** 服务主体是否已分配有“用户访问管理员”角色。

1. 如果不是，则选择“添加角色分配”以打开“添加角色分配”窗格。

1. 在“角色”下拉列表中，选择“用户访问管理员”角色。

1. 在“选择”列表中，找到并选择“MS-PIM”服务主体。

   ![“添加角色分配”窗格 - 为 MS-PIM 服务主体添加权限](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. 选择“保存”以分配角色。

   过一会后，MS-PIM 服务主体将分配有在订阅范围内的“用户访问管理员”角色。

   ![显示 MS-PIM 服务主体的用户访问管理角色分配的访问控制页](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中配置 Azure AD 管理员角色设置](pim-how-to-change-default-settings.md)
- [在 Privileged Identity Management 中分配 Azure 资源角色](pim-resource-roles-assign-roles.md)