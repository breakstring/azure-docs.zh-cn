---
title: 设置 Azure Red Hat OpenShift 开发环境
description: 下面是使用 Microsoft Azure Red Hat OpenShift 的先决条件。
keywords: red hat openshift 安装程序设置
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
ms.custom: devx-track-azurecli
ms.openlocfilehash: c189c0902e694dc49d81a48433e3269e9f2a438c
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216872"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>设置 Azure Red Hat OpenShift 开发环境

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 将在年6月 30 2022 日停用。 支持创建新的 Azure Red Hat OpenShift 3.11 群集持续到30年 11 2020 月30日。 停用后，剩余的 Azure Red Hat OpenShift 3.11 群集将关闭，以防出现安全漏洞。
> 
> 按照本指南 [创建 Azure Red Hat OpenShift 4 群集](tutorial-create-cluster.md)。
> 如果有特定问题， [请](mailto:arofeedback@microsoft.com)联系我们。

若要生成并运行 Microsoft Azure Red Hat OpenShift 应用程序，需要：

* 安装版本 2.0.65 (或更高版本的 Azure CLI () 或使用 Azure Cloud Shell) 。
* 注册 `AROGA` 功能和关联的资源提供程序。
* ) 租户创建 Azure Active Directory (Azure AD。
* 创建 Azure AD 应用程序对象。
* 创建 Azure AD 用户。

以下说明将指导你完成所有这些先决条件。

## <a name="install-the-azure-cli"></a>安装 Azure CLI

Azure Red Hat OpenShift 要求 Azure CLI 2.0.65 或更高版本。 如果你已经安装了 Azure CLI，则可以通过运行来检查你的版本：

```azurecli
az --version
```

例如，输出的第一行将包含 CLI 版本 `azure-cli (2.0.65)` 。

如果需要全新安装或升级，请参阅以下 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 说明。

或者，您可以使用 [Azure Cloud Shell](../cloud-shell/overview.md)。 使用 Azure Cloud Shell 时，请确保选择 **Bash** 环境（如果打算按照 [创建和管理 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md) 教程系列中的说明进行操作）。

## <a name="register-providers-and-features"></a>注册提供程序和功能

在 `Microsoft.ContainerService AROGA` `Microsoft.Solutions` `Microsoft.Compute` `Microsoft.Storage` `Microsoft.KeyVault` `Microsoft.Network` 部署第一个 Azure Red Hat OpenShift 群集之前，必须将功能、、、和提供程序手动注册到你的订阅。

若要手动注册这些提供程序和功能，请使用 Bash shell 中的以下说明（如果已安装 CLI），或从你的 Azure 门户中的 Azure Cloud Shell (Bash) 会话使用以下说明：

1. 如果有多个 Azure 订阅，请指定相关订阅 ID：

    ```azurecli
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. 注册 ContainerService AROGA 功能：

    ```azurecli
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. 注册 Microsoft. 存储提供程序：

    ```azurecli
    az provider register -n Microsoft.Storage --wait
    ```
    
1. 注册 Microsoft. 计算提供程序：

    ```azurecli
    az provider register -n Microsoft.Compute --wait
    ```

1. 注册 Microsoft 解决方案提供商：

    ```azurecli
    az provider register -n Microsoft.Solutions --wait
    ```

1. 注册 Microsoft 网络提供程序：

    ```azurecli
    az provider register -n Microsoft.Network --wait
    ```

1. 注册 KeyVault 提供程序：

    ```azurecli
    az provider register -n Microsoft.KeyVault --wait
    ```

1. 刷新 ContainerService 资源提供程序的注册：

    ```azurecli
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>创建 Azure Active Directory (Azure AD) 租户

Azure Red Hat OpenShift 服务需要一个关联的 Azure Active Directory (Azure AD 表示你的组织及其与 Microsoft 的关系的) 租户。 你的 Azure AD 租户使你能够注册、构建和管理应用，并使用其他 Azure 服务。

如果没有 Azure AD 用作 Azure Red Hat OpenShift 群集的租户，或者想要创建用于测试的租户，请按照为 [Azure Red Hat OpenShift 群集创建 Azure AD 租户](howto-create-tenant.md) 中的说明，然后再继续本指南。

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>创建 Azure AD 用户、安全组和应用程序对象

Azure Red Hat OpenShift 需要权限才能在群集上执行任务，如配置存储。 这些权限通过 [服务主体](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)来表示。 还需要创建新的 Active Directory 用户，以便测试在 Azure Red Hat OpenShift 群集上运行的应用。

按照 [创建 Azure AD 应用对象和用户](howto-aad-app-configuration.md) 中的说明，创建服务主体，为你的应用生成客户端密钥和身份验证回叫 URL，并创建新的 Azure AD 安全组和用户来访问该群集。

## <a name="next-steps"></a>后续步骤

你现在已准备好使用 Azure Red Hat OpenShift！

尝试教程：
> [!div class="nextstepaction"]
> [创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)

[azure-cli-install]: /cli/azure/install-azure-cli