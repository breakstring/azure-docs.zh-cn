---
title: 使用 Azure 基于角色的访问控制来共享 Azure 门户仪表板
description: 本文介绍如何使用 Azure 基于角色的访问控制在 Azure 门户中共享仪表板。
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: de2efd75d07c3dc7df771aad1bd9c73453dad212
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745972"
---
# <a name="share-azure-dashboards-by-using-azure-role-based-access-control"></a>使用 Azure 基于角色的访问控制共享 Azure 仪表板

配置仪表板后，可将其发布，并与组织中的其他用户共享。 使用 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) 来允许其他人查看仪表板。 将某个用户或用户组分配到角色。 该角色定义这些用户是否可以查看或修改发布的仪表板。

所有已发布的仪表板作为 Azure 资源实现。 它们作为订阅中的可管理的项存在，并包含在资源组中。 从访问控制角度来看，仪表板与其他资源（例如虚拟机或存储帐户）没有什么不同。

> [!TIP]
> 仪表板中的各个磁贴会根据其显示的资源强制实施自身的访问控制要求。 可以广泛地共享仪表板，同时在单个磁贴中保护数据。
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>了解仪表板的访问控制

使用 Azure 基于角色的访问控制 (Azure RBAC)，可以将用户分配到处于三个不同范围级别的角色：

* 订阅
* 资源组
* resource

分配的权限从订阅向下继承到资源。 已发布的仪表板是一个资源。 可能已将用户分配到订阅的角色，这些角色适用于已发布的仪表板。

假设已有 Azure 订阅，并且团队中的各个成员都分配了订阅的 *所有者*、*参与者* 或 *读取者* 角色。 作为所有者或参与者的用户能够列出、查看、创建、修改或删除该订阅中的仪表板。 作为读取者的用户能够列出并查看仪表板，但不能修改或删除它们。 拥有读取者访问权限的用户能够对已发布的仪表板进行本地编辑（例如排查问题时），但不能将这些更改发布回到服务器。 他们可以自行创建仪表板的专用副本。

也可以将权限分配给包含若干个仪表板的资源组或单个仪表板。 例如，可以决定一组用户在整个订阅中应具有有限的权限，但对特定仪表板具有更高的访问权限。 将这些用户分配到该仪表板的角色。

## <a name="publish-dashboard"></a>发布仪表板

假设你将配置一个要与订阅中的一组用户共享的仪表板。 以下步骤说明如何与名为“存储管理者”的组共享某个仪表板。 可以使用任意名称为组命名。 有关详细信息，请参阅[在 Azure Active Directory 中管理组](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

在分配访问权限之前，必须先发布仪表板。

1. 在仪表板中，选择“共享”  。

    ![选择仪表板中的“共享”](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. 在“共享 + 访问控制”中选择“发布”。  

    ![发布仪表板](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     默认情况下，共享操作会将仪表板发布到名为 **dashboards** 的资源组。 若要选择不同的资源组，请清除该复选框。

仪表板现已发布。 如果从订阅继承的权限合适，则不需要执行更多的操作。 组织中的其他用户能够根据其订阅级别角色访问和修改仪表板。

## <a name="assign-access-to-a-dashboard"></a>向仪表板分配访问权限

可将一组用户分配到该仪表板的角色。

1. 发布仪表板后，选择“共享”  或“取消共享”  选项以访问“共享 + 访问控制”  。

1. 在“共享 + 访问控制”中选择“管理用户”。  

    ![管理仪表板的用户](./media/azure-portal-dashboard-share-access/manage-users-for-access-control.png)

1. 选择“角色分配”，查看已为其分配此仪表板角色的现有用户。 

1. 若要添加新用户或组，请选择“添加”  ，然后选择“添加角色分配”  。

    ![添加有权访问仪表板的用户](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. 选择表示要授予的权限的角色。 对于此示例，请选择“参与者”  。

1. 选择要分配到该角色的用户或组。 如果在列表中没有看到要查找的用户或组，请使用搜索框。 可用组列表取决于已在 Active Directory 中创建的组。

1. 完成添加用户或组后，请选择“保存”  。

## <a name="next-steps"></a>后续步骤

* 有关角色列表，请参阅 [Azure 内置角色](../role-based-access-control/built-in-roles.md)。
* 若要了解有关如何管理资源，请参阅[使用 Azure 门户管理 Azure 资源](../azure-resource-manager/management/manage-resources-portal.md)。