---
title: 在 Azure HDInsight 中将 Data Lake Storage Gen1 与 Hadoop 配合使用
description: 了解如何从 Azure Data Lake Storage Gen1 查询数据并存储分析结果。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 35941f585a0ae5c0d3915c769db5b18737b299f0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945408"
---
# <a name="use-data-lake-storage-gen1-with-azure-hdinsight-clusters"></a>将 Data Lake Storage Gen1 与 Azure HDInsight 群集配合使用

> [!Note]
> 使用 [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md) 部署新的 HDInsight 群集以提高性能并获得新功能。

若要分析 HDInsight 群集中的数据，可以将数据存储在 [`Azure Blob storage`](../storage/common/storage-introduction.md) 、 [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)或 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)中。 使用所有存储选项都能安全地删除用于计算的 HDInsight 群集，而不会丢失用户数据。

在本文中，你将了解如何将 Data Lake Storage Gen1 与 HDInsight 群集配合使用。 若要了解 Azure Blob 存储如何与 HDInsight 群集配合使用，请参阅 [将 Azure blob 存储与 Azure HDInsight 群集配合使用](hdinsight-hadoop-use-blob-storage.md)。 若要深入了解如何创建 HDInsight 群集，请参阅[在 HDInsight 中创建 Apache Hadoop 群集](hdinsight-hadoop-provision-linux-clusters.md)。

> [!NOTE]  
> 因为始终可通过安全通道访问 Data Lake Storage Gen1，因此没有 `adls` 文件系统方案名称。 始终使用 `adl`。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability-for-hdinsight-clusters"></a>HDInsight 群集的可用性

Apache Hadoop 支持默认文件系统的概念。 默认文件系统意指默认方案和授权。 它还可用于解析相对路径。 在 HDInsight 群集创建过程中，请将 Azure 存储中的 blob 容器指定为默认文件系统。 或者使用 HDInsight 3.5 和更新版本，可以选择 "Azure Blob 存储" 或 "Azure Data Lake Storage Gen1 为默认文件系统，但有一些例外情况。 群集和存储帐户必须位于同一区域。

HDInsight 群集可通过以下两种方式使用 Data Lake Storage Gen1：

* 作为默认存储
* 作为附加存储，将 Azure Blob 存储用作默认存储。

目前，只有某些 HDInsight 群集类型/版本支持使用 Data Lake Storage Gen1 作为默认存储和其他存储帐户：

| HDInsight 群集类型 | 将 Data Lake Storage Gen1 用作默认存储 | 将 Data Lake Storage Gen1 用作附加存储| 注意 |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight 版本4。0 | 否 | 否 |HDInsight 4.0 不支持 ADLS Gen1 |
| HDInsight 版本 3.6 | 是 | 是 | HBase 除外|
| HDInsight 版本 3.5 | 是 | 是 | HBase 除外|
| HDInsight 版本 3.4 | 否 | 是 | |
| HDInsight 版本 3.3 | 否 | 否 | |
| HDInsight 版本 3.2 | 否 | 是 | |
| Storm | | |可使用 Data Lake Storage Gen1 从 Storm 拓扑写入数据。 你还可以将 Data Lake Storage Gen1 用于可通过风暴拓扑读取的引用数据。|

> [!WARNING]  
> Azure Data Lake Storage Gen1 不支持 HDInsight HBase

使用 Data Lake Storage Gen1 作为附加存储帐户不会影响性能。 也可以从群集读取或写入 Azure blob 存储。

## <a name="use-data-lake-storage-gen1-as-default-storage"></a>将 Data Lake Storage Gen1 用作默认存储

使用 Data Lake Storage Gen1 作为默认存储来部署 HDInsight 时，与群集相关的文件将存储在 `adl://mydatalakestore/<cluster_root_path>/` 中，其中 `<cluster_root_path>` 是你在 Data Lake Storage 中创建的文件夹的名称。 通过指定每个群集的根路径，可将同一 Data Lake Storage 帐户用于多个群集。 因此可以进行设置，其中：

* Cluster1 可以使用路径 `adl://mydatalakestore/cluster1storage`
* Cluster2 可以使用路径 `adl://mydatalakestore/cluster2storage`

请注意，这两个群集使用的是同一个 Data Lake Storage Gen1 帐户 **mydatalakestore**。 每个群集都有权访问 Data Lake Storage 中其自身的根文件系统。 Azure 门户部署体验提示你使用文件夹名称（如 **/clusters/ \<clustername>** ）作为根路径。

