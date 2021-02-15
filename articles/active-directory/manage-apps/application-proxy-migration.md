---
title: 升级到 Azure AD 应用程序代理 | Microsoft Docs
description: 如果要从 Microsoft Forefront 或 Unified Access Gateway 升级，请选择哪个代理解决方案最佳。
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7d089d8f57bf8ef1278d262129cc8ddfc81d581
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257737"
---
# <a name="compare-remote-access-solutions"></a>比较远程访问解决方案

Azure Active Directory 应用程序代理是 Microsoft 提供的两个远程访问解决方案之一。 另一个是 Web 应用程序代理（本地版本）。 这两个解决方案取代了 Microsoft 提供的早期产品： Microsoft Forefront 威胁管理网关 (TMG) 和统一访问网关 (UAG)。 通过本文了解如何对这四个解决方案进行比较。 对于仍在使用已弃用的 TMG 或 UAG 解决方案的用户，可借助本文规划迁移到其中一个应用程序代理。 


## <a name="feature-comparison"></a>功能比较

通过此表了解如何对威胁管理网关 (TMG)、统一访问网关 (UAG)、Web 应用程序代理 (WAP) 和 Azure AD 应用程序代理 (AP) 进行比较。

| 功能 | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| 证书身份验证 | 是 | 是 | - | - |
| 有选择地发布浏览器应用 | 是 | 是 | 是 | 是 |
| 预身份验证和单一登录 | 是 | 是 | 是 | 是 | 
| 第 2/3 层防火墙 | 是 | 是 | - | - |
| 正向代理功能 | 是 | - | - | - |
| VPN 功能 | 是 | 是 | - | - |
| 丰富的协议支持 | - | 是 | 是，如果通过 HTTP 运行 | 是，如果通过 HTTP 或远程桌面网关运行 |
| 用作 ADFS 代理服务器 | - | 是 | 是 | - |
| 一个用于访问应用程序的门户 | - | 是 | - | 是 |
| 响应正文链接转换 | 是 | 是 | - | 是 | 
| 使用标头进行身份验证 | - | 是 | - | 是，使用 PingAccess | 
| 云级安全性 | - | - | - | 是 | 
| 条件性访问 | - | 是 | - | 是 |
| 外围安全区域 (DMZ) 中无组件 | - | - | - | 是 |
| 无入站连接 | - | - | - | 是 |

在大多数情况下，我们建议 Azure AD 应用程序代理作为新式解决方案。 仅在需要为 AD FS 提供代理服务器以及无法使用 Azure Active Directory 中的自定义域时，才优先考虑 Web 应用程序代理。 

与同类产品相比，Azure AD 应用程序代理提供许多独一无二的优势，其中包括：

- 将 Azure AD 扩展到本地资源
   - 云级安全性和保护
   - 条件访问和多重身份验证等功能易于启用
- 外围安全区域中无组件
- 不需要任何入站连接
- 用户可以在 "我的应用" 页上找到其所有应用程序，包括 Microsoft 365、Azure AD 集成的 SaaS 应用和本地 web 应用。 


## <a name="next-steps"></a>后续步骤

- [使用 Azure AD 应用程序提供对本地应用程序的安全远程访问](application-proxy.md)
