---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 84fa97ec964d490eb9571c7e030704562a4a81d0
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95564097"
---
[Azure 文件](../articles/storage/files/storage-files-introduction.md) 支持通过服务器消息块进行基于身份的身份验证 (SMB) 通过 [本地 Active Directory 域服务 (AD DS ](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)) Azure Active Directory (Azure AD) 和 [域服务 ](../articles/active-directory-domain-services/overview.md)。 本文重点介绍 Azure 文件共享如何在本地或 Azure 中使用域服务，以支持基于 SMB 的 Azure 文件共享进行基于标识的访问。 为 Azure 文件共享启用基于标识的访问，可将现有文件服务器替换为 Azure 文件共享，而无需替换现有的目录服务，从而无缝用户访问共享。 

Azure 文件强制对共享和目录/文件级别的用户访问权限。 共享级权限分配可以在 Azure Active Directory (Azure AD) 通过 [AZURE RBAC (模型) azure 基于角色的访问控制 ](../articles/role-based-access-control/overview.md) 来管理的用户或组。 使用 RBAC 时，用于文件访问的凭据应该可用或同步到 Azure AD。 可以将存储文件数据 SMB 共享读取器等 Azure 内置角色分配到 Azure AD 中的用户或组，以授予对 Azure 文件共享的读取权限。

在目录/文件级别，Azure 文件支持保留、继承和强制实施 [Windows dacl](/windows/win32/secauthz/access-control-lists) ，就像任何 windows 文件服务器一样。 在现有文件共享和 Azure 文件共享之间通过 SMB 复制数据时，可以选择保留 Windows Dacl。 无论你是否打算强制实施授权，都可以使用 Azure 文件共享来备份 Acl 和数据。