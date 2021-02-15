---
title: 将非 Microsoft 帐户添加到 Microsoft Authenticator 应用 - Azure AD
description: 使用双重验证时，将非 Microsoft 帐户（如 Google 或 Facebook 帐户）添加到 Microsoft Authenticator 应用，以验证你的身份。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 11/02/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 21c8e75ac81a443b1dd9d4a0f43263bbf40bee88
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359194"
---
# <a name="add-non-microsoft-accounts-to-the-microsoft-authenticator-app"></a>将非 Microsoft 帐户添加到 Microsoft Authenticator 应用

如果你有非 Microsoft 帐户（例如适用于 Google、Facebook 或 GitHub），可以将它们添加到 Microsoft Authenticator 应用进行双重验证。 Microsoft Authenticator 应用适用于使用双重验证的任何应用，以及支持基于时间的一次性密码 (TOTP) 标准的任何帐户。

>[!Important]
>必须先下载并安装 Microsoft Authenticator 应用，然后才能添加帐户。 如果尚未这样做，请按照[下载并安装应用](user-help-auth-app-download-install.md)一文中的步骤操作。

## <a name="add-personal-accounts"></a>添加个人帐户

通常情况下，对于所有个人帐户，必须：

1. 先登录帐户，再使用设备或电脑启用双因素验证。

2. 将帐户添加到 Microsoft Authenticator 应用。 在此过程中，系统可能会要求扫描 QR 码。

    >[!Note]
    >如果这是你首次设置 Microsoft Authenticator 应用，则可能会收到一个提示，询问你是允许该应用访问你的相机 (iOS)，还是允许该应用拍摄照片和录制视频 (Android)。 你必须选择“允许”，以便验证器应用可以访问你的相机，在下一步中拍摄 QR 码的照片。 如果你不允许使用相机，仍然可以设置验证器应用，但需要手动添加代码信息。 有关如何手动添加代码的信息，请参阅[手动将帐户添加到应用](user-help-auth-app-add-account-manual.md)。

我们将在此处为 Facebook、Google、GitHub 和 Amazon 帐户提供此过程，但对于其他应用（如 Instagram 和 Adobe），该过程是相同的。

## <a name="add-your-google-account"></a>添加 Google 帐户

若要添加 Google 帐户，请启用双因素验证，并将帐户添加到应用。

### <a name="turn-on-two-factor-verification"></a>启用双因素验证

1. 在计算机上，转到 https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcome ，选择“开始使用”，然后验证身份。

2. 按照页面上的步骤操作，以为个人 Google 帐户启用双因素验证。

### <a name="add-your-google-account-to-the-app"></a>向应用添加 Google 帐户

