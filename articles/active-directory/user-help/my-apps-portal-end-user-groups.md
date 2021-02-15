---
title: 在“我的应用”门户上更新群组信息 - Azure AD
description: 了解如何查看和更新与组相关的信息，包括查看你拥有的组、创建新组、查看你已是其成员的组以及加入你尚未加入的任何组。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/19/2021
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: fc8d211160e757c78a8e2adafad703118628e6dc
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2021
ms.locfileid: "100095038"
---
# <a name="update-your-groups-info-on-the-my-apps-portal"></a>在“我的应用”门户上更新群组信息

你可以通过基于 web 的 **"我的应用** " 门户使用工作或学校帐户来查看和启动你的组织中的多个基于云的应用，更新你的某些配置文件和帐户信息，查看你的 **组** 信息，并为应用和组执行 **访问评审** 。 如果无法访问“我的应用”门户，则必须联系支持人员以获取相关权限。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>此内容适用于用户。 如果你是管理员，可以在[应用程序管理文档](../manage-apps/index.yml)中查找有关如何设置和管理基于云的应用的详细信息。
>
> 如果在使用个人 Microsoft 帐户登录时出现错误，你仍可以使用组织的域名 (例如，你可以通过以下 Url 之一中的管理员使用你的组织的域名例如 contoso.com) 或你的组织的 **租户 ID** ：
>
>   - https://myapplications.microsoft.com?tenantId=*your_domain_name*
>   - https://myapplications.microsoft.com?tenant=*your_tenant_ID*

## <a name="view-your-groups-information"></a>查看组信息

如果管理员已授予你查看“组”磁贴的权限，你可以：

- **作为组成员。** 查看详细信息或离开任何组。

- **作为组所有者。** 查看详细信息、创建新组、添加或删除成员或删除组。

### <a name="to-view-your-groups-information"></a>若要查看你的组信息

1. 登录到工作或学校帐户。

2. 打开 Web 浏览器并转到 https://myapps.microsoft.com ，或使用组织提供的链接。 例如，你可能会被定向到组织的自定义页面，如 https://myapps.microsoft.com/contoso.com 。 如果你使用个人 Microsoft 帐户登录，则仍可以使用组织的域名 (例如，你可以通过以下 Url 之一在管理员中使用组织的域名例如 contoso.com) 或你的组织的 **租户 ID** ：

   - https://myapplications.microsoft.com?tenantId=*your_domain_name*
   - https://myapplications.microsoft.com?tenant=*your_tenant_ID*


    此时将显示“应用”页面，其中显示组织拥有并可供你使用的所有基于云的应用。

    ![“我的应用”门户中的“应用”页](media/my-apps-portal-end-user-groups/my-apps-home-large.png)

3. 下拉 " **我的应用** " 菜单并选择 **"我的组** "，查看与组相关的信息。

