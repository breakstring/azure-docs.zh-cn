---
title: " (用于 FHIR 的 Azure API 的本地 RBAC) 配置本地基于角色的访问控制"
description: 本文介绍如何将用于 FHIR 的 Azure API 配置为使用外部 Azure AD 租户作为数据平面
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: matjazl
ms.openlocfilehash: 096e4e3ecbcedaec674e074a2baccbb336e03c94
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95524192"
---
# <a name="configure-local-rbac-for-fhir"></a>配置 FHIR 的本地 RBAC 

本文介绍如何将用于 FHIR 的 Azure API 配置为使用外部的辅助 Azure Active Directory 租户来管理数据平面访问。 仅在不可能使用与订阅关联的 Azure Active Directory 租户时才使用此模式。

> [!NOTE]
> 如果你的 FHIR 服务数据平面配置为使用与你的订阅关联的主要 Azure Active Directory 租户，请 [使用 AZURE RBAC 分配数据平面角色](configure-azure-rbac.md)。

## <a name="add-service-principal"></a>添加服务主体

本地 RBAC 允许将外部 Azure Active Directory 租户与 FHIR 服务器一起使用。 为了允许本地 RBAC 系统检查此租户中的组成员身份，Azure API for FHIR 必须在租户中有一个服务主体。 此服务主体将自动在与已部署 Azure API for FHIR 的订阅相关联的租户中自动创建，但如果你的租户没有关联的订阅，则租户管理员将需要使用以下命令之一创建此服务主体：

使用 `Az` PowerShell 模块：

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

或者，可以使用 `AzureAd` PowerShell 模块：

```azurepowershell-interactive
New-AzureADServicePrincipal -AppId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

或者，您可以使用 Azure CLI：

```azurecli-interactive
az ad sp create --id 3274406e-4e0a-4852-ba4f-d7226630abb7
```

## <a name="configure-local-rbac"></a>配置本地 RBAC

可以在 " **身份验证** " 边栏选项卡中将 Azure API for FHIR 配置为使用外部或辅助 Azure Active Directory 租户：

![本地 RBAC 分配](media/rbac/local-rbac-guids.png).

在 "权限" 框中，输入有效的 Azure Active Directory 租户。 验证租户后，应激活 " **允许的对象 id** " 框，然后可以输入标识对象 id 的列表。 这些 Id 可以是的标识对象 Id：

* Azure Active Directory 用户。
* 一个 Azure Active Directory 服务主体。
* Azure Active directory 安全组。

有关更多详细信息，请阅读有关如何 [查找标识对象 id](find-identity-object-ids.md) 的文章。

输入所需的对象 Id 后，请单击 " **保存** " 并等待要保存的更改，然后再尝试使用分配的用户、服务主体或组访问数据平面。

## <a name="caching-behavior"></a>缓存行为

适用于 FHIR 的 Azure API 会缓存最多5分钟的决策。 如果通过将 FHIR 服务器添加到允许的对象 Id 列表中来向用户授予对该服务器的访问权限，或者从列表中删除这些对象，则应预计该用户需要5分钟才能传播权限更改。

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用外部 (辅助) Azure Active Directory 租户来分配 FHIR 数据平面访问权限。 接下来了解适用于 FHIR 的 Azure API 的其他设置：
 
>[!div class="nextstepaction"]
>[其他设置 Azure API for FHIR](azure-api-for-fhir-additional-settings.md)
