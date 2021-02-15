---
title: 使用 Microsoft Authenticator 应用登录 - Azure AD
description: 通过 Microsoft Authenticator 应用登录到工作或学校帐户或者个人的 Microsoft 和非 Microsoft 帐户，可以使用双重验证，也可以使用手机登录。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 06/03/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 3a9e4c8f3dd9e4cf291388bc102ae9a73d8de6dd
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131558"
---
# <a name="sign-in-to-your-accounts-using-the-microsoft-authenticator-app"></a>使用 Microsoft Authenticator 应用登录到帐户

Microsoft Authenticator 应用可帮助你在使用双重验证的情况下登录到帐户。 双重验证可帮助你更安全地访问帐户，尤其是在查看敏感信息时。 由于密码可能会遗忘、失窃或者泄露，双重验证作为一种附加的安全保障步骤，可帮助保护帐户的安全，因为其他人更难破解它。

可通过多种方式使用 Microsoft Authenticator 应用，包括：

- 使用用户名和密码登录后，为第二重验证方法提供提示。

- 使用用户名和移动设备并结合指纹、人脸或 PIN 来提供登录，而无需输入密码。

  >[!Important]
  >此手机登录方法仅适用于工作或学校以及个人 Microsoft 帐户。 非 Microsoft 帐户要求使用标准的双重验证过程。

## <a name="prerequisites"></a>先决条件

在使用 Microsoft Authenticator 应用之前，必须：

 1. 下载并安装 Microsoft Authenticator 应用。 如果尚未执行该操作，请参阅[下载并安装应用](user-help-auth-app-download-install.md)。

 2. 将工作/学校帐户、个人帐户以及第三方帐户添加到 Microsoft Authenticator 应用。 有关详细步骤，请参阅[添加工作或学校帐户](user-help-auth-app-add-work-school-account.md)、[添加个人帐户](user-help-auth-app-add-personal-ms-account.md)和[添加非 Microsoft 帐户](user-help-auth-app-add-non-ms-account.md)。

## <a name="turn-on-and-use-phone-sign-in-for-your-work-or-school-account"></a>为工作或学校帐户启用和使用手机登录

手机登录是一种双重验证。 你仍然必须提供所知道和拥有的事物来验证自己的身份，但使用手机登录时，不需输入帐户密码即可在移动设备上执行所有的身份验证。

在启用手机登录之前，必须启用双重验证。 若要详细了解如何为帐户启用双重验证，请参阅[添加工作或学校帐户](user-help-auth-app-add-work-school-account.md)和[添加个人帐户](user-help-auth-app-add-personal-ms-account.md)。

手机登录只能在 iOS 设备和运行 Android 6.0 或更高版本的 Android 设备上使用。

### <a name="turn-on-phone-sign-in"></a>启用手机登录

打开 Microsoft Authenticator 应用，转到工作或学校帐户，然后启用手机登录。

- **点击 "帐户" 磁贴时** ，会看到该帐户的全屏视图。 如果看到“已启用手机登录”，则表示已完全设置，无需密码即可登录。 如果看到“启用手机登录”，请点击它以启用手机登录。
- **如果你已经使用了应用进行双重验证** ，则可以点击 "帐户" 磁贴以查看帐户的全屏视图。 然后点击“启用手机登录”以启用手机登录。
- 如果在应用的“帐户”屏幕上找不到工作或学校帐户，则意味着尚未将其添加到应用。  按照 [添加工作或学校帐户帮助](user-help-auth-app-add-work-school-account.md)中的步骤操作，添加工作或学校帐户。

打开手机登录后，只能使用 Microsoft Authenticator 应用登录。 下面介绍如何操作：

1. 登录到工作或学校帐户。

    键入用户名后，会出现一个“批准登录”屏幕，其中显示一个两位数的数字，并要求你通过 Microsoft Authenticator 应用登录。 如果不想要使用此登录方法，可以选择“改用密码”，并使用密码登录。

    ![计算机中的“批准登录”框](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in.png)

2. 打开设备上的通知或 Microsoft Authenticator 应用，然后点击与电脑的“批准登录”屏幕上看到的数字匹配的数字。

    ![设备中的“批准登录”框](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in-numbers.png)

3. 如果你认可这次登录，请选择“批准”。 否则请选择“拒绝”。

4. 使用手机的 PIN 或生物识别密钥完成身份验证。

## <a name="turn-on-and-use-phone-sign-in-for-your-personal-microsoft-accounts"></a>针对 Microsoft 个人帐户启用并使用手机登录

可为个人 Microsoft 帐户（例如，用来登录 Outlook.com、Xbox、或 Skype 的帐户）启用手机登录。

>[!NOTE]
>Microsoft Authenticator 应用要求在设备上设置 PIN 或生物识别锁，这样有助于保护帐户。 如果将手机保持解锁状态，该应用会要求在启用手机登录之前设置安全锁。

### <a name="turn-on-phone-sign-in"></a>启用手机登录 

打开 Microsoft Authenticator 应用，转到工作或学校帐户，然后启用手机登录。

