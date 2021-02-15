---
title: 快速入门：在 .NET 中创建搜索索引
titleSuffix: Azure Cognitive Search
description: 此 C# 快速入门介绍如何使用 Azure.Search.Documents 客户端库创建索引、加载数据以及运行查询。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f0d912d5b14932c43d109f8f955d5f16381cf773
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2021
ms.locfileid: "98180092"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>快速入门：使用 Azure.Search.Documents 客户端库创建搜索索引

使用新的 [Azure.Search.Documents（版本 11）客户端库](/dotnet/api/overview/azure/search.documents-readme)以 C# 创建 .NET Core 控制台应用程序，用于创建、加载和查询搜索索引。

可以[下载源代码](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11)，从一个已完成的项目着手，或按照本文中的步骤操作，创建自己的项目。

> [!NOTE]
> 正在查找早期版本？ 请转而参阅[使用 Microsoft.Azure.Search v10 创建搜索索引](search-get-started-dotnet-v10.md)。

## <a name="prerequisites"></a>先决条件

开始之前，需具备以下工具和服务：

+ 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/)。

+ Azure 认知搜索服务。 [创建服务](search-create-service-portal.md)或[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 可以使用本快速入门的免费服务。 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)（版本不限）。 示例代码已在 Visual Studio 2019 的免费社区版上进行了测试。

