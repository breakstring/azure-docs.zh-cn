---
title: 教程：Azure Active Directory 与 AlertMedia 的单一登录 (SSO) 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 AlertMedia 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/17/2020
ms.author: jeedes
ms.openlocfilehash: e876c819ea797eb75ca8b2365fba83d416ff6168
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2020
ms.locfileid: "92318898"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alertmedia"></a>教程：Azure Active Directory 与 AlertMedia 的单一登录 (SSO) 集成

本教程介绍如何将 AlertMedia 与 Azure Active Directory (Azure AD) 集成。 将 AlertMedia 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 AlertMedia。
* 让用户使用其 Azure AD 帐户自动登录到 AlertMedia。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 AlertMedia 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* AlertMedia 支持 IDP  发起的 SSO
* AlertMedia 支持实时  用户预配
* 配置 AlertMedia 后，就可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-alertmedia-from-the-gallery"></a>从库中添加 AlertMedia

若要配置 AlertMedia 与 Azure AD 的集成，需要从库中将 AlertMedia 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分中，在搜索框中键入“AlertMedia”   。
1. 在结果面板中选择“AlertMedia”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on-for-alertmedia"></a>配置并测试 AlertMedia 的 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置并测试 AlertMedia 的 Azure AD SSO  。 若要使 SSO 正常工作，需要在 Azure AD 用户与 AlertMedia 中的相关用户之间建立链接关系。

若要配置并测试 AlertMedia 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 AlertMedia SSO](#configure-alertmedia-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 AlertMedia 测试用户](#create-alertmedia-test-user)** - 在 AlertMedia 中创建 B.Simon 的对应用户，并将其关联到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中，在“AlertMedia”应用程序集成页面上，找到“管理”部分并选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“使用 SAML 设置单一登录”页上，输入以下字段的值： 

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.alertmedia.com`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.alertmedia.com/api/sso/saml/`

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 请联系 [AlertMedia 客户端支持团队](mailto:support@alertmedia.com)来获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. AlertMedia 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![图像](common/default-attributes.png)

1. 除了上述属性之外，AlertMedia 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

| 名称 | 源属性|
| ---- | --------------- |
| email | user.userprincipalname |
| firstname | user.givenname |
| lastname | user.surname |

1. 在“使用 SAML 设置单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，你将通过授予 B.Simon 访问 AlertMedia 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“AlertMedia”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-alertmedia-sso"></a>配置 AlertMedia SSO

1. 在新的 Web 浏览器窗口中，以管理员身份登录到 AlertMedia 公司站点。
1. 导航到“公司”，选择“单一登录”   。

    ![“帐户”按钮](./media/alertmedia-tutorial/Configure1.png)
1. 在“身份验证方法”中，选择“远程 SAML 元数据”  
1. 开启“对请求签名” 
1. 开启“允许被动请求” 
1. 在“元数据 URL”文本框中，粘贴从 Azure 门户复制的“应用联合元数据 URL”值。  
1. 在“请求的身份验证上下文比较”中选择“精确”  
1. 在“IDP 登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。
1. 单击“保存” 

### <a name="create-alertmedia-test-user"></a>创建 AlertMedia 测试用户

在本部分中，我们在 AlertMedia 中创建名为 Britta Simon 的用户。 AlertMedia 支持默认启用的实时用户预配。 此部分不需要你执行任何操作。 如果 AlertMedia 中尚不存在用户，则会在进行身份验证后创建一个新用户。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 AlertMedia 磁贴时，应当会自动登录到为其设置了 SSO 的 AlertMedia。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](./tutorial-list.md)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

- [什么是 Azure Active Directory 中的条件访问？](../conditional-access/overview.md)

- [尝试通过 Azure AD 使用 AlertMedia](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](/cloud-app-security/proxy-intro-aad)

- [如何通过高级可见性和控制保护 AlertMedia](/cloud-app-security/proxy-intro-aad)