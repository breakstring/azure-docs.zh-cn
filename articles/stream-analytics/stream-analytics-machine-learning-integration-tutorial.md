---
title: Azure 流分析与 Azure 机器学习工作室（经典）的集成
description: 本文介绍如何使用用户定义的函数快速设置集成了 Azure 机器学习工作室（经典）的简单 Azure 流分析作业。
author: krishna0815
ms.author: krishmam
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/12/2020
ms.custom: seodec18
ms.openlocfilehash: d7d74887b4b4e205645a45fa7e07768a1af73940
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015294"
---
# <a name="do-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>使用 Azure 流分析和 Azure 机器学习工作室（经典）执行情绪分析

本文介绍如何设置使用 Azure 机器学习工作室（经典）进行情绪分析的简单 Azure 流分析作业。 使用 Cortana Intelligence Gallery 中的工作室（经典）情绪分析模型来分析流式处理文本数据并确定情绪分数。

> [!TIP]
> 强烈建议使用 [Azure 机器学习 UDF](machine-learning-udf.md)，而不是 Azure 机器学习工作室（经典）UDF 来提高性能和可靠性。

可以将从本文学习的知识应用于诸如下列方案：

* 在流式处理 Twitter 数据时分析实时情绪。
* 分析客户与支持人员之间的聊天记录。
* 评估论坛、博客和视频上的评论。
* 许多其他实时的预测性评分方案。

你创建的流分析作业将情绪分析模型用作用户定义的函数 (UDF)，用于处理来自 blob 存储的示例文本数据。 输出（情绪分析的结果）写入到同一 blob 存储中的另一个 CSV 文件中。 

## <a name="prerequisites"></a>先决条件

在开始之前，请确保具有以下各项：

* 一个有效的 Azure 订阅。

* 其中包含某些 Twitter 数据的 CSV 文件。 可以从 [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv) 下载示例文件，也可以创建自己的文件。 在实际方案中，将直接从 Twitter 数据流获取数据。

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>创建一个存储容器并上传 CSV 输入文件

在此步骤中，将 CSV 文件上传到存储容器。

1. 在 Azure 门户中，选择“创建资源” > “存储” > “存储帐户”  。

