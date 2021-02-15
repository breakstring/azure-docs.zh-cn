---
author: baanders
description: Azure 数字孪生安装程序中的 "访问权限" 步骤的包含文件
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: cbcaf4b4ad1b6c00f8c452582b986b6ee3b2806e
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2020
ms.locfileid: "92478809"
---
对于基于角色的访问控制 (RBAC) ，Azure 数字孪生使用 [Azure Active Directory (Azure AD) ](../articles/active-directory/fundamentals/active-directory-whatis.md) 。 这意味着，在用户可以对 Azure 数字孪生实例进行数据平面调用之前，需要为该用户分配具有相应权限的角色。

对于 Azure 数字孪生，此角色是 _**Azure 数字孪生数据所有者**_。 有关角色和安全性的详细信息，请参阅 [*概念： Azure 数字孪生解决方案的安全性*](../articles/digital-twins/concepts-security.md)。

[!INCLUDE [digital-twins-role-rename-note.md](digital-twins-role-rename-note.md)]

本部分将介绍如何在 azure 数字孪生实例中为用户创建角色分配，并在 Azure 订阅中的 Azure AD 租户中使用该用户的电子邮件。 根据你在组织中的角色，你可以为自己设置此权限，或者代表将管理 Azure 数字孪生实例的其他人设置此权限。

### <a name="assign-the-role"></a>分配角色

若要授予用户管理 Azure 数字孪生实例的权限，必须在实例中为其分配 _**Azure 数字孪生数据所有者**_ 角色。

> [!NOTE]
> 请注意，此角色与 Azure AD *所有者* 角色不同，也可以在 Azure 数字孪生实例的作用域中分配。 它们是两个不同的管理角色，Azure AD *所有者* 不会授予对 *Azure 数字孪生数据所有者*授予的数据平面功能的访问权限。