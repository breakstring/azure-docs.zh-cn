---
title: 许可证自助密码重置 - Azure Active Directory
description: 了解各种 Azure Active Directory 自助式密码重置许可要求
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f8f270f740951b95b0d4c00cbcf9b08e20a8e0c
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743718"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Azure Active Directory 自助式密码重置的许可要求

为了减少用户无法登录到其设备或应用程序时的支持人员呼叫和生产力损失，可以为 Azure Active Directory (Azure AD) 中的用户帐户启用自助式密码重置 (SSPR)。 构成 SSPR 的功能包括密码更改、重置、解锁和写回到本地目录。 可在 Microsoft 365 商业标准版或更高版本以及所有 Azure AD Premium SKU 中免费使用基本 SSPR 功能。

本文详细介绍了可以许可和使用自助式密码重置的各种方法。 有关定价和计费的具体详细信息，请参阅 [Azure AD 定价页](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="compare-editions-and-features"></a>比较版本和功能

SSPR 按用户许可。 为了保持合规性，组织需要为其用户分配相应的许可证。

下表概述了需要进行密码更改、重置或本地写回的各种 SSPR 场景，以及哪些 SKU 提供此功能。

| 功能 | Azure AD Free | Microsoft 365 商业标准版 | Microsoft 365 商业高级版 | Azure AD Premium P1 或 P2 |
| --- |:---:|:---:|:---:|:---:|
| **仅限云的用户密码更改**<br />Azure AD 中的用户知道自己的密码，并希望将其更改为新密码。 | ● | ● | ● | ● |
| **仅限云的用户密码重置**<br />Azure AD 中的用户忘记了自己的密码，需要对其进行重置。 | | ● | ● | ● |
| **带本地写回功能的混合用户密码更改或重置**<br />使用 Azure AD Connect 从本地目录同步的 Azure AD 中的用户想要更改或重置其密码并将新密码写回到本地。 | | | ● | ● |

> [!WARNING]
> 独立的 Microsoft 365 基本和标准许可计划不支持具有本地写回功能的 SSPR。 本地写回功能需要 Azure AD Premium P1、Premium P2 或 Microsoft 365 商业高级版。

有关其他许可信息（包括成本），请参阅以下页面：

* [Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory 特性和功能](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [企业移动性 + 安全性](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 企业版](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 商业版](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>启用基于组或基于用户的许可

Azure AD 支持基于组的许可。 管理员可以将许可证批量分配给一组用户，而不是一次一个用户地分配。 有关详细信息，请参阅[分配、验证许可证和解决许可证问题](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses)。

某些 Microsoft 服务并非在所有位置都可以使用。 将许可证分配给用户之前，管理员必须为该用户指定“使用位置”属性。 可在 Azure 门户中的“用户” > “配置文件” > “设置”部分下完成分配许可证  。 *使用组许可证分配时，任何没有指定使用位置的用户将继承该目录的位置。*

## <a name="next-steps"></a>后续步骤

若要开始使用 SSPR，请完成以下教程：

> [!div class="nextstepaction"]
> [教程：启用自助式密码重置 (SSPR)](tutorial-enable-sspr.md)