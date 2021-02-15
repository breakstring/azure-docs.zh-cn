---
title: Microsoft Spark 实用工具简介
description: 教程： Azure Synapse Analytics 笔记本中的 MSSparkutils
author: ruixinxu
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: spark
ms.date: 09/10/2020
ms.author: ruxu
ms.reviewer: ''
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: 58672bd68d9a2ea85f58b3761f3b89098b9f5afc
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368653"
---
# <a name="introduction-to-microsoft-spark-utilities"></a>Microsoft Spark 实用工具简介

Microsoft Spark 实用工具 (MSSparkUtils) 是一个内置包，可帮助你轻松执行常见任务。 你可以使用 MSSparkUtils 来处理文件系统、获取环境变量以及使用机密。 MSSparkUtils 在 `PySpark (Python)` 、 `Scala` 和 `.NET Spark (C#)` 笔记本和 Synapse 管道中可用。

## <a name="pre-requisites"></a>先决条件

### <a name="configure-access-to-azure-data-lake-storage-gen2"></a>配置对 Azure Data Lake Storage Gen2 的访问权限 

Synapse 笔记本使用 Azure active directory (Azure AD) 传递来访问 ADLS Gen2 帐户。 你需要是 **Blob 存储数据参与者** 才能访问 ADLS Gen2 帐户 (或) 文件夹。 

Synapse 管道使用工作区标识 (MSI) 来访问存储帐户。 若要在管道活动中使用 MSSparkUtils，你的工作区标识需要是 **Blob 存储数据参与者** ，才能访问 (或文件夹) 的 ADLS Gen2 帐户。

请按照以下步骤操作，确保 Azure AD 和工作区 MSI 可以访问 ADLS Gen2 帐户：
1. 打开 [Azure 门户](https://portal.azure.com/) 和要访问的存储帐户。 可以导航到要访问的特定容器。
2. 从左侧面板中选择 " **访问控制 (IAM")** 。
3. 将 **Azure AD 帐户** 和 **工作区标识** (与你的工作区名称相同) 到存储帐户上的 " **存储 Blob 数据参与者** " 角色（如果尚未分配）。 
4. 选择“保存”。

可以通过以下 URL 访问 Synapse Spark ADLS Gen2 上的数据：

<code>abfss://<container_name>@<storage_account_name>.dfs.core.windows.net/<path></code>

### <a name="configure-access-to-azure-blob-storage"></a>配置对 Azure Blob 存储的访问权限  

Synapse 利用 **共享访问签名 (SAS)** 访问 Azure Blob 存储。 若要避免在代码中公开 SAS 密钥，我们建议在 Synapse 工作区中创建一个新的链接服务，并将其连接到要访问的 Azure Blob 存储帐户。

按照以下步骤为 Azure Blob 存储帐户添加新的链接服务：

