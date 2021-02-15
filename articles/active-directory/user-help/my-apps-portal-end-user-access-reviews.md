---
title: 管理组织对应用和组的访问 - Azure AD
description: 了解如何在“我的应用”门户中执行访问评审，以管理组织的应用和组的安全访问。
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
ms.openlocfilehash: 34885e2a364778a2f81f4920aa26aa3bb5f40320
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2021
ms.locfileid: "100095012"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>在“我的应用”门户中执行访问评审

你可以将工作或学校帐户与基于 Web 的“我的应用”门户一起使用，来为应用和组执行访问评审。 访问评审可帮助你管理过时的访问权限或更改访问要求，确保它们经过评审和更新。

如果无法访问“我的应用”门户，请联系支持人员以获取相关权限。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>此内容适用于“我的应用”用户。 如果你是管理员，可以在[应用程序管理文档](../manage-apps/index.yml)中查找有关如何设置和管理基于云的应用的详细信息。
>
> 如果在使用个人 Microsoft 帐户登录时出现错误，你仍可以使用组织的域名 (例如，你可以通过以下 Url 之一中的管理员使用你的组织的域名例如 contoso.com) 或你的组织的 **租户 ID** ：
>
>   - https://myapplications.microsoft.com?tenantId=*your_domain_name*
>   - https://myapplications.microsoft.com?tenant=*your_tenant_ID*

## <a name="manage-access-reviews"></a>管理访问评审

如果管理员已授予你执行自己的访问评审的权限，则可以在“我的应用”门户页上的“访问评审”磁贴中管理对组或应用的访问 。

>[!Note]
>如果未显示“访问评审”磁贴，则表示你没有执行访问评审的权限，或者你没有等待批准的任何待定评审。 如果你认为自己应有权限访问该磁贴，请与支持人员联系以获得帮助。

## <a name="to-perform-your-access-reviews"></a>执行自己的访问评审

1. 登录到工作或学校帐户。

1. 打开 Web 浏览器并转到 https://myapps.microsoft.com ，或使用组织提供的链接。 例如，你可能会被定向到组织的自定义页面，如 https://myapps.microsoft.com/contoso.com 。

    此时将显示“应用”页面，其中显示组织拥有并可供你使用的所有基于云的应用。

    ![“我的应用”门户中的“应用”页](media/my-apps-portal/my-apps-home.png)

1. 选择“访问评审”磁贴，查看等待批准的访问评审列表。

    ![“访问评审”页，其中显示了组织的待定访问评审](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

1. 选择“开始评审”以开始访问评审。

5. 查看访问权限并确定该权限是否仍是必需的。

    ![“访问评审”页，其中显示了评审详细信息](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >如果你是管理员，并且可以评审组织对组和应用的访问权限，则会显示不同的页面。 有关评审组织对组或应用的访问权限的详细信息，请参阅[在 Azure AD 访问评审中评审对组或应用程序的访问权限](../governance/perform-access-review.md)。

6. 选择“是”将保留该访问权限，选择“否”将删除该访问权限。

    如果选择“是”，则可能需要在“原因”框中说明理由。

    ![“访问评审”页，其中显示了“原因”框和框中的示例文本](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. 选择“提交”。

    访问评审已完成，你将返回到“我的应用”门户。

    >[!Note]
    >在访问评审期结束之前，你可以随时更改访问权限。 如果删除对应用或组的访问权限，访问权限不会立即被删除。 当访问评审期结束或管理员关闭评审时，才会删除相应的访问权限。

## <a name="next-steps"></a>后续步骤

- [访问并使用“我的应用”门户上的应用](my-apps-portal-end-user-access.md)
- [更改个人资料信息](./my-account-portal-settings.md)
- [查看和更新与组相关的信息](my-apps-portal-end-user-groups.md)