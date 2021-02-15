---
title: 适用于 Azure AD 应用的高级 SAML 令牌证书签名选项
description: 了解如何使用 Azure Active Directory 中预先集成的应用的 SAML 令牌中的高级证书签名选项
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
ms.date: 03/25/2019
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9dd6a7a34ac231f8af9d08aab99e3d552f33ea1d
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259615"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Azure Active Directory 中库应用的 SAML 令牌中的高级证书签名选项

Azure Active Directory (Azure AD) 目前支持 Azure Active Directory 应用库中的数千个预先集成应用程序。 超过500的应用程序通过使用 [安全断言标记语言](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2.0 协议（如 [NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) 应用程序）支持单一登录。 当客户通过使用 SAML Azure AD 向应用程序进行身份验证时，Azure AD 通过 HTTP POST) 将令牌发送到应用程序 (。 然后，应用程序验证并使用该令牌来登录客户，而不是提示输入用户名和密码。 这些 SAML 令牌已使用 Azure AD 中生成的唯一证书根据特定的标准算法签名。

Azure AD 对库应用程序使用一些默认设置。 默认值是根据应用程序的要求设置的。

在 Azure AD 中，可以设置证书签名选项和证书签名算法。

## <a name="certificate-signing-options"></a>证书签名选项

Azure AD 支持三个证书签名选项：

* **为 SAML 断言签名**。 已针对大多数库应用程序设置此默认选项。 如果选择此选项，Azure AD 作为标识提供者 (IdP) 使用应用程序的 [x.509](https://wikipedia.org/wiki/X.509) 证书对 SAML 断言和证书进行签名。

* **为 SAML 响应签名**。 如果选择此选项，Azure AD 为 IdP 会使用应用程序的 x.509 证书为 SAML 响应签名。

* **为 SAML 响应和断言签名**。 如果选择此选项，Azure AD 为 IdP 将使用应用程序的 x.509 证书为整个 SAML 令牌签名。

## <a name="certificate-signing-algorithms"></a>证书签名算法

Azure AD 支持两种签名算法或安全哈希算法 (Sha) ，以便对 SAML 响应进行签名：

* **SHA-256**。 Azure AD 支持使用此默认算法来为 SAML 响应签名： 这是最新的算法，比 SHA-1 更安全。 大多数应用程序支持 SHA-256 算法。 如果应用程序仅支持将 SHA-1 用作签名算法，可以更改算法。 否则，我们建议使用 SHA-256 算法来为 SAML 响应签名。

* **Sha-1**。 此算法较旧，其安全性被视为低于 SHA-256。 如果应用程序仅支持此签名算法，可以在“签名算法”下拉列表选择此选项。 然后，Azure AD 会使用 SHA-1 算法为 SAML 响应签名。

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>更改证书签名选项和签名算法

若要更改应用程序的 SAML 证书签名选项和证书签名算法，请选择有问题的应用程序：

1. 在 [Azure Active Directory 门户](https://aad.portal.azure.com/)中，登录到你的帐户。 此时会显示“Azure Active Directory 管理中心”页。
1. 在左窗格中，选择“企业应用程序”。 此时将显示帐户中企业应用程序的列表。
1. 选择应用程序。 此时将显示应用程序的 "概述" 页。

   ![示例： "应用程序概述" 页](./media/certificate-signing-options/application-overview-page.png)

接下来，在该应用程序的 SAML 令牌中更改证书签名选项：

1. 在应用程序概述页的左窗格中，选择 " **单一登录**"。
1. 如果出现 " **设置单个 Sign-On，其中包含 SAML 预览** " 页，则请跳到步骤5。
1. 如果未出现 " **选择单一登录方法** " 页，请选择 " **更改单一登录模式** " 以显示该页。
1. 在 " **选择单一登录方法** " 页上，选择 " **SAML** （如果可用）"。  (如果 **SAML** 不可用，则该应用程序不支持 saml，你可以忽略此过程和文章的其余部分。 ) 
1. 在 " **使用 SAML 预览设置单个 Sign-On** " 页中，找到 " **saml 签名证书** " 标题，并选择 " **编辑** " 图标 (铅笔) "。 此时将显示 " **SAML 签名证书** " 页。

   ![示例： "SAML 签名证书" 页](./media/certificate-signing-options/saml-signing-page.png)

1. 在“签名选项”下拉列表中，选择“为 SAML 响应签名”、“为 SAML 断言签名”或“为 SAML 响应和断言签名”。    本文前面的 [证书签名选项](#certificate-signing-options)中显示了这些选项的说明。
1. 在“签名算法”下拉列表中，选择“SHA-1”或“SHA-256”。   本文前面的 " [证书签名算法](#certificate-signing-algorithms) " 部分中显示了这些选项的说明。
1. 如果你对选择满意，请选择 " **保存** " 以应用新的 SAML 签名证书设置。 否则，请选择 **X** 以放弃更改。

## <a name="next-steps"></a>后续步骤

* [针对不在 Azure Active Directory 应用库中的应用程序配置单一登录](./configure-saml-single-sign-on.md)
* [排查基于 SAML 的单一登录的问题](./debug-saml-sso-issues.md)