---
title: 教程：Azure Active Directory 与 Zscaler Beta 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Zscaler Beta 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 4e8dce970c9e7cfb2aa7887cf1fb3ed6ef9030a1
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735571"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>教程：Azure Active Directory 与 Zscaler Beta 的集成

本教程介绍如何将 Zscaler Beta 与 Azure Active Directory (Azure AD) 集成。
将 Zscaler Beta 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Zscaler Beta。
* 可让用户使用其 Azure AD 帐户自动登录到 Zscaler Beta。 这种访问控制称为单一登录 (SSO)。
* 使用 Azure 门户在一个中心位置管理帐户。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 Zscaler Beta 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 使用单一登录的 Zscaler Beta 订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Zscaler Beta 支持 SP 发起的 SSO。
* Zscaler Beta 支持实时用户预配。

## <a name="adding-zscaler-beta-from-the-gallery"></a>从库中添加 Zscaler Beta

若要配置 Zscaler Beta 与 Azure AD 的集成，需从库中将 Zscaler Beta 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Zscaler Beta” 。
1. 从结果面板中选择“Zscaler Beta”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-zscaler-beta"></a>配置并测试 Zscaler Beta 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Zscaler Beta 的 Azure AD SSO。 若要使 SSO 正常运行，需要在 Azure AD 用户与 Zscaler Beta 中的相关用户之间建立链接关系。

若要配置并测试 Zscaler Beta 的 Azure AD SSO，请执行以下步骤：


