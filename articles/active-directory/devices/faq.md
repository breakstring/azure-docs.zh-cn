---
title: Azure Active Directory 设备管理常见问题解答 | Microsoft Docs
description: Azure Active Directory 设备管理常见问题解答。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73946eea846b06b28d0a0f017ea1317c8cc7326d
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165138"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory 设备管理常见问题解答

## <a name="general-faq"></a>常见问题解答

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>问：我最近注册了设备， 为什么我看不到 Azure 门户中的用户信息下的设备？ 或对于加入混合Azure Active Directory (Azure AD) 的设备，为什么设备所有者标记为 N/A？

**答：** 已加入混合 Azure AD 的 Windows 10 设备不会显示在 " **用户设备**" 下。
使用 Azure 门户中的“所有设备”视图。 还可以使用 PowerShell [Get-MsolDevice](/powershell/module/msonline/get-msoldevice) cmdlet。

“USER 设备”下面只会列出以下设备：

- 所有未加入混合 Azure AD 的个人设备。 
- 所有非 Windows 10 或 Windows Server 2016 设备。
- 所有非 Windows 设备。 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>问：如何了解客户端的设备注册状态？

**答：** 在 Azure 门户中，请切换到 " **所有设备**"。 使用设备 ID 搜索设备。 检查“联接类型”列下的值。 有时，设备可能已重置或已重置映像。 因此，还必须检查设备上的设备注册状态：

- 对于 Windows 10 和 Windows Server 2016 或更高版本的设备，请运行 `dsregcmd.exe /status`。
- 对于低级别操作系统版本，请运行 `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`。

