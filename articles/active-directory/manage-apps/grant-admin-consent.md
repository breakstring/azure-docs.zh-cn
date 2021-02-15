---
title: 向应用程序授予租户范围的管理员许可 - Azure AD
description: 了解如何向应用程序授予租户范围的许可，以便在最终用户登录到该应用程序时不提示他们授予许可。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/04/2019
ms.author: kenwith
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66a6317b0cb59d656cdb2e402c5ade1b78ed60aa
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258314"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>向应用程序授予租户范围的管理员许可

了解如何通过向应用程序授予租户范围的管理员许可来简化用户体验。 本文提供实现此目的的不同方式。 这些方法适用于 Azure Active Directory (Azure AD) 租户中的所有最终用户。

有关许可应用程序的详细信息，请参阅 [Azure Active Directory 许可框架](../develop/consent-framework.md)。

## <a name="prerequisites"></a>必备条件

授予租户范围的管理员许可需要以[全局管理员](../roles/permissions-reference.md#global-administrator)、[应用程序管理员](../roles/permissions-reference.md#application-administrator)或[云应用程序管理员](../roles/permissions-reference.md#cloud-application-administrator)的身份登录。

> [!IMPORTANT]
> 为应用程序授予租户范围的管理员许可后，除非已配置为需要用户分配，否则所有用户都可以登录到该应用。 若要限制哪些用户可登录到某个应用程序，需要提供用户分配，然后将用户或组分配到该应用程序。 有关详细信息，请参阅[分配用户和组的方法](./assign-user-or-group-access-portal.md)。
>
> 若要为 Microsoft Graph API 应用程序权限提供管理员许可，需要“全局管理员”角色。

> [!WARNING]
> 向某个应用程序授予租户范围的管理员许可将为该应用及其发布者授予对组织数据的访问权限。 在授予许可之前，请仔细查看应用程序请求的权限。
>
> 若要为 Microsoft Graph API 应用程序权限提供管理员许可，需要“全局管理员”角色。

## <a name="grant-admin-consent-from-the-azure-portal"></a>从 Azure 门户授予管理员许可

### <a name="grant-admin-consent-in-enterprise-apps"></a>在企业应用中授予管理员许可

如果应用程序已在租户中预配，则你可以通过“企业应用程序”授予租户范围的管理员许可。 例如，如果已至少许可一个用户登录到某个应用程序，则可以在租户中预配该应用。 有关详细信息，请参阅[如何以及为何将应用程序添加到 Azure Active Directory](../develop/active-directory-how-applications-are-added.md)。

若要向“企业应用程序”中列出的应用授予租户范围的管理员许可：

1. 以[全局管理员](../roles/permissions-reference.md#global-administrator)、[应用程序管理员](../roles/permissions-reference.md#application-administrator)或[云应用程序管理员](../roles/permissions-reference.md#cloud-application-administrator)的身份登录到 [Azure 门户](https://portal.azure.com)。
2. 依次选择“Azure Active Directory”、“企业应用程序”。
3. 选择要向其授予租户范围的管理员许可的应用程序。
4. 选择“权限”，然后单击“授予管理员许可”。
5. 仔细查看应用程序所需的权限。
6. 如果你同意应用程序所需的权限，请授予许可。 否则，请单击“取消”或关闭窗口。

> [!WARNING]
> 通过“企业应用”授予租户范围内的管理员同意将撤消先前已授予的租户范围内的所有权限。 之前用户自己已授予的权限将不受影响。 

### <a name="grant-admin-consent-in-app-registrations"></a>在应用注册中授予管理员许可

对于组织开发的应用程序，或已直接在 Azure AD 租户中注册的应用程序，还可以通过 Azure 门户中的“应用注册”授予租户范围的管理员许可。

若要通过“应用注册”授予租户范围的管理员许可：

1. 以[全局管理员](../roles/permissions-reference.md#global-administrator)、[应用程序管理员](../roles/permissions-reference.md#application-administrator)或[云应用程序管理员](../roles/permissions-reference.md#cloud-application-administrator)的身份登录到 [Azure 门户](https://portal.azure.com)。
2. 依次选择“Azure Active Directory”、“应用注册”。
3. 选择要向其授予租户范围的管理员许可的应用程序。
4. 选择“API 权限”，然后单击“授予管理员许可”。
5. 仔细查看应用程序所需的权限。
6. 如果你同意应用程序所需的权限，请授予许可。 否则，请单击“取消”或关闭窗口。

> [!WARNING]
> 通过“应用注册”授予租户范围内的管理员同意将撤销先前已授予的租户范围内的所有权限。 之前用户自己已授予的权限将不受影响。 

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>构建用于授予租户范围的管理员许可的 URL

使用上述任一方法授予租户范围的管理员许可时，Azure 门户中会打开一个窗口，提示授予租户范围的管理员许可。 如果你知道应用程序的客户端 ID（也称为应用程序 ID），则可以生成相同的 URL 来授予租户范围的管理员许可。

租户范围的管理员许可 URL 采用以下格式：

```http
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

其中：

* `{client-id}` 是应用程序的客户端 ID（也称为应用 ID）。
* `{tenant-id}` 是组织的租户 ID 或任何已验证的域名。

在授予许可之前，始终请仔细查看应用程序请求的权限。

> [!WARNING]
> 通过该 URL 授予租户范围内的管理员同意将撤消先前已授予的租户范围内的所有权限。 之前用户自己已授予的权限将不受影响。 

## <a name="next-steps"></a>后续步骤

[配置最终用户如何对应用程序表示同意](configure-user-consent.md)

[配置管理员同意工作流](configure-admin-consent-workflow.md)

[Microsoft 标识平台中的权限和许可](../develop/v2-permissions-and-consent.md)

[Microsoft Azure AD 上的&](https://docs.microsoft.com/answers/topics/azure-active-directory.html)