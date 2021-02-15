---
title: 以编程方式监视 Azure 数据工厂
description: 了解如何使用不同的软件开发工具包 (SDK) 监视数据工厂中的管道。
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/16/2018
author: dcstwh
ms.author: weetok
ms.custom: devx-track-python
ms.openlocfilehash: 038da033c2bdf78a0a2547cc713944bc11bf093d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379890"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>以编程方式监视 Azure 数据工厂

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文介绍如何使用不同的软件开发工具包 (SDK) 监视数据工厂中的管道。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>数据范围

数据工厂仅将管道运行数据存储 45 天。 以编程方式查询有关数据工厂管道运行的数据时 - 比如使用 PowerShell 命令 `Get-AzDataFactoryV2PipelineRun` 来查询，对于可选的 `LastUpdatedAfter` 和 `LastUpdatedBefore` 参数，无最大日期限制。 但是，如果您查询过去一年的数据，则不会收到错误，但只有管道运行过去45天的数据。

如果希望将管道运行数据保持超过45天，请设置自己的诊断日志记录，并 [Azure Monitor](monitor-using-azure-monitor.md)。

## <a name="pipeline-run-information"></a>管道运行信息

有关管道运行属性，请参阅 [PIPELINERUN API 参考](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/get#pipelinerun)。 管道运行在其生命周期中具有不同状态，下面列出了可能的运行状态值：

* 已排队
* 正在进行
* 已成功
* 失败
* 正在取消
* 已取消

## <a name="net"></a>.NET
有关使用 .NET SDK 创建和监视管道的完整演练，请参阅 [使用 .Net 创建数据工厂和管道](quickstart-create-data-factory-dot-net.md)。

1. 添加以下代码以持续检查管道运行状态，直到它完成数据复制为止。

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress" || pipelineRun.Status == "Queued")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. 添加以下代码，用于检索复制活动的运行详细信息，例如，读取/写入的数据大小。

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

有关 .NET SDK 的完整文档，请参阅[数据工厂 .NET SDK 参考](/dotnet/api/microsoft.azure.management.datafactory)。

## <a name="python"></a>Python
有关使用 Python SDK 创建和监视管道的完整演练，请参阅 [使用 Python 创建数据工厂和管道](quickstart-create-data-factory-python.md)。

要监视管道的运行，请添加以下代码：

```python
# Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(
    rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(
    rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

有关 Python SDK 的完整文档，请参阅[数据工厂 Python SDK 参考](/python/api/overview/azure/datafactory)。

## <a name="rest-api"></a>REST API
有关使用 REST API 创建和监视管道的完整演练，请参阅 [使用 REST API 创建数据工厂和管道](quickstart-create-data-factory-rest-api.md)。
 
1. 运行以下脚本来持续检查管道运行状态，直到它完成数据复制为止。

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ( ($response.Status -eq "InProgress") -or ($response.Status -eq "Queued") ) {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```
2. 运行以下脚本来检索复制活动运行详细信息，例如，读取/写入的数据的大小。

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

有关 REST API 的完整文档，请参阅[数据工厂 REST API 参考](/rest/api/datafactory/)。

## <a name="powershell"></a>PowerShell
有关使用 PowerShell 创建和监视管道的完整演练，请参阅 [使用 Powershell 创建数据工厂和管道](quickstart-create-data-factory-powershell.md)。

1. 运行以下脚本来持续检查管道运行状态，直到它完成数据复制为止。

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ( ($run.Status -ne "InProgress") -and ($run.Status -ne "Queued") ) {
                Write-Output ("Pipeline run finished. The status is: " +  $run.Status)
                $run
                break
            }
            Write-Output ("Pipeline is running...status: " + $run.Status)
        }

        Start-Sleep -Seconds 30
    }
    ```
2. 运行以下脚本来检索复制活动运行详细信息，例如，读取/写入的数据的大小。

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

有关 PowerShell cmdlet 的完整文档，请参阅[数据工厂 PowerShell cmdlet 参考](/powershell/module/az.datafactory)。

## <a name="next-steps"></a>后续步骤
若要了解如何使用 Azure Monitor 监视数据工厂管道，请参阅[使用 Azure Monitor 监视管道](monitor-using-azure-monitor.md)一文。 

