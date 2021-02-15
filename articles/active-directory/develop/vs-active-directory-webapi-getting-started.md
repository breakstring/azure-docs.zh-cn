---
title: 开始在 Visual Studio WebApi 项目中使用 Azure AD
description: 通过 Visual Studio 连接服务连接到或创建 Azure AD 之后，如何在 WebApi 项目中开始使用 Azure Active Directory
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.openlocfilehash: fb8c1f25c8a22c96679a0310353f474f6b9d5ba5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88165424"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Azure Active Directory 入门（WebApi 项目）

> [!div class="op_single_selector"]
> - [入门](vs-active-directory-webapi-getting-started.md)
> - [发生了什么情况](vs-active-directory-webapi-what-happened.md)

本文提供通过 Visual Studio 的“项目”>“连接的服务”  命令将 Active Directory 添加到 ASP.NET WebAPI 项目后的其他指南。 如果尚未将该服务添加到你的项目，可以随时执行该操作。

请参阅[我的 WebAPI 项目发生了什么情况？](vs-active-directory-webapi-what-happened.md)，了解添加连接服务时对项目所做的更改。

## <a name="requiring-authentication-to-access-controllers"></a>访问控制器需要身份验证

项目中的所有控制器均带有 `[Authorize]` 属性。 此属性要求用户先进行身份验证，才能访问由这些控制器定义的 API。 若要允许匿名访问控制器，请从控制器删除此属性。 如果想要更详细地设置这些权限，请将该属性应用到需要身份验证的每个方法，而不是将它应用到控制器类。

## <a name="next-steps"></a>后续步骤

- [Azure Active Directory 的身份验证方案](./authentication-vs-authorization.md)
- [向 ASP.NET Web 应用添加 Microsoft 登录功能](quickstart-v2-aspnet-webapp.md)