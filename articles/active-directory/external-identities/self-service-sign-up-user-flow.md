---
title: 添加自助注册用户流 - Azure AD
description: 为组织构建的应用创建用户流。 然后，访问该应用的用户可以使用用户流中配置的选项获得来宾帐户。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51602e97a8424bade542eec6f88b673130fee8b5
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586017"
---
# <a name="add-a-self-service-sign-up-user-flow-to-an-app-preview"></a>向应用添加自助注册用户流（预览）
> [!NOTE]
> 自助注册是 Azure Active Directory 的公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

可以为组织生成的应用创建用户流。 通过将用户流与应用关联，可以在相应应用上启用注册。 可以选择多个与用户流关联的应用。 将用户流与一个或多个应用关联后，访问相应应用的用户将能够使用用户流中配置的选项注册并获得来宾帐户。

> [!NOTE]
> 可以将用户流与组织生成的应用相关联。 用户流不能用于 Microsoft 应用，如 SharePoint 或 Teams。

## <a name="before-you-begin"></a>开始之前

### <a name="add-social-identity-providers-optional"></a>添加社交标识提供者（可选）

Azure AD 是自助注册的默认标识提供者。 也就是说，在默认情况下，用户可以使用 Azure AD 帐户进行注册。 也可以通过在这些注册流中添加社交标识提供者来支持 Google 和 Facebook 帐户。

- [向社交标识提供者列表添加 Facebook](facebook-federation.md)
- [向社交标识提供者列表添加 Google](google-federation.md)

> [!NOTE]
> 在当前的预览版中，如果自助注册用户流已与某个应用关联，而你向某位用户发送了该应用的邀请，那么该用户将无法使用 Gmail 帐户兑换该邀请。 作为解决方法，用户可以执行自助服务注册流程。 此外，用户可以通过访问其他应用或使用“我的应用”门户（位于 https://myapps.microsoft.com ）来兑换邀请。

### <a name="define-custom-attributes-optional"></a>定义自定义属性（可选）

用户属性是在自助注册期间收集的用户值。 Azure AD 随附了一组内置属性，但你也可以创建自定义属性，以用于用户流。 还可以使用 Microsoft Graph API 读写这些属性。 请参阅[定义用户流的自定义属性](user-flow-add-custom-attributes.md)。

## <a name="enable-self-service-sign-up-for-your-tenant"></a>为租户启用自助注册

必须先为租户启用自助注册功能，然后才能向应用添加自助注册用户流。 启用后，用户流中的控件就可用了，可以将用户流与应用关联起来。

1. 以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 在“Azure 服务”下，选择“Azure Active Directory”。
3. 选择“用户设置”，然后在“外部用户”下，选择“管理外部协作设置”。
4. 将“启用通过用户流进行来宾自助注册(预览)”切换设置为“是”。

   ![启用来宾自助注册](media/self-service-sign-up-user-flow/enable-self-service-sign-up.png)
5. 选择“保存”。
## <a name="create-the-user-flow-for-self-service-sign-up"></a>创建自助注册用户流

接下来，你将创建自助注册用户流，并将它添加到应用中。

1. 以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 在“Azure 服务”下，选择“Azure Active Directory”。
3. 在左侧菜单中，选择“外部标识”。
4. 依次选择“用户流(预览)”和“新建用户流”。

   ![“添加新用户流”按钮](media/self-service-sign-up-user-flow/new-user-flow.png)

5. 在“创建”页上的“名称”中，输入用户流的名称。 请注意，名称自动以 B2X_1_ 为前缀。
6. 在“标识提供者”列表中，选择一个或多个可供外部用户用来登录应用的标识提供者。 默认选择的是“Azure Active Directory 注册”。 （若要了解如何添加标识提供者，请参阅本文前面的[开始之前](#before-you-begin)。）
7. 在“用户属性”下，选择要收集的用户属性。 若要选择其他属性，请选择“显示更多”。 例如，选择“显示更多”，然后选择“国家/地区”、“显示名称”和“邮政编码”的属性和声明。 选择“确定”。

   ![“新建用户流”页](media/self-service-sign-up-user-flow/create-user-flow.png)

> [!NOTE]
> 仅可在用户首次注册时收集属性。 用户注册后，将不再提示他们收集属性信息，即使更改用户流也是如此。

8. 选择“创建”。
9. 此时，新用户流显示在“用户流(预览)”列表中。 如果需要，请刷新页面。

## <a name="select-the-layout-of-the-attribute-collection-form"></a>选择“属性集合”窗体的布局

可以选择属性在注册页上显示的顺序。 

1. 在 [Azure 门户](https://portal.azure.com)中，选择“Azure Active Directory”。
2. 依次选择“外部标识”和“用户流(预览)”。
3. 从列表中选择自助注册用户流。
4. 在“自定义”下选择“页面布局”。 
5. 此时列出你选择收集的属性。 若要更改显示顺序，请选择一个属性，然后选择“上移”、“下移”、“移至顶部”或“移至底部”。
6. 选择“保存”。

## <a name="add-applications-to-the-self-service-sign-up-user-flow"></a>将应用添加到自助注册用户流

现在，可以关联应用与用户流。

1. 以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 在“Azure 服务”下，选择“Azure Active Directory”。
3. 在左侧菜单中，选择“外部标识”。
4. 在“自助注册”下，选择“用户流(预览)”。
5. 从列表中选择自助注册用户流。
6. 在左侧菜单中的“使用”下，选择“应用”。
7. 选择“添加应用程序”。

   ![将应用分配到用户流](media/self-service-sign-up-user-flow/assign-app-to-user-flow.png)

8. 从列表中选择应用。 或者，使用搜索框来查找应用，然后选择它。
9. 单击“选择”。

## <a name="next-steps"></a>后续步骤

- [向社交标识提供者列表添加 Google](google-federation.md)
- [向社交标识提供者列表添加 Facebook](facebook-federation.md)
- [使用 API 连接器通过 web Api 自定义和扩展用户流](api-connectors-overview.md)
- [向用户流中添加自定义审批工作流](self-service-sign-up-add-approvals.md)
