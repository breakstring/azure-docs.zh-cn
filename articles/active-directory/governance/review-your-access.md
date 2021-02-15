---
title: 在访问评审中评审自己对组和应用的访问权限 - Azure AD
description: 了解如何在 Azure Active Directory 访问评审中评审自己对组或应用程序的访问权限。
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3fef2f85ca7e7b4034c8582477796d49446ea44
ms.sourcegitcommit: 6e2d37afd50ec5ee148f98f2325943bafb2f4993
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/23/2020
ms.locfileid: "97746773"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>在 Azure AD 访问评审中评审自己对组或应用程序的访问权限

Azure Active Directory (Azure AD) 借助称为“Azure AD 访问评审”的功能，简化了企业对 Azure AD 及其他 Microsoft Online Services 中的组或应用程序访问权限的管理方式。

本文介绍如何评审自己对组或应用程序的访问权限。

## <a name="review-your-access-using-my-apps"></a>使用“我的应用”评审访问权限

执行访问评审的第一步是找到并打开访问评审。

>[!IMPORTANT]
> 接收电子邮件可能存在延迟，在某些情况下，可能需要长达 24 小时来接收。 将 azure-noreply@microsoft.com 添加到安全收件人列表以确保收到所有电子邮件。

1. 找到要求你执行访问评审的 Microsoft 电子邮件。 以下示例电子邮件要求评审你对某个组的访问权限。

    ![要求评审你对某个组的访问权限的 Microsoft 示例电子邮件](./media/review-your-access/access-review-email.png)

1. 单击“评审访问权限”链接，打开访问评审。

如果未收到该电子邮件，可遵循以下步骤找到待处理的访问评审。

1. 登录到“我的应用”门户 ([https://myapps.microsoft.com](https://myapps.microsoft.com))。

    ![“我的应用”门户，其中列出了你有权访问的应用](./media/review-your-access/myapps-access-panel.png)

1. 在页面右上角，单击用户符号，其中显示了你的姓名和默认组织。 如果列出多个组织，请选择已请求访问评审的组织。

1. 在页面右侧，单击“访问评审”磁贴来查看待处理的访问评审。

    如果该磁贴不可见，则该组织没有要执行的访问评审，此时不需要执行任何操作。

    ![应用和组的待处理访问评审列表](./media/review-your-access/access-reviews-list.png)

1. 单击你要执行的访问评审对应的“开始评审”链接。

### <a name="perform-the-access-review"></a>执行访问评审

打开访问评审以后，即可看到自己的访问权限。

1. 评审你的访问权限，决定是否仍需要访问权限。

    如果请求是评审他人的访问权限，则页面内容会有所不同。 有关详细信息，请参阅[评审对组或应用程序的访问权限](perform-access-review.md)。

    ![屏幕截图显示了开放访问评审，其中询问是否仍需访问某个组。](./media/review-your-access/perform-access-review.png)

1. 单击“是”保留访问权限，或者单击“否”删除访问权限。

1. 如果单击“是”，你可能需要在“原因”框中指出原因。 

    ![屏幕截图显示已完成的访问评审，其中系统询问你是否仍需访问某个组，且已选择“是”。](./media/review-your-access/perform-access-review-submit.png)

1. 单击“提交” 。

    此时会提交你所做的选择，并会将你返回到“我的应用”门户。

    若要更改响应，请重新打开“访问评审”页面并更新响应。 在访问评审结束之前，随时可以更改响应。

    > [!NOTE]
    > 如果你指出不再需要访问权限，系统不会立即将你删除。 系统会在评审结束时或者在管理员停止评审时将你删除。

## <a name="review-your-own-access-using-my-access-new"></a>使用 "我的 Access (新建) 来查看你自己的访问权限

你可以使用以下几种不同的方式，通过 "我的 Access" 中更新的用户界面尝试新体验：

### <a name="my-apps-portal"></a>我的应用门户

1. 登录到“我的应用”门户 ([https://myapps.microsoft.com](https://myapps.microsoft.com))。

    ![“我的应用”门户，其中列出了你有权访问的应用](./media/review-your-access/myapps-access-panel.png)

2. 单击“访问评审”磁贴，查看待处理的访问评审列表。

    > [!NOTE]
    > 如果“访问评审”磁贴不可见，则表明该组织没有要执行的访问评审，此时不需要执行任何操作。

3. 单击 " **试用"！** 在页面顶部的标题中，请访问新的 "我的访问体验"。

    ![在预览期间显示有新的可用体验横幅的应用和组的挂起访问审阅列表](./media/review-your-access/banner-your-access.png)

4. 继续 **执行访问评审** 部分

### <a name="email"></a>电子邮件

>[!IMPORTANT]
> 接收电子邮件可能存在延迟，在某些情况下，可能需要长达 24 小时来接收。 将 azure-noreply@microsoft.com 添加到安全收件人列表以确保收到所有电子邮件。

1. 查找要求你执行访问评审的 Microsoft 电子邮件。 你可以在下面看到一个示例电子邮件：

 ![要求评审对某个组的访问权限的 Microsoft 示例电子邮件](./media/review-your-access/access-review-email-preview.png)

2. 单击“评审访问权限”链接，打开访问评审。

3. 继续 **执行访问评审** 部分

>[!NOTE]
>如果单击 "开始审阅"，则会将你转到 **"我的应用** "，然后按照上面标题为 **"我的应用门户**" 部分列出

### <a name="directly-at-my-access"></a>直接在我的访问

还可以通过使用浏览器打开 "我的访问" 来查看待定的访问评审。

1. 在上登录到 "我的访问" https://myaccess.microsoft.com/

2. 从左侧栏上的菜单中选择 " **访问评审** "，查看分配给你的待定访问评审的列表。

   ![菜单中的访问评审](./media/review-your-access/access-review-menu.png)

### <a name="perform-the-access-review"></a>执行访问评审

1. 在 "组和应用" 下，可以看到：
    
    - **名称** 访问评审的名称。
    - **截止日期** 评审的截止日期。 在此日期后，可以从正在查看的组或应用中删除用户。
    - **资源** 要查看的资源的名称。
    - **进度** 在此访问评审的总用户数量中审阅的用户数。
    
2. 单击 "访问评审" 的名称即可开始使用。

   ![应用和组的待处理访问评审列表](./media/review-your-access/access-reviews-list-preview.png)

3. 评审你的访问权限，决定是否仍需要访问权限。

    如果请求是评审他人的访问权限，则页面内容会有所不同。 有关详细信息，请参阅[评审对组或应用程序的访问权限](perform-access-review.md)。

    ![打开访问评审，系统会询问你是否仍需访问某个组](./media/review-your-access/review-access-preview.png)

1. 选择 **"是"** 以保留访问权限，或选择 " **否** " 以删除访问权限。

1. 如果单击“是”，你可能需要在“原因”框中指出原因。 

    ![完成访问评审，系统会询问你是否仍需访问某个组](./media/review-your-access/review-access-yes-preview.png)

1. 单击“提交” 。

    你的选择已提交，你将返回到 "我的访问" 页。

    若要更改响应，请重新打开“访问评审”页面并更新响应。 在访问评审结束之前，随时可以更改响应。

    > [!NOTE]
    > 如果你指出不再需要访问权限，系统不会立即将你删除。 系统会在评审结束时或者在管理员停止评审时将你删除。

## <a name="next-steps"></a>后续步骤

- [完成组或应用程序的访问评审](complete-access-review.md)
