---
title: 选择如何在 Azure 门户中授予对队列数据的访问权限
titleSuffix: Azure Storage
description: 使用 Azure 门户访问队列数据时，门户会在后台对 Azure 存储发出请求。 可以使用 Azure AD 帐户或存储帐户访问密钥对这些 Azure 存储请求进行身份验证和授权。
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozguns
ms.date: 09/08/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: contperf-fy21q1
ms.openlocfilehash: 504d2eb939758e6045a2af095c66093c8754cb94
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590743"
---
# <a name="choose-how-to-authorize-access-to-queue-data-in-the-azure-portal"></a>选择如何在 Azure 门户中授予对队列数据的访问权限

使用 [Azure 门户](https://portal.azure.com)访问队列数据时，门户会在后台对 Azure 存储发出请求。 可以使用 Azure AD 帐户或存储帐户访问密钥对 Azure 存储请求进行授权。 门户会指示使用的是哪种方法，如果你有相应的权限，则门户还允许在这两种方法之间切换。

## <a name="permissions-needed-to-access-queue-data"></a>访问队列数据所需的权限

视你要如何在 Azure 门户中授予对队列数据的访问权限而定，你将需要特定权限。 在大多数情况下，这些权限是通过 Azure 基于角色的访问控制 (Azure RBAC) 提供的。 有关 Azure RBAC 的详细信息，请参阅[什么是 Azure 基于角色的访问控制 (Azure RBAC)？](../../role-based-access-control/overview.md)。

### <a name="use-the-account-access-key"></a>使用帐户访问密钥

若要使用帐户访问密钥访问队列数据，你必须将 Azure 角色分配给你，其中包含 Azure RBAC 操作 `Microsoft.Storage/storageAccounts/listkeys/action` 。 此 Azure 角色可以是内置角色，也可以是自定义角色。 支持的内置角色 `Microsoft.Storage/storageAccounts/listkeys/action` 包括：

- Azure 资源管理器 [所有者角色](../../role-based-access-control/built-in-roles.md#owner)
- Azure 资源管理器 [参与者角色](../../role-based-access-control/built-in-roles.md#contributor)
- [存储帐户参与者角色](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

尝试访问 Azure 门户中的队列数据时，门户首先会检查是否已为分配了角色 `Microsoft.Storage/storageAccounts/listkeys/action` 。 如果你被分配了包含此操作的角色，则门户将使用帐户密钥来访问队列数据。 如果你不拥有包含此操作的角色，则门户会尝试使用你的 Azure AD 帐户访问数据。

> [!NOTE]
> 经典订阅管理员角色 **服务管理员** 和 **共同管理员** 包括 Azure 资源管理器角色的等效项 [`Owner`](../../role-based-access-control/built-in-roles.md#owner) 。 **所有者** 角色包括所有操作，包括 `Microsoft.Storage/storageAccounts/listkeys/action` ，因此具有其中一个管理角色的用户也可以使用帐户密钥访问队列数据。 有关详细信息，请参阅[经典订阅管理员角色、Azure 角色和 Azure AD 管理员角色](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)。

### <a name="use-your-azure-ad-account"></a>使用 Azure AD 帐户

若要使用 Azure AD 帐户从 Azure 门户访问队列数据，必须符合以下条件：

- 已将 Azure 资源管理器 [`Reader`](../../role-based-access-control/built-in-roles.md#reader) 角色至少分配给存储帐户级别或更高级别。 “读取者”角色授予限制性最高的权限，但也接受可授予存储帐户管理资源访问权限的其他 Azure 资源管理器角色。
- 拥有一个可提供队列数据访问权限的内置角色或自定义角色。

必须提供“读取者”角色分配或其他 Azure 资源管理器角色分配，使用户能够在 Azure 门户中查看和导航存储帐户管理资源。 授予队列数据访问权限的 Azure 角色不会授予存储帐户管理资源访问权限。 若要在门户中访问队列数据，用户需要有权在存储帐户资源中导航。 有关此要求的详细信息，请参阅[分配“读取者”角色以访问门户](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access)。

支持访问队列数据的内置角色包括：

- [存储队列数据参与者](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor)：对队列的读取/写入/删除权限。
- [存储队列数据读取者](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader)：对队列的只读权限。

自定义角色能够支持内置角色所提供的相同权限的不同组合。 若要详细了解如何创建 Azure 自定义角色，请参阅 [Azure 自定义角色](../../role-based-access-control/custom-roles.md)和[了解 Azure 资源的角色定义](../../role-based-access-control/role-definitions.md)。

不支持使用经典订阅管理员角色列出队列。 若要列出队列，用户必须拥有 Azure 资源管理器“读取者”角色、“存储队列数据读取者”角色或“存储队列数据参与者”角色。  

> [!IMPORTANT]
> Azure 门户中存储资源管理器的预览版不支持使用 Azure AD 凭据来查看和修改队列数据。 Azure 门户中的存储资源管理器始终使用帐户密钥来访问数据。 若要在 Azure 门户中使用存储资源管理器，则必须分配包含的角色 `Microsoft.Storage/storageAccounts/listkeys/action` 。

## <a name="navigate-to-queues-in-the-azure-portal"></a>在 Azure 门户中导航到队列

若要在门户中查看队列数据，请导航到存储帐户的“概述”，然后单击“队列”对应的链接。  或者，您可以导航到菜单中的 **队列服务** 部分。

:::image type="content" source="media/authorize-data-operations-portal/queue-access-portal.png" alt-text="显示如何在 Azure 门户中导航到队列数据的屏幕截图":::

## <a name="determine-the-current-authentication-method"></a>确定当前的身份验证方法

导航到队列时，Azure 门户会指示当前是使用帐户访问密钥还是使用 Azure AD 帐户进行身份验证。

### <a name="authenticate-with-the-account-access-key"></a>使用帐户访问密钥进行身份验证

如果使用帐户访问密钥进行身份验证，则会在门户中看到“访问密钥”已指定为身份验证方法：

:::image type="content" source="media/authorize-data-operations-portal/auth-method-access-key.png" alt-text="显示用户当前正在使用帐户密钥访问队列的屏幕截图":::

若要改用 Azure AD 帐户，请单击图中突出显示的链接。 如果你通过分配给你的 Azure 角色获得了相应的权限，则可以继续访问。 但是，如果你缺少相应的权限，则会看到如下所示的错误消息：

:::image type="content" source="media/authorize-data-operations-portal/auth-error-azure-ad.png" alt-text="Azure AD 帐户不支持访问时显示的错误":::

请注意，如果你的 Azure AD 帐户缺少队列查看权限，则列表中不会显示任何队列。 单击“切换为访问密钥”链接，以再次使用访问密钥进行身份验证。

### <a name="authenticate-with-your-azure-ad-account"></a>使用 Azure AD 帐户进行身份验证

如果使用 Azure AD 帐户进行身份验证，则会在门户中看到“Azure AD 用户帐户”已指定为身份验证方法：

:::image type="content" source="media/authorize-data-operations-portal/auth-method-azure-ad.png" alt-text="显示用户当前正在使用 Azure AD 访问队列的屏幕截图":::

若要改用帐户访问密钥，请单击图中突出显示的链接。 如果你有权访问帐户密钥，则可以继续访问。 但是，如果你缺少帐户密钥的访问权限，则 Azure 门户会显示一条错误消息。

如果你无权访问帐户密钥，则门户中不会列出队列。 单击“切换为 Azure AD 用户帐户”链接，以再次使用 Azure AD 帐户进行身份验证。

## <a name="next-steps"></a>后续步骤

- [使用 Azure Active Directory 验证对 Azure Blob 和队列的访问权限](../common/storage-auth-aad.md)
- [使用 Azure 门户为 blob 和队列数据分配 Azure 角色](../common/storage-auth-aad-rbac-portal.md)
- [使用 Azure CLI 分配用于访问 blob 和队列数据的 Azure 角色](../common/storage-auth-aad-rbac-cli.md)
- [使用 Azure PowerShell 模块分配用于访问 blob 和队列数据的 Azure 角色](../common/storage-auth-aad-rbac-powershell.md)