2. 使用以下详细信息填写“基础”选项卡，并为其余字段保留默认值：

   |字段  |Value  |
   |---------|---------|
   |订阅|选择订阅。|
   |资源组|选择资源组。|
   |存储帐户名称|为存储帐户输入名称。 该名称在 Azure 中必须唯一。|
   |位置|选择一个位置。 所有资源应使用相同的位置。|
   |帐户类型|BlobStorage|

   ![提供存储帐户详细信息](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

3. 选择“查看 + 创建”  。 然后选择“创建”，创建存储帐户。

4. 部署完成后，导航到存储帐户。 在“Blob 服务”下，选择“容器”。 然后选择“+ 容器”以创建新容器。

   ![为输入创建 blob 存储容器](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. 为容器提供名称，并验证“公共访问级别”是否设置为“专用” 。 完成操作后，选择“创建”  。

   ![指定 blob 容器详细信息](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. 导航到新创建的容器，然后选择“上传”。 上传之前下载的 sampleinput.csv 文件。

   ![容器的“上传”按钮](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>从 Cortana Intelligence 库中添加情绪分析模型

现在，示例数据已在 blob 中，可以启用 Cortana Intelligence 库中的情绪分析模型了。

1. 转到 Cortana Intelligence 库中的[预测情绪分析模型](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1)页面。  

2. 选择“在工作室(经典)中打开”。  
   
   ![流分析 Azure 机器学习工作室（经典），打开工作室（经典）](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. 登录并转到工作区。 选择一个位置。

4. 选择页面底部的“运行”。 进程将运行，这将花费大约一分钟。

   ![在工作室（经典）中运行试验](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. 在进程成功运行后，在页面底部选择“部署 Web 服务”。

   ![在工作室（经典）中将实验部署为 Web 服务](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. 若要验证情绪分析模型是否可供使用，请选择“测试”按钮。 提供文本输入，例如“I love Microsoft”。

   ![在工作室（经典）中测试试验](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

   如果测试正常运行，将显示类似于以下示例的结果：

   ![工作室（经典）中的测试结果](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. 在“应用”列中，选择“Excel 2010 或更低版本的工作簿”链接以下载 Excel 工作簿 。 该工作簿包含稍后设置流分析作业时所需的 API 密钥和 URL。

    ![流分析 Azure 机器学习工作室（经典），速览](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-studio-classic-model"></a>创建使用工作室（经典）模型的流分析作业

现在可以创建一个从 blob 存储中的 CSV 文件读取示例推文的流分析作业。

### <a name="create-the-job"></a>创建作业

转到 [Azure 门户](https://portal.azure.com)并创建流分析作业。 如果你不熟悉此过程，请参阅[使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)。

### <a name="configure-the-job-input"></a>配置作业输入

该作业从之前上传到 blob 存储的 CSV 文件获取其输入。

1. 导航到流分析作业。 在“作业拓扑”下，选择“输入”选项 。 选择“添加流输入” >“Blob 存储” 。

2. 使用以下值填写“Blob 存储”详细信息：

   |字段  |值  |
   |---------|---------|
   |输入别名|为输入命名。 编写查询时，请记住此别名。|
   |订阅|选择订阅。|
   |存储帐户|选择在上一步中创建的存储帐户。|
   |容器|选择在上一步中创建的容器。|
   |事件序列化格式|CSV|

3. 选择“保存”。

### <a name="configure-the-job-output"></a>配置作业输出

作业将结果发送到它从中获取输入的同一 blob 存储。

1. 导航到流分析作业。 在“作业拓扑”下，选择“输出”选项 。 选择“添加” > “Blob 存储” 。

2. 使用以下值填写“Blob 存储”表单：

   |字段  |值  |
   |---------|---------|
   |输入别名|为输入命名。 编写查询时，请记住此别名。|
   |订阅|选择订阅。|
   |存储帐户|选择在上一步中创建的存储帐户。|
   |容器|选择在上一步中创建的容器。|
   |事件序列化格式|CSV|

3. 选择“保存”。

### <a name="add-the-studio-classic-function"></a>添加工作室（经典）函数

之前，已将工作室（经典）模型发布到 Web 服务。 在此方案中，当流分析作业运行时，它将来自输入的每个示例推文发送到用于情绪分析的 Web 服务。 工作室（经典）Web 服务返回一种情绪（`positive`、`neutral` 或 `negative`）和推文为正面内容的概率。

在本部分中，将在流分析作业中定义一个函数。 可以调用该函数来将推文发送到 Web 服务并返回响应。

1. 请确保具有之前在 Excel 工作簿中下载的 Web 服务 URL 和 API 密钥。

2. 导航到流分析作业。 然后选择“函数” > “+ 添加” > “Azure ML 工作室”  

3. 使用以下值填写“Azure 机器学习函数”表单：

   |字段  |值  |
   |---------|---------|
   | 函数别名 | 使用名称 `sentiment` 并选择“手动提供 Azure 机器学习函数设置”，该选项会提供输入 URL 和密钥的选项。      |
   | URL| 粘贴 Web 服务 URL。|
   |Key | 粘贴 API 密钥。 |

4. 选择“保存”。

### <a name="create-a-query-to-transform-the-data"></a>创建查询来转换数据

流分析使用声明性的、基于 SQL 的查询来检查输入，并对其进行处理。 本部分将创建一个查询，该查询先读取输入中的每条推文，然后调用工作室（经典）函数来执行情绪分析。 然后，该查询将结果发送到你定义的输出（blob 存储）。

1. 返回到流分析作业概述。

2. 在“作业拓扑”下选择“查询”。  

3. 输入以下查询：

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM <input>  
    )  

    SELECT text, result.[Score]  
    INTO <output>
    FROM sentiment  
    ```    

   该查询调用前面创建的函数 (`sentiment`)，以对输入中的每条推文执行情绪分析。

4. 选择“保存”以保存查询。

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>启动流分析作业并检查输出

现在可以启动流分析作业。

### <a name="start-the-job"></a>启动作业

1. 返回到流分析作业概述。

2. 在页面顶部选择“保存”。

3. 在“启动作业”中，选择“自定义”，然后选择将 CSV 文件上传到 blob 存储之前的某一天 。 完成后，选择“启动”。  

### <a name="check-the-output"></a>检查输出

1. 让作业运行几分钟，直到在“监视”框中看到活动。

2. 如果你有通常用来观察 blob 存储内容的工具，可使用该工具来观察容器。 另外，也可以在 Azure 门户中执行以下步骤：

      1. 在 Azure 门户中，找到存储帐户，然后在该帐户中找到容器。 可以在该容器中看到两个文件：包含示例推文的文件和由流分析作业生成的 CSV 文件。
      2. 右键单击生成的文件并选择“下载”。

3. 打开生成的 CSV 文件。 可以看到类似以下示例的内容：  

   ![流分析 Azure 机器学习工作室（经典），CSV 视图](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

### <a name="view-metrics"></a>查看指标

还可以查看与工作室（经典）函数相关的指标。 作业概述中的“监视”框中会显示以下与函数相关的指标：

* “函数请求数”表示发送到工作室（经典）Web 服务的请求数。  
* **函数事件** 指示请求中的事件数。 默认情况下，发送到工作室（经典）Web 服务的每个请求最多包含 1000 个事件。

## <a name="next-steps"></a>后续步骤

* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析查询语言参考](/stream-analytics-query/stream-analytics-query-language-reference)
* [集成 REST API 和机器学习工作室（经典）](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Azure 流分析管理 REST API 参考](/rest/api/streamanalytics/)