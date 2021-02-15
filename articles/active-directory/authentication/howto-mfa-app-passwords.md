---
title: 为 Azure AD 多重身份验证配置应用密码-Azure Active Directory
description: 了解如何在 Azure AD 多重身份验证中配置和使用旧版应用程序的应用密码
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfb38f9fcdba6898b690d0af68b715fea07e80bb
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743099"
---
# <a name="enable-and-use-azure-ad-multi-factor-authentication-with-legacy-applications-using-app-passwords"></a>使用应用密码启用和使用旧版应用程序 Azure AD 多重身份验证

在 iOS 11 之前，某些旧的非浏览器应用（如 Office 2010 或更早版本）和 Apple Mail 不了解身份验证过程中的暂停或中断。 如果为用户启用了 Azure AD 多重身份验证，并尝试使用这些较旧的非浏览器应用之一，则无法成功进行身份验证。 若要使用为用户帐户启用 Azure AD 多重身份验证的安全方式来使用这些应用程序，可以使用应用密码。 这些应用密码替换了你的传统密码，以允许应用绕过多重身份验证并正常工作。

Microsoft Office 2013 客户端及更高版本支持现代身份验证。 Office 2013 客户端（包括 Outlook）支持新式身份验证协议，并且可以启用以进行双重验证。 启用客户端后，客户端即不要求使用应用密码。

本文介绍如何为不支持多重身份验证提示的旧版应用程序启用和使用应用密码。

>[!NOTE]
> 应用密码不适用于基于多重身份验证策略和新式身份验证的条件性访问。

## <a name="overview-and-considerations"></a>概述和注意事项

为用户帐户启用 Azure AD 多重身份验证时，请求进行其他验证将中断常规登录提示。 一些较旧的应用程序不会在登录过程中了解这一中断，因此身份验证会失败。 若要维护用户帐户安全性并使 Azure AD 启用多重身份验证，可以使用应用密码，而不是用户的常规用户名和密码。 如果在登录过程中使用了应用密码，则没有其他验证提示，因此身份验证成功。

应用密码是自动生成的，不由用户指定。 这种自动生成的密码会使攻击者更难猜到，因此更安全。 用户无需跟踪密码或每次都输入密码，因为每个应用程序只需输入一次应用密码。

使用应用密码时，请注意以下事项：

* 每个用户的应用密码限制为40。
* 缓存密码并在本地方案中使用密码的应用程序可能会失败，因为该应用程序密码不是工作或学校帐户之外的已知。 这种情况的一个例子是 Exchange 电子邮件位于本地而存档邮件位于云中。 在这种情况下，不能使用同一密码。
* 在用户帐户上启用 Azure AD 多重身份验证后，应用密码可用于 Outlook 和 Microsoft Skype for Business 等大多数非浏览器客户端。 但是，不能通过非浏览器应用程序（例如 Windows PowerShell）使用应用程序密码来执行管理操作。 即使用户具有管理帐户，也无法执行这些操作。
    * 若要运行 PowerShell 交吧，应创建具有强密码的服务帐户，并且不为该帐户启用双重验证。
* 如果怀疑某个用户帐户已泄露，并撤消/重置了帐户密码，则还应更新应用密码。 撤消/重置用户帐户密码时，不会自动吊销应用密码。 用户应删除现有的应用密码，并创建新密码。
   * 有关详细信息，请参阅 ["其他安全性验证" 页中的 "创建和删除应用密码"](../user-help/multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page)。

>[!WARNING]
> 在客户端同时与本地和云自动发现终结点通信的混合环境中，应用密码无效。 需要域密码才能进行本地身份验证。 需要应用密码才能进行云身份验证。

### <a name="app-password-names"></a>应用密码名称

应用密码名称应能反映使用它们的设备。 如果有安装了非浏览器应用（如 Outlook、Word 和 Excel）的笔记本电脑，可为这些应用创建名为 Laptop 的应用密码。 为在台式计算机上运行的相同应用程序创建名为 Desktop 的另一个应用密码。