1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Zscaler BetaSSO](#configure-zscaler-beta-sso) - 在应用程序端配置单一登录设置。
    1. [创建 Zscaler Beta 测试用户](#create-zscaler-beta-test-user) - 在 Zscaler Beta 中创建 B.Simon 的对应用户，并将其与用户的 Azure AD 表示形式相关联。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的 Zscaler Beta 应用程序集成页上，找到“管理”部分，并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页面上，单击“基本 SAML 配置”旁边的铅笔图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    在“登录 URL”框中，键入用户用来登录 Zscaler Beta 应用程序的 URL。

    > [!NOTE]
    > 此值不是真实值。 请使用实际的“登录 URL”值更新此值。 若要获取此值，请联系 [Zscaler Beta 客户端支持团队](https://www.zscaler.com/company/contact)。

5. Zscaler Beta 应用程序需要特定格式的 SAML 断言。 必须将自定义属性映射添加到 SAML 令牌属性配置。 以下屏幕截图显示了默认属性的列表。 选择“编辑”以打开“用户属性”对话框。

    ![“用户属性”对话框](common/edit-attribute.png)

6. Zscaler Beta 应用程序还要求在 SAML 响应中传回其他几个属性。 在“用户属性”对话框的“用户声明”部分执行以下步骤，以添加 SAML 令牌属性，如下表所示。
    
    | 名称 | 源属性 | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. 选择“添加新声明”打开“管理用户声明”对话框。  

    b. 在“名称”框中，键入该行显示的属性名称。 

    c. 将“命名空间”框留空。

    d. 对于“源”，请选择“属性”。

    e. 在“源属性”列表中，输入该行显示的属性值。

    f. 选择“确定”。

    g. 选择“保存”。

    > [!NOTE]
    > 若要了解如何在 Azure AD 中配置角色，请单击[此处](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)  。

7. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，选择“下载”以下载“证书(Base64)”。 然后将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

8. 在“设置 Zscaler Beta”部分，根据要求复制所需的 URL：

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，你将通过授予 B.Simon 访问 Zscaler Beta 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Zscaler Beta”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果已如上述所示设置角色，可从“选择角色”下拉列表中选择角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-zscaler-beta-sso"></a>配置 Zscaler Beta SSO

1. 若要在 Zscaler Beta 中自动执行配置，需要通过选择“安装扩展”，安装“我的应用安全登录浏览器扩展”。

    ![“我的应用”扩展](common/install-myappssecure-extension.png)

2. 将该扩展添加到浏览器后，选择“设置 Zscaler Beta”会定向到 Zscaler Beta 应用程序。 在此处，请提供管理员凭据以登录到 Zscaler Beta。 浏览器扩展会自动配置应用程序，并自动执行步骤 3 至 6。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 Zscaler Beta，请打开新的 Web 浏览器窗口。 以管理员身份登录到 Zscaler Beta 公司站点，然后执行以下步骤。

4. 转到“管理” > “身份验证” > “身份验证设置”，然后执行以下步骤。  
   
    ![管理](./media/zscaler-beta-tutorial/ic800206.png "管理")

    a. 在“身份验证类型”下选择“SAML”。

    b. 选择“配置 SAML”。

5. 在“编辑 SAML”窗口中执行以下步骤： 
            
    ![管理用户和身份验证](./media/zscaler-beta-tutorial/ic800208.png "管理用户和身份验证")
    
    a. 在“SAML 门户 URL”框中，粘贴从 Azure 门户复制的“登录 URL”。

    b. 在“登录名属性”框中输入 **NameID**。

    c. 在“公共 SSL 证书”框中，选择“上传”以上传从 Azure 门户下载的 Azure SAML 签名证书。

    d. 切换为“启用 SAML 自动预配”。

    e. 若要为 displayName 属性启用 SAML 自动预配，请在“用户显示名称属性”框中输入 **displayName**。

    f. 若要为 memberOf 属性启用 SAML 自动预配，请在“组名称属性”框中输入 **memberOf**。

    g. 若要为 department 属性启用 SAML 自动预配，请在“部门名称属性”框中输入 **department**。

    h. 选择“保存”。 

6. 在“配置用户身份验证”对话框页上执行以下步骤：

    ![“激活”菜单和“激活”按钮](./media/zscaler-beta-tutorial/ic800207.png)

    a. 将鼠标悬停在左下角的“激活”菜单上。

    b. 选择“激活”  。

## <a name="configure-proxy-settings"></a>配置代理设置
若要在 Internet Explorer 中配置代理设置，请执行以下步骤。

1. 启动 **Internet Explorer**。

2. 从“工具”菜单中选择“Internet 选项”，打开“Internet 选项”对话框。 
    
     ![“Internet 选项”对话框](./media/zscaler-beta-tutorial/ic769492.png "Internet 选项")

3. 选择“连接”选项卡。 
  
     ![“连接”选项卡](./media/zscaler-beta-tutorial/ic769493.png "连接")

4. 选择“LAN 设置”打开“局域网(LAN)设置”对话框。

5. 在“代理服务器”部分执行以下步骤： 
   
    ![“代理服务器”部分](./media/zscaler-beta-tutorial/ic769494.png "代理服务器")

    a. 选中“对 LAN 使用代理服务器”复选框。

    b. 在“地址”框中输入 **gateway.Zscaler Beta.net**。

    c. 在“端口”框中输入 **80**。

    d. 选中“对本地地址不使用代理服务器”复选框。

    e. 选择“确定”关闭“局域网(LAN)设置”对话框。

6. 选择“确定”关闭“Internet 选项”对话框。

### <a name="create-zscaler-beta-test-user"></a>创建 Zscaler Beta 测试用户

在本部分，我们将在 Zscaler Beta 中创建用户 Britta Simon。 Zscaler Beta 支持默认启用的 **恰时用户预配**。 在本部分不需要执行任何操作。 如果 Zscaler Beta 中尚不存在用户，则会在身份验证后创建一个新用户。

>[!Note]
>若要手动创建用户，请联系 [Zscaler Beta 支持团队](https://www.zscaler.com/company/contact)。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Zscaler Beta 登录 URL，可以在其中启动登录流。 

* 直接转到 Zscaler Beta 登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 单击“我的应用”中的 Zscaler Beta 磁贴时，会重定向到 Zscaler Beta 登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。


## <a name="next-steps"></a>后续步骤

配置 Zscaler Beta 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。
