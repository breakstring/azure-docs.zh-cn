---
title: 通过 CloudSimple 访问 Azure VMware 解决方案-门户
description: 介绍如何在 Azure 门户中访问 VMware Solution by CloudSimple
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0aa7a9a1f19a9d4fb2c555b08753e0b57c657974
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895149"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>通过 CloudSimple 中的门户访问 VMware 解决方案 Azure 门户

支持单一登录以访问 CloudSimple 门户。 登录到 Azure 门户后，可以访问 CloudSimple 门户，而无需再次登录。 首次访问 CloudSimple 门户时，系统将提示你授权 [CloudSimple Service 授权](#consent-to-cloudsimple-service-authorization-application) 应用程序。  授权是一次性的操作。

## <a name="before-you-begin"></a>开始之前

具有内置 **所有者** 和 **参与者** 角色的用户可以访问 CloudSimple 门户。  必须在部署 CloudSimple 服务的资源组上配置角色。  还可以在 CloudSimple 服务对象上配置角色。  有关检查角色的详细信息，请参阅 [查看角色分配](../role-based-access-control/check-access.md) 一文。 只有具有内置 **所有者** 和 **参与者** 角色的用户才能访问 CloudSimple 门户。  必须在订阅上配置角色。  有关检查角色的详细信息，请参阅 [查看角色分配](../role-based-access-control/check-access.md) 一文。

如果你使用的是自定义角色，则角色应在下具有以下任何操作 ```Actions``` 。  有关自定义角色的详细信息，请参阅 [Azure 自定义角色](../role-based-access-control/custom-roles.md)。  如果任一操作是的一部分 ```NotActions``` ，则用户无法访问 CloudSimple 门户。

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="access-the-cloudsimple-portal"></a>访问 CloudSimple 门户

1. 选择“所有服务”  。

2. 搜索 " **CloudSimple Services**"。

3. 选择要在其上创建私有云的 CloudSimple 服务。

4. 在 " **概述** " 页上，单击 **"前往 CloudSimple 门户"**。  如果你是第一次从 Azure 门户访问 CloudSimple 门户，则系统会提示你授权 [CloudSimple Service 授权](#consent-to-cloudsimple-service-authorization-application) 应用程序。 

    ![启动 CloudSimple 门户](media/launch-cloudsimple-portal.png)

> [!NOTE]
> 如果选择私有云操作 (如直接从 Azure 门户创建或扩展私有云) ，CloudSimple 门户会打开到指定的页面。

在 CloudSimple 门户中，选择侧边菜单上的 " **主页** " 以显示有关私有云的摘要信息。 显示私有云的资源和容量以及需要注意的警报和任务。 对于常见任务，请单击页面顶部的命名图标。

![主页](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>同意 CloudSimple Service Authorization 应用程序

首次从 Azure 门户启动 CloudSimple 门户要求你同意 CloudSimple Service 授权应用程序。  选择 " **接受** " 以授予请求的权限并访问 CloudSimple 门户。

![同意 CloudSimple 服务授权-管理员](media/cloudsimple-azure-consent.png)

如果你具有全局管理员权限，则可以同意你的组织。  选择“代表组织授予同意”。

![同意 CloudSimple 服务授权-全局管理员](media/cloudsimple-azure-consent-global-admin.png)

如果你的权限不允许访问 CloudSimple 门户，请联系你的租户的全局管理员以授予所需的权限。  全局管理员可以代表您的组织同意。

![同意 CloudSimple 服务授权-需要管理员](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>后续步骤

* 了解如何 [创建私有云](./create-private-cloud.md)
* 了解如何 [配置私有云环境](quickstart-create-private-cloud.md)
