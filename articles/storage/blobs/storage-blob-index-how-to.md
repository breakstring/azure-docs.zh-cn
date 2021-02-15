---
title: 使用 blob 索引标记来管理和查找 Azure Blob 存储上的数据
description: 请参阅如何使用 blob 索引标记对 blob 对象进行分类、管理和查询的示例。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/19/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.custom: devx-track-csharp
ms.openlocfilehash: 411815ca2f947c47b8dfb0d2e5d61f8ea18f3545
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95541243"
---
# <a name="use-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>使用 blob 索引标记 (预览) 在 Azure Blob 存储中管理和查找数据

Blob 索引标记使用键-值标记特性对存储帐户中的数据进行分类。 这些标记会自动编制索引，并作为可搜索的多维索引公开，以方便地查找数据。 本文介绍了如何使用 blob 索引标记来设置、获取和查找数据。

> [!NOTE]
> Blob 索引处于公共预览阶段，可在 **加拿大中部**、 **加拿大东部**、 **法国中部** 和 **法国南部** 地区使用。 若要了解有关此功能以及已知问题和限制的详细信息，请参阅 [通过 Blob 索引标记管理和查找 Azure Blob 数据 (预览) ](storage-manage-find-blobs.md)。

## <a name="prerequisites"></a>先决条件

# <a name="portal"></a>[Portal](#tab/azure-portal)

- 已注册并批准访问 blob 索引预览的 Azure 订阅
- 访问 [Azure 门户](https://portal.azure.com/)

# <a name="net"></a>[.NET](#tab/net)

由于 blob 索引处于预览阶段，因此将在预览版 NuGet 源中发布 .NET 存储包。 此库在预览期可能会更改。

1. 将 Visual Studio 项目设置为开始处理适用于 .NET 的 Azure Blob 存储客户端库 v12。 若要了解详细信息，请参阅 [.Net 快速入门](storage-quickstart-blobs-dotnet.md)

2. 在 NuGet 包管理器中，找到 "12.7.0" 包，并安装 **项目的版本**"" 或更高版本 **。** 你还可以运行 PowerShell 命令： `Install-Package Azure.Storage.Blobs -Version 12.7.0-preview.1`

   若要了解如何操作，请参阅[查找并安装包](/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)。

3. 将下列 using 语句添加到你的代码文件的顶部。

    ```csharp
    using Azure;
    using Azure.Storage.Blobs;
    using Azure.Storage.Blobs.Models;
    using Azure.Storage.Blobs.Specialized;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    ```

---

## <a name="upload-a-new-blob-with-index-tags"></a>上传带有索引标记的新 blob

此任务可以由 [存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 或安全主体执行，已 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` 通过自定义 azure 角色向其授予 [Azure 资源提供程序操作](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) 的权限。

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 在 [Azure 门户](https://portal.azure.com/)中，选择你的存储帐户 

2. 导航到 " **Blob 服务**" 下的 "**容器**" 选项，选择容器

3. 选择 " **上传** " 按钮，然后浏览本地文件系统以查找要上传的文件作为块 blob。

4. 展开“高级”下拉列表，并转到“Blob 索引标记”部分

5. 输入要应用于数据的键/值 blob 索引标记

6. 选择“上传”按钮以上传 Blob

:::image type="content" source="media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png" alt-text="演示如何上传具有索引标记的 blob 的 Azure 门户屏幕截图。":::

# <a name="net"></a>[.NET](#tab/net)

下面的示例展示了如何使用在创建过程中设置的标记创建一个追加 blob。

```csharp
static async Task BlobIndexTagsOnCreate()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create an append blob
          AppendBlobClient appendBlobWithTags = container.GetAppendBlobClient("myAppendBlob0.logs");

          // Blob index tags to upload
          AppendBlobCreateOptions appendOptions = new AppendBlobCreateOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Sealed", "false" },
              { "Content", "logs" },
              { "Date", "2020-04-20" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags.CreateAsync(appendOptions);
      }
      finally
      {
      }
   }
```

---

## <a name="get-set-and-update-blob-index-tags"></a>获取、设置和更新 blob 索引标记

获取 blob 索引标记可以由 [存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 或已 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` 通过自定义 azure 角色授予 [Azure 资源提供程序操作](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) 权限的安全主体来执行。

设置和更新 blob 索引标记可以由 [存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 或已 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` 通过自定义 azure 角色授予 [Azure 资源提供程序操作](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) 权限的安全主体来执行。

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 在 [Azure 门户](https://portal.azure.com/)中，选择你的存储帐户 

2. 导航到“Blob 服务”下的“容器”选项，选择你的容器

3. 从所选容器中的 blob 列表中选择 blob

4. Blob 概览选项卡会显示 blob 的属性，包括任何 **Blob 索引标记**

5. 可以获取、设置、修改或删除 blob 的任何键/值索引标记

6. 选择“保存”按钮以确认对 blob 所做的任何更新

:::image type="content" source="media/storage-blob-index-concepts/blob-index-get-set-tags.png" alt-text="显示如何获取、设置、更新和删除 blob 上索引标记的 Azure 门户屏幕截图。":::

# <a name="net"></a>[.NET](#tab/net)

```csharp
static async Task BlobIndexTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create a new append blob
          AppendBlobClient appendBlob = container.GetAppendBlobClient("myAppendBlob1.logs");
          await appendBlob.CreateAsync();

          // Set or update blob index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get blob index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List blobs with all options returned including blob index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing blob index tags by replacing all tags
          Dictionary<string, string> noTags = new Dictionary<string, string>();
          await appendBlob.SetTagsAsync(noTags);

      }
      finally
      {
      }
   }
