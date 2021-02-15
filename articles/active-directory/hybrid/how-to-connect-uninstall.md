---
title: 卸载 Azure AD Connect
description: 本文档介绍如何卸载 Azure AD Connect。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5eb537a70c69745c8067ffb71cfb895a0875945
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96934160"
---
# <a name="uninstall-azure-ad-connect"></a>卸载 Azure AD Connect

本文档介绍如何正确卸载 Azure AD Connect。

## <a name="uninstall-azure-ad-connect-from-the-server"></a>从服务器卸载 Azure AD Connect
你需要做的第一件事是从运行它的服务器删除 Azure AD Connect。  使用以下步骤：

 1. 在运行 Azure AD Connect 的服务器上，导航到 **"控制面板"**。
 2. 单击 "**卸载程序**" " 
  ![ 卸载程序"](media/how-to-connect-uninstall/uninstall-1.png)</br>
 
 3. 选择“Azure AD Connect”。
 ![选择 Azure AD Connect](media/how-to-connect-uninstall/uninstall-2.png)</br>
 
 4. 出现提示时，单击 **"是"** 以确认。
 5. 此确认将显示 Azure AD Connect 屏幕。  单击“删除”。
 ![删除](media/how-to-connect-uninstall/uninstall-3.png)</br>
 
 6. 完成此操作后，单击 " **退出**"。
 7. ![退出](media/how-to-connect-uninstall/uninstall-4.png)</br>
 
 8. 返回到 " **控制面板** "，单击 " **刷新** "，所有组件都应该已被删除。


## <a name="next-steps"></a>后续步骤

- 了解有关[将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。
- [使用现有 ADSync 数据库安装 Azure AD Connect](how-to-connect-install-existing-database.md)
- [使用 SQL 委派的管理员权限安装 Azure AD Connect](how-to-connect-install-sql-delegation.md)