1. 在 (的计算机上的 "Google 帐户安全性" 页上 https://myaccount.google.com/security) ，请参阅 "**添加更多第二步，验证是否为**" 部分，从 "**验证器应用**" 部分选择 "**设置**"。

2. 在“从 Authenticator 应用获取验证码”页上，根据电话类型选择“Android”或“iPhone”，再选择“下一步”。

    你会收到 QR 码，可用于自动将帐户与 Microsoft Authenticator 应用相关联。 请勿关闭此窗口。

3. 打开 Microsoft Authenticator 应用，选择右上角“自定义和控制”图标中的“添加帐户”，再选择“其他帐户(Google、Facebook 等)”。

4. 使用设备摄像头扫描电脑上“设置验证器”页显示的 QR 码。

    >[!Note]
    >如果摄像头不能正常工作，可以手动输入 QR 码和 URL。

5. 检查设备上 Microsoft Authenticator 应用的“帐户”页，以确保帐户信息正确，并有关联的六位数验证码。

    为了提高安全性，验证码每 30 秒更改一次，以防有人多次使用一个代码。

6. 在电脑上，选择“设置验证器”页中的“下一步”，键入应用中为 Google 帐户提供的六位数验证码，再选择“验证”  。

7. 帐户已验证，可以选择“完成”来关闭“设置认证器”页。

    >[!NOTE]
    >若要详细了解双因素验证和 Google 帐户，请参阅[启用双因素验证](https://support.google.com/accounts/answer/185839)和[详细了解双因素验证](https://www.google.com/landing/2step/help.html)。

## <a name="add-your-facebook-account"></a>添加 Facebook 帐户

若要添加 Facebook 帐户，请启用双因素验证，并将帐户添加到应用。

### <a name="turn-on-two-factor-verification"></a>启用双因素验证

1. 在电脑上，打开 Facebook，选择右上角的下拉菜单，再依次转到“设置” > “安全和登录” 。

    此时，“安全和登录”页显示。

2. 向下转到“双因素身份验证”部分中的“使用双因素身份验证”选项，再选择“编辑”。

    此时，“双因素身份验证”页显示。

3. 选择“启用”。

### <a name="add-your-facebook-account-to-the-app"></a>向应用添加 Facebook 帐户

1. 在电脑的 Facebook 页上，转到“添加备份”部分，再选择“身份验证应用”区域中的“设置”  。

    你会收到 QR 码，可用于自动将帐户与 Microsoft Authenticator 应用相关联。 请勿关闭此窗口。

2. 打开 Microsoft Authenticator 应用，选择右上角“自定义和控制”图标中的“添加帐户”，再选择“其他帐户(Google、Facebook 等)”。

3. 使用设备摄像头扫描电脑上“双因素身份验证”页显示的 QR 码。

    >[!Note]
    >如果摄像头不能正常工作，可以手动输入 QR 码和 URL。

4. 检查设备上 Microsoft Authenticator 应用的“帐户”页，以确保帐户信息正确，并有关联的六位数验证码。

    为了提高安全性，验证码每 30 秒更改一次，以防有人多次使用一个代码。

5. 在电脑上，选择“双因素身份验证”页中的“下一步”，再键入应用中为 Facebook 帐户提供的六位数验证码 。

    帐户已验证，现在可使用应用来验证帐户了。

    >[!NOTE]
    >若要详细了解双因素验证和 Facebook 帐户，请参阅[什么是双因素身份验证及其工作原理？](https://www.facebook.com/help/148233965247823)。

## <a name="add-your-github-account"></a>添加 GitHub 帐户

若要添加 GitHub 帐户，请启用双因素验证，并将帐户添加到应用。

### <a name="turn-on-two-factor-verification"></a>启用双因素验证

1. 在电脑上，打开 GitHub，再依次选择右上角的图像和“设置”。

    此时，“双因素身份验证”页显示。

2. 选择“个人设置”边栏中的“安全”，再选择“双因素身份验证”区域中的“启用双因素身份验证”。

### <a name="add-your-github-account-to-the-app"></a>向应用添加 GitHub 帐户

1. 在电脑上，选择“双因素身份验证”页中的“使用应用进行设置” 。

2. 保存可便于在失去访问权限时恢复帐户的恢复代码，再选择“下一步”。

    若要保存代码，可以将代码下载到设备、打印打印件，或将代码复制到密码管理器工具中。

3. 选择“双因素身份验证”页中的“使用应用进行设置”。

    此时，页面更改为显示 QR 码。 请勿关闭此页。

4. 打开 Microsoft Authenticator 应用，选择右上角“自定义和控制”图标中的“添加帐户”，再依次选择“其他帐户(Google、Facebook 等)”和页面顶部文本中的“输入此文本密码”。

    由于 Microsoft Authenticator 应用无法扫描 QR 码，因此必须手动输入密码。

5. 输入“帐户名”（例如，“GitHub”），并键入第 4 步中记下的“密钥”，再选择“完成”。

6. 在电脑的“双因素身份验证器”页上，键入应用中为 GitHub 帐户提供的六位数验证码，然后选择“启用” 。

    此时，应用的“帐户”页显示帐户名，以及六位数验证码。 为了提高安全性，验证码每 30 秒更改一次，以防有人多次使用一个代码。

    >[!NOTE]
    >若要详细了解双因素验证和 GitHub 帐户，请参阅[关于双因素身份验证](https://help.github.com/articles/about-two-factor-authentication/)。

## <a name="add-your-amazon-account"></a>添加 Amazon 帐户

若要添加 Amazon 帐户，请启用双因素验证，并将帐户添加到应用。

### <a name="turn-on-two-factor-verification"></a>启用双因素验证

1. 在电脑上，打开 Amazon，选择“帐户和列表”下拉菜单，然后选择“帐户” 。

2. 选择“登录与安全”，登录 Amazon 帐户，再选择“高级安全设置”区域中的“编辑”。

    此时，“高级安全设置”页显示。

3. 选择“入门”。

4. 选择“选择如何接收验证码”页中的“Authenticator 应用”。

    此时，页面更改为显示 QR 码。 请勿关闭此页。

5. 打开 Microsoft Authenticator 应用，选择右上角“自定义和控制”图标中的“添加帐户”，再选择“其他帐户(Google、Facebook 等)”。

6. 使用设备的照相机扫描电脑上“选择如何接收验证码”页显示的 QR 码。

    >[!Note]
    >如果摄像头不能正常工作，可以手动输入 QR 码和 URL。

7. 检查设备上 Microsoft Authenticator 应用的“帐户”页，以确保帐户信息正确，并有关联的六位数验证码。

    为了提高安全性，验证码每 30 秒更改一次，以防有人多次使用一个代码。

8. 在电脑的“选择如何接收验证码”页上，键入应用中为 Amazon 帐户提供的六位数验证码，再选择“验证验证码并继续” 。

9. 完成注册过程的剩余步骤（包括添加短信等备份验证方法），再选择“发送验证码”。

10. 在计算机的“添加备份验证方法”页上，键入备份验证方法为 Amazon 帐户提供的六位数验证码，然后选择“验证验证码并继续” 。

11. 在“**即将完成**”页上，决定是否让计算机成为受信任的设备，然后选择 **“知道了。启用双因素验证”。

    此时，“高级安全设置”页显示，其中包含更新后的双因素验证详细信息。

    >[!NOTE]
    >若要详细了解双因素验证和 Amazon 帐户，请参阅[关于双因素验证](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330)和[通过双因素验证进行登录](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440)。

## <a name="next-steps"></a>后续步骤

- 将帐户添加到应用后，可以在设备上使用 Authenticator 应用登录。 有关详细信息，请参阅[使用应用登录](user-help-auth-app-sign-in.md)。

- 对于运行 iOS 的设备，还可以将帐户凭据和相关的应用设置（如帐户顺序）备份到云中。 有关详细信息，请参阅 [Microsoft Authenticator 应用备份和恢复](user-help-auth-app-backup-recovery.md)。
