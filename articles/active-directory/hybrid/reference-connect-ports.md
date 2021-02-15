---
title: 混合标识所需的端口和协议 - Azure | Microsoft Docs
description: 此技术参考页面描述了需要为 Azure AD Connect 打开的端口
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da318840426d1c0b94eab06b89ff3152df9d26fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "80331091"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>混合标识所需的端口和协议
以下文档是用于实现混合标识解决方案所需的端口和协议的技术参考。 使用下图并参考相应的表格。

![什么是 Azure AD Connect](./media/reference-connect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>表 1 - Azure AD Connect 和本地 AD
此表描述了 Azure AD Connect 服务器与本地 AD 之间通信所需的端口和协议。

| 协议 | 端口 | 说明 |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |在目标林中进行 DNS 查找。 |
| Kerberos |88 (TCP/UDP) |对 AD 林进行 Kerberos 身份验证。 |
| MS-RPC |135 (TCP) |该端口绑定到 AD 林后，将在初始配置 Azure AD Connect 向导期间及密码同步期间使用。 |
| LDAP |389 (TCP/UDP) |用于从 AD 导入数据。 数据将使用 Kerberos 签名和签章加密。 |
| SMB | 445 (TCP) |由无缝 SSO 用于在 AD 林中创建计算机帐户。 |
| LDAP/SSL |636 (TCP/UDP) |用于从 AD 导入数据。 数据传输经过签名和加密。 仅在使用 TLS 时使用。 |
| RPC |49152-65535（随机高 RPC 端口）(TCP) |该端口绑定到 AD 林后，将在初始配置 Azure AD Connect 期间及密码同步期间使用。 有关详细信息，请参阅 [KB929851](https://support.microsoft.com/kb/929851)、[KB832017](https://support.microsoft.com/kb/832017) 和 [KB224196](https://support.microsoft.com/kb/224196)。 |
|WinRM  | 5985 (TCP) |仅在通过 Azure AD Connect 向导使用 gMSA 安装 AD FS 时使用|
|AD DS Web 服务 | 9389 (TCP) |仅在通过 Azure AD Connect 向导使用 gMSA 安装 AD FS 时使用 |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>表 2 - Azure AD Connect 和 Azure AD
此表描述了 Azure AD Connect 服务器与 Azure AD 之间通信所需的端口和协议。

| 协议 | 端口 | 说明 |
| --- | --- | --- |
| HTTP |80 (TCP) |用于下载 CRL（证书吊销列表）以验证 TLS/SSL 证书。 |
| HTTPS |443(TCP) |用来与 Azure AD 同步。 |

有关需要在防火墙中打开的 URL 和 IP 地址的列表，请参阅 [Office 365 URL 和 IP 地址范围](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)和 [Azure AD Connect 连接故障排除](tshoot-connect-connectivity.md#troubleshoot-connectivity-issues-in-the-installation-wizard)。

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>表 3 - Azure AD Connect 和 AD FS 联合身份验证服务器/WAP
此表描述了 Azure AD Connect 服务器与 AD FS 联合身份验证服务器/WAP 服务器之间通信所需的端口和协议。  

| 协议 | 端口 | 说明 |
| --- | --- | --- |
| HTTP |80 (TCP) |用于下载 CRL（证书吊销列表）以验证 TLS/SSL 证书。 |
| HTTPS |443(TCP) |用来与 Azure AD 同步。 |
| WinRM |5985 |WinRM 侦听器 |

## <a name="table-4---wap-and-federation-servers"></a>表 4 - WAP 和联合服务器
此表描述了联合服务器与 WAP 服务器之间通信所需的端口和协议。

| 协议 | 端口 | 说明 |
| --- | --- | --- |
| HTTPS |443(TCP) |用于身份验证。 |

## <a name="table-5---wap-and-users"></a>表 5 - WAP 和用户
此表描述了用户与 WAP 服务器之间通信所需的端口和协议。

| 协议 | 端口 | 说明 |
| --- | --- | --- |
| HTTPS |443(TCP) |用于设备身份验证。 |
| TCP |49443 (TCP) |用于证书身份验证。 |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>表 6a 和 6b - 通过单一登录 (SSO) 进行直通身份验证和通过单一登录 (SSO) 进行密码哈希同步
下表描述了 Azure AD Connect 与 Azure AD 之间通信所需的端口和协议。

### <a name="table-6a---pass-through-authentication-with-sso"></a>表 6a - 通过 SSO 进行直通身份验证
|协议|端口号|说明
| --- | --- | ---
|HTTP|80|启用出站 HTTP 流量以进行安全验证，例如 SSL。 连接器自动更新功能要正常工作也需要完成此操作。
|HTTPS|443| 为操作（例如启用和禁用功能、注册连接器、下载连接器更新和处理所有用户登录请求）启用出站 HTTPS 流量。

此外，Azure AD Connect 需要能够建立到 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)的直接 IP 连接。

### <a name="table-6b---password-hash-sync-with-sso"></a>表 6b - 通过 SSO 进行密码哈希同步

|协议|端口号|说明
| --- | --- | ---
|HTTPS|443| 启用 SSO 注册（只有 SSO 注册过程才需要）。

此外，Azure AD Connect 需要能够建立到 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)的直接 IP 连接。 同样，这只是 SSO 注册过程所需的。

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>表 7a 和 7b - 适用于 (AD FS/Sync) 和 Azure AD 的 Azure AD Connect Health 代理
下表描述了在 Azure AD Connect Health 代理与 Azure AD 之间通信所需的终结点、端口和协议

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>表 7a - 适用于 (AD FS/Sync) 和 Azure AD 的 Azure AD Connect Health 代理的端口和协议
此表描述了 Azure AD Connect Health 代理与 Azure AD 之间通信所需的以下出站端口和协议。  

| 协议 | 端口 | 说明 |
| --- | --- | --- |
| HTTPS |443(TCP) |出站 |
| Azure 服务总线 |5671 (TCP)  |出站 |

最新版本的代理不再需要 Azure 服务总线端口5671。 最新 Azure AD Connect Health 代理版本仅需端口443。

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b - 适用于 (AD FS/Sync) 和 Azure AD 的 Azure AD Connect Health 代理的终结点
有关终结点的列表，请参阅 [Azure AD Connect Health 代理的要求部分](how-to-connect-health-agent-install.md#requirements)。

