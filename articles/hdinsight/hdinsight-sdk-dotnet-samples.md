---
title: Azure HDInsight：.NET 示例
description: 针对用于 .NET 的 HDInsight SDK，请在 GitHub 上查找有关使用它的常见任务的 C# .NET 示例。
ms.service: hdinsight
ms.topic: sample
ms.date: 12/06/2019
ms.openlocfilehash: 6182deb657631135742351583d51171739c8ee68
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931970"
---
# <a name="azure-hdinsight-net-samples"></a>Azure HDInsight：.NET 示例

> [!div class="op_single_selector"]
> * [.NET 示例](hdinsight-sdk-dotnet-samples.md)
> * [Python 示例](hdinsight-sdk-python-samples.md)
> * [Java 示例](hdinsight-sdk-java-samples.md)
<!-- * [Go Examples](hdinsight-sdk-go-samples.md)-->

本文将提供：

* 有关群集创建任务的示例链接。
* 有关其他管理任务的参考内容链接。

可以[激活 Visual Studio 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)：Visual Studio 订阅每月为用户提供可用来试用付费版 Azure 服务的信用额度。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisite"></a>先决条件

[用于 .NET 的 Azure HDInsight SDK](/dotnet/api/overview/azure/hdinsight#sdk-installation)

## <a name="cluster-management---creation"></a>群集管理 - 创建

* [创建 Kafka 群集](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateKafkaClusterSample.cs)
* [创建 Spark 群集](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateSparkClusterSample.cs)
* [创建包含 Azure Data Lake Storage Gen2 的 Spark 群集](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateHadoopClusterWithAdlsGen2Sample.cs)
* [创建包含企业安全性套餐 (ESP) 的 Spark 群集](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples/blob/master/Management/Microsoft.Azure.Management.HDInsight.Samples/Microsoft.Azure.Management.HDInsight.Samples/CreateEspClusterSample.cs)

通过克隆 [hdinsight-dotnet-sdk-samples](https://github.com/Azure-Samples/hdinsight-dotnet-sdk-samples) GitHub 存储库，可获取 .NET 的这些示例。

[!INCLUDE [hdinsight-sdk-additional-functionality](../../includes/hdinsight-sdk-additional-functionality.md)]

在[用于 .NET 的 HDInsight SDK 参考文档](/dotnet/api/overview/azure/hdinsight)中，可找到这一附加 SDK 功能的代码片段。
