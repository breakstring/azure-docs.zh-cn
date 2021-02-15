---
title: 在 .NET 中使用 Microsoft.Azure.Search (v10)
titleSuffix: Azure Cognitive Search
description: '了解如何在 .NET 应用程序中使用 c # 和 Microsoft (版本 10) 的 .NET SDK 创建和管理搜索对象。 代码片段演示了如何连接到服务、创建索引，以及如何查询。'
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 11102d95bc2aba65e6bc3cba71805a67f195947b
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681430"
---
# <a name="how-to-use-microsoftazuresearch-in-a-c-net-application"></a>如何在 c # .NET 应用程序中使用 Microsoft Azure。

本文介绍如何使用 Azure SDK for .NET 中的 c # 和旧的客户端库 [**(版本**](/dotnet/api/overview/azure/search/client10) 10) 来创建和管理搜索对象。 

版本 10 是 Microsoft.Azure.Search 包的最新版。 前进后，将在 Azure SDK 团队 [**Azure.Search.Documents**](/dotnet/api/overview/azure/search.documents-readme) 中推出新功能。

> [!NOTE]
> 如果现有或即时开发项目，则可以继续使用版本10。 对于新项目，或若要使用新功能，应转换为 [新库](/dotnet/api/overview/azure/search.documents-readme)。

## <a name="about-version-10"></a>关于版本10

SDK 包括一些客户端库。借助它，不仅可以管理索引、数据源、索引器和同义词映射，还能上传和管理文档并执行查询，所有这些操作都无需处理 HTTP 和 JSON 的详细信息。 这些客户端库全部作为 NuGet 包进行分发。

主 NuGet 包是 `Microsoft.Azure.Search`，它是一个元包，包括所有作为依赖关系的其他程序包。 如果你刚入门，或者如果你知道应用程序将需要 Azure 认知搜索的所有功能，请使用此程序包。

SDK 中的其他 NuGet 程序包有：
 
  - `Microsoft.Azure.Search.Data`：如果使用 Azure 认知搜索开发 .NET 应用程序，则使用此包，并且只需查询或更新索引中的文档。 如果还需要创建或更新索引、同义词映射或其他服务级资源，请改用 `Microsoft.Azure.Search` 包。
  - `Microsoft.Azure.Search.Service`：如果在 .NET 中开发自动化以管理 Azure 认知搜索索引、同义词映射、索引器、数据源或其他服务级资源，请使用此包。 如果只需要查询或更新索引中的文档，请改用 `Microsoft.Azure.Search.Data` 包。 如果需要 Azure 认知搜索的所有功能，请改用 `Microsoft.Azure.Search` 包。
  - `Microsoft.Azure.Search.Common`：Azure 认知搜索 .NET 库需要的常见类型。 无需在应用程序中直接使用此包。 此包仅用作依赖项。

各种客户端库定义 `Index`、`Field` 和 `Document` 等类，以及 `SearchServiceClient` 和 `SearchIndexClient` 类中的 `Indexes.Create` 和 `Documents.Search` 等操作。 这些类已组织成以下命名空间：

* [Microsoft.Azure.Search](/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](/dotnet/api/microsoft.azure.search.models)

