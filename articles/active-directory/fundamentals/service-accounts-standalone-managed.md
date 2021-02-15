---
title: 保护独立托管服务帐户 |Azure Active Directory
description: 保护独立托管服务帐户的指南。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08a919338789a02d50cbb6976ee50b214cb0d612
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100416974"
---
# <a name="securing-standalone-managed-service-accounts"></a>保护独立托管服务帐户

 (sMSAs) 的独立托管服务帐户是用于保护服务器上运行的一个或多个服务的托管域帐户。 它们不能在多个服务器之间重复使用。 sMSAs 提供自动密码管理，简化的服务主体名称 (SPN) 管理和将管理委派给其他管理员的能力。 

在 Active Directory 中，sMSAs 与运行服务的特定服务器相关联。 你可以在 Microsoft 管理控制台的 "Active Directory 用户和计算机" 管理单元中找到这些帐户。

![显示托管服务帐户 OU Active Directory "用户和计算机" 管理单元的屏幕截图。](./media/securing-service-accounts/secure-standalone-msa-image-1.png)

托管服务帐户是随 Windows Server 2008R2 Active Directory 架构引入的，并要求最低操作系统级别为 Windows Server 2008R2。 

## <a name="benefits-of-using-smsas"></a>使用 sMSAs 的好处

与用作服务帐户的用户帐户相比，sMSAs 提供了更高的安全性，同时通过以下方式降低了管理开销：

* 设置强密码。 sMSAs 使用240字节随机生成的复杂密码。 SMSA 密码的复杂性和长度最大程度地降低了服务受到暴力破解或字典攻击的可能性。

* 定期重启密码。 Windows 每30天自动更改 sMSA 密码。 服务和域管理员无需计划密码更改或管理相关的停机时间。

* 简化 SPN 管理。 如果域功能级别 (DFL) 为 Windows Server 2008 R2，则服务主体名称会自动更新。 例如，在以下情况下，服务主体名称会自动更新：

   * 主机计算机帐户已重命名。 

   * 主计算机的 DNS 名称已更改。

   * 使用[PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-adserviceaccount?view=win10-ps)添加或删除其他 sam-accountname 或 dns 主机名参数时

## <a name="when-to-use-smsas"></a>何时使用 sMSAs

sMSAs 可简化管理和安全任务。 在将一个或多个服务部署到单台服务器，并且不能使用 gMSA 时，可使用 sMSAs。 

> [!NOTE] 
> 虽然可以将 sMSAs 用于多个服务，但建议每个服务都有自己的标识用于审核目的。 

如果软件的创建者无法告诉你它是否可以使用 MSA，则必须测试你的应用程序。 为此，请创建一个测试环境，并确保它可以访问所有所需的资源。 有关分步说明，请参阅 [创建和安装 sMSA](https://docs.microsoft.com/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) 。

### <a name="assess-security-posture-of-smsas"></a>评估 sMSAs 的安全状况

sMSAs 本质上比标准用户帐户更安全，需要进行密码管理。 但是，请务必将 sMSAs 的访问范围视为整体安全状况的一部分。

下表显示了如何减少 sMSAs 造成的潜在安全问题。

| 安全问题| 缓解措施 |
| - | - |
| sMSA 是特权组的成员|从提升权限的组中删除 sMSA，如域管理员)  (。 <br> 使用最少特权模型并仅向 sMSA 授予运行其服务 () 所需的权限。 <br> 如果你不确定所需的权限，请参阅服务创建者。 |
| sMSA 对敏感资源具有读/写访问权限。|审核对敏感资源的访问。 将审核日志存档到 SIEM (Azure Log Analytics 或 Azure Sentinel) 进行分析。 <br> 如果检测到不适当的访问级别，请修正资源权限。 |
| 默认情况下，sMSA 密码滚动更新频率为30天| 根据企业安全要求，可以使用组策略来优化持续时间。 <br> * 可以使用以下路径设置密码过期期限。 <br>计算机配置 \windows 设置 \ 安全设置 \ 安全 Options\Domain 成员：最大计算机帐户密码期限 |



### <a name="challenges-with-smsas"></a>SMSAs 的挑战

与 sMSAs 关联的挑战如下：

| 挑战| 缓解措施 |
| - | - |
| 它们可在单个服务器上使用。| 如果需要跨服务器使用帐户，请使用 Gmsa。 |
| 它们不能跨域使用。| 如果需要跨域使用此帐户，请使用 Gmsa。 |
| 并非所有应用程序都支持 sMSAs。| 如果可能，请使用 Gmsa。 如果不使用应用程序创建者建议的标准用户帐户或计算机帐户，则为。 |


## <a name="find-smsas"></a>查找 sMSAs

在任何域控制器上，运行 DSA.MSC 并展开 "托管服务帐户" 容器以查看所有 sMSAs。 

以下 PowerShell 命令将返回 Active Directory 域中的所有 sMSAs 和 Gmsa。 

`Get-ADServiceAccount -Filter *`

以下命令仅返回 Active Directory 域中的 sMSAs。

`Get-ADServiceAccount -Filter * | where { $_.objectClass -eq "msDS-ManagedServiceAccount" }`

## <a name="manage-smsas"></a>管理 sMSAs

你可以使用以下 Active Directory PowerShell cmdlet 来管理 sMSAs：

`Get-ADServiceAccount`

` Install-ADServiceAccount`

` New-ADServiceAccount`

` Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Ininstall-ADServiceAccount`

## <a name="move-to-smsas"></a>移到 sMSAs

如果应用程序服务支持 sMSA 而不是 Gmsa，并且当前正在使用用户帐户或计算机帐户进行安全上下文，请在服务器上 [创建并安装 sMSA](https://docs.microsoft.com/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) 。 

理想情况下，将资源移到 Azure，并使用 Azure 托管标识或服务主体。

 

## <a name="next-steps"></a>后续步骤
请参阅以下文章，了解如何保护服务帐户

* [本地服务帐户简介](service-accounts-on-premises.md)

* [安全组托管服务帐户](service-accounts-group-managed.md)

* [保护独立托管服务帐户](service-accounts-standalone-managed.md)

* [安全计算机帐户](service-accounts-computer.md)

* [保护用户帐户](service-accounts-user-on-premises.md)

* [管理本地服务帐户](service-accounts-govern-on-premises.md)

 
