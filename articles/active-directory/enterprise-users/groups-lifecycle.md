---
title: 为 Microsoft 365 组设置过期时间-Azure Active Directory |Microsoft Docs
description: 如何在 Azure Active Directory 中为 Microsoft 365 组设置过期时间
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60d71c44ebaba5b0cbcb03afa41ad15237dceaef
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547468"
---
# <a name="configure-the-expiration-policy-for-microsoft-365-groups"></a>为 Microsoft 365 组配置过期策略

本文说明如何通过为 Microsoft 365 组设置过期策略来管理这些组的生命周期。 只能为 Azure Active Directory (Azure AD) 中的 Microsoft 365 组设置过期策略。

将某个组设置为过期后：

- 随着过期时间的临近，会自动续订包含用户活动的组。
- 如果组未自动续订，则会通知组的所有者续订组。
- 删除任何未续订的组。
- 组所有者或管理员可在30天内还原删除的任何 Microsoft 365 组。

目前，只能为 Azure AD 组织中的所有 Microsoft 365 组配置一个过期策略。

> [!NOTE]
> 为 Microsoft 365 组配置和使用过期策略要求你拥有（但不一定）为应用过期策略的所有组的成员分配 Azure AD Premium 许可证。

有关如何下载和安装 Azure AD PowerShell cmdlet 的信息，请参阅 [Azure Active Directory PowerShell for Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)。

## <a name="activity-based-automatic-renewal"></a>基于活动的自动续订

使用 Azure AD 智能，将根据最近是否使用组来自动续订组。 此功能消除了组所有者对手动操作的需要，因为它基于 Outlook、SharePoint 或团队等 Microsoft 365 服务的组中的用户活动。 例如，如果某个所有者或组成员执行类似于在 SharePoint 中上传文档的操作，请访问一个团队渠道，或将电子邮件发送到 Outlook 中的组，则该组将自动续订，所有者不会获得任何续订通知。

### <a name="activities-that-automatically-renew-group-expiration"></a>自动续订组过期的活动

以下用户操作导致自动组续订：

- SharePoint：查看、编辑、下载、移动、共享或上传文件
- Outlook：通过组空间加入组、读取/写入组消息，如 Outlook Web 访问中的消息 () 
- 团队：访问团队渠道

### <a name="auditing-and-reporting"></a>审核和报告

管理员可以从 Azure AD 中的活动审核日志获取自动续订的组的列表。

