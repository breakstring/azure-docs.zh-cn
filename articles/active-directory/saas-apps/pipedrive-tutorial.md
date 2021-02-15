---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Pipedrive 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Pipedrive 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: jeedes
ms.openlocfilehash: f85cb97406e8b6cbb4811268696fc36f47ec3adb
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896525"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pipedrive"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Pipedrive 的集成

本教程介绍如何将 Pipedrive 与 Azure Active Directory (Azure AD) 集成。 将 Pipedrive 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Pipedrive。
* 让用户使用其 Azure AD 帐户自动登录到 Pipedrive。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Pipedrive 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Pipedrive 支持 **SP 和 IDP** 发起的 SSO
* 配置 Pipedrive SSO 后，可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。


## <a name="adding-pipedrive-from-the-gallery"></a>从库中添加 Pipedrive

若要配置 Pipedrive 与 Azure AD 的集成，需要从库中将 Pipedrive 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Pipedrive** 。 
1. 在结果面板中选择“Pipedrive”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-pipedrive"></a>配置并测试 Pipedrive 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 Pipedrive 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Pipedrive 中的相关用户之间建立链接关系。

若要配置并测试 Pipedrive 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Pipedrive SSO](#configure-pipedrive-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 Pipedrive 测试用户](#create-pipedrive-test-user)** - 在 Pipedrive 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Pipedrive”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp/metadata.xml`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp`

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<COMPANY-NAME>.pipedrive.com/`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [Pipedrive 客户端支持团队](mailto:support@pipedrive.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. Pipedrive 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除上述属性以外，Pipedrive 应用程序还要求在 SAML 响应中传回其他几个属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 | 源属性|
    | ------------ | --------- |
    | 电子邮件 | user.mail |

1. 在“设置 SAML 单一登录”页上的“SAML 签名证书”部分中找到“证书(Base64)”，然后选择“下载”以下载该证书并将其保存在计算机上，同时请复制“应用联合元数据 URL”并将其保存在计算机上。     

    ![证书下载链接](./media/pipedrive-tutorial/certificate-data.png)

1. 在“设置 Pipedrive”部分，根据要求复制相应的 URL。 

    ![复制配置 URL](common/copy-configuration-urls.png)

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

在本部分，你将通过授予 B.Simon 访问 Pipedrive 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中选择“Pipedrive”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-pipedrive-sso"></a>配置 Pipedrive SSO

1. 在另一个浏览器窗口中，以管理员身份登录到 Pipedrive 网站。

1. 单击“用户配置文件”并选择“设置”。  

    ![显示从“用户个人资料”菜单选择了“设置”的屏幕截图。](./media/pipedrive-tutorial/configure1.png)

1. 向下滚动到“安全中心”，然后选择“单一登录”。 

    ![显示在“安全中心”中选择了“单一登录”的屏幕截图。](./media/pipedrive-tutorial/configure2.png)

1. 在“Pipedrive 的 SAML 配置”部分执行以下步骤： 

    ![显示“Pipedrive 的 SAML 配置”部分的屏幕截图，其中突出显示了所有文本框。](./media/pipedrive-tutorial/configure3.png)

    a. 在“颁发者”文本框中，粘贴从 Azure 门户复制的“应用联合元数据 URL”值。  

    b. 在“单一登录(SSO) URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。  

    c. 在“单一注销(SLO) URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。  

    d. 使用记事本打开从 Azure 门户下载的“证书(Base64)”文件，将其内容复制并粘贴到“X.509 证书”文本框中并保存更改。   

### <a name="create-pipedrive-test-user"></a>创建 Pipedrive 测试用户

1. 在另一个浏览器窗口中，以管理员身份登录到 Pipedrive 网站。

1. 向下滚动到“公司”，然后选择“管理用户”。 

    ![显示从“公司”菜单选择了“管理用户”的屏幕截图。](./media/pipedrive-tutorial/user1.png)

1. 单击“添加用户”。 
    
    ![显示在右侧选择了“添加用户”按钮的“管理用户”页的屏幕截图。](./media/pipedrive-tutorial/user2.png)

1. 在“管理用户”部分执行以下步骤： 

    ![Pipedrive 配置](./media/pipedrive-tutorial/user3.png)

    a. 在“电子邮件”文本框中，输入用户的电子邮件地址（如 `B.Simon@contoso.com`）  。

    b. 在“名字”文本框中，输入用户的名字。 

    c. 在“姓氏”文本框中，输入用户的姓氏。 

    d. 单击“确认并邀请用户”。 

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Pipedrive”磁贴时，应会自动登录到设置了 SSO 的 Pipedrive。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](./tutorial-list.md)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

- [什么是 Azure Active Directory 中的条件访问？](../conditional-access/overview.md)

- [在 Azure AD 中试用 Pipedrive](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](/cloud-app-security/proxy-intro-aad)