建议为每个设备创建一个应用密码，而不是每个应用程序创建一个应用密码。

## <a name="federated-or-single-sign-on-app-passwords"></a>联合或单一登录应用密码

Azure AD 支持联合或单一登录 (SSO) ，其中本地 Active Directory 域服务 (AD DS) 。 如果你的组织与 Azure AD 联合并使用 Azure AD 多重身份验证，则以下应用密码注意事项适用：

>[!NOTE]
> 以下要点仅适用于联合 (SSO) 客户。

* 应用密码由 Azure AD 进行验证，从而绕过了联合。 仅在设置应用密码时，才会主动使用联合。
* 与被动流程不同，对于联合 (SSO) 用户，不会与标识提供者 (IdP) 联系。 应用密码存储在工作或学校帐户中。 如果用户离开公司，则该用户的信息通过 **DirSync** 实时流向工作或学校帐户。 帐户的禁用/删除可能需要长达三个小时才能同步，这会延迟 Azure AD 的应用密码的禁用/删除。
* 应用密码功能不遵循“本地客户端访问控制”设置。
* 应用密码功能不提供本地身份验证日志记录或审核功能。

某些高级体系结构需要结合使用多因素身份验证和客户端凭据。 凭据可以包括工作或学校帐户的用户名和密码以及应用密码。 要求取决于如何执行身份验证。 对于针对本地基础结构进行身份验证的客户端，需要使用工作或学校帐户用户名和密码。 对于针对 Azure AD 进行身份验证的客户端，需要使用应用密码。

例如，假设有以下体系结构：

* Active Directory 的本地实例与 Azure AD 联合。
* 使用 Exchange online。
* 在本地使用 Skype for Business。
* 使用 Azure AD 多重身份验证。

在此方案中，使用以下凭据：

* 若要登录到 Skype for Business，请使用工作或学校帐户的用户名和密码。
* 若要从连接到 Exchange Online 的 Outlook 客户端访问通讯簿，请使用应用密码。

## <a name="allow-users-to-create-app-passwords"></a>允许用户创建应用密码

默认情况下，用户无法创建应用密码。 必须先启用应用密码功能，然后用户才能使用它们。 若要使用户能够创建应用密码，请完成以下步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 搜索并选择 " **Azure Active Directory**"，然后选择 " **用户**"。
3. 在 "*用户*" 窗口顶部的导航栏中选择 "**多重身份验证**"。
4. 在“多重身份验证”下，选择“服务设置”。
5. 在“服务设置”页上，选择“允许用户创建应用密码，以登录非浏览器应用”选项。 

    ![Azure 门户的屏幕截图，其中显示了允许用户使用应用密码进行多重身份验证的服务设置](media/concept-authentication-methods/app-password-authentication-method.png)
    
> [!NOTE]
>
> 禁用用户创建应用密码功能后，现有的应用密码将继续工作。 但是，在禁用此功能后，用户将无法管理或删除这些现有的应用密码。
>
> 禁用创建应用密码功能的功能时，还建议 [创建条件性访问策略，禁止使用旧身份验证](../conditional-access/block-legacy-authentication.md)。 此方法可防止现有的应用密码正常工作，并强制使用新式身份验证方法。

## <a name="create-an-app-password"></a>创建应用密码

当用户完成对 Azure AD 多重身份验证的初始注册时，可以选择在注册过程结束时创建应用密码。

用户还可以在注册后创建应用密码。 有关用户的详细信息和详细步骤，请参阅 [Azure AD 多重身份验证中的应用密码是什么？](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="next-steps"></a>后续步骤

有关如何允许用户快速注册 Azure AD 多重身份验证的详细信息，请参阅 [组合式 security information registration 概述](concept-registration-mfa-sspr-combined.md)。