```

---

## <a name="filter-and-find-data-with-blob-index-tags"></a>筛选和查找包含 blob 索引标记的数据

此任务可以由 [存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 或安全主体执行，已 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` 通过自定义 azure 角色向其授予 [Azure 资源提供程序操作](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) 的权限。

# <a name="portal"></a>[Portal](#tab/azure-portal)

在 Azure 门户中，"blob 索引标记" 筛选器会自动应用 `@container` 参数以限定所选容器的范围。 如果要跨整个存储帐户筛选和查找标记的数据，请使用我们的 REST API、Sdk 或工具。

1. 在 [Azure 门户](https://portal.azure.com/)中，选择存储帐户。 

2. 导航到 " **Blob 服务**" 下的 "**容器**" 选项，选择容器

3. 选择“Blob 索引标记筛选器”按钮，在所选容器内进行筛选

4. 输入 blob 索引标记键和标记值

5. 选择“Blob 索引标记筛选器”按钮，以便添加更多标记筛选器（最多 10 个）

:::image type="content" source="media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png" alt-text="显示如何使用索引标记筛选和查找带标记的 blob 的 Azure 门户屏幕截图":::

# <a name="net"></a>[.NET](#tab/net)

```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      // Blob index queries and selection
      String singleEqualityQuery = @"""Archive"" = 'false'";
      String andQuery = @"""Archive"" = 'false' AND ""Priority"" = '01'";
      String rangeQuery = @"""Date"" >= '2020-04-20' AND ""Date"" <= '2020-04-30'";
      String containerScopedQuery = @"@container = 'mycontainer' AND ""Archive"" = 'false'";

      String queryToUse = containerScopedQuery;

      try
      {
          // Create a container
          await container1.CreateIfNotExistsAsync();
          await container2.CreateIfNotExistsAsync();

          // Create append blobs
          AppendBlobClient appendBlobWithTags0 = container1.GetAppendBlobClient("myAppendBlob00.logs");
          AppendBlobClient appendBlobWithTags1 = container1.GetAppendBlobClient("myAppendBlob01.logs");
          AppendBlobClient appendBlobWithTags2 = container1.GetAppendBlobClient("myAppendBlob02.logs");
          AppendBlobClient appendBlobWithTags3 = container2.GetAppendBlobClient("myAppendBlob03.logs");
          AppendBlobClient appendBlobWithTags4 = container2.GetAppendBlobClient("myAppendBlob04.logs");
          AppendBlobClient appendBlobWithTags5 = container2.GetAppendBlobClient("myAppendBlob05.logs");
           
          // Blob index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Archive", "false" },
              { "Priority", "01" },
              { "Date", "2020-04-20" }
          };
          
          CreateAppendBlobOptions appendOptions2 = new CreateAppendBlobOptions();
          appendOptions2.Tags = new Dictionary<string, string>
          {
              { "Archive", "true" },
              { "Priority", "02" },
              { "Date", "2020-04-24" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags0.CreateAsync(appendOptions);
          await appendBlobWithTags1.CreateAsync(appendOptions);
          await appendBlobWithTags2.CreateAsync(appendOptions2);
          await appendBlobWithTags3.CreateAsync(appendOptions);
          await appendBlobWithTags4.CreateAsync(appendOptions2);
          await appendBlobWithTags5.CreateAsync(appendOptions2);

          // Find Blobs given a tags query
          Console.WriteLine("Find Blob by Tags query: " + queryToUse + Environment.NewLine);

          List<TaggedBlobItem> blobs = new List<TaggedBlobItem>();
          await foreach (TaggedBlobItem taggedBlobItem in serviceClient.FindBlobsByTagsAsync(queryToUse))
          {
              blobs.Add(taggedBlobItem);
          }

          foreach (var filteredBlob in blobs)
          {
              Console.WriteLine($"BlobIndex result: ContainerName= {filteredBlob.ContainerName}, " +
                  $"BlobName= {filteredBlob.Name}");
          }

      }
      finally
      {
      }
   }
```

---

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>使用 blob 索引标记筛选器进行生命周期管理

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 在 [Azure 门户](https://portal.azure.com/)中，选择你的存储帐户。 

2. 导航到“Blob 服务”下的“生命周期管理”选项 

3. 选择“添加规则”，然后填写操作集窗体字段

4. 选择 " **筛选器** 集"，为前缀匹配和 blob 索引匹配添加可选筛选器

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png" alt-text="显示如何为生命周期管理添加索引标记的 Azure 门户屏幕截图。":::

5. 选择 "查看" " **+ 添加** " 以查看规则设置

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png" alt-text="显示包含 blob 索引标记的生命周期管理规则筛选器示例的 Azure 门户屏幕截图":::

6. 选择“添加”，将新规则应用于生命周期管理策略

# <a name="net"></a>[.NET](#tab/net)

[生命周期管理](storage-lifecycle-management-concepts.md)策略在控制平面级别应用于每个存储帐户。 对于 .NET，安装 [Microsoft Azure 管理存储库](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) 版本16.0.0 或更高版本。

---

## <a name="next-steps"></a>后续步骤

 - 了解有关 blob 索引标记的详细信息，请参阅 [通过 blob 索引标记管理和查找 Azure blob 数据 (预览) ](storage-manage-find-blobs.md )
 - 了解有关生命周期管理的详细信息，请参阅 [管理 Azure Blob 存储生命周期](storage-lifecycle-management-concepts.md)