---
title: 适用于 PIM 的 Microsoft Graph API（预览版）- Azure AD | Microsoft Docs
description: 介绍如何使用适用于 Azure AD Privileged Identity Management (PIM) 的 Microsoft Graph API（预览版）。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: how-to
ms.date: 01/02/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb102c23d80095b8eb79fc25d1c1fd9d7f374fce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91529688"
---
# <a name="microsoft-graph-apis-for-privileged-identity-management-preview"></a>适用于 Privileged Identity Management 的 Microsoft Graph API（预览版）

可以使用 Azure Active Directory 的 [Microsoft Graph API](/graph/overview) 执行 Privileged Identity Management 任务。 本文介绍使用适用于 Privileged Identity Management 的 Microsoft Graph API 的重要概念。

有关 Microsoft Graph API 的详细信息，请查看 [Azure AD Privileged Identity Management API 参考](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta)。

> [!IMPORTANT]
> Microsoft Graph 中 /beta 版本下的 API 为预览版，可能会进行更改。 不支持在生产应用程序中使用这些 API。

## <a name="required-permissions"></a>所需的权限

若要调用适用于 Privileged Identity Management 的 Microsoft Graph API，必须拥有下述权限中的**一项或多项**：

- `Directory.AccessAsUser.All`
- `Directory.Read.All`
- `Directory.ReadWrite.All`
- `PrivilegedAccess.ReadWrite.AzureAD`

### <a name="set-permissions"></a>设置权限

应用程序必须具有必要的权限才能调用适用于 Privileged Identity Management 的 Microsoft Graph API。 若要指定所需的权限，最容易的方式是使用 [Azure AD 许可框架](../develop/consent-framework.md)。

### <a name="set-permissions-in-graph-explorer"></a>在 Graph 浏览器中设置权限

如果使用 Graph 浏览器来测试调用，则可在工具中指定权限。

1. 以全局管理员身份登录到 [Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)。

1. 单击“修改权限”。

    ![显示 "图形资源管理器" 页并选中 "修改权限" 操作的屏幕截图。](./media/pim-apis/graph-explorer.png)

1. 选中要包括的权限旁边的复选框。 `PrivilegedAccess.ReadWrite.AzureAD` 在 Graph 浏览器中尚不可用。

    ![Graph 浏览器 - 修改权限](./media/pim-apis/graph-explorer-modify-permissions.png)

1. 单击“修改权限”以应用权限更改。

## <a name="next-steps"></a>后续步骤

- [Azure AD Privileged Identity Management API 参考](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta)