---
title: 与他人协作 - LUIS
titleSuffix: Azure Cognitive Services
description: 应用所有者可以向创作资源添加参与者。 这些参与者可以修改模型，训练并发布应用。
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: ea95b2097c611635eb937498c6f1d87cb544bcd1
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98681088"
---
# <a name="add-contributors-to-your-app"></a>向应用添加参与者

应用所有者可以向应用添加参与者。 这些参与者可以修改模型，训练并发布应用。 [迁移](luis-migration-authoring.md)帐户以后，可以在 Azure 门户中通过“访问控制(IAM)”页管理该创作资源的参与者。 使用协作者的电子邮件地址和“参与者”角色添加用户。

## <a name="add-contributor-to-azure-authoring-resource"></a>向 Azure 创作资源添加参与者

如果你的 LUIS 创作体验已绑定到 LUIS 门户中“管理 -> Azure 资源”页上的某个创作资源，则表明你已完成迁移。

1. 在 Azure 门户中，找到语言理解 (LUIS) 创作资源。 它的类型为 `LUIS.Authoring`。
1. 在该资源的“访问控制(标识和访问管理)”页上选择“+ 添加”，然后选择“添加角色分配”。

    ![在 Azure 门户中，在创作资源上添加角色分配。](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. 在“添加角色分配”窗口中，选择“参与者”作为“角色”。 在“分配访问权限至”选项中，选择“Azure AD 用户、组或服务主体”。 在“选择”选项中，输入用户的电子邮件地址。 如果已知用户有同一个域的多个电子邮件地址，请确保输入主电子邮件帐户。

    ![将用户的电子邮件添加到 Azure AD 的参与者角色](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    找到用户的电子邮件以后，请选择该帐户，然后选择“保存”。

    如果无法进行此角色分配，请查看“[Azure 角色分配](../../role-based-access-control/role-assignments-portal.md)”和“[Azure 访问控制故障排除](../../role-based-access-control/troubleshooting.md#problems-with-azure-role-assignments)”。

## <a name="view-the-app-as-a-contributor"></a>以参与者身份查看应用

被添加为参与者后，请[登录到 LUIS 门户](sign-in-luis-portal.md)。

[!INCLUDE [switch azure directories](includes/switch-azure-directories.md)]

### <a name="users-with-multiple-emails"></a>有多个电子邮件的用户

如果将参与者添加到 LUIS 应用，则需指定确切的电子邮件地址。 虽然 Azure Active Directory (Azure AD) 允许单个用户交替使用多个电子邮件帐户，但 LUIS 要求用户使用在添加参与者时指定的电子邮件地址登录。

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Azure Active Directory 资源

如果你在组织中使用了 [Azure Active Directory](../../active-directory/index.yml) (Azure AD)，则在用户希望使用语言理解 (LUIS) 时，LUIS 需要有权访问用户的访问权限相关信息。 LUIS 需要的资源是最少的。

尝试使用已获得管理员同意或不需要管理员同意的帐户进行登录时，你将看到详细说明，例如管理员同意：

* 允许你使用组织帐户登录到应用并让应用读取你的配置文件。 它还允许应用读取基本的公司信息。 这将授权 LUIS 读取基本的配置文件数据，例如用户 ID、电子邮件、姓名
* 允许应用查看和更新你的数据，即使你当前未使用应用。 此权限是刷新用户的访问令牌时所必需的。


### <a name="azure-active-directory-tenant-user"></a>Azure Active Directory 租户用户

LUIS 使用标准的 Azure Active Directory (Azure AD) 许可流程。

租户管理员直接处理需要访问权限才能在 Azure AD 中使用 LUIS 的用户。

* 用户首先要登录 LUIS，此时看到需要管理员批准的弹出对话框。 继续操作之前，用户需联系租户管理员。
* 然后，租户管理员登录 LUIS，并看到一个“同意流”弹出对话框。 管理员需在此对话框中向用户授予权限。 管理员接受权限后，用户才能够继续使用 LUIS。 如果租户管理员不登录 LUIS，还可访问 LUIS 的[同意](https://account.activedirectory.windowsazure.com/r#/applications)部分。 在此页上，您可以将列表筛选为包含名称的项 `LUIS` 。

如果租户管理员只希望某些用户使用 LUIS，则有几种可能的解决方案：
* 给予“管理员同意”（同意 Azure AD 的所有用户），但随后在“企业应用程序属性”下将“需要进行用户分配”设置为“是”，最后仅将所需用户分配/添加到应用程序。 使用此方法，管理员仍然向应用提供“管理员同意”，但是，可以控制可以访问应用的用户。
* 另一种解决方法是使用 [Microsoft Graph 中的 Azure AD 标识和访问管理 API](/graph/azuread-identity-access-management-concept-overview) 向每个特定用户提供许可。

详细了解 Azure Active Directory 用户和同意：
* [将应用限制为](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md)供一组用户使用

## <a name="next-steps"></a>后续步骤

* 了解[如何使用版本](luis-how-to-manage-versions.md)来控制应用生命周期。
* 了解概念，包括[创作资源](luis-how-to-azure-subscription.md#authoring-key)和该资源上的[参与者](luis-how-to-azure-subscription.md#contributions-from-other-authors)。
* 了解[如何创建](luis-how-to-azure-subscription.md)创作和运行时资源
* 迁移到新的[创作资源](luis-migration-authoring.md)