- **点击 "帐户" 磁贴时** ，会看到该帐户的全屏视图。 如果看到“已启用手机登录”，则表示已完全设置，无需密码即可登录。 如果看到“启用手机登录”，请点击它以启用手机登录。
- **如果已将应用用于双因素验证** ，则可以点击 "帐户" 磁贴以查看帐户的全屏视图。 然后点击“启用手机登录”以启用手机登录。
- 如果在应用的“帐户”屏幕上找不到帐户，则意味着尚未将其添加到应用。  请按照[添加 Microsoft 个人帐户](user-help-auth-app-add-personal-ms-account.md)一文中的步骤添加 Microsoft 个人帐户。

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>使用手机登录登录到帐户

1. 转到个人 Microsoft 帐户登录页，然后选择“改用 Microsoft Authenticator 应用”链接，而不要键入密码。

    Microsoft 向手机发送通知。

2. 批准通知。

## <a name="sign-in-using-two-factor-verification-for-your-account"></a>使用针对帐户的双重验证进行登录

标准双因素验证方法要求您在要登录到的设备中输入用户名和密码，然后选择 Microsoft Authenticator 应用程序是否收到通知，或者是否要从验证器应用程序复制验证代码。 在 Android 设备上，可以在 " **帐户** " 屏幕上找到验证代码。 在 iOS 设备上，可以 **在帐户的 "屏幕"** 或 "全屏" 视图中找到这些验证代码，具体取决于帐户类型。 在将帐户添加到 Microsoft Authenticator 应用时，为你的帐户启用双重验证。

>[!Note]
>如果在 Microsoft Authenticator 应用的“帐户”屏幕上看不到工作或学校帐户或个人帐户，则意味着尚未将帐户添加到 Microsoft Authenticator 应用。 若要添加帐户，请参阅[添加工作或学校帐户](user-help-auth-app-add-work-school-account.md)或[添加个人帐户](user-help-auth-app-add-personal-ms-account.md)。

若要了解通过哪些步骤才能使用各种双重验证方法登录到工作或学校帐户或个人帐户，请参阅[使用双重验证或安全信息登录](user-help-sign-in.md)。

## <a name="frequently-asked-questions"></a>常见问题

| 问题 | 解决方案 |
| -------- | -------- |
| 为什么使用手机登录比键入密码更安全？ | 当今，大多数人在登录网站或应用时，使用用户名和密码。 遗憾的是，密码可能会丢失、被盗或被黑客猜出。<br><br>安装 Microsoft Authenticator 应用后，该应用会在手机上创建一个密钥，用于解锁受手机 PIN 或生物识别锁保护的帐户。 登录时，可以使用此密钥来证明自己的身份。<br><br>**重要说明**<br>你的数据仅用于在本地保护你的密钥。 这些数据永远不会发送到或者存储在云中。 |
| 手机登录是否会替代双重验证？ 我是否应将其关闭？ | 手机登录是一种在移动设备上进行的双重验证。 应该让双重验证保持启用状态，以便为帐户提供额外的安全性。 |
| 如果为帐户保持启用双重验证，是否必须批准两个通知？ | 不是。 使用手机登录到 Microsoft 帐户也算作双重验证，因此不需要再次批准。 |
| 如果手机遗失或未随身携带， 如何访问我的帐户？ | 始终可以在登录页中选择“改用密码”链接，重新改用密码登录。 但是，如果使用双重验证，则仍需要使用第二种方法来验证身份。<br><br>**重要说明**<br>强烈建议确保有多个最新的验证方法与你的帐户相关联。<br><br>可以通过[安全设置](https://account.live.com/proofs/manage)页管理个人帐户的验证方法。 对于工作或学校帐户，如果管理员已启用安全信息，则你可以转到组织的[其他安全验证](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)页或“保护帐户安全”页。 有关安全信息的详细信息，请参阅[安全信息（预览版）概述](./security-info-setup-signin.md)。<br><br>如果无法管理验证方法，则必须与管理员联系。 |
| 如何停用此功能，并重新改用密码登录？ | 对于个人帐户，请在登录期间选择“改用密码”链接。 系统会记住你最近的选择，并在下次登录时默认提供此选项。 如果想要改用手机登录，请在登录期间选择“改用应用”链接。<br><br>对于工作或学校帐户，必须从 Microsoft Authenticator 应用的“设置”页中取消注册设备，或从配置文件的“设备和活动”区域中禁用设备。 有关从配置文件禁用设备的详细信息，请参阅[从“我的应用”门户更新配置文件和帐户信息](./my-account-portal-devices-page.md#disable-a-device)。 |
| 为何不能使用多个工作或学校帐户进行手机登录？ | 一部手机只能注册到单个工作或学校帐户。 如果想要为不同的工作或学校帐户启用手机登录，必须先通过“设置”页从此设备取消注册帐户。 |
| 是否可以使用手机登录到电脑？ | 我们建议使用 Windows 10 上的 Windows Hello 登录到电脑。 Windows Hello 允许使用人脸、指纹或 PIN 登录。 |

## <a name="next-steps"></a>后续步骤

- 如果在获取个人 Microsoft 帐户的验证码时遇到问题，请参阅 [Microsoft 帐户安全信息和验证码](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)一文中的“排查验证码问题”部分。

- 如果对该应用存在其他一般性的问题，请参阅 [Microsoft Authenticator 常见问题解答](user-help-auth-app-faq.md)

- 如需有关双重验证的详细信息，请参阅[为帐户设置双重验证](multi-factor-authentication-end-user-first-time.md)

- 如需有关安全信息的详细信息，请参阅[安全信息（预览版）概述](./security-info-setup-signin.md)