**答：** 有关疑难解答信息，请参阅以下文章：
- [使用 dsregcmd 命令排查设备问题](troubleshoot-device-dsregcmd.md)
- [排查已加入混合 Azure Active Directory 的 Windows 10 和 Windows Server 2016 设备问题](troubleshoot-hybrid-join-windows-current.md)
- [排查已加入混合 Azure Active Directory 的下层设备问题](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>问：我在 Azure 门户的 "用户信息" 下看到设备记录。 我在设备上看到状态为已注册。 是否正确设置了使用条件性访问？

**答：** 由 **deviceID** 显示的设备加入状态必须与 Azure AD 上的状态相匹配，并满足条件访问的任何评估条件。 有关详细信息，请参阅 [要求使用条件访问的云应用访问的托管设备](../conditional-access/require-managed-devices.md)。

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices"></a>问：为什么我的用户在其 Windows 10 设备上看到一条错误消息，指出 "你的组织已删除设备" 或 "你的组织已禁用了设备"？

**答：** 在加入或注册到 Azure AD 的 Windows 10 设备上，将向用户颁发一个 [主刷新令牌 (PRT)](concept-primary-refresh-token.md) 启用单一登录。 PRT 的有效性取决于设备本身的有效性。 如果 Azure AD 在中删除或禁用了设备，则用户将看到此消息，而无需从设备本身启动操作。 在以下任一情况下，都可以删除或禁用设备 Azure AD： 

- 用户从 "我的应用" 门户禁用该设备。 
- 管理员 (或用户) 在 Azure 门户或通过使用 PowerShell 删除或禁用设备
- 仅联接混合 Azure AD：管理员将设备 OU 从同步范围中删除，导致设备从 Azure AD
- 升级 Azure AD 连接到版本 1.4. x. x. x. x. x. x。 [了解 Azure AD Connect 1.4. x. x 和设备消失](../hybrid/reference-connect-device-disappearance.md)。


请参阅下面有关如何纠正这些操作的说明。

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>问：我在 Azure 门户或使用 Windows PowerShell 中禁用或删除了我的设备。 但设备上的本地状态仍显示为已注册。 应采取何种操作？

**答：** 此操作是由设计决定的。 在这种情况下，设备无法访问云中的资源。 管理员可以对过时的、丢失或被盗的设备执行此操作，以防止未经授权的访问。 如果无意中执行了此操作，则需要重新启用或重新注册设备，如下所述

- 如果在 Azure AD 中禁用了设备，则具有足够权限的管理员可以从 Azure AD 门户启用它。  
  > [!NOTE]
  > 如果要使用 Azure AD Connect 同步设备，则会在下一个同步周期中自动重新启用混合 Azure AD 加入的设备。 因此，如果需要禁用混合 Azure AD 连接的设备，则需要从本地 AD 禁用它

 - 如果在 Azure AD 中删除了设备，则需要重新注册设备。 若要重新注册，必须在设备上执行手动操作。 请参阅下面的说明，了解如何根据设备状态重新注册。 

      若要重新注册混合 Azure AD 连接的 Windows 10 和 Windows Server 2016/2019 设备，请执行以下步骤：

      1. 以管理员身份打开命令提示符。
      1. 输入 `dsregcmd.exe /debug /leave`。
      1. 注销并再次登录，以触发可以将设备注册到 Azure AD 的计划任务。 

      对于已加入混合 Azure AD 的下层 Windows OS 版本，请执行以下步骤：

      1. 以管理员身份打开命令提示符。
      1. 输入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`。
      1. 输入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`。

      对于已加入 Azure AD Windows 10 设备的设备，请执行以下步骤：

      1. 以管理员身份打开命令提示符
      1. 输入 `dsregcmd /forcerecovery` (注意：你必须是管理员才能执行此操作) 。
      1. 在打开的对话框中单击 "登录"，并继续登录过程。
      1. 注销并重新登录到设备，以完成恢复。

      对于 Azure AD 注册的 Windows 10 设备，请执行以下步骤：

      1. 中转到 "**设置**" "帐户" "  >    >  **访问工作单位或学校**"。 
      1. 选择该帐户，然后选择 " **断开连接**"。
      1. 单击 "+ 连接"，然后通过登录过程再次注册设备。

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>问：为什么在 Azure 门户中看不到重复的设备条目？

**的**

- 对于 Windows 10 和 Windows Server 2016，如果反复尝试分离再重新加入同一个设备，则可能会出现重复条目。 
- 使用“添加工作或学校帐户”的每个 Windows 用户将创建具有相同设备名称的新设备记录。
- 对于已加入本地 Azure Directory 域的低级别 Windows 操作系统版本，自动注册将为登录设备的每个域用户创建具有相同设备名称的新设备记录。 
- 在擦除后重新安装并使用相同名称重新加入 Azure AD 计算机会显示为具有相同设备名称的另一条记录。

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>问：在 FIPS 模式下 Azure AD 中的 Windows 10 设备注册是否支持 Tpm？

**答：** Windows 10 设备注册仅支持符合 FIPS 标准的 TPM 2.0，不支持 TPM 1.2。 如果设备具有符合 FIPS 标准的 TPM 1.2，则必须先将其禁用，然后才能继续 Azure AD 联接或混合 Azure AD 加入。 Microsoft 不提供任何工具用于为 Tpm 禁用 FIPS 模式，因为它依赖于 TPM 制造商。 联系你的硬件 OEM 以获得支持。 

---

**问：为什么用户仍可以从 Azure 门户中禁用的设备访问资源？**

**答：** 从 Azure AD 设备标记为禁用的时间起，将需要长达一小时的时间。

>[!NOTE] 
>对于已注册的设备，建议擦除该设备，确保用户无法访问这些资源。 有关详细信息，请参阅[什么是设备注册？](/mem/intune/user-help/use-managed-devices-to-get-work-done)。 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>问：为什么在 Azure 门户中的 "已注册" 列下，哪些设备标记为 "挂起"？

**答**： "挂起" 表示设备未注册。 此状态表示设备已使用 Azure AD 从本地 AD 连接并已准备好进行设备注册。 这些设备的联接类型设置为 "混合 Azure AD 联接"。 了解有关 [如何计划混合 Azure Active Directory 联接实现](hybrid-azuread-join-plan.md)的详细信息。

>[!NOTE]
>设备还可以从 "已注册" 状态更改为 "挂起"
>* 如果从 Azure AD 删除了某个设备，并从本地 AD 重新同步了该设备。
>* 如果从 Azure AD Connect 上的同步作用域中删除了某个设备，并将其重新添加回来。
>
>在这两种情况下，必须在每台设备上手动重新注册设备。 若要查看设备是否之前已注册，可以 [使用 dsregcmd.exe 命令对设备进行故障排除](troubleshoot-device-dsregcmd.md)。

---

### <a name="q-i-cannot-add-more-than-3-azure-ad-user-accounts-under-the-same-user-session-on-a-windows-10-device-why"></a>问：我无法在 Windows 10 设备上的同一个用户会话下添加超过3个 Azure AD 用户帐户，为什么？

**答**： Azure AD 增加了对 Windows 10 1803 版本中的多个 Azure AD 帐户的支持。 但是，Windows 10 会将设备上 Azure AD 帐户的数目限制为3，以限制令牌请求的大小并启用 (SSO) 的可靠单一登录。 添加3个帐户后，用户将看到后续帐户出现错误。 错误屏幕上的其他问题信息提供了以下消息，指示原因： "由于达到帐户限制，添加帐户操作被阻止"。 

---
## <a name="azure-ad-join-faq"></a>Azure AD 加入常见问题解答

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>问：如何实现在设备本地脱离 Azure AD 已加入设备？

**答：** 对于纯 Azure AD 联接设备，请确保具有脱机的本地管理员帐户或创建一个。 无法使用 Azure AD 用户凭据登录。 接下来，请访问 "**设置**" "帐户" "  >    >  **访问工作或学校**"。 选择帐户，然后选择“断开连接”。 按照提示操作，并在出现提示时提供本地管理员凭据。 重新启动设备以完成取消加入过程。

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>问：我的用户是否可以登录到 Azure AD 中删除或禁用的 Azure AD 联接设备？

**答:** 是的。 Windows 具有缓存用户名和密码功能，该功能允许先前登录的用户即使没有网络连接也能快速访问桌面。 

设备在 Azure AD 中已删除或禁用时，Windows 设备不会知道此情况。 因此，先前登录的用户继续使用缓存的用户名和密码访问桌面。 但在删除或禁用设备后，用户将无法访问受基于设备的条件访问保护的任何资源。 

先前没有登录的用户无法访问设备。 他们没有启用的缓存用户名和密码。 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>问：已禁用或已删除的用户是否可以登录到已加入 Azure AD 的设备

**答：** 是，但仅限有限的时间。 在 Azure AD 中已删除或禁用用户时，Windows 设备不会立即知道此情况。 因此，先前登录的用户可以使用缓存的用户名和密码访问桌面。 

通常，设备在不到 4 小时的时间内即可了解用户状态。 然后 Windows 会阻止这些用户访问桌面。 由于在 Azure AD 中已删除或禁用用户，因此他们的所有令牌都会撤销。 因此他们无法访问任何资源。 

先前未登录的已删除或禁用用户无法访问设备。 他们没有启用的缓存用户名和密码。 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>问：为什么我的用户在更改其 UPN 后在 Azure AD 联接设备上遇到问题？

**答：** 目前，Azure AD 联接的设备上不完全支持 UPN 更改。 因此，在更改其 UPN 后，他们的 Azure AD 身份验证会失败。 这样一来，用户的设备上会存在 SSO 和条件访问问题。 此时，用户需要使用其新 UPN 通过“其他用户”磁贴来登录 Windows，以解决此问题。 我们目前正致力于解决此问题。 但是，使用 Windows Hello 企业版登录的用户不会遇到这个问题。 

Windows 10 2004 更新支持 UPN 更改。 更改其 Upn 后，具有此更新的设备上的用户将不会有任何问题

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>问：我的用户无法从 Azure AD 联接的设备中搜索打印机。 如何从这些设备启用打印？

**答：** 若要为 Azure AD 联接的设备部署打印机，请参阅 [利用预身份验证部署 Windows Server 混合云打印](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy)。 需要安装本地 Windows Server 才能部署混合云打印。 当前，无法使用基于云的打印服务。 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>问：如何实现连接到已加入远程 Azure AD 设备？

**答：** 请参阅 [连接到远程 Azure Active Directory 联接的 PC](/windows/client-management/connect-to-remote-aadj-pc)。

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>问：为什么我的用户看到 *无法从这里获取*？

**答：** 是否配置了某些条件性访问规则来要求特定设备状态？ 如果设备不满足条件，就会阻止用户，并且他们会看到该消息。 评估条件访问策略规则。 确保设备满足条件，避免出现该消息。

---

### <a name="q-why-dont-some-of-my-users-get-azure-ad-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>问：为什么我的某些用户无法在 Azure AD 联接的设备上 Azure AD 多重身份验证提示？

**答：** 用户可以通过使用多重身份验证将设备加入或注册到 Azure AD。 然后，设备本身成为该用户信任的第二个因素。 每当同一用户登录到设备并访问应用程序，Azure AD 都会将该设备视为第二个因素。 它使用户能够无缝访问应用程序，而无需额外的多重身份验证提示。 

此行为：

- 适用于已加入和注册 Azure AD 的设备，但不适用于加入混合 Azure AD 的设备。
- 不适用于登录到该设备的任何其他用户。 因此，访问该设备的所有其他用户都需要进行多重身份验证。 然后他们可以访问需要多重身份验证的应用程序。

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>问：为什么对于刚刚加入到 Azure AD 的设备，我的 *用户名或密码不正确* ？

**答：** 此方案的常见原因如下：

- 用户凭据不再有效。
- 计算机无法与 Azure Active Directory 通信。 请检查是否存在任何网络连接问题。
- 联合登录要求联合服务器支持已启用的和可访问的 WS-Trust 终结点。 
- 已启用传递身份验证。 因此，登录时需要更改临时密码。

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>问：为何我看 *不到 .。。出现错误！* 尝试 Azure AD 加入我的电脑时的对话框？

**答：** 当你设置 Azure Active Directory 向 Intune 注册时，将发生此错误。 确保尝试进行 Azure AD 加入的用户已获得正确的 Intune 许可证。 有关详细信息，请参阅[设置 Windows 设备的注册](/intune/windows-enroll)。  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>问：为什么我没有收到任何错误信息，我 Azure AD 加入 PC 的尝试会失败？

**答：** 可能的原因是您使用本地内置管理员帐户登录到设备。 请在使用 Azure Active Directory 加入之前创建不同的本地帐户以完成设置。 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>问：Windows 10 设备上的 MS-Organization-P2P-Access 证书是什么？

**答：** MS 组织 P2P 访问证书由 Azure AD 颁发，Azure AD 加入和混合 Azure AD 加入的设备。 这些证书用于在同一租户中的设备之间为远程桌面场景启用信任。 一个证书颁发给设备，另一个颁发给用户。 设备证书在 `Local Computer\Personal\Certificates` 中提供，且有效期为一天。 如果设备在 Azure AD 中仍然处于活动状态，则续订此证书（通过颁发新证书）。 用户证书在 `Current User\Personal\Certificates` 中提供，且此证书有效期为一天，但是用户尝试与另一个加入 Azure AD 的设备进行远程桌面会话时，会按需颁发。 到期不续期。 这两个证书都是使用 `Local Computer\AAD Token Issuer\Certificates` 中的 MS-Organization-P2P-Access 证书颁发的。 此证书由 Azure AD 在设备注册期间颁发。 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>问：为什么在 Windows 10 设备上看到多个由 MS-Organization-P2P-Access 颁发的过期证书？ 如何删除它们？

**答：** Windows 10 版本1709和更低版本上确定出现问题，因为存在加密问题，因此，在计算机存储中，这些证书已过期。 如果你使用的是任何 VPN 客户端 (例如，无法处理大量过期证书的 Cisco AnyConnect) ，你的用户可能会面临网络连接问题。 此问题在 Windows 10 1803 版本中已修复，自动删除任何此类过期的 MS-Organization-P2P-Access 证书。 可以通过将设备更新到 Windows 10 1803 来解决此问题。 如果无法更新，可以删除这些证书，而不产生任何不利影响。  

---

## <a name="hybrid-azure-ad-join-faq"></a>混合 Azure AD 加入常见问题解答

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>问：如何实现在设备上将已加入混合 Azure AD 的设备本地脱离？

**答：** 对于已加入混合 Azure AD 设备，请确保关闭自动注册。 然后计划任务不会再次注册设备。 接下来，以管理员身份打开命令提示符并输入 `dsregcmd.exe /debug /leave`。 或者将此命令作为脚本在多个设备上运行，以批量取消加入。

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>问：在哪里可以找到疑难解答信息来诊断混合 Azure AD 联接失败？

**答：** 有关疑难解答信息，请参阅以下文章：

- [排查已加入混合 Azure Active Directory 的 Windows 10 和 Windows Server 2016 设备问题](troubleshoot-hybrid-join-windows-current.md)
- [排查已加入混合 Azure Active Directory 的下层设备问题](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>问：为什么在 "Azure AD 设备" 列表中看到 "我的 Windows 10 混合设备" Azure AD 联接设备的重复 Azure AD 注册记录？

**答：** 当你的用户将其帐户添加到加入域的设备上的应用时，系统可能会提示你 **将帐户添加到 Windows？** 如果他们在提示时输入“确定”，设备会注册 Azure AD。 信任类型标记为已注册 Azure AD。 在组织中启用混合 Azure AD 加入后，设备还将加入混合 Azure AD。 然后显示同一设备的两种设备状态。 

混合 Azure AD 加入优先于 Azure AD 已注册状态。 因此，在进行任何身份验证和条件性访问评估时，设备被视为混合 Azure AD 联接。 可以安全地从 Azure AD 门户中删除已注册 Azure AD 设备记录。 了解[避免或清理 Windows 10 计算机上的此双状态](hybrid-azuread-join-plan.md#review-things-you-should-know)。 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>问：为什么我的用户在更改其 UPN 后在 Windows 10 混合 Azure AD 联接设备上遇到问题？

**答：** 目前，混合 Azure AD 联接设备不完全支持 UPN 更改。 虽然用户可以登录到设备并访问其本地应用程序，但在 UPN 更改后，他们的 Azure AD 身份验证仍会失败。 这样一来，用户的设备上会存在 SSO 和条件访问问题。 此时，需要将设备从 Azure AD 中脱离 (以提升的权限运行 "dsregcmd.exe/leave") ，并自动重新加入 () 解决该问题。 我们目前正致力于解决此问题。 但是，使用 Windows Hello 企业版登录的用户不会遇到这个问题。 

Windows 10 2004 更新支持 UPN 更改。 更改其 Upn 后，具有此更新的设备上的用户将不会有任何问题

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>问：是否需要对域控制器进行 Windows 10 混合 Azure AD 连接的设备才能访问云资源？

**答：** 否，除非用户的密码已更改。 在 Windows 10 混合 Azure AD join 完成并且用户至少登录一次后，设备无需向域控制器提供线路即可访问云资源。 Windows 10 可以通过 internet 连接从任何位置单一登录到 Azure AD 应用程序，但更改密码时除外。 即使更改了密码，使用 Windows Hello 企业版登录的用户仍可继续单一登录到 Azure AD 的应用程序，即使这些用户对其域控制器不可见。 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>问：如果用户更改了密码，并尝试登录到 Windows 10 混合 Azure AD 公司网络外部的已加入设备，会发生什么情况？

**答：** 如果在企业网络外部更改了密码 (例如，通过使用 Azure AD SSPR) ，则使用新密码登录的用户将失败。 对于已加入混合 Azure AD 设备，本地 Active Directory 是主颁发机构。 当设备对域控制器没有线路视觉时，它无法验证新密码。 因此，用户需要建立与域控制器的连接 (通过 VPN 或在企业网络中) ，才能通过其新密码登录到设备。 否则，它们只能用旧密码登录，因为 Windows 中已缓存的登录功能。 但是，旧密码会在令牌请求期间 Azure AD 失效，因此会阻止单一登录，并使任何基于设备的条件访问策略失败。 如果使用 Windows Hello 企业版，则不会出现此问题。 

---

## <a name="azure-ad-register-faq"></a>Azure AD 注册常见问题解答

### <a name="q-how-do-i-remove-an-azure-ad-registered-state-for-a-device-locally"></a>问：如何实现删除设备在本地 Azure AD 注册状态吗？

**的** 
- 对于 Windows 10 Azure AD 注册的设备，请访问 **设置**"帐户" "  >    >  **访问工作或学校**"。 选择帐户，然后选择“断开连接”。 设备注册为 Windows 10 上的每个用户配置文件。
- 对于 iOS 和 Android，你可以使用 Microsoft Authenticator 应用程序 **设置**"  >  **设备注册**"，然后选择 "**注销设备**"。
- 对于 macOS，可以使用 Microsoft Intune 公司门户应用程序从管理中取消注册设备，并删除任何注册。 

---
### <a name="q-how-can-i-block-users-from-adding-additional-work-accounts-azure-ad-registered-on-my-corporate-windows-10-devices"></a>问：如何阻止用户向企业 Windows 10 设备上 (Azure AD 注册) 添加其他工作帐户？

**答：** 启用以下注册表，阻止用户将其他工作帐户添加到加入企业域、Azure AD 联接或混合 Azure AD 加入 Windows 10 设备。 此策略还可用于阻止加入域的计算机无意中获取 Azure AD 注册到同一个用户帐户。 

`HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001`

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>问：我可以注册 Android 或 iOS BYOD 设备吗？

**答：** 是，但仅适用于 Azure 设备注册服务和混合客户。 Active Directory 联合身份验证服务 (AD FS) 中的本地设备注册服务不支持此功能。

---
### <a name="q-how-can-i-register-a-macos-device"></a>问：如何注册 macOS 设备？

**答：** 执行以下步骤：

1.    [创建合规性策略](/intune/compliance-policy-create-mac-os)
1.    [定义 macOS 设备的条件访问策略](../conditional-access/overview.md) 

**备注：**

- 你的条件访问策略中包含的用户需要 [macOS 的支持的 Office 版本](../conditional-access/concept-conditional-access-conditions.md) 来访问资源。 
- 在首次尝试访问期间，用户使用公司门户时会看到注册设备的提示。

---
## <a name="next-steps"></a>后续步骤

- 详细了解[已注册到 Azure AD 的设备](concept-azure-ad-register.md)
- 详细了解[已加入 Azure AD 的设备](concept-azure-ad-join.md)
- 详细了解[已加入混合 Azure AD 的设备](concept-azure-ad-join-hybrid.md)
