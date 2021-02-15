---
title: 模拟 Azure AD Identity Protection 中的风险检测
description: 了解如何在 Identity Protection 中模拟风险检测
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7eafeaf59757fcda978fa89b4bc2f9882b769e48
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835894"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>模拟 Identity Protection 中的风险检测

管理员可能想要模拟其环境中的风险，以完成以下各项：

- 通过模拟风险检测和漏洞来填充标识保护环境中的数据。
- 设置基于风险的条件性访问策略，并测试这些策略的影响。

本文提供了用于模拟以下风险检测类型的步骤：

- 匿名 IP 地址 (easy) 
- 适中)  (不熟悉的登录属性
- 典型的旅游 (困难) 

不能以安全方式模拟其他风险检测。

有关每个风险检测的详细信息，请参阅 " [什么是风险](concept-identity-protection-risks.md)" 一文。

## <a name="anonymous-ip-address"></a>匿名 IP 地址

完成以下过程需要使用：

- [Tor 浏览器](https://www.torproject.org/projects/torbrowser.html.en)，用于模拟匿名 IP 地址。 如果组织限制使用 Tor 浏览器，则可能需要使用虚拟机。
- 尚未注册 Azure AD 多重身份验证的测试帐户。

**若要模拟从匿名 IP 登录，请执行以下步骤**：

1. 使用 [Tor 浏览器](https://www.torproject.org/projects/torbrowser.html.en)，导航到 [https://myapps.microsoft.com](https://myapps.microsoft.com)。   
2. 输入要在 **从匿名 IP 地址登录** 报告中显示的帐户的凭据。

登录会在 10 - 15 分钟内显示在“标识保护”仪表板上。 

## <a name="unfamiliar-sign-in-properties"></a>不熟悉的登录属性

若要模拟不熟悉的位置，必须从测试帐户之前未登录过的位置和设备登录。

下面的过程使用新创建的以下项目：

- VPN 连接，用于模拟新位置。
- 虚拟机，用于模拟新设备。

完成以下过程需要使用具有以下特征的用户帐户：

- 至少 30 天的登录历史记录。
- 已启用 Azure AD 多重身份验证。

**若要模拟从不熟悉的位置登录，请执行以下步骤**：

1. 使用测试帐户登录时，多重身份验证将无法通过 MFA 质询 (MFA) 质询。
2. 使用新的 VPN 导航到， [https://myapps.microsoft.com](https://myapps.microsoft.com) 并输入测试帐户的凭据。

登录会在 10 - 15 分钟内显示在“标识保护”仪表板上。

## <a name="atypical-travel"></a>异常位置登录

模拟典型旅行情况非常困难，因为算法使用机器学习来清除无用误报，例如来自熟悉设备的异常旅行，或来自目录中其他用户使用的 Vpn 的登录。 另外，此算法要求用户 14 天 10 次的登录历史记录，才能开始生成风险检测。 考虑到机器学习模型的复杂性以及上述规则的存在，以下步骤有可能不会导致风险检测。 你可能想要为多个 Azure AD 帐户复制这些步骤来模拟此检测。

**若要模拟典型的旅行风险检测，请执行以下步骤**：

1. 使用标准浏览器，导航到 [https://myapps.microsoft.com](https://myapps.microsoft.com) 。  
2. 输入要为其生成非典型旅行风险检测的帐户的凭据。
3. 更改用户代理。 可以通过开发人员工具 (F12) 更改 Microsoft Edge 中的用户代理。
4. 更改 IP 地址。 可以通过使用 VPN、Tor 外接程序或在其他数据中心的 Azure 中创建新的虚拟机，来更改 IP 地址。
5. 在 [https://myapps.microsoft.com](https://myapps.microsoft.com) 上一次登录后的几分钟内，使用相同的凭据登录。

登录会在 2-4 小时内显示在“标识保护”仪表板中。

## <a name="testing-risk-policies"></a>测试风险策略

本部分提供的步骤用于测试用户和在本文中创建的登录风险策略， [如何：配置和启用风险策略](howto-identity-protection-configure-risk-policies.md)。

### <a name="user-risk-policy"></a>用户风险策略

若要测试用户风险安全策略，请执行以下步骤：

1. 导航到 [Azure 门户](https://portal.azure.com)。
1. 浏览到 **Azure Active Directory**  >  **安全**  >  **概述**。
1. 选择 " **配置用户风险策略**"。
   1. 在 **分配** 下
      1. **用户** -选择 " **所有用户** " **，或选择 "个人和组** " （如果限制你的部署）。
         1. 还可以选择从策略中排除用户。
      1. **条件**  - **用户风险** Microsoft 建议将此选项设置为 "**高**"。
   1. **控件** 下面
      1. **访问** -Microsoft 的建议是 **允许访问** 并 **需要更改密码**。
   1. **强制执行策略**  - **关闭**
   1. **保存** -此操作将返回到 " **概述** " 页。
1. 可以通过特定的方式来提升测试帐户的用户风险，例如，可以多次模拟某个风险检测。
1. 等待几分钟，然后验证是否已为用户提升了风险。 如果否，请为用户模拟更多的风险检测。
1. 返回到风险策略，并将 " **强制策略** " 设置为 **"开"** 并 **保存** 策略更改。
1. 现在可以使用风险级别已提升的用户登录，以便测试基于用户风险的条件访问。

### <a name="sign-in-risk-security-policy"></a>登录风险安全策略

若要测试登录风险策略，请执行以下步骤：

1. 导航到 [Azure 门户](https://portal.azure.com)。
1. 浏览到 **Azure Active Directory**  >  **安全**  >  **概述**。
1. 选择 " **配置登录风险策略"**。
   1. 在 **分配** 下
      1. **用户** -选择 " **所有用户** " **，或选择 "个人和组** " （如果限制你的部署）。
         1. 还可以选择从策略中排除用户。
      1. **条件**  - **登录风险** Microsoft 建议将此选项设置为 "**中" 和 "上**"。
   1. **控件** 下面
      1. **访问** -Microsoft 的建议是 **允许访问** 并 **需要多重身份验证**。
   1. **强制执行策略**  - **开启**
   1. **保存** -此操作将返回到 " **概述** " 页。
1. 现在可以使用风险会话（例如，使用 Tor 浏览器）登录，以便测试基于登录风险的条件访问。 

## <a name="next-steps"></a>后续步骤

- [什么是风险？](concept-identity-protection-risks.md)

- [如何：配置和启用风险策略](howto-identity-protection-configure-risk-policies.md)

- [Azure Active Directory 标识保护](overview-identity-protection.md)
