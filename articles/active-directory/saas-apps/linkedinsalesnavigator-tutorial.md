---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 LinkedIn Sales Navigator 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 LinkedIn Sales Navigator 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.openlocfilehash: 3f4d4d1e0fc44b9fb031ce5e3e45219fa8169562
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2020
ms.locfileid: "92458500"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-sales-navigator"></a>教程：Azure Active Directory 单一登录 (SSO) 与 LinkedIn Sales Navigator 的集成

本教程介绍如何将 LinkedIn Sales Navigator 与 Azure Active Directory (Azure AD) 进行集成。 将 LinkedIn Sales Navigator 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 LinkedIn Sales Navigator。
* 让用户使用其 Azure AD 帐户自动登录到 LinkedIn Sales Navigator。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 LinkedIn Sales Navigator 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* LinkedIn Sales Navigator 支持 **SP 和 IDP** 发起的 SSO
* LinkedIn Sales Navigator 支持 **实时** 用户预配
* LinkedIn Sales Navigator 支持 [**自动** 用户预配](linkedinsalesnavigator-provisioning-tutorial.md)

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>从库中添加 LinkedIn Sales Navigator

要配置 LinkedIn Sales Navigator 与 Azure AD 的集成，需要从库中将 LinkedIn Sales Navigator 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **LinkedIn Sales Navigator** 。 
1. 在结果面板中选择“LinkedIn Sales Navigator”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-sales-navigator"></a>配置并测试 LinkedIn Sales Navigator 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 LinkedIn Sales Navigator 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 LinkedIn Sales Navigator 中的相关用户之间建立链接关系。

若要配置并测试 LinkedIn Sales Navigator 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 LinkedIn Sales Navigator SSO](#configure-linkedin-sales-navigator-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 LinkedIn Sales Navigator 测试用户](#create-linkedin-sales-navigator-test-user)** - 在 LinkedIn Sales Navigator 中创建 B.Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“LinkedIn Sales Navigator”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    a. 在“标识符”文字框中，输入“实体 ID”值。本教程稍后会介绍如何从 LinkedIn 门户复制“实体 ID”值。  

    b. 在“回复 URL”文字框中，输入“断言使用者访问(ACS) URL”值。本教程稍后会介绍如何从 LinkedIn 门户复制“断言使用者访问(ACS) URL”值。  

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

1. LinkedIn Sales Navigator 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除上述属性以外，LinkedIn Sales Navigator 应用程序还要求在 SAML 响应中传回其他几个属性如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 | 源属性|
    | --- | --- |
    | 电子邮件| user.mail |
    | department| user.department |
    | 名| user.givenname |
    | 姓| user.surname |
    | 唯一用户标识符 | user.mail |

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 LinkedIn Sales Navigator”部分，根据要求复制相应的 URL。 

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

在本部分中，我们将通过向 B.Simon 授予对 LinkedIn Sales Navigator 的访问权限使她能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“LinkedIn Sales Navigator”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-linkedin-sales-navigator-sso"></a>配置 LinkedIn Sales Navigator SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 **LinkedIn Sales Navigator** 网站。

1. 在“帐户中心”  ，单击“设置”  下的“全局设置”  。 此外，请从下拉列表中选择“Sales Navigator”。 

    ![屏幕截图显示了“应用程序设置”，你可以在其中选择“销售导航”。](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. 单击“或单击此处从表单加载并复制单个字段”并执行以下步骤： 

    ![屏幕截图显示了“单一登录”，你可以在其中输入所述值。](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

    a. 复制“实体 ID”，并将其粘贴到 Azure 门户上“基本 SAML 配置”中的“标识符”文本框内。   

    b. 复制“断言使用者访问(ACS) URL”，并将其粘贴到 Azure 门户上“基本 SAML 配置”中的“回复 URL”文本框内。   

1. 转到“LinkedIn 管理设置”  分区。 单击“上传 XML 文件”选项，上传从 Azure 门户下载的 XML 文件。 

    ![屏幕截图显示了“配置 LinkedIn 服务提供商 SSO 设置”，你可以在其中上传 XML 文件。](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. 单击“打开”  启用 SSO。 SSO 状态将从“未连接”  更改为“已连接” 

    ![屏幕截图显示了单一登录，你可以在其中启用“通过 SSO 对用户进行身份验证”。](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-sales-navigator-test-user"></a>创建 LinkedIn Sales Navigator 测试用户

LinkedIn Sales Navigator 应用程序支持适时用户 (JIT) 预配，且进行身份验证后，会在应用程序中自动创建用户。 激活“自动分配许可证”以便为用户分配许可证。 

   ![创建 Azure AD 测试用户](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“LinkedIn Sales Navigator”磁贴时，应会自动登录到设置了 SSO 的 LinkedIn Sales Navigator。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](./tutorial-list.md)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

- [什么是 Azure Active Directory 中的条件访问？](../conditional-access/overview.md)

- [在 Azure AD 中试用 LinkedIn Sales Navigator](https://aad.portal.azure.com/)