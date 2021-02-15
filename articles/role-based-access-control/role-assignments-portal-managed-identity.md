---
title: 为托管标识添加角色分配 (预览) -Azure RBAC
description: 了解如何添加角色分配，方法是从托管标识开始，然后使用 Azure 门户和 Azure 基于角色的访问控制 (Azure RBAC) 中选择作用域和角色。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: a01246c0cf35653f4d13262183cf9df28b056c69
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122020"
---
# <a name="add-a-role-assignment-for-a-managed-identity-preview"></a>为托管标识添加角色分配（预览版）

可以按照使用 [Azure 门户添加或删除 Azure 角色分配](role-assignments-portal.md)中所述，使用 "**访问控制 (IAM")** 页添加托管标识的角色分配。 在使用访问控制 (IAM) 页时，先从范围开始，然后选择托管标识和角色。 本文介绍了一种添加托管标识的角色分配的替代方法。 使用这些步骤时，先从托管标识开始，然后选择范围和角色。

> [!IMPORTANT]
> 使用这些替代步骤为托管标识添加角色分配的功能目前以预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="system-assigned-managed-identity"></a>系统分配的托管标识

按照以下步骤，从系统分配的托管标识开始，将角色分配到该托管标识。

1. 在 Azure 门户中，打开系统分配的托管标识。

1. 在左侧菜单中，单击“标识”。

    ![系统分配的托管标识](./media/shared/identity-system-assigned.png)

1. 在“权限”下，单击“Azure 角色分配” 。

    如果已将角色分配到所选的系统分配托管标识，则会看到角色分配的列表。 此列表包括你有权读取的所有角色分配。

    ![系统分配的托管标识的角色分配](./media/shared/role-assignments-system-assigned.png)

1. 若要更改订阅，请单击“订阅”列表。

1. 单击“添加角色分配(预览版)”。

1. 使用下拉列表来选择角色分配应用到的资源集，如订阅、资源组或资源 。

    如果你对所选范围没有角色分配写入权限，将会显示一条内联消息。 

1. 在“角色”下拉列表中选择一个角色，例如“虚拟机参与者”。

   ![系统分配的托管标识的“添加角色分配”窗格](./media/role-assignments-portal-managed-identity/add-role-assignment-with-scope.png)

1. 单击“保存”以分配该角色。

   片刻之后，就会在所选范围为托管标识分配角色。

## <a name="user-assigned-managed-identity"></a>用户分配的托管标识

按照以下步骤，从用户分配的托管标识开始，将角色分配到该托管标识。

1. 在 Azure 门户中，打开用户分配的托管标识。

1. 在左侧菜单中，单击“Azure 角色分配”。

    如果已将角色分配到所选的用户分配托管标识，则会看到角色分配的列表。 此列表包括你有权读取的所有角色分配。

    ![用户分配的托管标识的角色分配](./media/shared/role-assignments-user-assigned.png)

1. 若要更改订阅，请单击“订阅”列表。

1. 单击“添加角色分配(预览版)”。

1. 使用下拉列表来选择角色分配应用到的资源集，如订阅、资源组或资源 。

    如果你对所选范围没有角色分配写入权限，将会显示一条内联消息。 

1. 在“角色”下拉列表中选择一个角色，例如“虚拟机参与者”。

   ![用户分配的托管标识的“添加角色分配”窗格](./media/role-assignments-portal-managed-identity/add-role-assignment-with-scope.png)

1. 单击“保存”以分配该角色。

   片刻之后，就会在所选范围为托管标识分配角色。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure 资源的托管标识？](../active-directory/managed-identities-azure-resources/overview.md)
- [使用 Azure 门户添加或删除 Azure 角色分配](role-assignments-portal.md)
- [使用 Azure 门户列出 Azure 角色分配](role-assignments-list-portal.md)