1. 打开 [Azure Synapse Studio](https://web.azuresynapse.net/)。
2. 从左侧面板中选择 "**管理**"，并选择 "**外部连接**" 下的 "**链接服务**"。
3. 在右侧的 "**新建链接服务**" 面板中搜索 " **Azure Blob 存储**"。
4. 选择“继续”。
5. 选择要访问的 Azure Blob 存储帐户，并配置链接服务名称。 建议使用 **身份验证方法** 的 **帐户密钥**。
6. 选择 " **测试连接** " 以验证设置是否正确。
7. 依次选择 " **创建** " 和 " **全部发布** " 以保存所做的更改。 

可以通过以下 URL 通过 Synapse Spark 访问 Azure Blob 存储上的数据：

<code>wasb[s]://<container_name>@<storage_account_name>.blob.core.windows.net/<path></code>

下面是一个代码示例：

:::zone pivot = "programming-language-python"

```python
from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val blob_account_name = "" // replace with your blob name
val blob_container_name = "" //replace with your container name
val blob_relative_path = "/" //replace with your relative folder path
val linked_service_name = "" //replace with your linked service name


val blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

val wasbs_path = f"wasbs://$blob_container_name@$blob_account_name.blob.core.windows.net/$blob_relative_path"
spark.conf.set(f"fs.azure.sas.$blob_container_name.$blob_account_name.blob.core.windows.net",blob_sas_token)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var blob_account_name = "";  // replace with your blob name
var blob_container_name = "";     // replace with your container name
var blob_relative_path = "";  // replace with your relative folder path
var linked_service_name = "";    // replace with your linked service name
var blob_sas_token = Credentials.GetConnectionStringOrCreds(linked_service_name);

spark.SparkContext.GetConf().Set($"fs.azure.sas.{blob_container_name}.{blob_account_name}.blob.core.windows.net", blob_sas_token);

var wasbs_path = $"wasbs://{blob_container_name}@{blob_account_name}.blob.core.windows.net/{blob_relative_path}";

Console.WriteLine(wasbs_path);

```

::: zone-end 
 
###  <a name="configure-access-to-azure-key-vault"></a>配置对 Azure Key Vault 的访问权限

你可以添加一个 Azure Key Vault 作为链接服务，以在 Synapse 中管理你的凭据。 按照以下步骤将 Azure Key Vault 添加为 Synapse 链接服务：
1. 打开 [Azure Synapse Studio](https://web.azuresynapse.net/)。
2. 从左侧面板中选择 "**管理**"，并选择 "**外部连接**" 下的 "**链接服务**"。
3. 在右侧的 "**新建链接服务**" 面板中搜索 **Azure Key Vault** 。
4. 选择要访问的 Azure Key Vault 帐户，并配置链接服务名称。
5. 选择 " **测试连接** " 以验证设置是否正确。
6. 选择 "首先 **创建** "，然后单击 " **全部发布** " 以保存更改。 

Synapse 笔记本使用 Azure active directory (Azure AD) 传递来访问 Azure Key Vault。 Synapse 管道使用工作区标识 (MSI) 访问 Azure Key Vault。 若要确保你的代码在笔记本和 Synapse 管道中都能正常工作，我们建议为你的 Azure AD 帐户和工作区标识授予机密访问权限。

请按照以下步骤授予对工作区标识的密钥访问权限：
1. 打开要访问的 [Azure 门户](https://portal.azure.com/) 和 Azure Key Vault。 
2. 从左侧面板中选择 " **访问策略** "。
3. 选择 " **添加访问策略**"： 
    - 选择 **"密钥"、"机密" 和 "将证书管理 &** 为配置模板"。
    - 选择 **Azure AD 帐户** 和 **工作区标识** (与选择主体中) 的工作区名称相同，或者确保已分配此帐户。 
4. 选择 " **选择** 并 **添加**"。
5. 选择 " **保存** " 按钮以提交更改。  

## <a name="file-system-utilities"></a>文件系统实用工具

`mssparkutils.fs` 提供用于处理各种文件系统的实用程序，包括 Azure Data Lake Storage Gen2 (ADLS Gen2) 和 Azure Blob 存储。 请确保正确配置对 [Azure Data Lake Storage Gen2](#configure-access-to-azure-data-lake-storage-gen2) 和 [Azure Blob 存储](#configure-access-to-azure-blob-storage) 的访问。

有关可用方法的概述，请运行以下命令：

:::zone pivot = "programming-language-python"

```python
from notebookutils import mssparkutils
mssparkutils.fs.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Notebook.MSSparkUtils;
FS.Help()
```

::: zone-end

结果：
```
mssparkutils.fs provides utilities for working with various FileSystems.

Below is overview about the available methods:

cp(from: String, to: String, recurse: Boolean = false): Boolean -> Copies a file or directory, possibly across FileSystems
mv(from: String, to: String, recurse: Boolean = false): Boolean -> Moves a file or directory, possibly across FileSystems
ls(dir: String): Array -> Lists the contents of a directory
mkdirs(dir: String): Boolean -> Creates the given directory if it does not exist, also creating any necessary parent directories
put(file: String, contents: String, overwrite: Boolean = false): Boolean -> Writes the given String out to a file, encoded in UTF-8
head(file: String, maxBytes: int = 1024 * 100): String -> Returns up to the first 'maxBytes' bytes of the given file as a String encoded in UTF-8
append(file: String, content: String, createFileIfNotExists: Boolean): Boolean -> Append the content to a file
rm(dir: String, recurse: Boolean = false): Boolean -> Removes a file or directory

Use mssparkutils.fs.help("methodName") for more info about a method.

```

### <a name="list-files"></a>列出文件
列出目录的内容。


:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.ls('Your directory path')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.ls("Your directory path")
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Ls("Your directory path")
```

::: zone-end


### <a name="view-file-properties"></a>查看文件属性
返回文件属性，其中包括文件名、文件路径、文件大小，以及它是目录还是文件。

:::zone pivot = "programming-language-python"

```python
files = mssparkutils.fs.ls('Your directory path')
for file in files:
    print(file.name, file.isDir, file.isFile, file.path, file.size)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val files = mssparkutils.fs.ls("/")
files.foreach{
    file => println(file.name,file.isDir,file.isFile,file.size)
}
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var Files = FS.Ls("/");
foreach(var File in Files) {
    Console.WriteLine(File.Name+" "+File.IsDir+" "+File.IsFile+" "+File.Size);
}
```

::: zone-end

### <a name="create-new-directory"></a>创建新目录

如果给定的目录不存在，则创建该目录，并创建任何必要的父目录。

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mkdirs('new directory name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mkdirs("new directory name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mkdirs("new directory name")
```

::: zone-end

### <a name="copy-file"></a>复制文件

复制文件或目录。 支持跨文件系统复制。

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.cp('source file or directory', 'destination file or directory', True)# Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```

::: zone-end

### <a name="preview-file-content"></a>预览文件内容

返回到以 UTF-8 编码的字符串形式的给定文件的第一个 "maxBytes" 字节。

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.head('file path', maxBytes to read)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.head("file path", maxBytes to read)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Head("file path", maxBytes to read)
```

::: zone-end

### <a name="move-file"></a>移动文件

移动文件或目录。 支持跨文件系统移动。

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mv('source file or directory', 'destination directory', True) # Set the last parameter as True to firstly create the parent directory if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mv("source file or directory", "destination directory", true) // Set the last parameter as True to firstly create the parent directory if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mv("source file or directory", "destination directory", true)
```

::: zone-end

### <a name="write-file"></a>写入文件

将给定的字符串写入文件，并以 UTF-8 编码。

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.put("file path", "content to write", True) # Set the last parameter as True to overwrite the file if it existed already
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

### <a name="append-content-to-a-file"></a>将内容追加到文件

将给定的字符串追加到文件中，以 UTF-8 编码。

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.append('file path','content to append',True) # Set the last parameter as True to create the file if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

### <a name="delete-file-or-directory"></a>删除文件或目录

删除文件或目录。

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.rm('file path', True) # Set the last parameter as True to remove all files and directories recursively 
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end


## <a name="credentials-utilities"></a>凭据实用工具

你可以使用 MSSparkUtils 凭据实用程序获取链接服务的访问令牌，并在 Azure Key Vault 中管理机密。 

运行以下命令以获取可用方法的概述：

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.Help()
```

::: zone-end

获取结果：

```
getToken(audience, name): returns AAD token for a given audience, name (optional)
isValidToken(token): returns true if token hasn't expired
getConnectionStringOrCreds(linkedService): returns connection string or credentials for linked service
getSecret(akvName, secret, linkedService): returns AKV secret for a given AKV linked service, akvName, secret key
getSecret(akvName, secret): returns AKV secret for a given akvName, secret key
putSecret(akvName, secretName, secretValue, linkedService): puts AKV secret for a given akvName, secretName
putSecret(akvName, secretName, secretValue): puts AKV secret for a given akvName, secretName
```

### <a name="get-token"></a>获取令牌

返回给定受众 Azure AD 令牌，名称 (可选) 。 下表列出了所有可用的受众类型： 

|受众类型|访问群体密钥|
|--|--|
|受众解析类型|汇总|
|存储受众资源|储存|
|数据仓库访问群体资源|DW|
|Data Lake 访问群体资源|'AzureManagement'|
|保管库受众资源|DataLakeStore|
|Azure OSSDB 受众资源|'AzureOSSDB'|
|Azure Synapse 资源|'Synapse'|
|Azure 数据工厂资源|ADF|

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getToken('audience Key')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getToken("audience Key")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetToken("audience Key")
```

::: zone-end


### <a name="validate-token"></a>验证令牌

如果令牌未过期，则返回 true。

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.isValidToken('your token')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.isValidToken("your token")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.IsValidToken("your token")
```

::: zone-end


### <a name="get-connection-string-or-credentials-for-linked-service"></a>获取链接服务的连接字符串或凭据

返回链接服务的连接字符串或凭据。 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getConnectionStringOrCreds('linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getConnectionStringOrCreds("linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetConnectionStringOrCreds("linked service name")
```

::: zone-end


### <a name="get-secret-using-workspace-identity"></a>使用工作区标识获取机密

使用工作区标识返回给定 Azure Key Vault 名称、机密名称和链接服务名称 Azure Key Vault 机密。 请确保正确配置对 [Azure Key Vault](#configure-access-to-azure-key-vault) 的访问。

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name","linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name","linked service name")
```

::: zone-end


### <a name="get-secret-using-user-credentials"></a>使用用户凭据获取机密

使用用户凭据返回给定 Azure Key Vault 名称、机密名称和链接服务名称 Azure Key Vault 机密。 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name")
```

::: zone-end

<!-- ### Put secret using workspace identity

Puts Azure Key Vault secret for a given Azure Key Vault name, secret name, and linked service name using workspace identity. Make sure you configure the access to [Azure Key Vault](#configure-access-to-azure-key-vault) appropriately. -->

:::zone pivot = "programming-language-python"

### <a name="put-secret-using-workspace-identity"></a>使用工作区标识放置机密

使用工作区标识将 Azure Key Vault 的机密放入给定的 Azure Key Vault 名称、机密名称和链接服务名称。 请确保正确配置对 [Azure Key Vault](#configure-access-to-azure-key-vault) 的访问权限。

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

### <a name="put-secret-using-workspace-identity"></a>使用工作区标识放置机密

使用工作区标识将 Azure Key Vault 的机密放入给定的 Azure Key Vault 名称、机密名称和链接服务名称。 请确保正确配置对 [Azure Key Vault](#configure-access-to-azure-key-vault) 的访问权限。

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value","linked service name")
```

::: zone-end

<!-- :::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end -->


<!-- ### Put secret using user credentials

Puts Azure Key Vault secret for a given Azure Key Vault name, secret name, and linked service name using user credentials.  -->

:::zone pivot = "programming-language-python"

### <a name="put-secret-using-user-credentials"></a>使用用户凭据放置机密

使用用户凭据将 Azure Key Vault 的机密放入给定的 Azure Key Vault 名称、机密名称和链接服务名称。 

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value')
```
::: zone-end

:::zone pivot = "programming-language-scala"

### <a name="put-secret-using-user-credentials"></a>使用用户凭据放置机密

使用用户凭据将 Azure Key Vault 的机密放入给定的 Azure Key Vault 名称、机密名称和链接服务名称。 

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value")
```

::: zone-end

<!-- :::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end -->


## <a name="environment-utilities"></a>环境实用工具 

运行以下命令以获取可用方法的概述：

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.Help()
```

::: zone-end

获取结果：
```
GetUserName(): returns user name
GetUserId(): returns unique user id
GetJobId(): returns job id
GetWorkspaceName(): returns workspace name
GetPoolName(): returns Spark pool name
GetClusterId(): returns cluster id
```

### <a name="get-user-name"></a>获取用户名

返回当前用户名。

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserName()
```

::: zone-end

### <a name="get-user-id"></a>获取用户 ID

返回当前用户 ID。

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserId()
```

::: zone-end

### <a name="get-job-id"></a>获取作业 ID

返回作业 ID。

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getJobId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getJobId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetJobId()
```

::: zone-end

### <a name="get-workspace-name"></a>获取工作区名称

返回工作区名称。

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getWorkspaceName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getWorkspaceName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetWorkspaceName()
```

::: zone-end

### <a name="get-pool-name"></a>获取池名称

返回 Spark 池名称。

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getPoolName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getPoolName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetPoolName()
```

::: zone-end

### <a name="get-cluster-id"></a>获取群集 ID

返回当前群集 ID。

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getClusterId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getClusterId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetClusterId()
```

::: zone-end

## <a name="next-steps"></a>后续步骤

- [查看 Synapse 示例笔记本](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [快速入门：使用 Web 工具在 Azure Synapse Analytics 中创建 Apache Spark 池](../quickstart-apache-spark-notebook.md)
- [Azure Synapse Analytics 中的 Apache Spark 是什么](apache-spark-overview.md)
- [Azure Synapse Analytics](../index.yml)