4. 根据你的权限，你可以使用 " **我的组** " 页执行以下操作：

    - **查看你拥有的组。** 从“我拥有的组”区域查看有关你在组织内拥有的任何组的信息。 选择特定组名称可查看有关组的更多详细信息，包括组类型、成员数、加入策略和活动成员列表。

    - **创建一个新组。** 从“我拥有的组”区域创建一个以你为所有者的新组。 有关特定步骤，请参阅本文[创建一个新组](#create-a-new-group)部分。

    - **编辑现有组。** 编辑你拥有的任何组的详细信息。 有关特定步骤，请参阅本文的[编辑现有组](#edit-an-existing-group)部分。

    - **添加或删除成员。** 添加或删除你拥有的任何组的成员。 有关特定步骤，请参阅本文的[添加或删除成员](#add-or-remove-a-member)部分。

    - **续订 Office 365 组。** 如果你的组织允许，你可以续订 Office 365 组。 有关特定步骤，请参阅本文的[续订 Office 365 组](#renew-an-office-365-group)部分。 

    - **删除组。** 删除你拥有的所有组。 有关特定步骤，请参阅本文的[删除组](#delete-a-group)部分。

    - **查看你所属的组。** 在“我加入的组”区域中查看以你为成员的任何组的名称。 选择特定组名称可查看有关组的更多详细信息，包括组类型、成员数、加入策略和活动成员列表。

    - **加入组。** 在“我加入的组”区域中，加入你尚未成为其成员的某个现有组。 有关特定步骤，请参阅[加入现有组](#join-an-existing-group)。

## <a name="create-a-new-group"></a>创建一个新组

1. 在“组”页面上，从“我拥有的组”区域中选择“创建组”  。

    随即会显示“创建组”框。

    ![“创建组”框](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. 输入所需信息：

    - **组类型：**

        - **安全性。** 用于管理成员和一组用户对共享资源的计算机访问权限。 例如，你可以创建一个安全组来实施特定的安全策略。 以此方式可以一次性为所有成员赋予一组权限，而不必单独地向每个成员添加权限。

        - **Office 365。** 通过向成员赋予对共享邮箱、日历、文件、SharePoint 站点等的访问权限，提供协作机会。 你也可以通过此选项向组织外部的人员赋予对组的访问权限。

    - **组名。** 添加组名称，可以使用容易记住以及具有某种意义的名称作为组名称。

    - **组描述(可选)。** 向组添加说明（可选操作）。

    - **组策略。** 选择允许所有人加入组或仅允许组的所有者添加成员。

3. 选择“创建”。

    新组创建后以你为所有者，它将显示在“我拥有的组”列表中。 由于你是所有者，因此该组也会显示在“我加入的组”列表中。

## <a name="edit-an-existing-group"></a>编辑现有组

创建组后，可以编辑其详细信息，包括更新任何现有信息。

1. 从“组”页中选择要编辑的组，然后在“&lt;group_name&gt;”页上选择“编辑详细信息” 。

    此时将显示“编辑详细信息”框，你可以更新最初创建组时添加的信息。

2. 进行所需的全部更改，然后选择“更新”。

## <a name="add-or-remove-a-member"></a>添加或删除成员

可以添加或删除你拥有的任何组的成员。

1. 选择要向其中添加成员的组，然后选择“&lt;group_name&gt;”页上的“+”。

    ![使用突出显示的“+”符号添加组成员](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. 从“添加成员”框中搜索要添加的成员，然后选择“添加” 。

    ![“添加成员”框，显示了要添加的新成员](media/my-apps-portal/my-apps-portal-add-member-page.png)

    会向新成员发送邀请，以便使其能够开始访问组织的应用。

3. 如果错误地添加了成员，或者成员已离开你的组织，则可以通过选择“&lt;group_name&gt;”页上的成员名称旁边的“删除成员”来删除成员。

    ![“删除成员”，其中突出显示了删除链接](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="renew-an-office-365-group"></a>续订 Office 365 组

如果你的组织允许，你可以续订 Office 365 组，延长到期日期。

1. 选择要续订的 Office 365 组，然后选择“续订组”。

    ![续订 Office 365 组，延长到期日期](media/my-apps-portal/my-apps-portal-renew-group-link.png)

2. 单击“确认”以关闭确认消息。

    刷新页面后，你将看到已更新的“上次续订”和“组过期”日期 。

## <a name="delete-a-group"></a>删除组

可以随时删除自己的任何组。 但是，如果错误地删除了某个组，则需要创建该组并再次添加成员。

1. 选择要永久删除的组，然后在“&lt;group_name&gt;”页上选择“删除组”。

    ![突出显示了“删除组”链接的 <Group_name> 页面](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. 在确认消息中选择“是”。

    已永久删除该组。

## <a name="join-an-existing-group"></a>加入现有组

可以在“组”页面中加入或离开已存在的组。

1. 在“组”页面上，从“我加入的组”区域中选择“加入组”  。

    随即显示“加入组”页面。

    ![“加入组”页面，突出显示“加入组”按钮](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. 在“加入组”页上，选择要加入的组的名称，查看关联的组详细信息，然后，如果组可用，选择“加入组” 。

    如果组要求组所有者批准成员资格，系统将要求你输入业务理由，说明你需要加入组的原因，然后选择“请求”。 如果组不需要审批，你将立即被添加为成员，并且该组将显示在“我加入的组”列表中。

3. 如果你错误地加入了组，或者不再需要成为该组的成员，可以从“加入组”页面中选择组名称，然后选择“离开组” 。

    ![“加入组”页面，其中突出显示了“退出组”按钮](media/my-apps-portal/my-apps-portal-leave-group-link.png)

## <a name="next-steps"></a>后续步骤

- [访问并使用“我的应用”门户上的应用](my-apps-portal-end-user-access.md)。

- [更改个人资料信息](./my-account-portal-settings.md)。

- [执行自己的访问评审](my-apps-portal-end-user-access-reviews.md)。