若要使用 Data Lake Storage Gen1 作为默认存储，则必须授予服务主体对以下路径的访问权限：

* Data Lake Storage Gen1 帐户根目录。  例如：adl://mydatalakestore/。
* 所有群集文件夹的文件夹。  例如：adl://mydatalakestore/clusters。
* 群集文件夹。  例如：adl://mydatalakestore/clusters/cluster1storage。

有关如何创建服务主体和授予访问权限的详细信息，请参阅配置 Data Lake Storage 访问权限。

### <a name="extracting-a-certificate-from-azure-keyvault-for-use-in-cluster-creation"></a>从 Azure Keyvault 提取证书以在创建群集时使用

如果服务主体的证书存储在 Azure Key Vault 中，则必须将该证书转换为正确的格式。 下面的代码段演示如何执行转换。

首先，从 Key Vault 下载证书并提取 `SecretValueText`。

```powershell
$certPassword = Read-Host "Enter Certificate Password"
$cert = (Get-AzureKeyVaultSecret -VaultName 'MY-KEY-VAULT' -Name 'MY-SECRET-NAME')
$certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
```

其次，将 `SecretValueText` 转换为证书。

```powershell
$certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue,$null,"Exportable, PersistKeySet"
$certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);
$identityCertificate = [System.Convert]::ToBase64String($certBytes)
```

然后，可以使用 `$identityCertificate` 部署新的群集，如以下代码片段中所示：

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $pathToArmTemplate `
    -identityCertificate $identityCertificate `
    -identityCertificatePassword $certPassword.SecretValueText `
    -clusterName  $clusterName `
    -clusterLoginPassword $SSHpassword `
    -sshPassword $SSHpassword `
    -servicePrincipalApplicationId $application.ApplicationId
```

## <a name="use-data-lake-storage-gen1-as-additional-storage"></a>将 Data Lake Storage Gen1 用作附加存储

也可将 Data Lake Storage Gen1 用作群集的附加存储。 在这种情况下，群集默认存储可以是 Azure Blob 存储或 Azure Data Lake Storage Gen1 帐户。 针对作为附加存储 Azure Data Lake Storage Gen1 中存储的数据运行 HDInsight 作业时，请使用完全限定的路径。 例如：

`adl://mydatalakestore.azuredatalakestore.net/<file_path>`

URL 中现在没有 **cluster_root_path** 。 这是因为在这种情况下 Data Lake Storage 不是默认存储。 因此，只需提供文件的路径即可。

若要使用 Data Lake Storage Gen1 作为附加存储，请向服务主体授予对存储文件的路径的访问权限。  例如：

`adl://mydatalakestore.azuredatalakestore.net/<file_path>`

有关如何创建服务主体和授予访问权限的详细信息，请参阅配置 Data Lake Storage 访问权限。

## <a name="use-more-than-one-data-lake-storage-gen1-account"></a>使用多个 Data Lake Storage Gen1 帐户

添加 Data Lake Storage 帐户作为附加帐户，并添加多个 Data Lake Storage 帐户。 为 HDInsight 群集权限授予一个或多个 Data Lake Storage 帐户中的数据。 请参阅下面的配置 Data Lake Storage Gen1 访问。

## <a name="configure-data-lake-storage-gen1-access"></a>配置 Data Lake Storage Gen1 访问

若要从 HDInsight 群集配置 Azure Data Lake Storage Gen1 访问，必须将 Azure Active directory (Azure AD) 服务主体。 只有 Azure AD 管理员才能创建服务主体。 必须使用证书创建服务主体。 有关详细信息，请参阅[快速入门：在 HDInsight 中设置群集](./hdinsight-hadoop-provision-linux-clusters.md)以及[使用自签名证书创建服务主体](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)。

> [!NOTE]  
> 如果要将 Azure Data Lake Storage Gen1 用作 HDInsight 群集的附加存储，强烈建议在创建该群集时按本文说明进行此项操作。 将 Azure Data Lake Storage Gen1 作为附加存储添加到现有的 HDInsight 群集是不受支持的方案。

有关访问控制模型的详细信息，请参阅 [Azure Data Lake Storage Gen1 中的访问控制](../data-lake-store/data-lake-store-access-control.md)。

## <a name="access-files-from-the-cluster"></a>从群集访问文件

可以通过多种方法从 HDInsight 群集访问 Data Lake Storage 中的文件。

* **使用完全限定的名称**。 使用此方法时，需提供要访问的文件的完整路径。

    ```
    adl://<data_lake_account>.azuredatalakestore.net/<cluster_root_path>/<file_path>
    ```

