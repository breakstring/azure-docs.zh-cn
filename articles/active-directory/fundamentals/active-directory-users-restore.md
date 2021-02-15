---
title: 还原或永久移除最近删除的用户 - Azure AD
description: 如何使用 Azure Active Directory 查看可还原的用户、还原已删除的用户或永久删除用户。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/23/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4d42e1cafa58aaf6a47188c9e86c24ac175047
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2020
ms.locfileid: "92514383"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>使用 Azure Active Directory 还原或删除最近删除的用户
删除用户后，帐户将保持挂起状态 30 天。 在此 30 天期间，可以还原用户帐户及其所有属性。 经过30天后，永久删除过程会自动启动。

可在 Azure 门户中使用 Azure Active Directory (Azure AD) 查看可还原的用户、还原已删除的用户或永久删除用户。

>[!Important]
>你本人或 Microsoft 客户支持人员均无法还原已永久删除的用户。

## <a name="required-permissions"></a>所需的权限
必须具有以下角色之一才能还原和永久删除用户。

- 全局管理员

- 合作伙伴一线支持人员

- 合作伙伴二线支持人员

- 用户管理员

## <a name="view-your-restorable-users"></a>查看可还原的用户
可以查看 30 天以内删除的所有用户。 这些用户可以还原。

### <a name="to-view-your-restorable-users"></a>查看可还原的用户
1. 使用组织的全局管理员帐户登录到 [Azure 门户](https://portal.azure.com/)。

2. 依次选择“Azure Active Directory”、“用户”、“已删除的用户”    。

    查看可还原的用户列表。

    ![“用户 - 已删除的用户”页，显示仍可还原的用户](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>还原最近删除的用户

从组织中删除某个用户帐户时，该帐户将处于挂起状态，所有相关的组织信息将会保留。 还原某个用户时，也会还原此组织信息。

> [!Note]
> 还原用户后，在删除时已分配到该用户的许可证也会还原，即使这些许可证没有可用的席位。 如果随后使用的许可证超过了购买的许可证数目，则你的组织可能暂时不符合许可证的用法规则。

### <a name="to-restore-a-user"></a>还原用户
1. 在“用户 - 已删除的用户”页上，搜索并选择一个可用用户  。 例如，Mary Parker  。

2. 选择“还原用户”  。

    ![“用户 - 已删除的用户”页，其中突出显示“还原用户”选项](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>永久删除用户
可从组织中永久性删除某个用户，而无需等待 30 天后自动删除。 你本人、其他管理员和 Microsoft 客户支持人员不能还原已永久删除的用户。

>[!Note]
>如果错误地永久删除了用户，必须创建新用户并手动输入以前的所有信息。 有关创建新用户的详细信息，请参阅[添加或删除用户](add-users-azure-active-directory.md)。

### <a name="to-permanently-delete-a-user"></a>永久删除用户

1. 在“用户 - 已删除的用户”页上，搜索并选择一个可用用户  。 例如，Rae Huff  。

2. 选择“永久删除”  。

    !["用户-删除用户" 页，突出显示 "删除用户" 选项](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>后续步骤
还原或删除用户后，可以：

- [添加或删除用户](add-users-azure-active-directory.md)

- [向用户分配角色](active-directory-users-assign-role-azure-portal.md)

- [添加或更改配置文件信息](active-directory-users-profile-azure-portal.md)

- [添加来自其他组织的来宾用户](../external-identities/what-is-b2b.md)

有关其他可用的用户管理任务的详细信息，请参阅 [Azure AD 用户管理文档](../enterprise-users/index.yml)。
