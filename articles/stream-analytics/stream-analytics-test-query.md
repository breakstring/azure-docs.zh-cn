---
title: 使用样本数据测试 Azure 流分析作业
description: 本文介绍如何使用 Azure 门户测试 Azure 流分析作业、示例输入以及上传样本数据。
author: ajetasin
ms.author: ajetasi
ms.service: stream-analytics
ms.topic: how-to
ms.date: 3/6/2020
ms.custom: seodec18
ms.openlocfilehash: eff9103f476e6074ab46198ff8cc78588675569f
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020037"
---
# <a name="test-an-azure-stream-analytics-job-in-the-portal"></a>在门户中测试 Azure 流分析作业

在 Azure 流分析中，无需启动或停止作业即可测试查询。 可以对流源中的传入数据测试查询，也可以从 Azure 门户上的本地文件上传示例数据。 还可以在 [Visual Studio](stream-analytics-live-data-local-testing.md) 和 [Visual Studio Code](visual-studio-code-local-run-live-input.md) 中，基于本地示例数据或实时数据在本地测试查询。

## <a name="automatically-sample-incoming-data-from-input"></a>来自输入的自动示例传入数据

Azure 流分析自动从流输入中提取事件。 可以针对默认示例运行查询，或者为示例设置特定的期限。

1. 登录到 Azure 门户。

2. 找到并选择现有的流分析作业。

3. 在“流分析作业”页上的“作业拓扑”标题下，选择“查询”以打开“查询编辑器”窗口。 

4. 若要查看传入事件的示例列表，请使用文件图标选择输入，示例事件将自动显示在“输入预览”中。

   a. 如果数据的序列化类型为 JSON 或 CSV，系统会自动检测到该类型。 还可以通过更改下拉菜单中的选项，手动将序列化类型更改为 JSON、CSV 或 AVRO。
    
   b. 使用选择器查看“表”或“原始”格式的数据。
    
   c. 如果显示的数据不是最新的，请选择“刷新”查看最新事件。

   下表显示了采用“表”格式的数据示例：

   ![采用表格式的 Azure 流分析输入示例](./media/stream-analytics-test-query/asa-sample-table.png)

   下表显示了“原始”格式的数据示例：

   ![采用原始格式的 Azure 流分析输入示例](./media/stream-analytics-test-query/asa-sample-raw.png)

5. 若要使用传入数据测试查询，请选择“测试查询”。 结果将显示在“测试结果”选项卡中。还可以选择“下载结果”来下载结果。

   ![Azure 流分析中的示例测试查询结果](./media/stream-analytics-test-query/asa-test-query.png)

6. 若要针对传入事件的特定时间范围测试查询，请选择“选择时间范围”。
   
   ![传入示例事件的 Azure 流分析时间范围](./media/stream-analytics-test-query/asa-select-time-range.png)

7. 设置用于测试查询的事件的时间范围，然后选择“示例”。 在此期限内，最多可以检索 1000 个事件或 1 MB 的数据大小（以先达到的限制为准）。

   ![在 Azure 流分析中设置传入示例事件的时间范围](./media/stream-analytics-test-query/asa-set-time-range.png)

8. 根据所选时间范围对事件采样后，这些事件将显示在“输入预览”选项卡中。

   ![在 Azure 流分析中查看测试结果](./media/stream-analytics-test-query/asa-view-test-results.png)

9. 选择“重置”，查看传入事件的示例列表。 如果选择“重置”，选择的时间范围将会丢失。 选择“测试查询”以测试查询，然后在“测试结果”选项卡中查看结果。

10. 更改查询后，请选择“保存查询”以测试新的查询逻辑。 这样就可以反复修改查询，并再次对其进行测试，以查看输出有何变化。

11. 确认结果显示在浏览器中后，可以 **启动** 作业。

## <a name="upload-sample-data-from-a-local-file"></a>从本地文件上传示例数据

可以使用本地文件中的示例数据（而不是实时数据）来测试 Azure 流分析查询。

1. 登录到 Azure 门户。
   
2. 找到现有流分析作业并选择它。

3. 在“流分析作业”页上的“作业拓扑”标题下，选择“查询”以打开“查询编辑器”窗口。

4. 若要使用本地文件测试查询，请在“输入预览”选项卡上选择“上传示例输入”。 

   ![屏幕截图显示了“上传示例输入”选项。](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. 上传本地文件以测试查询。 只能上传采用 JSON、CSV 或 AVRO 格式的文件。 选择“确定”。

   ![屏幕截图显示了“上传示例数据”对话框，你可在其中选择文件。](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. 上传文件后，还可以在窗体中查看表格式或原始格式的文件内容。 如果选择“重置”，示例数据将还原为上一部分所述的传入输入数据。 随时可以上传任何其他文件来测试查询。

7. 选择“测试查询”，以针对上传的示例文件测试查询。

8. 将根据查询显示测试结果。 可以更改查询，然后选择“保存查询”来测试新的查询逻辑。 这样就可以反复修改查询，并再次对其进行测试，以查看输出有何变化。

9. 在查询中使用多个输出时，将根据所选输出显示结果。 

   ![Azure 流分析中选定的输出](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. 确认结果显示在浏览器中后，可以 **启动** 作业。

## <a name="limitations"></a>限制

1.  门户测试中不支持时间策略：

    * 无序：将对所有传入事件进行排序。
    * 延迟到达：由于流分析只能使用现有数据进行测试，因此不会出现延迟到达事件。
   
2.  不支持 C# UDF。

3.  所有测试都将在具有一个流单元的作业下运行。

4.  超时大小为一分钟。 因此，任何窗口大小超过一分钟的查询都无法获取任何数据。

5.  不支持机器学习。

6. 在 15 分钟时段内，示例数据 API 将在发出 5 个请求后受到限制。 在 15 分钟时段结束后，你可执行更多示例数据请求。 此限制在订阅级别实施。

## <a name="troubleshooting"></a>疑难解答

1.  如果出现此错误，则提取结果时出现网络连接问题。 请检查你的网络和防火墙设置。 "，请执行以下步骤：

  * 若要检查与服务的连接，请在浏览器中打开 [https://queryruntime.azurestreamanalytics.com/api/home/index](https://queryruntime.azurestreamanalytics.com/api/home/index)。 如果无法打开此链接，请更新防火墙设置。
  
2. 如果遇到“请求大小过大。 请减小输入数据大小，然后重试。”错误，请执行以下步骤：

  * 减小输入大小–使用较小的大小示例文件或较小的时间范围测试查询。
  * 缩小查询大小–若要测试选定的查询，请选择部分查询，然后单击 " **测试选定的查询**"。


## <a name="next-steps"></a>后续步骤
* [使用流分析构建 IoT 解决方案](./stream-analytics-build-an-iot-solution-using-stream-analytics.md)：本教程将指导使用数据生成器构建端到端解决方案，该生成器将模拟收费站的流量。

* [Azure 流分析查询语言参考](/stream-analytics-query/stream-analytics-query-language-reference)

* [常用流分析使用模式的查询示例](stream-analytics-stream-analytics-query-patterns.md)

* [理解 Azure 流分析的输入](stream-analytics-add-inputs.md)

* [了解 Azure 流分析的输出](stream-analytics-define-outputs.md)