设置项目时，将下载 [Azure.Search.Documents NuGet 包](https://www.nuget.org/packages/Azure.Search.Documents/)。

用于 .NET 的 Azure SDK 符合 [.NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support)，这意味着 .NET Framework 4.6.1 和 .NET Core 2.0 是最低要求。

## <a name="set-up-your-project"></a>设置项目

汇总服务连接信息，然后启动 Visual Studio 以新建可在 .NET Core 上运行的控制台应用项目。

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>复制密钥和终结点

对服务的调用要求每个请求都有一个 URL 终结点和一个访问密钥。 第一步，找到要添加到项目中的 API 密钥和 URL。 在稍后的步骤中创建客户端时，将指定这两个值。

1. [登录到 Azure 门户](https://portal.azure.com/)，在搜索服务的“概述”页中获取 URL。 示例终结点可能类似于 `https://mydemo.search.windows.net`。

2. 在“设置” > “密钥”中，获取管理员密钥以获得针对服务的完全权限，需要该密钥才可创建或删除对象 。 有两个可互换的主要密钥和辅助密钥。 可以使用其中任意一个。

   ![获取 HTTP 终结点和访问密钥](media/search-get-started-rest/get-url-key.png "获取 HTTP 终结点和访问密钥")

所有请求对发送到服务的每个请求都需要 API 密钥。 具有有效的密钥可以在发送请求的应用程序与处理请求的服务之间建立信任关系，这种信任关系以每个请求为基础。

### <a name="install-the-nuget-package"></a>安装 NuGet 包

项目创建后，添加客户端库。 [Azure.Search.Documents 包](https://www.nuget.org/packages/Azure.Search.Documents/)包含一个客户端库，其中提供了用于与 .NET 中的搜索服务一起使用的所有 API。

1. 启动 Visual Studio 并创建 .NET Core 控制台应用程序。

1. 在“工具” > “NuGet 包管理器”中，选择“管理解决方案的 NuGet 包...”  。 

1. 单击“浏览”。

1. 搜索 `Azure.Search.Documents`，并选择 11.0 或更高版本。

1. 单击右侧的“安装”，将该程序集添加到你的项目和解决方案。

### <a name="create-a-search-client"></a>创建搜索客户端

1. 在“Program.cs”中，将命名空间更改为 `AzureSearch.SDK.Quickstart.v11`，然后添加以下 `using` 指令。

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. 创建两个客户端：[SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) 创建索引，[SearchClient](/dotnet/api/azure.search.documents.searchclient) 加载并查询现有索引。 两者都需要服务终结点和管理员 API 密钥才能使用创建/删除权限进行身份验证。

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

        // Create a SearchIndexClient to send create/delete index commands
        Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
        AzureKeyCredential credential = new AzureKeyCredential(apiKey);
        SearchIndexClient adminClient = new SearchIndexClient(serviceEndpoint, credential);

        // Create a SearchClient to load and query documents
        SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1 - 创建索引

本快速入门生成 Hotels 索引，你将在其中加载酒店数据并对其执行查询。 在此步骤中，定义索引中的字段。 每个字段定义都包含名称、数据类型以及确定如何使用该字段的属性。

在此示例中，为了简单和可读性，使用了 Azure.Search.Documents 库的同步方法。 但是，对于生产场景，应使用异步方法来保持应用程序的可缩放性和响应性。 例如，使用 [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync)，而不是 [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex)。

1. 向项目添加一个空的类定义：Hotel.cs

1. 将以下代码复制到 Hotel.cs 以定义酒店文档的结构。 该字段的特性决定字段在应用程序中的使用方式。 例如，`IsFilterable` 属性必须分配给每个支持筛选表达式的字段。

    ```csharp
    using System;
    using System.Text.Json.Serialization;
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;

    namespace AzureSearch.Quickstart
    {
        public partial class Hotel
        {
            [SimpleField(IsKey = true, IsFilterable = true)]
            public string HotelId { get; set; }

            [SearchableField(IsSortable = true)]
            public string HotelName { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Category { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public bool? ParkingIncluded { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public double? Rating { get; set; }

            [SearchableField]
            public Address Address { get; set; }
        }
    }
    ```

   在 Azure.Search.Documents 客户端库中，可以使用 [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) 和 [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield) 来简化字段定义。 两者都是 [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) 的派生形式，可能会简化你的代码：

   + `SimpleField` 可以是任何数据类型，始终不可搜索（全文搜索查询将忽略它），并且可检索（未隐藏）。 其他属性默认情况下处于关闭状态，但可以启用。 你可能会将 `SimpleField` 用于仅在筛选器、facet 或计分概要文件中使用的文档 ID 或字段。 如果是这样，请确保应用该方案所需的所有属性，例如为文档 ID 应用 `IsKey = true`。 有关详细信息，请参阅源代码中的 [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs)。

   + `SearchableField` 必须是字符串，并且始终可搜索、可检索。 其他属性默认情况下处于关闭状态，但可以启用。 因为此字段类型是可搜索的，所以它支持同义词和分析器属性的完整补集。 有关详细信息，请参阅源代码中的 [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs)。

   无论使用基本 `SearchField` API 还是任一帮助程序模型，都必须显式启用筛选器、facet 和排序属性。 例如，[IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable)、[IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable) 和 [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) 必须进行显式属性化，如上例中所示。 

1. 向项目添加第二个空的类定义：Address.cs。  将以下代码复制到类中。

   ```csharp
   using Azure.Search.Documents.Indexes;

    namespace AzureSearch.Quickstart
    {
        public partial class Address
        {
            [SearchableField(IsFilterable = true)]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
   ```

1. 再创建两个类：ToString() 的 Hotel.Methods.cs 和 Address.Methods.cs 将重写 。 这些类用于在控制台输出中呈现搜索结果。  本文未提供这些类的内容，但可以从 [GitHub 中的文件](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11/AzureSearchQuickstart-v11)里复制代码。

1. 在 Program.cs 中，创建一个 [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) 对象，然后调用 [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) 方法来表示搜索服务中的索引。 此索引还包括一个 [SearchSuggester](/dotnet/api/azure.search.documents.indexes.models.searchsuggester) 以便在指定字段上启用自动完成。

   ```csharp
    // Create hotels-quickstart index
    private static void CreateIndex(string indexName, SearchIndexClient adminClient)
    {
        FieldBuilder fieldBuilder = new FieldBuilder();
        var searchFields = fieldBuilder.Build(typeof(Hotel));

        var definition = new SearchIndex(indexName, searchFields);

        var suggester = new SearchSuggester("sg", new[] { "HotelName", "Category", "Address/City", "Address/StateProvince" });
        definition.Suggesters.Add(suggester);

        adminClient.CreateOrUpdateIndex(definition);
    }
   ```

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - 加载文档

Azure 认知搜索对存储在服务中的内容进行搜索。 在此步骤中，加载符合刚刚创建的酒店索引的 JSON 文档。

在 Azure 认知搜索中，搜索文档这一数据结构既是索引输入，也是查询输出。 文档输入从外部数据源获取，可能是数据库中的行、Blob 存储中的 blob 或磁盘上的 JSON 文档。 在此示例中，我们采用了快捷方式，并在代码本身中嵌入了四个酒店的 JSON 文档。 

上传文档时，必须使用 [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) 对象。 `IndexDocumentsBatch` 对象包含[操作](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions)集合，其中每个操作均包含一个文档和一个属性，该属性用于指示 Azure 认知搜索要执行什么操作（[upload、merge、delete 和 mergeOrUpload](search-what-is-data-import.md#indexing-actions)）。

1. 在 Program.cs 中，创建文档和索引操作的数组，然后将该数组传递给 `IndexDocumentsBatch`。 以下文档符合 hotel 类定义的 hotels-quickstart 索引。

    ```csharp
    // Upload documents in a single Upload request.
    private static void UploadDocuments(SearchClient searchClient)
    {
        IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "1",
                    HotelName = "Secret Point Motel",
                    Description = "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
                    DescriptionFr = "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "air conditioning", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1970, 1, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.6,
                    Address = new Address()
                    {
                        StreetAddress = "677 5th Ave",
                        City = "New York",
                        StateProvince = "NY",
                        PostalCode = "10022",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "3",
                    HotelName = "Triple Landscape Hotel",
                    Description = "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Resort and Spa",
                    Tags = new[] { "air conditioning", "bar", "continental breakfast" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(2015, 9, 20, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.80,
                    Address = new Address()
                    {
                        StreetAddress = "3393 Peachtree Rd",
                        City = "Atlanta",
                        StateProvince = "GA",
                        PostalCode = "30326",
                        Country = "USA"
                    }
                }),
            IndexDocumentsAction.Upload(
                new Hotel()
                {
                    HotelId = "4",
                    HotelName = "Sublime Cliff Hotel",
                    Description = "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
                    DescriptionFr = "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
                    Category = "Boutique",
                    Tags = new[] { "concierge", "view", "24-hour front desk service" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1960, 2, 06, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.60,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                })
            );

        try
        {
            IndexDocumentsResult result = searchClient.IndexDocuments(batch);
        }
        catch (Exception)
        {
            // If for some reason any documents are dropped during indexing, you can compensate by delaying and
            // retrying. This simple demo just logs the failed document keys and continues.
            Console.WriteLine("Failed to index some of the documents: {0}");
        }
    }
    ```

    初始化 [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) 对象后，可以通过对 [SearchClient](/dotnet/api/azure.search.documents.searchclient) 对象调用 [IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments) 将其发送到索引。

1. 将以下代码行添加到 Main()。 加载文档是使用 SearchClient 完成的，但此操作还需要对服务的管理员权限，通常与 SearchIndexClient 相关联。 设置此操作的一种方法是通过 SearchIndexClient （在本示例中为 adminClient）获取 SearchClient。

   ```csharp
    SearchClient ingesterClient = adminClient.GetSearchClient(indexName);

    // Load documents
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(ingesterClient);
   ```

1. 由于这是一个按顺序运行所有命令的控制台应用，因此请在索引和查询之间添加 2 秒的等待时间。

    ```csharp
    // Wait 2 seconds for indexing to complete before starting queries (for demo and console-app purposes only)
    Console.WriteLine("Waiting for indexing...\n");
    System.Threading.Thread.Sleep(2000);
    ```

    2 秒的延迟可对索引编制进行补偿（这是异步操作），这样可在执行查询之前对所有文档编制索引。 以延迟方式编写代码通常仅在演示、测试和示例应用程序中是必要的。

## <a name="3---search-an-index"></a>3 - 搜索索引

对第一个文档编制索引后，可立即获取查询结果，但索引的实际测试应等到对所有文档编制索引后进行。

此部分添加了两个功能：查询逻辑和结果。 对于查询，请使用 [Search](/dotnet/api/azure.search.documents.searchclient.search) 方法。 此方法采用搜索文本（查询字符串）以及其他[选项](/dotnet/api/azure.search.documents.searchoptions)。

[SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1) 类表示结果。

1. 在 Program.cs 中，创建 WriteDocuments 方法，该方法用于将搜索结果输出到控制台 。

    ```csharp
    // Write search results to console
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> result in searchResults.GetResults())
        {
            Console.WriteLine(result.Document);
        }

        Console.WriteLine();
    }
    ```

1. 创建 RunQueries 方法，该方法用于执行查询并返回结果。 结果是 Hotel 对象。 此示例显示了方法签名和第一个查询。 此查询演示了 Select 参数，通过该参数可以使用文档中的选定字段来编写结果。

    ```csharp
    // Run queries, use WriteDocuments to print output
    private static void RunQueries(SearchClient srchclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on empty term '*' to return all documents, showing a subset of fields...\n");

        options = new SearchOptions()
        {
            IncludeTotalCount = true,
            Filter = "",
            OrderBy = { "" }
        };

        options.Select.Add("HotelId");
        options.Select.Add("HotelName");
        options.Select.Add("Address/City");

        response = srchclient.Search<Hotel>("*", options);
        WriteDocuments(response);
    ```

1. 在第二个查询中，搜索某个术语，添加筛选器（用于选择评级大于 4 的文档），然后按评级降序排序。 筛选器是布尔表达式，该表达式通过索引中的 [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) 字段求值。 筛选器查询包括或排除值。 同样，筛选器查询没有关联的相关性分数。 

    ```csharp
    Console.WriteLine("Query #2: Search on 'hotels', filter on 'Rating gt 4', sort by Rating in descending order...\n");

    options = new SearchOptions()
    {
        Filter = "Rating gt 4",
        OrderBy = { "Rating desc" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Rating");

    response = srchclient.Search<Hotel>("hotels", options);
    WriteDocuments(response);
    ```

1. 第三个查询演示了 searchFields，可用于将全文搜索操作的作用域限定为特定字段。

    ```csharp
    Console.WriteLine("Query #3: Limit search to specific fields (pool in Tags field)...\n");

    options = new SearchOptions()
    {
        SearchFields = { "Tags" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Tags");

    response = srchclient.Search<Hotel>("pool", options);
    WriteDocuments(response);
    ```

1. 第四个查询演示了 Facet，可用于构建分面导航结构。 

   ```csharp
    Console.WriteLine("Query #4: Facet on 'Category'...\n");

    options = new SearchOptions()
    {
        Filter = ""
    };

    options.Facets.Add("Category");

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Category");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
   ```

1. 在第五个查询中，返回一个特定文档。 文档查找是对结果集中的 OnClick 事件的典型响应。

   ```csharp
    Console.WriteLine("Query #5: Look up a specific document...\n");

    Response<Hotel> lookupResponse;
    lookupResponse = srchclient.GetDocument<Hotel>("3");

    Console.WriteLine(lookupResponse.Value.HotelId);
   ```

1. 最后一个查询显示了“自动完成”的语法，它模拟部分用户输入，即“sa”，该输入解析为 sourceFields 中的两个可能的匹配项，与你在索引中定义的建议器相关联。

   ```csharp
    Console.WriteLine("Query #6: Call Autocomplete on HotelName that starts with 'sa'...\n");

    var autoresponse = srchclient.Autocomplete("sa", "sg");
    WriteDocuments(autoresponse);
   ```

1. 将 RunQueries 添加到 Main()。

    ```csharp
    // Call the RunQueries method to invoke a series of queries
    Console.WriteLine("Starting queries...\n");
    RunQueries(srchclient);

    // End the program
    Console.WriteLine("{0}", "Complete. Press any key to end this program...\n");
    Console.ReadKey();
    ```

前面的查询显示了[在查询中匹配术语的多种方式](search-query-overview.md#types-of-queries)：全文搜索、筛选器和自动完成。

全文搜索和筛选器是使用 [SearchClient.Search](/dotnet/api/azure.search.documents.searchclient.search) 方法执行的。 搜索查询可在 `searchText` 字符串中传递，而筛选表达式则可在 [SearchOptions](/dotnet/api/azure.search.documents.searchoptions) 类的 [Filter](/dotnet/api/azure.search.documents.searchoptions.filter) 属性中传递。 若要筛选但不搜索，只需传递 `"*"` 作为 [Search](/dotnet/api/azure.search.documents.searchclient.search) 方法的 `searchText` 参数。 若要搜索但不筛选，则不设置 `Filter` 属性，或不在 `SearchOptions` 实例中传递。

## <a name="run-the-program"></a>运行程序

按 F5 可重新生成应用并完整运行该程序。 

输出包含 [Console.WriteLine](/dotnet/api/system.console.writeline) 中的消息，并添加了查询信息和结果。

## <a name="clean-up-resources"></a>清理资源

在自己的订阅中操作时，最好在项目结束时确定是否仍需要已创建的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

可以使用左侧导航窗格中的“所有资源”或“资源组”链接 ，在门户中查找和管理资源。

如果使用的是免费服务，请记住只能设置三个索引、索引器和数据源。 可以在门户中删除单个项目，以不超出此限制。 

## <a name="next-steps"></a>后续步骤

在此 C# 快速入门中，你已完成一系列任务，即创建索引、使用文档加载索引并运行查询。 在不同的阶段，我们采用快捷方式来简化代码，从而实现可读性和可理解性。 如果你已理解这些基本概念，我们建议阅读下一篇文章，探索替代方法和概念，进一步加深你的理解。 

> [!div class="nextstepaction"]
> [如何在 .NET 中开发](search-howto-dotnet-sdk.md)

希望优化并节省云支出？

> [!div class="nextstepaction"]
> [使用成本管理开始分析成本](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
