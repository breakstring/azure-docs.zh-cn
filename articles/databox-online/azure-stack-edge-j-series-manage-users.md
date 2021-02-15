---
title: Azure Stack Edge Pro GPU 管理用户 |Microsoft Docs
description: 介绍如何使用 Azure 门户来管理 Azure Stack Edge Pro GPU 上的用户。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/05/2021
ms.author: alkohli
ms.openlocfilehash: 0bef344414a9ba27d5808fcd17ed664b7f51bddc
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97915932"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-stack-edge-pro"></a>使用 Azure 门户管理 Azure Stack Edge Pro 上的用户

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

本文介绍如何在 Azure Stack Edge Pro 上管理用户。 可以通过 Azure 门户或通过本地 web UI 管理 Azure Stack Edge Pro。 使用 Azure 门户来添加、修改或删除用户。

在本文中，学习如何：

> [!div class="checklist"]
> * 添加用户
> * 修改用户
> * 删除用户

## <a name="about-users"></a>关于用户

用户可以是只读的，也可以是全权的。 只读用户只能查看共享数据。 完全权限用户可以读取共享数据、写入这些共享以及修改或删除共享数据。

 - **全权用户** - 拥有完全访问权限的本地用户。
 - **只读用户** - 拥有只读访问权限的本地用户。 这些用户与允许只读操作的共享相关联。

在创建共享期间创建用户时，首先会定义用户权限。 可以使用文件资源管理器对它们进行修改。


## <a name="add-a-user"></a>添加用户

在 Azure 门户中执行以下步骤可以添加用户。

1. 在 Azure 门户中，请切换到 Azure Stack Edge 资源，然后前往 " **用户**"。 在命令栏上选择 " **+ 添加用户** "。

    ![选择“添加用户”](media/azure-stack-edge-j-series-manage-users/add-user-1.png)

2. 指定要添加的用户的用户名和密码。 确认密码，然后选择“添加”。

    ![指定用户名和密码](media/azure-stack-edge-j-series-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > 以下用户由系统保留，不应使用：Administrator、EdgeUser、EdgeSupport、HcsSetupUser、WDAGUtilityAccount、CLIUSR、DefaultAccount、Guest。  

3. 用户创建过程开始和完成后，会显示通知。 创建用户后，在命令栏中选择“刷新”可查看更新的用户列表。


## <a name="modify-user"></a>修改用户

创建用户后，可以更改与该用户关联的密码。 从用户列表中进行选择。 输入并确认新密码。 保存更改。

![修改用户](media/azure-stack-edge-j-series-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>删除用户

在 Azure 门户中执行以下步骤可以删除用户。


1. 在 Azure 门户中，请切换到 Azure Stack Edge 资源，然后前往 " **用户**"。

    ![选择要删除的用户](media/azure-stack-edge-j-series-manage-users/delete-user-1.png)

2. 从用户列表中选择一个用户，然后选择“删除”。 出现提示时，确认删除。

    ![选择要删除的用户2](media/azure-stack-edge-j-series-manage-users/delete-user-2.png)

用户列表将会更新，以反映该用户已删除。

![更新的用户列表](media/azure-stack-edge-j-series-manage-users/delete-user-4.png)

## <a name="next-steps"></a>后续步骤

- 了解如何[管理带宽](azure-stack-edge-j-series-manage-bandwidth-schedules.md)。
