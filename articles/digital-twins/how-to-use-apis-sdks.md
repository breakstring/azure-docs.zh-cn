---
title: 使用 Azure 数字孪生 API 和 SDK
titleSuffix: Azure Digital Twins
description: 请参阅如何使用 Azure 数字孪生 Api，包括 via SDK。
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4a2667e4876682a6b0aa6d6b7a8cf67eaee376cc
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683661"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>使用 Azure 数字孪生 API 和 SDK

Azure 数字孪生附带了 **控制平面 api** 和 **数据平面 api** ，用于管理实例及其元素。 
* 控制平面 Api 是 [Azure 资源管理器 (ARM) ](../azure-resource-manager/management/overview.md) api，涵盖创建和删除实例等资源管理操作。 
* 数据平面 Api 是 Azure 数字孪生 Api，可用于管理模型、孪生和图形等数据管理操作。

本文概述了可用的 Api 以及与它们进行交互的方法。 可以通过 Postman 等工具（如) [](how-to-use-postman.md) ）或 SDK 直接将 REST api 与关联的 swagger (一起使用。

## <a name="overview-control-plane-apis"></a>概述：控制平面 Api

控制平面 Api 是用于将 Azure 数字孪生实例作为一个整体进行管理的 [ARM](../azure-resource-manager/management/overview.md) api，因此它们涵盖了创建或删除整个实例等操作。 你还将使用这些终结点来创建和删除终结点。

最新的控制平面 API 版本为 _**2020-12-01**_。

使用控制平面 Api：
* 您可以通过在 [控制平面 Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/stable)存储库中引用最新的 Swagger 文件夹，直接调用 api。 此文件夹还包括一个示例文件夹，其中显示了用法。
* 当前可在中访问控件 Api 的 Sdk .。。
  - [**.Net (c # )**](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) ([参考 [自动生成]](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true))  ([源](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins)) 
  - [**Java**](https://search.maven.org/search?q=a:azure-mgmt-digitaltwins) ([引用 [自动生成]](/java/api/overview/azure/digitaltwins?view=azure-java-stable&preserve-view=true))  ([源](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins)) 
  - [**JavaScript**](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([源](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins)) 
  - [**Python**](https://pypi.org/project/azure-mgmt-digitaltwins/) ([源](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins)) 
  - [**继续**](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt/2020-10-31/digitaltwins) ([源](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt/2020-10-31/digitaltwins)) 

还可以通过 [Azure 门户](https://portal.azure.com) 和 [CLI](how-to-use-cli.md)与 Azure 数字孪生交互，来运动控制平面 api。

## <a name="overview-data-plane-apis"></a>概述：数据平面 Api

数据平面 Api 是用于管理 Azure 数字孪生实例中的元素的 Azure 数字孪生 Api。 其中包括创建路由、上传模型、创建关系和管理孪生等操作。 它们可以大致分为以下几类：
* **DigitalTwinModels** -DigitalTwinModels 类别包含用于管理 Azure 数字孪生实例中的 [模型](concepts-models.md) 的 api。 管理活动包括上载、验证、检索和删除在 DTDL 中创作的模型。
* **DigitalTwins** -DigitalTwins 类别包含允许开发人员在 Azure 数字孪生实例中创建、修改和删除 [数字孪生](concepts-twins-graph.md) 及其关系的 api。
* **查询** -查询类别允许开发人员 [查找跨关系图形的数字孪生集](how-to-query-graph.md) 。
* **事件路由** -事件路由类别包含用于 [路由数据](concepts-route-events.md)的 api，通过系统和下游服务。

最新的数据平面 API 版本为 _**2020-10-31**_。

使用数据平面 Api：
* 可以通过 ... 直接调用 Api
   - 引用 [数据平面 swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)存储库中的最新 Swagger 文件夹。 此文件夹还包括一个示例文件夹，其中显示了用法。 
   - 查看 [API 参考文档](/rest/api/azure-digitaltwins/)。
* 可以使用 **.net (c # ) SDK**。 使用 .NET SDK .。。
   - 你可以从 NuGet 中查看并添加包： [DigitalTwins](https://www.nuget.org/packages/Azure.DigitalTwins.Core)。 
   - 您可以查看 [SDK 参考文档](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)。
   - 可以在 GitHub 中找到 SDK 源（包括示例的文件夹）：适用于 [.net 的 Azure IoT 数字孪生客户端库](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)。 
   - 若要查看详细信息和用法示例，请继续阅读本文的 [*.net (c # ) SDK (数据平面)*](#net-c-sdk-data-plane) 部分。
* 可以使用 **JAVA SDK**。 使用 Java SDK .。。
   - 可以从 Maven 查看和安装包： [`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)
   - 您可以查看 [SDK 参考文档](/java/api/overview/azure/digitaltwins/client?preserve-view=true&view=azure-java-stable)
   - 可以在 GitHub 中找到 SDK 源： [适用于 Java 的 Azure IoT 数字孪生客户端库](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* 您可以使用 **JAVASCRIPT SDK**。 使用 JavaScript SDK .。。
   - 可以从 npm： [Azure Azure 数字孪生 Core 客户端库](https://www.npmjs.com/package/@azure/digital-twins-core)查看和安装包。
   - 您可以查看 [SDK 参考文档](/javascript/api/@azure/digital-twins-core/?branch=master&view=azure-node-latest&preserve-view=true)。
   - 可以在 GitHub 中找到 SDK 源： [用于 JavaScript 的 Azure Azure 数字孪生核心客户端库](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)
* 可以使用 **PYTHON SDK**。 使用 Python SDK .。。
   - 可以从 PyPi： [Azure Azure 数字孪生 Core 客户端库](https://pypi.org/project/azure-digitaltwins-core/)查看和安装包。
   - 您可以查看 [SDK 参考文档](/python/api/azure-digitaltwins-core/azure.digitaltwins.core?view=azure-python&preserve-view=true)。
   - 可以在 GitHub 中找到 SDK 源： [用于 Python 的 Azure Azure 数字孪生核心客户端库](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* 可以使用 AutoRest 为其他语言生成 SDK。 按照 [*如何：创建适用于 Azure 数字孪生的自定义 Sdk AutoRest*](how-to-create-custom-sdks.md)中的说明进行操作。

还可以通过 [CLI](how-to-use-cli.md)与 Azure 数字孪生交互，来运用日期平面 api。

## <a name="net-c-sdk-data-plane"></a>.NET (c # ) SDK (数据平面) 

Azure 数字孪生 .NET (c # ) SDK 是用于 .NET 的 Azure SDK 的一部分。 它是开放源代码，基于 Azure 数字孪生数据平面 Api。

> [!NOTE]
> 有关 SDK 设计的详细信息，请参阅 [Azure sdk 的常规设计原则](https://azure.github.io/azure-sdk/general_introduction.html) 和特定 [.net 设计准则](https://azure.github.io/azure-sdk/dotnet_introduction.html)。

若要使用 SDK，请在项目中包括 NuGet 包 **DigitalTwins** 。 还需要最新版本的 **Azure. Identity** 包。 在 Visual Studio 中，你可以使用 NuGet 包管理器添加这些包， (通过 *> Nuget 包管理器的工具访问 > 管理解决方案) 的 NuGet 包* 。 或者，你可以将 .NET 命令行工具与下面的 NuGet 包链接中的命令一起使用，以将它们添加到你的项目：
* [**Azure.DigitalTwins.Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core). 这是用于[适用于 .NET 的 Azure 数字孪生 SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 的包。 
* [**Azure.Identity**](https://www.nuget.org/packages/Azure.Identity). 该库提供可帮助进行 Azure 身份验证的工具。

有关如何在实践中使用 Api 的详细演练，请参阅 [*教程：编写客户端应用代码*](tutorial-code.md)。 

### <a name="net-sdk-usage-examples"></a>.NET SDK 使用示例

下面是演示如何使用 .NET SDK 的一些代码示例。

对服务进行身份验证：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_basic":::

[!INCLUDE [Azure Digital Twins: local credentials note](../../includes/digital-twins-local-credentials-note.md)] 

上传模型：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

列出模型：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

创建孪生：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

查询孪生并循环遍历结果：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

有关此示例应用代码的演练，请参阅 [*教程：为客户端应用编写代码*](tutorial-code.md) 。 

你还可以在 [适用于 .net (c # ) SDK 的 GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)存储库中找到其他示例。

#### <a name="serialization-helpers"></a>序列化帮助器

序列化帮助程序是 SDK 内可用的帮助程序函数，用于快速创建或反序列化用于访问基本信息的数据。 由于核心 SDK 方法默认返回的是以 JSON 形式返回的数据，因此，使用这些帮助器类进一步打破了克隆数据会很有帮助。

可用的帮助程序类包括：
* `BasicDigitalTwin`：表示数字输出的核心数据
* `BasicRelationship`：表示关系的核心数据
* `UpdateOperationUtility`：表示更新调用中使用的 JSON 修补程序信息
* `WriteableProperty`：表示属性元数据

##### <a name="deserialize-a-digital-twin"></a>反序列化数字输出

始终可以使用所选的 JSON 库（如或）反序列化 `System.Test.Json` 数据 `Newtonsoft.Json` 。 对于对某个对等的基本访问，帮助器类使其更方便一些。

使用 `BasicDigitalTwin` helper 类，还可以通过访问在克隆上定义的属性 `Dictionary<string, object>` 。 若要列出克隆的属性，可以使用：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin":::

##### <a name="create-a-digital-twin"></a>创建数字输出

使用 `BasicDigitalTwin` 类，您可以准备用于创建克隆实例的数据：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

上面的代码等效于以下 "手动" 变体：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

##### <a name="deserialize-a-relationship"></a>反序列化关系

您始终可以将关系数据反序列化为您选择的类型。 对于关系的基本访问权限，请使用类型 `BasicRelationship` 。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

`BasicRelationship`通过帮助器类，还可以通过实现对关系定义的属性的访问 `IDictionary<string, object>` 。 若要列出属性，你可以使用：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="ListRelationshipProperties":::

##### <a name="create-a-relationship"></a>创建关系

通过使用 `BasicRelationship` 类，您还可以准备用于在克隆实例上创建关系的数据：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="CreateRelationship_short":::

##### <a name="create-a-patch-for-twin-update"></a>创建用于克隆更新的修补程序

针对孪生和关系的更新调用使用 [JSON 修补程序](http://jsonpatch.com/) 结构。 若要创建 JSON 修补程序操作列表，你可以使用，如下 `JsonPatchDocument` 所示。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

## <a name="general-apisdk-usage-notes"></a>常规 API/SDK 使用说明

> [!NOTE]
> 请注意，Azure 数字孪生目前不支持 **(CORS) 的跨域资源共享**。 有关影响和解决策略的详细信息，请参阅概念的 [*跨域资源共享 (CORS)*](concepts-security.md#cross-origin-resource-sharing-cors) *： Azure 数字孪生解决方案的安全性*。

下面的列表提供了有关使用 Api 和 Sdk 的其他详细信息和一般准则。

* 可以使用 HTTP REST 测试工具（如 Postman）对 Azure 数字孪生 Api 进行直接调用。 有关此过程的详细信息，请参阅 how [*to： requests requests With Postman*](how-to-use-postman.md)。
* 若要使用 SDK，请实例化 `DigitalTwinsClient` 类。 构造函数需要可使用包中各种身份验证方法获取的凭据 `Azure.Identity` 。 有关的详细信息 `Azure.Identity` ，请参阅其 [命名空间文档](/dotnet/api/azure.identity?preserve-view=true&view=azure-dotnet)。 
* 你可能会发现在入门 `InteractiveBrowserCredential` 时非常有用，但还有其他几个选项，包括 [托管标识](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet)的凭据，你可能会使用这些选项来验证使用 MSI 对 azure 数字孪生 [设置的 azure 功能](../app-service/overview-managed-identity.md?tabs=dotnet) 。 有关的详细信息 `InteractiveBrowserCredential` ，请参阅它的 [类文档](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet)。
* 所有服务 API 调用都公开为类的成员函数 `DigitalTwinsClient` 。
* 所有服务函数都存在于同步和异步版本中。
* 所有服务函数对于400或更高版本的返回状态均引发异常。 请确保将调用包装到 `try` 部分，并至少捕获 `RequestFailedExceptions` 。 有关此类异常的详细信息，请参阅 [此处](/dotnet/api/azure.requestfailedexception?preserve-view=true&view=azure-dotnet)。
* 大多数服务方法 `Response<T>` `Task<Response<T>>` 为异步调用) 返回或 (，其中 `T` 是服务调用的返回对象的类。 [`Response`](/dotnet/api/azure.response-1?preserve-view=true&view=azure-dotnet)类封装服务返回并在其字段中显示返回值 `Value` 。  
* 包含分页结果的服务方法返回 `Pageable<T>` 或 `AsyncPageable<T>` 结果。 有关类的详细信息 `Pageable<T>` ，请参阅 [此处](/dotnet/api/azure.pageable-1?preserve-view=true&view=azure-dotnet); 有关的详细信息 `AsyncPageable<T>` ，请参阅 [此处](/dotnet/api/azure.asyncpageable-1?preserve-view=true&view=azure-dotnet)。
* 您可以使用循环来循环访问分页 `await foreach` 的结果。 有关此过程的详细信息，请参阅 [此处](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8)。
* 基础 SDK 是 `Azure.Core` 。 请参阅 [Azure 命名空间文档](/dotnet/api/azure?preserve-view=true&view=azure-dotnet) ，了解 SDK 基础结构和类型。

服务方法尽可能返回强类型对象。 但是，因为 Azure 数字孪生基于) 用户在运行 (时由用户通过上传到服务的已配置的模型，因此，许多服务 Api 采用 JSON 格式获取并返回静态数据。

## <a name="monitor-api-metrics"></a>监视 API 指标

可以在 [Azure 门户](https://portal.azure.com/)中查看 API 指标，例如请求、延迟和失败率。 

在门户主页上，搜索 Azure 数字孪生实例以提取其详细信息。 从 Azure 数字孪生实例的菜单中选择 " **指标** " 选项，打开 " *指标* " 页。

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="显示 Azure 数字孪生的 &quot;指标&quot; 页的屏幕截图":::

在此处，你可以查看实例的度量值并创建自定义视图。

## <a name="next-steps"></a>后续步骤

请参阅如何使用 Postman 向 Api 发出直接请求：
* [*如何：通过 Postman 发出请求*](how-to-use-postman.md)

或者，通过使用本教程创建客户端应用程序，练习使用 .NET SDK：
* [*教程：* 为客户端应用编写代码](tutorial-code.md)