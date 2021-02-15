---
title: Azure Functions 的应用设置参考
description: 有关 Azure Functions 应用设置或环境变量的参考文档。
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 8cb3e12c48adf1273c58f4914e34590e21b9d3cc
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378292"
---
# <a name="app-settings-reference-for-azure-functions"></a>Azure Functions 的应用设置参考

函数应用中的应用设置包含对该函数应用的所有函数产生影响的全局配置选项。 在本地运行时，这些设置将作为本地[环境变量](functions-run-local.md#local-settings-file)进行访问。 本文列出可在函数应用中使用的应用设置。

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

[host.json](functions-host-json.md) 文件和 [local.settings.json](functions-run-local.md#local-settings-file) 文件中提供了其他全局配置选项。

> [!NOTE]  
> 可以使用应用程序设置替代 host.json 设置值，而不必更改 host.json 文件本身。 这对于需要针对特定环境配置或修改特定 host.json 设置的方案很有用。 这也让你可以更改 host.json 设置，而不必重新发布项目。 若要了解详细信息，请参阅 [host.json 参考文章](functions-host-json.md#override-hostjson-values)。 如果更改函数应用设置，则需要重启函数应用。

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Application Insights 的检测密钥。 仅使用 `APPINSIGHTS_INSTRUMENTATIONKEY` 或 `APPLICATIONINSIGHTS_CONNECTION_STRING` 中的一个。 当 Application Insights 在主权云中运行时，请使用 `APPLICATIONINSIGHTS_CONNECTION_STRING`。 有关详细信息，请参阅[如何配置对 Azure Functions 的监视](configure-monitoring.md)。 

|键|示例值|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connection_string"></a>APPLICATIONINSIGHTS_CONNECTION_STRING

Application Insights 的连接字符串。 在以下情况下使用 `APPLICATIONINSIGHTS_CONNECTION_STRING` 而不是 `APPINSIGHTS_INSTRUMENTATIONKEY`：

+ 当函数应用需要通过连接字符串提供支持的已添加自定义项时。 
+ 当 Application Insights 实例在需要自定义终结点的主权云中运行时。

有关详细信息，请参阅[连接字符串](../azure-monitor/app/sdk-connection-string.md)。 

|键|示例值|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTION_STRING|InstrumentationKey=[key];IngestionEndpoint=[url];LiveEndpoint=[url];ProfilerEndpoint=[url];SnapshotEndpoint=[url];|

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

默认情况下，[Functions 代理](functions-proxies.md)使用快捷方式从代理直接将 API 调用发送到同一函数应用中的函数。 使用此快捷方式取代创建新的 HTTP 请求。 此设置让你能够禁用该快捷方式行为。

|键|“值”|说明|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|是|具有指向本地函数应用中函数的后端 URL 的调用不会直接发送到函数， 相反，请求会定向回函数应用的 HTTP 前端。|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|具有指向本地函数应用中函数的后端 URL 的调用会直接转发到函数。 这是默认值。 |

## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

此设置控制字符 `%2F` 在路由参数插入后端 URL 时是否在路由参数中解码为斜杠。 

|键|“值”|说明|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|是|包含编码斜杠的路由参数已解码。 |
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|所有路由参数均原样传递，这是默认行为。 |

例如，考虑位于 `myfunction.com` 域的函数应用的 proxies.json 文件。

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```

如果 `AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES` 设置为 `true`，则 URL `example.com/api%2ftest` 解析为 `example.com/api/test`。 默认情况下，URL 保持为 `example.com/test%2fapi` 不变。 有关详细信息，请参阅 [Functions 代理](functions-proxies.md)。

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

在 2.x 和更高版本的 Functions 运行时中，基于运行时环境配置应用行为。 [在初始化期间读取](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43)此值。 可以将 `AZURE_FUNCTIONS_ENVIRONMENT` 设置为任何值，但支持[三个值](/dotnet/api/microsoft.aspnetcore.hosting.environmentname)：[Development](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development)、[Staging](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging) 和 [Production](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production)。 如果未设置 `AZURE_FUNCTIONS_ENVIRONMENT`，则在本地环境中默认为 `Development`，在 Azure 中默认为 `Production`。 应使用此设置（而不是 `ASPNETCORE_ENVIRONMENT`）来设置运行时环境。 

## <a name="azurefunctionsjobhost__"></a>AzureFunctionsJobHost__\*

在 Functions 运行时的版本 2.x 和更高版本中，应用程序设置可以替代当前环境中的 [host.json](functions-host-json.md) 设置。 这些替代表示为名为 `AzureFunctionsJobHost__path__to__setting` 的应用程序设置。 有关详细信息，请参阅[替代 host.json 值](functions-host-json.md#override-hostjson-values)。

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

用于存储日志并在门户上的“监视”选项卡中显示这些日志的可选存储帐户连接字符串。 此设置仅对面向 Azure Functions 运行时版本 1.x 的应用有效。 存储帐户必须是支持 Blob、队列和表的通用帐户。 有关详细信息，请参阅[存储帐户要求](storage-considerations.md#storage-account-requirements)。

|键|示例值|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>|

> [!NOTE]
> 为了获得更好的性能和体验，运行时版本 2.x 及更高版本使用 APPINSIGHTS_INSTRUMENTATIONKEY 和 App Insights 进行监视，而不是使用 `AzureWebJobsDashboard`。

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` 表示禁用针对函数应用根 URL 显示的默认登录页。 默认值为 `false`。

|键|示例值|
|---|------------|
|AzureWebJobsDisableHomepage|是|

如果省略此应用设置或将其设置为 `false`，则会显示类似于以下示例的页来响应 URL `<functionappname>.azurewebsites.net`。

![函数应用登录页](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` 表示在编译 .NET 代码时使用“发布”模式；`false` 表示使用“调试”模式。 默认值为 `true`。

|键|示例值|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|是|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

要启用的 beta 功能的逗号分隔列表。 由这些标志启用的 Beta 功能尚未准备好用于生产，但在发布这些功能之前可针对试验目的启用这些功能。

|键|示例值|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

指定用于密钥存储的存储库或提供程序。 目前，支持的存储库包括 blob 存储（“Blob”）和本地文件系统（“Files”）。 默认为在版本 2 中使用 blob，在版本 1 中使用文件系统。

|键|示例值|
|---|------------|
|AzureWebJobsSecretStorageType|文件|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Azure Functions 运行时使用此存储帐户连接字符串执行常规操作。 此存储帐户的一些用途包括密钥管理、计时器触发器管理和事件中心检查点。 存储帐户必须是支持 Blob、队列和表的通用帐户。 请参阅[存储帐户](functions-infrastructure-as-code.md#storage-account)和[存储帐户要求](storage-considerations.md#storage-account-requirements)。

|键|示例值|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

用于 TypeScript 的编译器的路径。 允许根据需要重写默认值。

|键|示例值|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>FUNCTION\_APP\_EDIT\_MODE

指示是否在 Azure 门户中启用了编辑。 有效值为“readwrite”和“readonly”。

|键|示例值|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functions_extension_version"></a>FUNCTIONS\_EXTENSION\_VERSION

要在此函数应用中使用的 Functions 运行时版本。 波浪符加主要版本号表示使用该主要版本的最新版本（例如“~2”）。 当同一主要版本的新版本可用时，会自动在函数应用中安装新版本。 若要让应用固定使用特定的版本，请使用完整版本号（例如“2.0.12345”）。 默认值为“~2”。 值 `~1` 让应用固定使用运行时版本 1.x。

|键|示例值|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~2|

## <a name="functions_v2_compatibility_mode"></a>FUNCTIONS\_V2\_COMPATIBILITY\_MODE

此设置使函数应用能够在版本 3.x 运行时上以版本 2.x 兼容模式运行。 仅当在[将函数应用从运行时版本 2.x 升级到版本 3.x](functions-versions.md#migrating-from-2x-to-3x) 时遇到问题的情况下，才使用此设置。 

>[!IMPORTANT]
> 在更新应用以便在版本 3.x 上正常运行时，此设置仅用作短期解决方法。 只要 [2.x 运行时受支持](functions-versions.md)，此设置就受支持。 如果在不使用此设置的情况下遇到阻止应用在版本 3.x 上运行的问题，请[报告问题](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md)。

需要 [FUNCTIONS\_EXTENSION\_VERSION](functions-app-settings.md#functions_extension_version) 设置为 `~3`。

|键|示例值|
|---|------------|
|FUNCTIONS\_V2\_COMPATIBILITY\_MODE|是|

## <a name="functions_worker_process_count"></a>FUNCTIONS\_WORKER\_PROCESS\_COUNT

指定语言工作进程的最大数量，其默认值为 `1`。 允许的最大值为 `10`。 函数调用均匀地分布在语言工作进程中。 语言工作进程每 10 秒生成一次，直到达到 FUNCTIONS\_WORKER\_PROCESS\_COUNT 设置的计数。 使用多个语言工作进程与[缩放](functions-scale.md)不同。 当工作负荷混合使用 CPU 绑定和 I/O 绑定调用时，请考虑使用此设置。 此设置适用于所有非 .NET 语言。

|键|示例值|
|---|------------|
|FUNCTIONS\_WORKER\_PROCESS\_COUNT|2|

## <a name="python_threadpool_thread_count"></a>PYTHON \_ THREADPOOL \_ 线程 \_ 计数

指定 Python 语言辅助角色将用于执行函数调用的最大线程数，其默认值 `1` 为 Python 版本及更高版本 `3.8` 。 对于 Python 版本 `3.9` 及更高版本，该值设置为 `None` 。 请注意，此设置不能保证在执行过程中设置的线程数。 此设置允许 Python 将线程数扩展到指定的值。 此设置仅适用于 Python 函数应用。 此外，此设置还适用于同步函数调用，不适用于协同程序。

|键|示例值|最大值|
|---|------------|---------|
|PYTHON \_ THREADPOOL \_ 线程 \_ 计数|2|32|


## <a name="functions_worker_runtime"></a>FUNCTIONS\_WORKER\_RUNTIME

要在函数应用中加载的语言辅助角色运行时。  这将对应于应用程序中使用的语言（例如，“dotnet”）。 对于使用多种语言的函数，需要将这些函数发布到多个应用（每个应用程序都有相应的辅助角色运行时值）。  有效值为 `dotnet` (C#/F#)、`node` (JavaScript/TypeScript)、`java` (Java)、`powershell` (PowerShell) 和 `python` (Python)。

|键|示例值|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|dotnet|

## <a name="pip_extra_index_url"></a>PIP \_ 额外 \_ 索引 \_ URL

此设置的值指示 Python 应用的自定义包索引 URL。 如果需要使用在额外的包索引中找到的自定义依赖项运行远程生成，请使用此设置。   

|键|示例值|
|---|------------|
|PIP \_ 额外 \_ 索引 \_ URL|http://my.custom.package.repo/simple |

若要了解详细信息，请参阅 Python 开发人员参考中的 [自定义依赖项](functions-reference-python.md#remote-build-with-extra-index-url) 。

## <a name="scale_controller_logging_enabled"></a>扩展 \_ 控制器 \_ 日志记录 \_ 已启用

_此设置当前处于预览状态。_  

此设置控制 Azure Functions 缩放控制器中的日志记录。 有关详细信息，请参阅[缩放控制器日志](functions-monitoring.md#scale-controller-logs)。

|键|示例值|
|-|-|
|SCALE_CONTROLLER_LOGGING_ENABLED|AppInsights:Verbose|

此键的值以 `<DESTINATION>:<VERBOSITY>` 格式提供，其定义如下：

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE\_CONTENTAZUREFILECONNECTIONSTRING

存储帐户的连接字符串，该帐户的函数应用代码和配置存储在 Windows 上运行的事件驱动缩放计划中。 有关详细信息，请参阅[创建函数应用](functions-infrastructure-as-code.md#windows)。

|键|示例值|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

仅在部署到高级计划或在 Windows 上运行的消耗计划时使用。 对于运行 Linux 的使用量计划不支持。 更改或删除此设置可能会导致函数应用无法启动。 若要了解详细信息，请参阅[此故障排除文章](functions-recover-storage-account.md#storage-account-application-settings-were-deleted)。 

## <a name="website_contentovervnet"></a>WEBSITE\_CONTENTOVERVNET

仅用于高级计划。 将存储帐户限制在虚拟网络中时，值 `1` 允许函数应用进行缩放。 将存储帐户限制于一个虚拟网络时，应启用此设置。 若要了解详细信息，请参阅[将存储帐户限制在虚拟网络中](functions-networking-options.md#restrict-your-storage-account-to-a-virtual-network)。

|键|示例值|
|---|------------|
|WEBSITE_CONTENTOVERVNET|1|

## <a name="website_contentshare"></a>WEBSITE\_CONTENTSHARE

Windows 上事件驱动的缩放计划中函数应用代码和配置的文件路径。 与 WEBSITE_CONTENTAZUREFILECONNECTIONSTRING 结合使用。 默认值是以函数应用名称开头的唯一字符串。 请参阅[创建函数应用](functions-infrastructure-as-code.md#windows)。

|键|示例值|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

仅在部署到高级计划或在 Windows 上运行的消耗计划时使用。 对于运行 Linux 的使用量计划不支持。 更改或删除此设置可能会导致函数应用无法启动。 若要了解详细信息，请参阅[此故障排除文章](functions-recover-storage-account.md#storage-account-application-settings-were-deleted)。

在部署期间使用 Azure 资源管理器创建函数应用时，请不要在模板中包括 WEBSITE_CONTENTSHARE。 此应用程序设置是在部署过程中生成的。 若要了解详细信息，请参阅[为函数应用自动执行资源部署](functions-infrastructure-as-code.md#windows)。   

## <a name="website_max_dynamic_application_scale_out"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

函数应用可以横向扩展到的最大实例数。 默认值为无限制。

> [!IMPORTANT]
> 此设置处于预览状态。  添加了一个[函数应用横向扩展上限属性](./event-driven-scaling.md#limit-scale-out)，建议使用此方法限制横向扩展。

|键|示例值|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|5|

## <a name="website_node_default_version"></a>WEBSITE\_NODE\_DEFAULT_VERSION

_仅限 Windows_。  
设置在 Windows 上运行函数应用时要使用的 Node.js 版本。 应使用波形符 (~) 让运行时使用目标主版本的最新可用版本。 例如，当设置为 `~10` 时，将使用最新版本 Node.js 10。 当目标主版本带有波形符时，无需手动更新次版本。 

|键|示例值|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|~10|

## <a name="website_run_from_package"></a>WEBSITE\_RUN\_FROM\_PACKAGE

让函数应用从已装载的包文件运行。

|键|示例值|
|---|------------|
|WEBSITE\_RUN\_FROM\_PACKAGE|1|

有效值是解析为部署包文件位置的 URL 或 `1`。 设置为 `1` 时，包必须位于 `d:\home\data\SitePackages` 文件夹中。 使用此设置的 zip 部署时，包将自动上传到此位置。 在预览版中，此设置名为 `WEBSITE_RUN_FROM_ZIP`。 有关详细信息，请参阅[从包文件运行函数](run-functions-from-deployment-package.md)。

## <a name="website_time_zone"></a>WEBSITE\_TIME\_ZONE

允许为函数应用设置时区。 

|键|(OS)|示例值|
|---|--|------------|
|WEBSITE\_TIME\_ZONE|Windows|东部标准时间|
|WEBSITE\_TIME\_ZONE|Linux|美洲/New_York|

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="next-steps"></a>后续步骤

[了解如何更新应用设置](functions-how-to-use-azure-function-app-settings.md#settings)

[查看 host.json 文件中的全局设置](functions-host-json.md)

[查看应用服务应用的其他应用设置](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