如果想要为 SDK 的未来更新提供反馈，请参阅我们的[反馈页](https://feedback.azure.com/forums/263029-azure-search/)，或者在 [GitHub](https://github.com/azure/azure-sdk-for-net/issues) 上创建问题并在问题标题中提到“Azure 认知搜索”。

.NET SDK 面向[ `2019-05-06` Azure 认知搜索](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/data-plane/Microsoft.Azure.Search.Data/stable/2019-05-06)的版本 REST API。 此版本包括在为 Azure Blob 编制索引时所需的对[复杂类型](search-howto-complex-data-types.md)、[AI 增强](cognitive-search-concept-intro.md)、[自动完成](/rest/api/searchservice/autocomplete)和 [JsonLines 分析模式](search-howto-index-json-blobs.md)的支持。 

此 SDK 不支持[管理操作](/rest/api/searchmanagement/)（如创建和缩放搜索服务以及管理 API 密钥）。 如果需要从 .NET 应用程序管理搜索资源，可以使用 [Azure 认知搜索 .NET 管理 SDK](/dotnet/api/overview/azure/search/management)。

## <a name="upgrade-to-v10"></a>升级到 v10
如果你已在使用较旧版本的 Azure 认知搜索 .NET SDK，并且想要升级到最新的正式版，[此文](search-dotnet-sdk-migration-version-9.md)介绍了操作方法。

## <a name="sdk-requirements"></a>SDK 要求
1. Visual Studio 2017 或更高版本。
2. 有自己的 Azure 认知搜索服务。 要使用 SDK，需要服务的名称以及一个或多个 API 密钥。 [在门户中创建服务](search-create-service-portal.md)将帮助你完成这些步骤。
3. 在 Visual Studio 中，通过使用“管理 NuGet 包”来下载 Azure 认知搜索 .NET SDK [NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.Search)。 只需在 NuGet.org 上搜索程序包名称 `Microsoft.Azure.Search`（或者如果你只需要其中一部分功能，则可以搜索上述其中一个其他程序包名称）。

Azure 认知搜索 .NET SDK 支持面向 .NET Framework 4.5.2 和更高版本以及 .NET Core 2.0 和更高版本的应用程序。

## <a name="core-scenarios"></a>核心方案
需要在搜索应用程序中完成几项操作。 在本教程中，我们介绍以下核心方案：

* 创建索引
* 使用文档填充索引
* 使用全文搜索和筛选器搜索文档

以下示例代码演示了上述每个方案。 可随意在自己的应用程序中使用代码片段。

### <a name="overview"></a>概述
我们将要探索的示例应用程序会创建一个名为“hotels”的新索引、用几个文档对该索引进行填充，并执行一些搜索查询。 以下是主程序，演示总体流程：

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);

    ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

> [!NOTE]
> 可以在 [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) 上找到本演练中所用示例应用程序的完整源代码。
> 
>

我们将逐步进行介绍。 首先，我们需要创建一个新的 `SearchServiceClient`。 使用此对象，可以管理索引。 要构建一个，需要提供 Azure 认知搜索服务名称以及管理 API 密钥。 可以在[示例应用程序](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)的 `appsettings.json` 文件中输入此信息。

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

> [!NOTE]
> 如果提供了不正确的密钥（例如，需要管理密钥的查询密钥），首次调用 `Indexes.Create` 之类上的操作方法时，`SearchServiceClient` 会引发 `CloudException`（错误消息为“已禁用”）。 如果遇到此情况，请仔细检查我们的 API 密钥。
> 
> 

以下几行调用方法来创建名为“hotels”的索引，如果该索引已存在，请事先删除它。 我们稍后会介绍这些方法。

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

