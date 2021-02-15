---
title: 创建应用注册
titleSuffix: Azure Digital Twins
description: 请参阅如何创建 Azure AD 应用注册，作为客户端应用的身份验证选项。
author: baanders
ms.author: baanders
ms.date: 10/13/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f252471cd3cd7e3a950bf2cfe324e580da129041
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209553"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins"></a>创建应用注册以用于 Azure 数字孪生

使用 Azure 数字孪生实例时，通常可以通过客户端应用程序（如自定义客户端应用程序或 [ADT 资源管理器](quickstart-adt-explorer.md)等示例）与该实例进行交互。 这些应用程序需要使用 Azure 数字孪生进行身份验证，以便与之进行交互，并且应用可以使用的一些 [身份验证机制](how-to-authenticate-client.md) 涉及 [Azure AD) ](../active-directory/fundamentals/active-directory-whatis.md) **应用注册**的 Azure Active Directory (。

这不是所有身份验证方案所必需的。 但是，如果你使用的是需要注册应用程序的身份验证策略或代码示例（包括 **客户端 id** 和 **租户 ID**），本文将演示如何设置一个。

## <a name="using-azure-ad-app-registrations"></a>使用 Azure AD 应用注册

[Azure Active Directory (Azure AD) ](../active-directory/fundamentals/active-directory-whatis.md) 是 Microsoft 的基于云的标识和访问管理服务。 在 Azure AD 中设置 **应用注册** 是授予客户端应用访问 Azure 数字孪生的一种方法。

通过此应用注册，你可以配置对 [Azure 数字孪生 api](how-to-use-apis-sdks.md)的访问权限。 以后，客户端应用可以使用注册的 **客户端和租户 ID 值**针对应用注册进行身份验证，因此，会将配置的访问权限授予 api。

>[!TIP]
> 你可能想要在每次需要时设置新的应用注册， *或* 仅执行一次注册，建立单个应用注册，将在所有需要它的方案之间共享。

## <a name="create-the-registration"></a>创建注册

首先，导航到 Azure 门户中的 [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) (可以使用此链接或通过门户搜索栏) 找到它。 从 "服务" 菜单中选择 " *应用注册* "，然后选择 " *+ 新注册*"。

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="Azure 门户中 &quot;Azure AD 服务&quot; 页的视图，突出显示 &quot;应用注册&quot; 菜单选项和 &quot;+ 新建注册&quot; 按钮&quot;:::

在下面的 &quot; *注册应用程序* &quot; 页中，填写所需的值：
* **名称**：要与注册关联的 Azure AD 应用程序显示名称
* **受支持的帐户类型**：仅 * (默认目录-单租户) 中选择此组织目录中的帐户 *
* **重定向 URI**： Azure AD 应用程序的 *Azure AD 应用程序答复 URL* 。 为添加 *公共客户端/本机 (移动 & 桌面) * URI `http://localhost` 。

完成后，单击 &quot; *注册* " 按钮。

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="Azure 门户中 &quot;Azure AD 服务&quot; 页的视图，突出显示 &quot;应用注册&quot; 菜单选项和 &quot;+ 新建注册&quot; 按钮&quot;:::

在下面的 &quot; *注册应用程序* &quot; 页中，填写所需的值：
* **名称**：要与注册关联的 Azure AD 应用程序显示名称
* **受支持的帐户类型**：仅 * (默认目录-单租户) 中选择此组织目录中的帐户 *
* **重定向 URI**： Azure AD 应用程序的 *Azure AD 应用程序答复 URL* 。 为添加 *公共客户端/本机 (移动 & 桌面) * URI `http://localhost` 。

完成后，单击 &quot; *注册* ":::

完成设置注册后，门户会将你重定向到其详细信息页面。

## <a name="collect-client-id-and-tenant-id"></a>收集客户端 ID 和租户 ID

接下来，从其详细信息页收集有关应用注册的一些重要值：

:::image type="content" source="media/how-to-create-app-registration/app-important-values.png" alt-text="Azure 门户中 &quot;Azure AD 服务&quot; 页的视图，突出显示 &quot;应用注册&quot; 菜单选项和 &quot;+ 新建注册&quot; 按钮&quot;:::

在下面的 &quot; *注册应用程序* &quot; 页中，填写所需的值：
* **名称**：要与注册关联的 Azure AD 应用程序显示名称
* **受支持的帐户类型**：仅 * (默认目录-单租户) 中选择此组织目录中的帐户 *
* **重定向 URI**： Azure AD 应用程序的 *Azure AD 应用程序答复 URL* 。 为添加 *公共客户端/本机 (移动 & 桌面) * URI `http://localhost` 。

完成后，单击 &quot; *注册* ":::

记下**在页面上显示的**_**应用程序 (客户端) id**_ 和_**目录 (租户) id**_ 。 这些值是客户端应用程序使用此注册来通过 Azure 数字孪生进行身份验证所需的值。

## <a name="provide-azure-digital-twins-api-permission"></a>提供 Azure 数字孪生 API 权限

接下来，将你创建的应用注册配置为具有对 Azure 数字孪生 Api 的基准权限。

从应用注册的门户页中，从菜单中选择 " *API 权限* "。 在 "下列权限" 页上，单击 " *+ 添加权限* " 按钮。

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="Azure 门户中 &quot;Azure AD 服务&quot; 页的视图，突出显示 &quot;应用注册&quot; 菜单选项和 &quot;+ 新建注册&quot; 按钮&quot;:::

在下面的 &quot; *注册应用程序* &quot; 页中，填写所需的值：
* **名称**：要与注册关联的 Azure AD 应用程序显示名称
* **受支持的帐户类型**：仅 * (默认目录-单租户) 中选择此组织目录中的帐户 *
* **重定向 URI**： Azure AD 应用程序的 *Azure AD 应用程序答复 URL* 。 为添加 *公共客户端/本机 (移动 & 桌面) * URI `http://localhost` 。

完成后，单击 &quot; *注册* " _**Azure 数字孪生**_ "，以继续分配 Azure 数字孪生 api 的权限。

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="Azure 门户中 &quot;Azure AD 服务&quot; 页的视图，突出显示 &quot;应用注册&quot; 菜单选项和 &quot;+ 新建注册&quot; 按钮&quot;:::

在下面的 &quot; *注册应用程序* &quot; 页中，填写所需的值：
* **名称**：要与注册关联的 Azure AD 应用程序显示名称
* **受支持的帐户类型**：仅 * (默认目录-单租户) 中选择此组织目录中的帐户 *
* **重定向 URI**： Azure AD 应用程序的 *Azure AD 应用程序答复 URL* 。 为添加 *公共客户端/本机 (移动 & 桌面) * URI `http://localhost` 。

完成后，单击 &quot; *注册* " _**Azure 智能空间服务**_ "。 对于同一组 Api，这是较旧的名称 (请注意， *应用程序 (客户端) ID* 与上面的屏幕截图) 中的相同，并且不会在此步骤之外更改体验。
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="Azure 门户中 &quot;Azure AD 服务&quot; 页的视图，突出显示 &quot;应用注册&quot; 菜单选项和 &quot;+ 新建注册&quot; 按钮&quot;:::

在下面的 &quot; *注册应用程序* &quot; 页中，填写所需的值：
* **名称**：要与注册关联的 Azure AD 应用程序显示名称
* **受支持的帐户类型**：仅 * (默认目录-单租户) 中选择此组织目录中的帐户 *
* **重定向 URI**： Azure AD 应用程序的 *Azure AD 应用程序答复 URL* 。 为添加 *公共客户端/本机 (移动 & 桌面) * URI `http://localhost` 。

完成后，单击 &quot; *注册* " 复选框以授予此应用注册读取器和编写器权限。

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="Azure 门户中 &quot;Azure AD 服务&quot; 页的视图，突出显示 &quot;应用注册&quot; 菜单选项和 &quot;+ 新建注册&quot; 按钮&quot;:::

在下面的 &quot; *注册应用程序* &quot; 页中，填写所需的值：
* **名称**：要与注册关联的 Azure AD 应用程序显示名称
* **受支持的帐户类型**：仅 * (默认目录-单租户) 中选择此组织目录中的帐户 *
* **重定向 URI**： Azure AD 应用程序的 *Azure AD 应用程序答复 URL* 。 为添加 *公共客户端/本机 (移动 & 桌面) * URI `http://localhost` 。

完成后，单击 &quot; *注册* ":::

完成后点击 *添加权限* 。

### <a name="verify-success"></a>验证是否成功

在 " *API 权限* " 页上，验证现在是否存在用于反映读/写权限的 Azure 数字孪生条目：

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="Azure 门户中 &quot;Azure AD 服务&quot; 页的视图，突出显示 &quot;应用注册&quot; 菜单选项和 &quot;+ 新建注册&quot; 按钮&quot;:::

在下面的 &quot; *注册应用程序* &quot; 页中，填写所需的值：
* **名称**：要与注册关联的 Azure AD 应用程序显示名称
* **受支持的帐户类型**：仅 * (默认目录-单租户) 中选择此组织目录中的帐户 *
* **重定向 URI**： Azure AD 应用程序的 *Azure AD 应用程序答复 URL* 。 为添加 *公共客户端/本机 (移动 & 桌面) * URI `http://localhost` 。

完成后，单击 &quot; *注册* " *清单* "，以查看应用注册的清单代码。 滚动到代码窗口的底部，在下查找这些字段 `requiredResourceAccess` 。 值应与以下屏幕截图中的值匹配：

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="Azure 门户中 &quot;Azure AD 服务&quot; 页的视图，突出显示 &quot;应用注册&quot; 菜单选项和 &quot;+ 新建注册&quot; 按钮&quot;:::

在下面的 &quot; *注册应用程序* &quot; 页中，填写所需的值：
* **名称**：要与注册关联的 Azure AD 应用程序显示名称
* **受支持的帐户类型**：仅 * (默认目录-单租户) 中选择此组织目录中的帐户 *
* **重定向 URI**： Azure AD 应用程序的 *Azure AD 应用程序答复 URL* 。 为添加 *公共客户端/本机 (移动 & 桌面) * URI `http://localhost` 。

完成后，单击 &quot; *注册* ":::

如果缺少这些值，请尝试 [添加 API 权限部分](#provide-azure-digital-twins-api-permission)中的步骤。

## <a name="other-possible-steps-for-your-organization"></a>组织的其他可能步骤

你的组织可能需要订阅所有者/管理员执行的其他操作才能成功设置应用注册。 根据组织的特定设置，所需的步骤可能会有所不同。

下面是订阅所有者/管理员可能需要执行的一些常见潜在活动。 这些操作和其他操作可以从 Azure 门户的 " [*Azure AD 应用注册*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) " 页中执行。
* 向管理员授予对应用注册的许可。 你的组织可能需要对订阅中的所有应用注册 Azure AD 全局启用 " *管理员许可* "。 如果是这样，所有者/管理员需要在应用注册的 " *API 权限* " 页上为你的公司选择此按钮，以使应用注册有效：

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="Azure 门户中 &quot;Azure AD 服务&quot; 页的视图，突出显示 &quot;应用注册&quot; 菜单选项和 &quot;+ 新建注册&quot; 按钮&quot;:::

在下面的 &quot; *注册应用程序* &quot; 页中，填写所需的值：
* **名称**：要与注册关联的 Azure AD 应用程序显示名称
* **受支持的帐户类型**：仅 * (默认目录-单租户) 中选择此组织目录中的帐户 *
* **重定向 URI**： Azure AD 应用程序的 *Azure AD 应用程序答复 URL* 。 为添加 *公共客户端/本机 (移动 & 桌面) * URI `http://localhost` 。

完成后，单击 &quot; *注册* ":::
  - 如果已成功授予许可，则 Azure 数字孪生的条目应显示已_授予给**公司 (** _的*状态*值) 
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="Azure 门户中 &quot;Azure AD 服务&quot; 页的视图，突出显示 &quot;应用注册&quot; 菜单选项和 &quot;+ 新建注册&quot; 按钮&quot;:::

在下面的 &quot; *注册应用程序* &quot; 页中，填写所需的值：
* **名称**：要与注册关联的 Azure AD 应用程序显示名称
* **受支持的帐户类型**：仅 * (默认目录-单租户) 中选择此组织目录中的帐户 *
* **重定向 URI**： Azure AD 应用程序的 *Azure AD 应用程序答复 URL* 。 为添加 *公共客户端/本机 (移动 & 桌面) * URI `http://localhost` 。

完成后，单击 &quot; *注册* ":::
* 激活公共客户端访问
* 为 web 和桌面访问设置特定回复 Url
* 允许隐式 OAuth2 身份验证流

有关应用注册及其不同安装选项的详细信息，请参阅 [*将应用程序注册到 Microsoft 标识平台*](/graph/auth-register-app-v2)。

## <a name="next-steps"></a>后续步骤

本文介绍如何设置 Azure AD 应用注册，该注册可用于通过 Azure 数字孪生 Api 对客户端应用程序进行身份验证。

接下来，请阅读身份验证机制，其中包括使用应用注册的身份验证机制，其他不使用的机制：
* [*操作说明：编写应用身份验证代码*](how-to-authenticate-client.md)