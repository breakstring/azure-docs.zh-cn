---
title: 精细的基于角色的访问 Azure HDInsight 群集配置
description: 了解在迁移到 HDInsight 群集配置的基于角色的细化访问权限时，需要进行哪些更改。
author: tylerfox
ms.author: tyfox
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: 08be42f4f0801fc700b3e0fc645c1bbc1b747e91
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944085"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>迁移到群集配置的基于角色的细化访问权限

我们正在引入一些重要更改，以支持使用更细化的基于角色的访问来获取敏感信息。 在实施这些更改的过程中，如果你使用的是某个 [受影响的实体/方案](#am-i-affected-by-these-changes)，则可能需要 **在 2019 年 9 月 3 日之前** 采取某种措施。

## <a name="what-is-changing"></a>有什么变化？

以前，可以通过 HDInsight API 获得机密，这些用户拥有所有者、参与者或读取者 [Azure 角色](../role-based-access-control/rbac-and-directory-admin-roles.md)，因为有 `*/read` 权访问权限。 机密定义为值，可用于获取比用户角色允许的访问权限更高的权限。 这些值包括群集网关 HTTP 凭据、存储帐户密钥和数据库凭据等值。

从 2019 年 9 月 3 日开始，访问这些机密需要 `Microsoft.HDInsight/clusters/configurations/action` 权限，这意味着这些机密不再可供具有“读取者”角色的用户访问。 拥有此权限的角色为“参与者”、“所有者”和新的“HDInsight 群集操作员”角色（下面将详细说明）。

另外，我们正在引入新的 [HDInisght 群集操作员](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator)角色，无需向此角色授予“参与者”或“所有者”的管理权限，即可让他们检索机密。 总结：

| 角色                                  | 以前                                                                                       | 今后       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| 读取器                                | - 读取访问权限，包括机密。                                                                   | - 读取访问权限，**不** 包括机密 |           |   |   |
| HDInsight 群集操作员<br>（新角色） | 空值                                                                                              | - 读/写访问权限，包括机密         |   |   |
| 参与者                           | - 读/写访问权限，包括机密。<br>- 创建和管理所有类型的 Azure 资源。<br>- 执行脚本操作。     | 没有变化 |
| 所有者                                 | - 读/写访问权限，包括机密。<br>- 对所有资源的完全访问权限<br>- 将访问权限委托给其他人。<br>- 执行脚本操作。 | 没有变化 |

了解如何向用户添加 HDInsight 群集操作员角色分配，以授予其对群集机密的读/写访问权限的信息，请参阅以下部分[将 HDInsight 群集操作员角色分配添加到用户](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)。

## <a name="am-i-affected-by-these-changes"></a>我是否受此更改的影响？

以下实体和方案将受到影响：

- [API](#api)：使用 `/configurations` 或 `/configurations/{configurationName}` 终结点的用户。
- [Azure HDInsight Tools for Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) 1.1.1 或更低版本。
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) 3.20.0 或更低版本。
- [用于 Visual Studio 的 Azure Data Lake 和流分析工具](#azure-data-lake-and-stream-analytics-tools-for-visual-studio)，低于版本 2.3.9000.1。
- [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse) 3.15.0 或更低版本。
- [SDK for .NET](#sdk-for-net)
    - [版本 1.x 或 2.x](#versions-1x-and-2x)：从 ConfigurationsOperationsExtensions 类使用 `GetClusterConfigurations`、`GetConnectivitySettings`、`ConfigureHttpSettings`、`EnableHttp` 或 `DisableHttp` 方法的用户。
    - [版本 3.x 和以上](#versions-3x-and-up)：从 `ConfigurationsOperationsExtensions` 类使用 `Get`、`Update`、`EnableHttp` 或 `DisableHttp` 方法的用户。
- [SDK for Python](#sdk-for-python)：从 `ConfigurationsOperations` 类使用 `get` 或 `update` 方法的用户。
- [SDK for Java](#sdk-for-java)：从 `ConfigurationsInner` 类使用 `update` 或 `get` 方法的用户。
- [SDK for Go](#sdk-for-go)：从 `ConfigurationsClient` 构造使用 `Get` 或 `Update` 方法的用户。
- [Az.HDInsight PowerShell](#azhdinsight-powershell)，低于版本 2.0.0。
请参阅以下部分（或使用上面的链接）了解适用于你的方案的迁移步骤。

### <a name="api"></a>API

以下 API 将会更改或弃用：

- [**GET /configurations/{configurationName}**](/rest/api/hdinsight/hdinsight-cluster#get-configuration)（已删除敏感信息）
    - 以前用于获取单个配置类型（包括机密）。
    - 从 2019 年 9 月 3 日开始，此 API 调用现在会返回省略机密的单个配置类型。 若要获取所有配置（包括机密），请使用新的 POST /configurations 调用。 如果只要获取网关设置，请使用新的 POST /getGatewaySettings 调用。
- [**GET /configurations**](/rest/api/hdinsight/hdinsight-cluster#get-configuration)（已弃用）
    - 以前用于获取所有配置（包括机密）
    - 从 2019 年 9 月 3 日开始，此 API 调用将弃用且不再受支持。 今后若要获取所有配置，请使用新的 POST /configurations 调用。 若要获取省略敏感参数的配置，请使用 GET /configurations/{configurationName} 调用。
- [**POST /configurations/{configurationName}**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)（已弃用）
    - 以前用于更新网关凭据。
    - 从 2019 年 9 月 3 日开始，此 API 调用将弃用且不再受支持。 请改用新的 POST /updateGatewaySettings。

已添加以下替换用的 API：</span>

- [**POST /configurations**](/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - 使用此 API 可以获取所有配置（包括机密）。
- [**POST /getGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - 使用此 API 可以获取网关设置。
- [**POST /updateGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - 使用此 API 可以更新网关设置（用户名和/或密码）。

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tools for Visual Studio Code

如果使用版本 1.1.1 或更低版本，请更新到[最新版本的 Azure HDInsight Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false)，以避免中断。

### <a name="azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ

如果使用版本 3.20.0 或更低版本，请更新到[最新版本的 Azure Toolkit for IntelliJ 插件](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij)，以避免中断。

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>用于 Visual Studio 的 Azure Data Lake 和流分析工具

更新到 2.3.9000.1 或更高版本的[用于 Visual Studio 的 Azure Data Lake 和流分析工具](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview)可以避免中断。  如需更新方面的帮助，请参阅文档：[更新用于 Visual Studio 的 Data Lake 工具](./hadoop/apache-hadoop-visual-studio-tools-get-started.md#update-data-lake-tools-for-visual-studio)。

### <a name="azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse

如果使用 3.15.0 或更低版本，请更新到[最新版本的 Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse)，以避免中断。

### <a name="sdk-for-net"></a>用于 .NET 的 SDK

#### <a name="versions-1x-and-2x"></a>版本 1.x 和 2.x

请更新到 HDInsight SDK for .NET [版本 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0)。 如果使用受这些更改影响的方法，则可能需要对代码进行少量的修改：

- `ClusterOperationsExtensions.GetClusterConfigurations` 将 **不再返回敏感参数**，例如存储密钥（核心站点）或 HTTP 凭据（网关）。
    - 今后若要检索所有配置（包括敏感参数），请使用 `ClusterOperationsExtensions.ListConfigurations`。  请注意，具有“读取者”角色的用户将无法使用此方法。 这样便可以精细控制哪些用户可以访问群集的敏感信息。
    - 如果只要检索 HTTP 网关凭据，请使用 `ClusterOperationsExtensions.GetGatewaySettings`。

- `ClusterOperationsExtensions.GetConnectivitySettings` 现已弃用，已由 `ClusterOperationsExtensions.GetGatewaySettings` 取代。

- `ClusterOperationsExtensions.ConfigureHttpSettings` 现已弃用，已由 `ClusterOperationsExtensions.UpdateGatewaySettings` 取代。

- `ConfigurationsOperationsExtensions.EnableHttp` 和 `DisableHttp` 现已弃用。 现在始终会启用 HTTP，因此不再需要这些方法。

#### <a name="versions-3x-and-up"></a>版本 3.x 和以上

请更新到 HDInsight SDK for .NET [版本 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) 或更高版本。 如果使用受这些更改影响的方法，则可能需要对代码进行少量的修改：

- [`ConfigurationOperationsExtensions.Get`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet&preserve-view=true) 将 **不再返回敏感参数**，例如存储密钥（核心站点）或 HTTP 凭据（网关）。
    - 今后若要检索所有配置（包括敏感参数），请使用 [`ConfigurationOperationsExtensions.List`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet&preserve-view=true)。  请注意，具有“读取者”角色的用户将无法使用此方法。 这样便可以精细控制哪些用户可以访问群集的敏感信息。 
    - 如果只要检索 HTTP 网关凭据，请使用 [`ClusterOperationsExtensions.GetGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet&preserve-view=true)。 
- [`ConfigurationsOperationsExtensions.Update`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet&preserve-view=true) 现已弃用，已由 [`ClusterOperationsExtensions.UpdateGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet&preserve-view=true) 取代。 
- [`ConfigurationsOperationsExtensions.EnableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet&preserve-view=true) 和 [`DisableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet&preserve-view=true) 现已弃用。 现在始终会启用 HTTP，因此不再需要这些方法。

### <a name="sdk-for-python"></a>用于 Python 的 SDK

请更新到 HDInsight SDK for Python [版本 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) 或更高版本。 如果使用受这些更改影响的方法，则可能需要对代码进行少量的修改：

- [`ConfigurationsOperations.get`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) 将 **不再返回敏感参数**，例如存储密钥（核心站点）或 HTTP 凭据（网关）。
    - 今后若要检索所有配置（包括敏感参数），请使用 [`ConfigurationsOperations.list`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)。  请注意，具有“读取者”角色的用户将无法使用此方法。 这样便可以精细控制哪些用户可以访问群集的敏感信息。 
    - 如果只要检索 HTTP 网关凭据，请使用 [`ClusterOperations.get_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)。
- [`ConfigurationsOperations.update`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) 现已弃用，已由 [`ClusterOperations.update_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) 取代。

### <a name="sdk-for-java"></a>SDK For Java

请更新到 HDInsight SDK for Java [版本 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) 或更高版本。 如果使用受这些更改影响的方法，则可能需要对代码进行少量的修改：

- `ConfigurationsInner.get` 将 **不再返回敏感参数**，例如存储密钥（核心站点）或 HTTP 凭据（网关）。
- `ConfigurationsInner.update` 现已弃用。

### <a name="sdk-for-go"></a>SDK For Go

请更新到 HDInsight SDK for Go [版本 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight) 或更高版本。 如果使用受这些更改影响的方法，则可能需要对代码进行少量的修改：

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Get) 将 **不再返回敏感参数**，例如存储密钥（核心站点）或 HTTP 凭据（网关）。
    - 今后若要检索所有配置（包括敏感参数），请使用 [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.List)。  请注意，具有“读取者”角色的用户将无法使用此方法。 这样便可以精细控制哪些用户可以访问群集的敏感信息。 
    - 如果只要检索 HTTP 网关凭据，请使用 [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.GetGatewaySettings)。
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Update) 现已弃用，已由 [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings) 取代。

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
更新到 [Az PowerShell 版本 2.0.0](https://www.powershellgallery.com/packages/Az) 或更高版本以避免中断。  如果使用受这些更改影响的方法，则可能需要对代码进行少量的修改。
- `Grant-AzHDInsightHttpServicesAccess` 现已弃用，已由新的 `Set-AzHDInsightGatewayCredential` cmdlet 取代。
- `Get-AzHDInsightJobOutput` 在更新后支持对存储密钥进行细化的基于角色的访问。
    - 具有 HDInsight 群集的“操作员”、“参与者”或“所有者”角色的用户将不受影响。
    - 只具有“读者”角色的用户将需要显式指定 `DefaultStorageAccountKey` 参数。
- `Revoke-AzHDInsightHttpServicesAccess` 现已弃用。 现在始终会启用 HTTP，因此不再需要此 cmdlet。
 如需更多详细信息，请参阅 [az.HDInsight 迁移指南](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight)。

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>将 HDInsight 群集操作员角色分配添加到用户

具有[所有者](../role-based-access-control/built-in-roles.md#owner)角色的用户可以将 [HDInsight 群集操作员](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator)角色分配给你希望对敏感 HDInsight 群集配置值（如群集网关凭据和存储帐户密钥）具有读/写访问权限的用户。

### <a name="using-the-azure-cli"></a>使用 Azure CLI

添加此角色分配的最简单方法是在 Azure CLI 中使用 `az role assignment create` 命令。

> [!NOTE]
> 此命令必须由具有“所有者”角色的用户运行，因为只有他们才能授予这些权限。 `--assignee` 是要将“HDInsight 群集操作员”角色分配到的用户的服务主体名称或电子邮件地址。 如果收到权限不足错误，请参阅下面的常见问题解答。

#### <a name="grant-role-at-the-resource-cluster-level"></a>在资源（群集）级别授予角色

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>在资源组级别授予角色

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>在订阅级别授予角色

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>使用 Azure 门户

或者，可以使用 Azure 门户将 HDInsight 群集操作员角色分配添加到用户。 请参阅文档， [添加或删除使用 Azure 门户-添加角色分配的 Azure 角色分配](../role-based-access-control/role-assignments-portal.md#add-a-role-assignment)。

## <a name="faq"></a>常见问题

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>更新 API 请求和/或工具后，为何会出现 403（禁止）响应？

群集配置现在受到精细的基于角色的访问控制，需要拥有 `Microsoft.HDInsight/clusters/configurations/*` 权限才能访问这些配置。 若要获取此权限，请将“HDInsight 群集操作员”、“参与者”或“所有者”角色分配到尝试访问配置的用户或服务主体。

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>在运行 Azure CLI 命令将 HDInsight 群集操作员角色分配给另一个用户或服务主体时，为什么看不到 "权限不足，无法完成操作"？

除了拥有 "所有者" 角色外，执行命令的用户或服务主体还需要有足够的 Azure AD 权限来查找工作负责人的对象 Id。 此消息指示 Azure AD 权限不足。 尝试将 `-–assignee` 参数替换为 `–assignee-object-id`，并提供被分配者的对象 ID 作为参数，而不要提供名称（如果使用托管标识，则提供主体 ID）。 有关详细信息，请参阅 [Azure 角色分配创建文档](/cli/azure/role/assignment#az-role-assignment-create)的“可选参数”部分。

如果这仍不起作用，请与 Azure AD 管理员联系以获取正确的权限。

### <a name="what-will-happen-if-i-take-no-action"></a>如果不采取任何措施，会发生什么情况？

从 2019 年 9 月 3 日开始，`GET /configurations` 和 `POST /configurations/gateway` 调用将不再返回任何信息，`GET /configurations/{configurationName}` 调用将不再返回存储帐户密钥或群集密码等敏感参数。 对于相应的 SDK 方法和 PowerShell cmdlet，也是如此。

如果使用上述适用于 Visual Studio、VSCode、IntelliJ 或 Eclipse 的旧版工具之一，在更新之前，这些工具将不再可正常运行。

有关更多详细信息，请参阅本文档中适用于你的方案的相应部分。