---
title: 如何配置自助应用程序分配 | Microsoft Docs
description: 启用自助服务应用程序访问以允许用户查找自己的应用程序
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/20/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9742a21cf00733607237c0eaf548f96b434abb33
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260208"
---
# <a name="how-to-configure-self-service-application-assignment"></a>如何配置自助应用程序分配

你需要启用 **自助应用程序访问** 你希望允许用户自行发现并请求访问的任何应用程序，然后你的用户才能从其 "我的应用程序" 中发现应用程序。 此功能可用于从 [Azure AD 库](./add-application-portal.md)中添加的应用程序， [Azure AD 应用程序代理](./application-proxy.md) 或通过 [用户或管理员同意](../develop/application-consent-experience.md)添加的应用程序。 

对于 IT 组，此功能是节省时间和金钱的绝佳方式，我们强烈建议将其作为 Azure Active Directory 的新式应用程序部署的一部分。

使用此功能，可以：

-   让用户从 " [我的应用](https://myapps.microsoft.com/) 程序" 自行发现应用程序，而无需麻烦 IT 组。

-   将这些用户添加到预配置组，以便查看请求访问的用户、删除访问权限以及管理分配给用户的角色。

-   （可选）允许业务审批人批准应用程序访问请求，从而为 IT 组省去麻烦。

-   （可选）最多配置 10 个可以批准访问此应用程序的人员。

-   （可选）允许业务审批者设置用户用于登录应用程序的密码，该密码来自企业审批者的 ["我的应用](https://myapps.microsoft.com/)"。

-   （可选）自动将自助服务分配的用户直接分配到应用程序角色。

> [!NOTE]
> 需要 Azure Active Directory Premium (P1 或 P2) 许可证才能请求加入自助服务应用，以及允许所有者批准或拒绝请求。 如果没有 Azure Active Directory Premium 许可证，用户将无法添加自助服务应用。

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>启用自助服务应用程序访问以允许用户查找自己的应用程序

自助应用程序访问是允许用户自行发现应用程序的绝佳方式，并且可以选择允许业务组批准对这些应用程序的访问。 对于密码单一登录应用程序，你还可以允许业务组管理从他们自己的 "我的应用" 访问面板分配给这些用户的凭据。

若要启用应用程序的自助应用程序访问，请执行以下步骤：

1. 以全局管理员的身份登录到 [Azure 门户](https://portal.azure.com)。

2. 选择“Azure Active Directory”。 在左侧导航菜单中，选择“企业应用程序”。

3. 从列表中选择应用。 如果看不到该应用程序，请在搜索框中键入其名称。 或者使用筛选控件选择应用程序类型、状态或可见性，然后选择“应用”。

4. 在左侧导航菜单中，选择 " **自助服务**"。

5. 要对此应用程序启用自助应用程序访问，请将“允许用户请求对此应用程序的访问权限?”切换到“是”。

6. 单击 **"要向哪个组添加用户？**" 旁边的 " **选择组**"。 选择一个组，然后单击 " **选择**"。 用户的请求获得批准后，将添加到此组。 查看此组的成员身份时，可以通过自助服务访问查看已被授予对应用程序的访问权限的用户。
  
    > [!NOTE]
    > 此设置不支持从本地同步的组。

7. **可选：** 若要在允许用户访问之前要求进行业务审批，请将 " **授予访问此应用程序之前需要审批？** " 切换到 **"是"**。

8. **可选：对于仅使用密码单一登录的应用程序，** 若要允许业务审批者为批准的用户指定发送到此应用程序的密码，请将 " **允许审批者设置此应用程序的用户密码？** " 切换为 **"是"**。

9. **可选：** 若要指定允许审批对此应用程序的访问权限的业务审批者，请在 **"要批准对此应用程序的访问权限"** 旁单击 " **选择审批者**"，然后选择多达10个单独的业务审批者。 然后单击“选择”。

    >[!NOTE]
    >不支持组。 最多可选择10个单独的业务审批者。 如果指定多个审批者，则任何单个审批者都可以批准访问请求。

10. **可选：** **对于公开角色的应用程序**，若要将自助服务批准的用户分配给角色，请 **在 "此应用程序中应向哪个角色分配用户？**" 旁边，单击 " **选择角色**"，然后选择应将这些用户分配到的角色。 然后单击“选择”。

11. 单击窗格顶部的“保存”按钮以完成操作。

完成自助服务应用程序配置后，用户可以导航到其 ["我的应用](https://myapps.microsoft.com/) "，然后单击 " **添加自助服务应用** 程序" 按钮以查找启用了自助访问的应用。 业务审批者还会在其 ["我的应用"](https://myapps.microsoft.com/)中看到通知。 可以启用电子邮件，在用户请求需要审批人批准的应用程序的访问权限时，向审批人发送电子邮件通知。

## <a name="next-steps"></a>后续步骤
[为自助组管理设置 Azure Active Directory](../enterprise-users/groups-self-service-management.md)