---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Amazon Web Services (AWS) 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Amazon Web Services (AWS) 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/08/2020
ms.author: jeedes
ms.openlocfilehash: 286dc20ba70c78f8248f611abd75e0acc303c068
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736182"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amazon-web-services-aws"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Amazon Web Services (AWS) 集成

本教程介绍如何将 Amazon Web Services (AWS) 与 Azure Active Directory (Azure AD) 集成。 将 Amazon Web Services (AWS) 与 Azure AD 集成时，可以执行以下操作：

* 在 Azure AD 中控制谁有权访问 Amazon Web Services (AWS)。
* 让用户可以使用其 Azure AD 帐户自动登录到 Amazon Web Services (AWS)。
* 在一个中心位置（Azure 门户）管理帐户。

> [!Note]
> Azure AD 不支持与 AWS SSO 的单一登录集成，后者与 AWS 是不同的产品。 尽管 AWS 在[这里](https://docs.aws.amazon.com/singlesignon/latest/userguide/azure-ad-idp.html)提到了这一点，但 Azure AD 建议客户改用 AWS IAM 集成，这样便可以在单个帐户上使用条件访问策略来实现更好的安全控制，并可以更好地治理这些应用程序。

![Azure AD 和 AWS 的关系示意图](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

可以为多个实例配置多个标识符。 例如：

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

借助这些值，Azure AD 将删除 **#** 值并发送正确的值 `https://signin.aws.amazon.com/saml` 作为 SAML 令牌中的受众 URL。

我们建议使用这种方法，原因如下：

- 每个应用程序将为你提供一个唯一的 X509 证书。 AWS 应用实例的每个实例都会有不同的证书到期日期，该日期可基于单个 AWS 帐户进行管理。 在这种情况下，整个证书滚动更新会更容易。

- 可以在 Azure AD 中使用 AWS 应用启用“用户设置”，我们的服务随后就会从该 AWS 帐户提取所有角色。 无需在应用中手动添加或更新 AWS 角色。

- 可单独为应用分配应用所有者。 应用所有者可以直接在 Azure AD 中管理应用。

> [!Note]
> 请确保仅使用库应用程序。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 AWS 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Amazon Web Services (AWS) 支持 **SP 和 IDP** 发起的 SSO

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>从库中添加 Amazon Web Services (AWS)

要配置 Amazon Web Services (AWS) 与 Azure AD 的集成，需要从库将 Amazon Web Services (AWS) 添加到托管 SaaS 应用列表。

1. 使用工作帐户、学校帐户或者个人 Microsoft 帐户登录到 Azure 门户。
1. 在 Azure 门户中，搜索并选择“Azure Active Directory”。
1. 在 Azure Active Directory 概述菜单中，选择“企业应用程序” > “所有应用程序” 。
1. 选择“新建应用程序”以添加应用程序。
1. 在“从库中添加”部分的搜索框中，键入 **Amazon Web Services (AWS)** 。
1. 在结果窗格中，选择“Amazon Web Services (AWS)”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-amazon-web-services-aws"></a>配置和测试 Amazon Web Services (AWS) 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置和测试 Amazon Web Services (AWS) 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Amazon Web Services (AWS) 中的相关用户之间建立链接关系。

若要配置和测试 Amazon Web Services (AWS) 的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Amazon Web Services (AWS) SSO](#configure-amazon-web-services-aws-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Amazon Web Services (AWS) 测试用户](#create-amazon-web-services-aws-test-user)** - 在 Amazon Web Services (AWS) 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
    1. **[如何在 Amazon Web Services (AWS) 中配置角色预配](#how-to-configure-role-provisioning-in-amazon-web-services-aws)**
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“Amazon Web Services (AWS)”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，使用相同默认值更新“标识符(实体 ID)”和“回复 URL”：`https://signin.aws.amazon.com/saml`。 必须选择“保存”以保存配置更改。

1. 在配置多个实例时，请提供标识符值。 从第二个实例开始，请使用以下格式，并包含一个 **#** 符号来指定唯一的 SPN 值。

    `https://signin.aws.amazon.com/saml#2`

1. AWS 应用程序需要特定格式的 SAML 断言，这需要向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除了上述属性，AWS 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。
    
    | 名称  | 源属性  | 命名空间 |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | 角色 | user.assignedroles |  `https://aws.amazon.com/SAML/Attributes` |
    | SessionDuration | “提供介于 900 秒（15 分钟）到 43200 秒（12 小时）之间的值” |  `https://aws.amazon.com/SAML/Attributes` |

    > [!NOTE]
    > AWS 需要分配给应用程序的用户的角色。 请在 Azure AD 中设置这些角色，以便可为用户分配相应的角色。 若要了解如何在 Azure AD 中配置角色，请参阅[此文](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”（步骤 3）对话框中，选择“添加证书”  。

    ![创建新的 SAML 证书](common/add-saml-certificate.png)

1. 生成新的 SAML 签名证书，然后选择“新建证书”。 输入证书通知的电子邮件地址。
   
    ![新的 SAML 证书](common/new-saml-certificate.png) 

1. 在“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上。  

    ![证书下载链接](./media/amazon-web-service-tutorial/certificate.png)

1. 在“设置 Amazon Web Services (AWS)”部分中，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户中，搜索并选择“Azure Active Directory”。
1. 在 Azure Active Directory 概述菜单中，选择“用户” > “所有用户” 。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过向 B.Simon 授予对 Amazon Web Services (AWS) 的访问权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Amazon Web Services (AWS)”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-amazon-web-services-aws-sso"></a>配置 Amazon Web Services (AWS) SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 AWS 公司站点。

2. 选择“AWS 主页”。

    ![AWS 公司站点的屏幕截图，其中突出显示了“AWS 主页”图标][11]

3. 选择“标识和访问管理”。

    ![“AWS 服务”页的屏幕截图，其中突出显示了 IAM][12]

4. 选择“标识提供者” > “创建提供者”。 

    ![IAM 页的屏幕截图，其中突出显示了“标识提供者”和“创建提供者”][13]

5. 在“配置提供者”页上执行以下步骤：

    ![“配置提供者”的屏幕截图][14]

    a. 对于“提供者类型”，请选择“SAML”。

    b. 在“提供者名称”中键入提供者名称（例如：*WAAD*）。

    c. 若要上传从 Azure 门户下载的 **元数据文件**，请选择“选择文件”。

    d. 选择“下一步”。

6. 在“验证提供者信息”页上，选择“创建” 。

    ![“验证提供程序信息”的屏幕截图，其中突出显示了“创建”][15]

7. 选择“角色” > “创建角色”。 

    ![“角色”页的屏幕截图][16]

8. 在“创建角色”页中，执行以下步骤：  

    ![“创建角色”页的屏幕截图][19]

    a. 在“选择可信实体的类型”下选择“SAML 2.0 联合身份验证”。 

    b. 在“选择 SAML 2.0 提供程序”下，选择前面创建的“SAML 提供程序”（例如： *WAAD*）。

    c. 选择“允许以编程方式和通过 AWS 管理控制台进行访问”。
  
    d. 在完成时选择“下一步:权限”。

9. 在“附加权限策略”对话框中，根据组织的要求附加相应的策略。 然后选择“下一步:查看”。  

    ![“附加权限策略”对话框的屏幕截图][33]

10. 在“复查”对话框中执行以下步骤：

    ![“复查”对话框的屏幕截图][34]

    a. 在“角色名称”中输入角色名称。

    b. 在“角色说明”中输入说明。

    c. 选择“创建角色”。

    d. 创建所需数量的角色，并将其映射到标识提供者。

11. 在 Azure AD 用户预配中使用 AWS 服务帐户凭据从 AWS 帐户提取角色。 为此，请打开 AWS 控制台主页。

12. 选择“服务”。 在“安全性、标识与合规性”下，选择“IAM”。 

    ![AWS 控制台主页的屏幕截图，其中突出显示了“服务”和“IAM”](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. 在“IAM”部分选择“策略”。

    ![“IAM”部分的屏幕截图，其中突出显示了“策略”](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. 通过选择“创建策略”来创建用于在 Azure AD 用户预配中从 AWS 帐户提取角色的新策略。

    ![“创建角色”页的屏幕截图，其中突出显示了“创建策略”](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. 创建自己的策略，以从 AWS 帐户提取所有角色。

    ![“创建策略”页的屏幕截图，其中突出显示了“JSON”](./media/amazon-web-service-tutorial/policy1.png)

    a. 在“创建策略”中，选择“JSON”选项卡。 

    b. 在策略文档中，添加以下 JSON：

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. 选择“检查策略”以验证策略。

    ![“创建策略”页的屏幕截图](./media/amazon-web-service-tutorial/policy5.png)

16. 定义新策略。

    ![“创建策略”页的屏幕截图，其中突出显示了“名称”和“说明”字段](./media/amazon-web-service-tutorial/policy2.png)

    a. 对于“名称”，请输入 **AzureAD_SSOUserRole_Policy**。

    b. 对于“说明”，请输入“此策略允许从 AWS 帐户提取角色”。 

    c. 选择“创建策略”。

17. 在 AWS IAM 服务中创建新的用户帐户。

    a. 在 AWS IAM 控制台中选择“用户”。

    ![AWS IAM 控制台的屏幕截图，其中突出显示了“用户”](./media/amazon-web-service-tutorial/policy3.png)

    b. 若要创建新用户，请选择“添加用户”。

    ![“添加用户”按钮的屏幕截图](./media/amazon-web-service-tutorial/policy4.png)

    c. 在“添加用户”部分：

    ![“添加用户”页的屏幕截图，其中突出显示了“用户名”和“访问类型”](./media/amazon-web-service-tutorial/adduser1.png)

    * 输入 **AzureADRoleManager** 作为用户名。

    * 对于访问类型，请选择“编程访问”。 这样，用户就可以调用 API 并从 AWS 帐户中提取角色。

    * 选择“下一步: 权限”。

18. 为此用户创建新策略。

    ![显示可在其中为用户创建策略的“添加用户”页的屏幕截图。](./media/amazon-web-service-tutorial/adduser2.png)

    a. 选择“直接附加现有策略”。

    b. 在筛选器部分中搜索新创建的策略 **AzureAD_SSOUserRole_Policy**。

    c. 选择策略，然后选择“下一步:查看”。

19. 检查附加用户的策略。

    ![“添加用户”页的屏幕截图，其中突出显示了“创建用户”](./media/amazon-web-service-tutorial/adduser3.png)

    a. 审阅映射到该用户的用户名、访问类型和策略。

    b. 选择“创建用户”。

20. 下载用户的用户凭据。

    ![显示具有获取用户凭据的“下载 csv 文件”按钮的“添加用户”页的屏幕截图。](./media/amazon-web-service-tutorial/adduser4.png)

    a. 复制用户的“访问密钥 ID”和“密码访问密钥”。

    b. 在 Azure AD 用户预配部分输入这些凭据，以便从 AWS 控制台提取角色。

    c. 选择“关闭”。

### <a name="how-to-configure-role-provisioning-in-amazon-web-services-aws"></a>如何在 Amazon Web Services (AWS) 中配置角色预配

1. 在 Azure AD 管理门户上的 AWS 应用中，转到“预配”。

    ![AWS 应用的屏幕截图，其中突出显示了“预配”](./media/amazon-web-service-tutorial/provisioning.png)

2. 分别在“客户端机密”和“机密令牌”字段中输入访问密钥和机密。 

    ![“管理凭据”对话框的屏幕截图](./media/amazon-web-service-tutorial/provisioning1.png)

    a. 在“客户端密码”字段中输入 AWS 用户访问密钥。

    b. 在“密码令牌”字段中输入 AWS 用户密码。

    c. 选择“测试连接”。

    d. 选择“保存”以保存设置。

3. 在“设置”部分，为“预配状态”选择“打开”。   再选择“保存”。

    ![“设置”部分的屏幕截图，其中突出显示了“打开”](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> 预配服务只会将 AWS 中的角色导入到 Azure AD。 该服务不会将 Azure AD 中的用户和组预配到 AWS。

> [!NOTE]
> 保存预配凭据之后，必须等待初始同步周期完成运行。 同步通常需要约 40 分钟才能完成。 你可以在“预配”页底部的“当前状态”下查看状态。

### <a name="create-amazon-web-services-aws-test-user"></a>创建 Amazon Web Services (AWS) 测试用户

本部分的目标是在 Amazon Web Services (AWS) 中创建一个名为 B.Simon 的用户。 Amazon Web Services (AWS) 不需要在其系统中创建用户进行 SSO，因此，你无需在此处执行任何操作。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

#### <a name="sp-initiated"></a>SP 启动的：

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 Amazon Web Services (AWS) 登录 URL，可以从那里启动登录流。  

* 直接转到 Amazon Web Services (AWS) 登录 URL，并从那里启动登录流。

#### <a name="idp-initiated"></a>IDP 启动的：

* 在 Azure 门户中单击“测试此应用程序”后，你应该会自动登录到为其设置了 SSO 的 Amazon Web Services (AWS) 

还可以使用 Microsoft 访问面板在任何模式下测试此应用程序。 在点击访问面板中的 Amazon Web Services (AWS) 磁贴时，如果是在 SP 模式下配置的，会重定向到应用程序登录页来启动登录流；如果是在 IDP 模式下配置的，则应会自动登录到为其设置了 SSO 的 Amazon Web Services (AWS)。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md)（访问面板简介）。


## <a name="known-issues"></a>已知问题

 * 在“预配”部分，“映射”子部分会显示“正在加载...”消息，但从不显示属性映射。  目前支持的唯一预配工作流是将角色从 AWS 导入到 Azure AD 中，以便在用户或组分配过程中对其进行选择。 上述属性映射是预先确定的，不可配置。

 * “预配”部分只支持一次为一个 AWS 租户输入一组凭据。 所有导入的角色都写入到 AWS 租户的 Azure AD [`servicePrincipal` 对象](/graph/api/resources/serviceprincipal?view=graph-rest-beta)的 `appRoles` 属性。

   可将多个 AWS 租户（由 `servicePrincipals` 表示）从库添加到 Azure AD 进行预配。 但存在一个已知的问题：无法自动将所有导入的角色从用于预配的多个 AWS `servicePrincipals` 写入到用于 SSO 的单个 `servicePrincipal` 中。

   若要解决此问题，可以使用 [Microsoft Graph API](/graph/api/resources/serviceprincipal?view=graph-rest-beta) 来提取所有已导入每个 AWS `servicePrincipal`（已在其中配置预配）中的 `appRoles`。 然后，可将这些角色字符串添加到 AWS `servicePrincipal`（已在其中配置 SSO）。

* 角色必须满足以下要求才有资格从 AWS 导入 Azure AD：

  * 角色必须仅有一个在 AWS 中定义的 saml-provider
  * 角色的 ARN（Amazon 资源名称）和关联的 saml-provider 的 ARN 的组合长度必须小于 240 个字符。

## <a name="change-log"></a>更改日志

* 2020/01/12 - 角色长度限制从 119 个字符增加到 239 个字符。 

## <a name="next-steps"></a>后续步骤

配置 Amazon Web Services (AWS) 后，可强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制扩展自条件访问。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)


[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
