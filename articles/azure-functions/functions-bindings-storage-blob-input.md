---
title: Azure Functions 的 Azure Blob 存储输入绑定
description: 了解如何向某个 Azure 函数提供 Azure Blob 存储输入绑定数据。
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 678b6f02245e463870fc5b2c4bfc8b5ffa50de60
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381677"
---
# <a name="azure-blob-storage-input-binding-for-azure-functions"></a>Azure Functions 的 Azure Blob 存储输入绑定

通过输入绑定，可读取 blob 存储数据作为 Azure Functions 的输入。

若要了解设置和配置详细信息，请参阅[概述](./functions-bindings-storage-blob.md)。

## <a name="example"></a>示例

# <a name="c"></a>[C#](#tab/csharp)

以下示例演示使用一个队列触发器和一个 blob 输入绑定的 [C# 函数](functions-dotnet-class-library.md)。 队列消息包含该 blob 的名称，函数记录该 blob 的大小。

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

<!--Same example for input and output. -->

以下示例演示 function.json  文件中的 blob 输入和输出绑定，以及使用这些绑定的 [C# 脚本 (.csx)](functions-reference-csharp.md) 代码。 此函数创建文本 blob 的副本。 该函数由包含要复制的 Blob 名称的队列消息触发。 新 Blob 名为 *{originalblobname}-Copy*。

在 *function.json* 文件中，`queueTrigger` 元数据属性用于指定 `path` 属性中的 Blob 名称：

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[配置](#configuration)部分解释了这些属性。

C# 脚本代码如下所示：

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="java"></a>[Java](#tab/java)

本部分包含以下示例：

* [HTTP 触发器，使用查询字符串查找 blob 名称](#http-trigger-look-up-blob-name-from-query-string)
* [队列触发器，接收来自队列消息的 blob 名称](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>HTTP 触发器，使用查询字符串查找 blob 名称

 下面的示例显示了一个 Java 函数，该函数使用 `HttpTrigger` 注释来接收包含 blob 存储容器中某个文件的名称的一个参数。 然后，`BlobInput` 注释读取该文件并将其作为 `byte[]` 传递给该函数。

```java
  @FunctionName("getBlobSizeHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage blobSize(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    final ExecutionContext context) {
      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>队列触发器，接收来自队列消息的 blob 名称

 下面的示例显示了一个 Java 函数，该函数使用 `QueueTrigger` 注释来接收包含 blob 存储容器中某个文件的名称的一个消息。 然后，`BlobInput` 注释读取该文件并将其作为 `byte[]` 传递给该函数。

```java
  @FunctionName("getBlobSize")
  @StorageAccount("Storage_Account_Connection_String")
  public void blobSize(
    @QueueTrigger(
      name = "filename", 
      queueName = "myqueue-items-sample") 
    String filename,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{queueTrigger}") 
    byte[] content,
    final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
  }
```

在 [Java 函数运行时库](/java/api/overview/azure/functions/runtime)中，对其值将来自 Blob 的参数使用 `@BlobInput` 注释。  可以将此注释与本机 Java 类型、POJO 或使用了 `Optional<T>` 的可为 null 的值一起使用。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

下面的示例展示了 function.json  文件中的 blob 输入和输出绑定，以及使用这些绑定的 [JavaScript 代码](functions-reference-node.md)。 该函数创建 Blob 的副本。 该函数由包含要复制的 Blob 名称的队列消息触发。 新 Blob 名为 *{originalblobname}-Copy*。

在 *function.json* 文件中，`queueTrigger` 元数据属性用于指定 `path` 属性中的 Blob 名称：

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[配置](#configuration)部分解释了这些属性。

JavaScript 代码如下所示：

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

以下示例显示了 function.json 文件中定义的 Blob 输入绑定，该绑定使传入的 blob 数据可供 [PowerShell](functions-reference-powershell.md) 函数使用。

下面是 json 配置：

```json
{
  "bindings": [
    {
      "name": "InputBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "source/{name}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

下面是函数代码：

```powershell
# Input bindings are passed in via param block.
param([byte[]] $InputBlob, $TriggerMetadata)

Write-Host "PowerShell Blob trigger: Name: $($TriggerMetadata.Name) Size: $($InputBlob.Length) bytes"
```

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

下面的示例展示了 *function.json* 文件中的 blob 输入和输出绑定，以及使用这些绑定的 [Python 代码](functions-reference-python.md)。 该函数创建 Blob 的副本。 该函数由包含要复制的 Blob 名称的队列消息触发。 新 Blob 名为 *{originalblobname}-Copy*。

在 *function.json* 文件中，`queueTrigger` 元数据属性用于指定 `path` 属性中的 Blob 名称：

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "dataType": "binary",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

[配置](#configuration)部分解释了这些属性。

`dataType`属性确定所使用的绑定。 以下值可用于支持不同的绑定策略：

| 绑定值 | 默认 | 说明 | 示例 |
| --- | --- | --- | --- |
| `undefined` | Y | 使用丰富绑定 | `def main(input: func.InputStream)` |
| `string` | N | 使用泛型绑定，并将输入类型强制转换为 `string` | `def main(input: str)` |
| `binary` | N | 使用泛型绑定并将输入 blob 强制转换为 `bytes` Python 对象 | `def main(input: bytes)` |

下面是 Python 代码：

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

---

## <a name="attributes-and-annotations"></a>特性和注释

# <a name="c"></a>[C#](#tab/csharp)

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)。

该特性的构造函数采用 Blob 的路径，以及一个表示读取或写入的 `FileAccess` 参数，如以下示例中所示：

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

可以设置 `Connection` 属性来指定要使用的存储帐户，如以下示例中所示：

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

可以使用 `StorageAccount` 特性在类、方法或参数级别指定存储帐户。 有关详细信息，请参阅[触发器 - 特性和注释](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations)。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="java"></a>[Java](#tab/java)

使用 `@BlobInput` 特性可以访问触发函数的 blob。 如果将字节数组与特性一起使用，请将 `dataType` 设置为 `binary`。 有关详细信息，请参阅[输入示例](#example)。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell 不支持特性。

# <a name="python"></a>[Python](#tab/python)

Python 不支持特性。

---

## <a name="configuration"></a>配置

下表解释了在 function.json  文件和 `Blob` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|type  | 不适用 | 必须设置为 `blob`。 |
|**direction** | 不适用 | 必须设置为 `in`。 [用法](#usage)部分中已阐述异常。 |
|name  | 不适用 | 表示函数代码中的 Blob 的变量的名称。|
|**路径** |**BlobPath** | Blob 的路径。 |
|连接  |**Connection**| 包含要用于此绑定的[存储连接字符串](../storage/common/storage-configure-connection-string.md)的应用设置的名称。 如果应用设置名称以“AzureWebJobs”开始，则只能在此处指定该名称的余下部分。 例如，如果将 `connection` 设置为“MyStorage”，则 Functions 运行时会查找名为“AzureWebJobsMyStorage”的应用设置。 如果将 `connection` 留空，函数运行时将使用名为 `AzureWebJobsStorage` 的应用设置中的默认存储连接字符串。<br><br>连接字符串必须属于某个常规用途存储帐户，而不能属于[仅限 Blob 的存储帐户](../storage/common/storage-account-overview.md#types-of-storage-accounts)。<br><br>如果你使用的是 [版本 5. x 或更高版本](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)，而不是连接字符串，则可以提供对定义该连接的配置节的引用。 请参阅 [连接](./functions-reference.md#connections)。|
|**dataType**| 不适用 | 对于动态类型化语言，指定基础数据类型。 可能的值为 `string`、`binary` 或 `stream`。 有关更多详细信息，请参阅 [触发器和绑定概念](functions-triggers-bindings.md?tabs=python#trigger-and-binding-definitions)。 |
|不适用 | **访问** | 表示是要读取还是写入。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用情况

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="java"></a>[Java](#tab/java)

使用 `@BlobInput` 特性可以访问触发函数的 blob。 如果将字节数组与特性一起使用，请将 `dataType` 设置为 `binary`。 有关详细信息，请参阅[输入示例](#example)。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

使用 `context.bindings.<NAME>` 访问 blob 数据，其中 `<NAME>` 与 function.json 中定义的值匹配。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

通过与 function.json 文件中绑定名称参数指定的名称匹配的字符串参数访问 Blob 数据。

# <a name="python"></a>[Python](#tab/python)

通过类型为 [InputStream](/python/api/azure-functions/azure.functions.inputstream?view=azure-python&preserve-view=true)的参数访问 blob 数据。 有关详细信息，请参阅[输入示例](#example)。

---

## <a name="next-steps"></a>后续步骤

- [在 blob 存储数据更改时运行函数](./functions-bindings-storage-blob-trigger.md)
- [通过函数写入 blob 存储数据](./functions-bindings-storage-blob-output.md)