* **使用缩短的路径格式**。 使用此方法时，需将群集根的路径替换为：

    ```
    adl:///<file path>
    ```

* **使用相对路径**。 使用此方法时，仅需提供要访问的文件的相对路径。

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>数据访问示例

示例基于到群集的头节点的 [ssh 连接](./hdinsight-hadoop-linux-use-ssh-unix.md)。 示例使用所有三个 URI 方案。 `DATALAKEACCOUNT`将和替换 `CLUSTERNAME` 为相关值。

#### <a name="a-few-hdfs-commands"></a>几个 hdfs 命令

1. 在本地存储上创建一个文件。

    ```bash
    touch testFile.txt
    ```

1. 在群集存储上创建目录。

    ```bash
    hdfs dfs -mkdir adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -mkdir adl:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. 将数据从本地存储复制到群集存储。

    ```bash
    hdfs dfs -copyFromLocal testFile.txt adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt adl:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt /sampledata3/
    ```

1. 列出群集存储上的目录内容。

    ```bash
    hdfs dfs -ls adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/sampledata1/
    hdfs dfs -ls adl:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>创建 Hive 表

但为了便于说明，显示了三个文件位置。 在实际执行中，只使用其中一个 `LOCATION` 条目。

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'adl://DATALAKEACCOUNT.azuredatalakestore.net/clusters/CLUSTERNAME/example/data/';
LOCATION 'adl:///example/data/';
LOCATION '/example/data/';
```

## <a name="identify-storage-path-from-ambari"></a>从 Ambari 标识存储路径

若要确定配置的默认存储的完整路径，请导航到 **HDFS**  >  **配置，** 然后 `fs.defaultFS` 在 "筛选器" 输入框中输入。

## <a name="create-hdinsight-clusters-with-access-to-data-lake-storage-gen1"></a>创建具有 Data Lake Storage Gen1 访问权限的 HDInsight 群集

请使用以下链接，详细了解有关如何创建具有 Data Lake Storage Gen1 访问权限的 HDInsight 群集的说明。

* [使用门户](./hdinsight-hadoop-provision-linux-clusters.md)
* [使用 PowerShell（将 Data Lake Storage Gen1 作为默认存储）](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [使用 PowerShell（将 Data Lake Storage Gen1 作为附加存储）](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [使用 Azure 模板](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-storage-gen1-access"></a>刷新用于访问 Data Lake Storage Gen1 的 HDInsight 证书

以下 PowerShell 代码示例从本地文件或 Azure Key Vault 读取证书，并使用新证书更新 HDInsight 群集以访问 Azure Data Lake Storage Gen1。 提供你自己的 HDInsight 群集名称、资源组名称、订阅 ID， `app ID` 以及证书的本地路径。 出现提示时键入密码。

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$KeyVaultName = "my-key-vault-name"
$KeyVaultSecretName = "my-key-vault-secret-name"
$certPassword = Read-Host "Enter Certificate Password"
# certSource
# 0 - create self signed cert
# 1 - read cert from file path
# 2 - read cert from key vault
$certSource = 0

Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

if($certSource -eq 0)
{
    Write-Host "Generating new SelfSigned certificate"

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
elseif($certSource -eq 1)
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}
elseif($certSource -eq 2)
{

    Write-Host "Reading the cert file from Azure Key Vault $KeyVaultName"

    $cert = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName)
    $certValue = [System.Convert]::FromBase64String($cert.SecretValueText)
    $certObject = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $certValue, $null,"Exportable, PersistKeySet"

    $certBytes = $certObject.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword.SecretValueText);

    $certString =[System.Convert]::ToBase64String($certBytes)
}

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 7 minutes for the permissions to get propagated"
    Start-Sleep -s 420 #7 minutes
}

Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>后续步骤

本文介绍了如何将 HDFS 兼容的 Azure Data Lake Storage Gen1 与 HDInsight 配合使用。 此存储允许你构建可自适应的长期存档数据采集解决方案。 并使用 HDInsight 来解锁所存储结构化和非结构化数据内的信息。

有关详细信息，请参阅：

* [快速入门：在 HDInsight 中设置群集](./hdinsight-hadoop-provision-linux-clusters.md)
* [通过 Azure PowerShell 创建使用 Data Lake Storage Gen1 的 HDInsight 群集](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [将数据上传到 HDInsight](hdinsight-upload-data.md)
* [使用 Azure Blob 存储共享访问签名来限制使用 HDInsight 访问数据](hdinsight-storage-sharedaccesssignature-permissions.md)