接下来，需要填充索引。 若要填充索引，需要 `SearchIndexClient`。 有两种方法可获取一个：构建它，或调用 `SearchServiceClient` 上的 `Indexes.GetClient`。 为方便起见，我们使用后者。

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> 在典型的搜索应用程序中，索引管理和填充可由搜索查询中的一个单独的组件处理。 `Indexes.GetClient` 对于填充索引很方便，因为使用它则无需提供额外的 `SearchCredentials`。 它通过向新 `SearchIndexClient` 传递用于创建 `SearchServiceClient` 的管理密钥来实现此目的。 但是，在执行查询的应用程序中，最好是直接创建 `SearchIndexClient` ，这样可以传入查询密钥（只允许读取数据）而不是管理密钥。 这与最小特权原则一致，可帮助使应用程序更安全。 可在[此处](/rest/api/searchservice/#authentication-and-authorization)了解有关管理密钥和查询密钥的详细信息。
> 
> 

现在，我们已有 `SearchIndexClient`，可填充索引。 索引填充是使用稍后介绍的另一种方法完成的。

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

最后，我们执行一些搜索查询，并显示结果。 这次我们使用不同的 `SearchIndexClient`：

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

我们会在以后详细地查看 `RunQueries` 方法。 下面是用于创建新 `SearchIndexClient` 的代码：

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

这次我们使用查询密钥，因为我们不需要对索引进行写访问。 可以在[示例应用程序](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)的 `appsettings.json` 文件中输入此信息。

如果使用有效服务名称和 API 密钥运行此应用程序，输出应如以下示例所示：（为方便演示，某些控制台输出已替换为“...”。）

```output

Deleting index...

Creating index...

Uploading documents...

Waiting for documents to be indexed...

Search the entire index for the term 'motel' and return only the HotelName field:

Name: Secret Point Motel

Name: Twin Dome Motel


Apply a filter to the index to find hotels with a room cheaper than $100 per night, and return the hotelId and description:

HotelId: 1
Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Times Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.

HotelId: 2
Description: The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.


Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Triple Landscape Hotel
Last renovated on: 9/20/2015 12:00:00 AM +00:00

Name: Twin Dome Motel
Last renovated on: 2/18/1979 12:00:00 AM +00:00


Search the hotel names for the term 'hotel':

HotelId: 3
Name: Triple Landscape Hotel
...

Complete.  Press any key to end application... 
```

本文末尾处提供了应用程序的完整源代码。

接下来，我们会详细介绍 `Main` 调用的每个方法。

### <a name="creating-an-index"></a>创建索引
创建 `SearchServiceClient` 后，`Main` 会删除“hotels”索引（如果该索引已存在）。 使用以下方法完成该删除操作：

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

此方法使用给定的 `SearchServiceClient` 来检查索引是否存在，如果存在，则删除该索引。

> [!NOTE]
> 为简单起见，本文中的示例代码使用 Azure 认知搜索 .NET SDK 的同步方法。 建议用户在自己的应用程序中使用异步方法，使应用程序保持可缩放且响应迅速。 例如，在上述方法中，可以使用 `ExistsAsync` 和 `DeleteAsync`，而不是 `Exists` 和 `Delete`。
> 
> 

接下来，`Main` 通过调用如下方法创建新的“hotels”索引：

```csharp
private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    
    serviceClient.Indexes.Create(definition);
}
```

此方法使用用于定义新索引架构的一列 `Field` 对象创建新的 `Index` 对象。 每个字段都有名称、数据类型和数个属性（定义其搜索行为）。 `FieldBuilder` 类通过检查给定 `Hotel` 模型类的公共属性和特性，使用反射来为索引创建 `Field` 对象的列表。 我们会在以后详细地查看 `Hotel` 类。

> [!NOTE]
> 如果需要，始终可以直接创建 `Field` 对象的列表，而不是使用 `FieldBuilder`。 例如，你可能不想使用模型类，或者可能需要使用不希望通过添加属性来进行修改的现有模型类。
>
> 

除了字段，还可以向索引添加计分配置文件、建议器或 CORS 选项（为简洁起见，示例中省略了这些参数）。 可在 [SDK 参考](/dotnet/api/microsoft.azure.search.models.index)以及 [Azure 认知搜索 REST API 参考](/rest/api/searchservice/)中，找到有关 Index 对象及其组成部分的详细信息。

### <a name="populating-the-index"></a>填充索引
`Main` 中的下一步骤填充新建的索引。 此索引填充操作是通过以下方法完成的：（为方便演示，某些代码已替换为“...”。  有关完整的数据填充代码，请参阅完整的示例解决方案。）

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
        new Hotel()
        {
            HotelId = "1",
            HotelName = "Secret Point Motel",
            ...
            Address = new Address()
            {
                StreetAddress = "677 5th Ave",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Budget Room, 1 Queen Bed (Cityside)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (City View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "2",
            HotelName = "Twin Dome Motel",
            ...
            {
                StreetAddress = "140 University Town Center Dr",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Suite, 2 Double Beds (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Standard Room, 1 Queen Bed (City View)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Waterfront View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "3",
            HotelName = "Triple Landscape Hotel",
            ...
            Address = new Address()
            {
                StreetAddress = "3393 Peachtree Rd",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Standard Room, 2 Queen Beds (Amenities)",
                    ...
                },
                new Room ()
                {
                    Description = "Standard Room, 2 Double Beds (Waterfront View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (Cityside)",
                    ...
                }
            }
        }
    };

    var batch = IndexBatch.Upload(hotels);

    try
    {
        indexClient.Documents.Index(batch);
    }
    catch (IndexBatchException e)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
}
```

此方法有四个部分。 第一个部分创建包含 3 个 `Hotel` 对象的数组，其中每个对象包含 3 个用作要上传到索引的输入数据的 `Room` 对象。 为简单起见，此数据是硬编码的。 在自己的应用程序中，数据可能来自外部数据源（如 SQL 数据库）。

第二部分创建包含文档的 `IndexBatch`。 创建 Batch 时，指定要应用到 Batch 的操作，在这种情况下应调用 `IndexBatch.Upload`。 然后，使用 `Documents.Index` 方法将批上传到 Azure 认知搜索索引。

> [!NOTE]
> 在本示例中，我们只需上传文档。 如果想要将更改合并到现有文档或删除文档，可以改为调用 `IndexBatch.Merge`、`IndexBatch.MergeOrUpload` 或 `IndexBatch.Delete` 创建 Batch。 还可以通过调用 `IndexBatch.New` 在单个批中混用不同操作，这会用到一个 `IndexAction` 对象的集合，其中的每个对象都会指示 Azure 认知搜索对文档执行特定操作。 可以通过调用 `IndexAction.Merge`、`IndexAction.Upload` 之类的相应方法，创建自带操作的每个 `IndexAction`。
> 
> 

此方法的第三部分是处理索引重要错误情况的 catch 块。 如果 Azure 认知搜索服务无法为批中的某些文档编制索引，`Documents.Index` 将引发 `IndexBatchException`。 如果在服务负载过大时为文档编制索引，可能会发生此异常。 **强烈建议在代码中显式处理这种情况。** 可以延迟为失败的文档编制索引，并重试，也可以像此示例一样记录并继续执行，还可以执行其他操作，具体取决于应用程序对数据一致性的要求。

> [!NOTE]
> 可以使用 [`FindFailedActionsToRetry`](/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) 方法来构造一个新的批处理，其中仅包含上次调用 `Index` 时失败的操作。 在 [StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry) 上有如何正确使用该方法的讨论。
>
>

最后，`UploadDocuments` 方法延迟了两秒钟。 编制索引在 Azure 认知搜索服务中异步进行，因此，示例应用程序需要等待很短时间，以确保文档可用于搜索。 此类延迟通常仅在演示、测试和示例应用程序中是必需的。

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>.NET SDK 如何处理文档
用户可能想知道 Azure 认知搜索 .NET SDK 如何将用户定义的类（如 `Hotel`）的实例上传到索引。 为了帮助回答这个问题，请看 `Hotel` 类：

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsSearchable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.EnLucene)]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("Description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    // SmokingAllowed reflects whether any room in the hotel allows smoking.
    // The JsonIgnore attribute indicates that a field should not be created 
    // in the index for this property and it will only be used by code in the client.
    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? Rating { get; set; }

    public Address Address { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }

    public Room[] Rooms { get; set; }
}
```

首先要注意的是，`Hotel` 类中的每个公共属性的名称将映射到索引定义中同名的字段。 如果你希望每个字段以小写字母开头（“camel 大小写”），可以告知 SDK 使用类中的 `[SerializePropertyNamesAsCamelCase]` 属性自动将属性名称映射为 camel 大小写格式。 这种情况在执行数据绑定的 .NET 应用程序中很常见，其中的目标架构不受应用程序开发人员的控制，且不违反 .NET 中的“Pascal 大小写”命名准则。

> [!NOTE]
> Azure 认知搜索 .NET SDK 使用 [NewtonSoft JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) 库将自定义模型对象序列化为 JSON 和从 JSON 反序列化。 如果需要，可以自定义此序列化。 有关详细信息，请参阅[使用 JSON.NET 的自定义序列](#JsonDotNet)。
> 
> 

第二个要注意的问题是，每个属性使用 `IsFilterable`、`IsSearchable`、`Key` 和 `Analyzer` 等属性进行修饰。 这些属性直接映射到 [Azure 认知搜索索引中的相应字段属性](/rest/api/searchservice/create-index)。 `FieldBuilder` 类使用这些属性来构造索引的字段定义。

有关 `Hotel` 类的第三个重要问题是公共属性的数据类型。 这些属性的 .NET 类型映射到它们在索引定义中的等效字段类型。 例如，`Category` 字符串属性映射到 `Edm.String` 类型的 `category` 字段。 `bool?` 和 `Edm.Boolean`、 `DateTimeOffset?`和 `Edm.DateTimeOffset` 等之间存在类似的类型映射。 [Azure 认知搜索 .NET SDK 参考](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get)中的 `Documents.Get` 方法记录了类型映射的具体规则。 `FieldBuilder` 类会处理此映射，但你最好还是了解此映射，以便在需要排查任何序列化问题时可以下手。

你是否注意到了 `SmokingAllowed` 属性？

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

此属性的 `JsonIgnore` 特性告知 `FieldBuilder` 不要将其序列化为字段形式的索引。  这是创建可在应用程序中用作帮助器的客户端计算属性的极佳方法。  在这种情况下，`SmokingAllowed` 属性将反映 `Rooms` 集合中的任何 `Room` 是否允许吸烟。  如果全部为 false，则表示整个酒店不允许吸烟。

某些属性（例如 `Address` 和 `Rooms`）是 .NET 类的实例。  这些属性表示更复杂的数据结构，因此，需要在索引中使用[复杂数据类型](./search-howto-complex-data-types.md)的字段。

`Address` 属性表示 `Address` 类中的多个值，定义如下：

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Address
    {
        [IsSearchable]
        public string StreetAddress { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string City { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string StateProvince { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string PostalCode { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Country { get; set; }
    }
}
```

此类包含用于描述美国或加拿大地址的标准值。 可以使用这种类型在索引中组合逻辑字段。

`Rooms` 属性表示 `Room` 对象的数组：

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Room
    {
        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string Type { get; set; }

        [IsFilterable, IsFacetable]
        public double? BaseRate { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string BedOptions { get; set; }

        [IsFilterable, IsFacetable]
        public int SleepsCount { get; set; }

        [IsFilterable, IsFacetable]
        public bool? SmokingAllowed { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }
    }
}
```

.NET 中的数据模型及其相应的索引架构在设计上应该能够支持面向最终用户的搜索体验。 .NET 中的每个顶级对象（例如索引中的文档）对应于在用户界面中显示的搜索结果。 例如，在酒店搜索应用程序中，最终用户可能想要按酒店名称、酒店特色或特定客房的特征进行搜索。 稍后我们将介绍一些查询示例。

使用自己的类来与索引中的文档交互的功能可以朝两个方向进行；此外，还可以检索搜索结果，并使用 SDK 自动将结果反序列化为所选类型，我们会在下一节中对此进行介绍。

> [!NOTE]
> Azure 认知搜索 .NET SDK 还使用 `Document` 类支持动态类型化文档，该类是字段名称到字段值的键/值映射。 如果在设计时不知道索引架构，或者绑定到特定模型类不太方便，这很有用。 该 SDK 中处理文档的所有方法都有使用 `Document` 类的重载，以及采用泛型类型参数的强类型重载。 本教程中的示例代码仅使用后者。 [`Document` 类](/dotnet/api/microsoft.azure.search.models.document)继承自 `Dictionary<string, object>`。
> 
>

**为何应使用可为 null 的数据类型**

设计自己的模型类以映射到 Azure 认知搜索索引时，建议将值类型的属性（如 `bool` 和 `int`）声明为可以为 null（例如，`bool?` 而不是 `bool`）。 如果使用不可为 null 属性，必须 **保证** 索引中的所有文档的对应字段都不包含 null 值。 该 SDK 和 Azure 认知搜索服务都不会帮助强制实施此检查。

这不只是假想的问题：假设将新字段添加到 `Edm.Int32` 类型的现有索引。 更新索引定义后，所有文档的该新字段都具有 null 值（因为 Azure 认知搜索中的所有类型都可以为 null）。 如果随后使用该字段具有不可为 null `int` 属性的模型类，则在尝试检索文档时会获得如下所示的 `JsonSerializationException`：

```output
Error converting value {null} to type 'System.Int32'. Path 'IntValue'.
```

由于此原因，最佳做法是建议在模型类中使用可以为 null 的类型。

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>使用 JSON.NET 的自定义序列
SDK 使用 JSON.NET 对文档进行序列化和反序列化。 如果需要，可以通过定义自己的 `JsonConverter` 或 `IContractResolver` 来自定义序列化和反序列化。 有关详细信息，请参阅 [JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。 想要使应用程序中的现有模型类适用于 Azure 认知搜索和其他更高级的方案时，这可能非常有用。 例如，使用自定义序列，可以：

* 包含或排除模型类的某些属性作为文档字段存储。
* 在代码中的属性名称与索引中的字段名称之间进行映射。
* 创建可用于将属性映射到文档字段的自定义属性。

可以在 GitHub 上的 Azure 认知搜索 .NET SDK 的单元测试中找到实现自定义序列化的示例。 一个好的起点是[此文件夹](https://github.com/Azure/azure-sdk-for-net/tree/4f6f4e4c90200c1b0621c4cead302a91e89f2aba/sdk/search/Microsoft.Azure.Search/tests/Tests/Models)。 它包含了自定义序列化测试所用的类。

### <a name="searching-for-documents-in-the-index"></a>在索引中搜索文档
示例应用程序中的最后一步是在索引中搜索一些文档：

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
    SearchParameters parameters;
    DocumentSearchResult<Hotel> results;

    Console.WriteLine("Search the entire index for the term 'motel' and return only the HotelName field:\n");

    parameters =
        new SearchParameters()
        {
            Select = new[] { "HotelName" }
        };

    results = indexClient.Documents.Search<Hotel>("motel", parameters);

    WriteDocuments(results);

    Console.Write("Apply a filter to the index to find hotels with a room cheaper than $100 per night, ");
    Console.WriteLine("and return the hotelId and description:\n");

    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(r: r/BaseRate lt 100)",
            Select = new[] { "HotelId", "Description" }
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    parameters =
        new SearchParameters()
        {
            OrderBy = new[] { "LastRenovationDate desc" },
            Select = new[] { "HotelName", "LastRenovationDate" },
            Top = 2
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.WriteLine("Search the entire index for the term 'hotel':\n");

    parameters = new SearchParameters();
    results = indexClient.Documents.Search<Hotel>("hotel", parameters);

    WriteDocuments(results);
}
```

每次执行查询时，此方法首先创建一个新的 `SearchParameters` 对象。 此对象用于为查询指定其他选项，如排序、筛选、分页和分面。 在此方法中，我们要为不同查询设置 `Filter`、`Select`、`OrderBy` 和 `Top` 属性。 所有 `SearchParameters` 属性在[此处](/dotnet/api/microsoft.azure.search.models.searchparameters)进行了说明。

下一步是实际执行搜索查询。 使用 `Documents.Search` 方法运行搜索。 对于每个查询，我们将要使用的搜索文本（或 `"*"`，如果没有搜索文本）作为字符串传递，还会传递先前创建的搜索参数。 此外，我们指定 `Hotel` 作为 `Documents.Search` 的类型参数，这指示 SDK 将搜索结果中的文档反序列化为类型为 `Hotel` 的对象。

> [!NOTE]
> 可在[此处](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)找到有关搜索查询表达式语法的详细信息。
> 
> 

最后，在每个查询后，该方法循环访问搜索结果中的所有匹配项，将每个文档打印到控制台：

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

让我们依次仔细查看每个查询。 下面是用于执行第一个查询的代码：

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

在本例中，我们将在任何可搜索字段中搜索“motel”一词的整个索引，并且我们只检索 `Select` 参数指定的酒店名称。 结果如下：

```output
Name: Secret Point Motel

Name: Twin Dome Motel
```

下一个查询更有趣一点。  我们想要查找客房价格不超过 100 美元的任何酒店，并仅返回酒店 ID 和说明：

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)",
        Select = new[] { "HotelId", "Description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

此查询使用 OData `$filter` 表达式 `Rooms/any(r: r/BaseRate lt 100)` 来筛选索引中的文档。 这会使用 [any 运算符](./search-query-odata-collection-operators.md)将“BaseRate lt 100”应用到 Rooms 集合中的每个项。 可在[此处](./query-odata-filter-orderby-syntax.md)找到有关 Azure 认知搜索支持的 OData 语法的详细信息。

下面是查询的结果：

```output
HotelId: 1
Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

HotelId: 2
Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...
```

接下来，我们想要查找最近翻修的前两个酒店，并显示酒店名称和上次翻修日期。 代码如下： 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "LastRenovationDate desc" },
        Select = new[] { "HotelName", "LastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

在此示例中，我们再次使用 OData 语法来指定 `OrderBy` 参数作为 `lastRenovationDate desc`。 我们还将 `Top` 设置为 2 以确保仅获取前两个文档。 与以前一样，我们设置 `Select` 以指定应返回的字段。

结果如下：

```output
Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00
```

最后，我们想要查找与“motel”一词匹配的所有酒店名称：

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

以下是结果，它包含所有字段，因为我们未指定 `Select` 属性：

```output
    HotelId: 3
    Name: Triple Landscape Hotel
    ...
```

本教程到此步骤结束，但不要就此打住。 **后续步骤提供了详细了解 Azure 认知搜索的其他资源。

## <a name="next-steps"></a>后续步骤
* 浏览 [.NET SDK](/dotnet/api/microsoft.azure.search) 和 [REST API](/rest/api/searchservice/) 的参考资料。
* 查看[命名约定](/rest/api/searchservice/Naming-rules)，了解命名各种对象的规则。
* 查看 Azure 认知搜索中[受支持的数据类型](/rest/api/searchservice/Supported-data-types)。