![基于活动自动续订组](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>角色和权限

下面是可以为 Azure AD 中的 Microsoft 365 组配置和使用过期的角色。

角色 | 权限
-------- | --------
全局管理员、组管理员或用户管理员 | 可以创建、读取、更新或删除 Microsoft 365 组过期策略设置<br>可以续订任何 Microsoft 365 组
用户 | 可以续订其拥有的 Microsoft 365 组<br>可以还原他们拥有的 Microsoft 365 组<br>可以读取过期策略设置

有关还原已删除组的权限的详细信息，请参阅 [还原已删除的 Microsoft 365 组 Azure Active Directory](groups-restore-deleted.md)。

## <a name="set-group-expiration"></a>设置组过期

1. 使用 Azure AD 组织中的全局管理员帐户打开 [Azure AD 管理中心](https://aad.portal.azure.com) 。

2. 选择 " **组**"，然后选择 " **过期** " 以打开过期设置。
  
   ![组的过期设置](./media/groups-lifecycle/expiration-settings.png)

3. 在 " **过期** " 页上，您可以：

    - 设置组的生存期（天）。 您可以选择一个预设值，或自定义值 (应为30天或更) 。
    - 指定当组没有所有者时续订和过期通知应发送到的电子邮件地址。
    - 选择过期的 Microsoft 365 组。 可以为以下内容设置过期时间：
      - **全部** Microsoft 365 组
      - **所选** Microsoft 365 组的列表
      - **无** 以限制所有组的过期时间
    - 设置完成后，选择“保存”来保存设置。

> [!NOTE]
> - 首次设置过期时，超过过期时间间隔的任何组都将设置为35天直到过期，除非自动续订了组或所有者续订了组。
> - 删除并还原动态组时，会将其视为新组，并根据规则重新填充。 此过程最多可能需要 24 小时。
> - 团队中使用的组的过期通知出现在 "团队所有者" 源中。

## <a name="email-notifications"></a>电子邮件通知

如果未自动续订组，则会将此类电子邮件通知发送到 Microsoft 365 组所有者30天、15天，以及在该组过期之前的1天。 电子邮件的语言由组所有者首选语言或 Azure AD 语言设置确定。 如果组所有者定义了首选语言，或多个所有者具有相同首选语言，则使用该语言。 对于所有其他情况，将使用 Azure AD 语言设置。

![过期电子邮件通知](./media/groups-lifecycle/expiration-notification.png)

组所有者可以从 " **续订组** " 通知电子邮件直接访问 " [访问面板](https://account.activedirectory.windowsazure.com/r#/applications)" 中的 "组详细信息" 页。 在此处，用户可以获取更多关于组的信息，例如该组的描述、上次续订时间、到期时间，并且还能续订组。 "组详细信息" 页现在还包括指向 Microsoft 365 组资源的链接，以便组所有者可以方便地查看其组中的内容和活动。

当某个组过期时，它将在到期日期的后一天被删除。 此类电子邮件通知将发送到 Microsoft 365 组所有者，通知他们其 Microsoft 365 组的过期和后续删除。

![组删除电子邮件通知](./media/groups-lifecycle/deletion-notification.png)

通过选择 " **还原组** " 或使用 PowerShell cmdlet，可以在删除后的30天内还原该组，如 [Azure Active Directory 中的 "还原已删除的 Microsoft 365 组](groups-restore-deleted.md)" 中所述。 请注意，30 天的组还原期不可自定义。

如果所还原的组包含文档、SharePoint 站点或其他持久对象，可能需要最多 24 小时才能完全还原该组及其内容。

## <a name="how-to-retrieve-microsoft-365-group-expiration-date"></a>如何检索 Microsoft 365 组过期日期

除了访问面板，用户可以在其中查看组详细信息（包括到期日期和上次续订日期），可以从 Microsoft Graph REST API Beta 中检索 Microsoft 365 组的到期日期。 Microsoft Graph Beta 中已启用 expirationDateTime 作为组属性。 可以使用 GET 请求来检索它。 有关更多详细信息，请参阅 [此示例](/graph/api/group-get?view=graph-rest-beta#example)。

> [!NOTE]
> 若要在访问面板上管理组成员身份，需要在 Azure Active Directory 组 "常规" 设置中将 "限制访问访问面板中的组" 设置为 "否"。

## <a name="how-microsoft-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Microsoft 365 组过期情况如何与法定持有邮箱一起使用

组到期并删除后，应用（例如 Planner、站点或 Teams）中删除的组数据将会在 30 天后永久删除，但合法保留的组邮箱将会保留下来并且不会永久删除。 管理员可以使用 Exchange cmdlets 来恢复邮箱以提取数据。

## <a name="how-microsoft-365-group-expiration-works-with-retention-policy"></a>Microsoft 365 组过期如何与保留策略一起使用

保留策略是通过安全和合规中心配置的。 如果你已为 Microsoft 365 组设置了保留策略，则当某个组到期并被删除时，组中 "邮箱" 和 "组中的文件" 组中的组对话会在保留期内保留，以确定保留策略中指定的天数。 到期后，用户无法查看组或其中的内容，但可以通过电子发现恢复站点和邮箱数据。

## <a name="powershell-examples"></a>PowerShell 示例

以下示例演示了如何使用 PowerShell cmdlet 来配置 Azure AD 组织中 Microsoft 365 组的过期设置：

1. 安装 PowerShell v2.0 模块并在 PowerShell 提示符下登录：

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. 配置过期设置使用 New-AzureADMSGroupLifecyclePolicy cmdlet 将 Azure AD 组织中所有 Microsoft 365 组的生存期设置为365天。 没有所有者的 Microsoft 365 组的续订通知将发送到 " emailaddress@contoso.com "
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. 检索现有策略 Remove-azureadmsgrouplifecyclepolicy：此 cmdlet 检索已配置的当前 Microsoft 365 组过期设置。 在此示例中，可以看到：

   - 策略 ID
   - Azure AD 组织中所有 Microsoft 365 组的生存期设置为365天
   - 没有所有者的 Microsoft 365 组的续订通知将发送到 "" emailaddress@contoso.com 。
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. 更新现有策略 Set-AzureADMSGroupLifecyclePolicy：此 cmdlet 用于更新现有策略。 在以下示例中，现有策略中的组生存期从 365 天更改为 180 天。
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. 将特定组添加到策略 Add-AzureADMSLifecyclePolicyGroup：此 cmdlet 将组添加到生命周期策略。 示例：
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. 删除现有策略 Remove-azureadmsgrouplifecyclepolicy：此 cmdlet 删除 Microsoft 365 组过期设置，但需要策略 ID。 此 cmdlet 将禁用 Microsoft 365 组的过期时间。
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
以下 cmdlet 可用于更详细地配置策略。 有关详细信息，请参阅 [PowerShell 文档](/powershell/module/azuread/?branch=master&view=azureadps-2.0-preview#groups)。

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>后续步骤

以下文章提供有关 Azure AD 组的更多信息。

- [查看现有组](../fundamentals/active-directory-groups-view-azure-portal.md)
- [管理组的设置](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [管理组的成员](../fundamentals/active-directory-groups-members-azure-portal.md)
- [管理组的成员身份](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [管理组中用户的动态规则](groups-dynamic-membership.md)