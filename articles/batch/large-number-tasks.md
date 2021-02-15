---
title: 将大量的任务提交到 Batch 作业
description: 了解如何在单个 Azure Batch 作业中有效地提交大量任务。
ms.topic: how-to
ms.date: 12/30/2020
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 08cf92507a4556afbf56c9cb7e2c9c1b3a6c9479
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831510"
---
# <a name="submit-a-large-number-of-tasks-to-a-batch-job"></a>将大量的任务提交到 Batch 作业

运行大规模的 Azure Batch 工作负荷时，你可能需要将数万、数十万甚至更多的任务提交到单个作业。

本文介绍如何提交大量任务，这些任务的吞吐量明显提高到单个批处理作业。 提交任务后，它们将会进入 Batch 队列，等待在为作业指定的池中进行处理。

## <a name="use-task-collections"></a>使用任务集合

添加大量任务时，请使用批处理 Api 提供的适当方法或重载，将任务添加为 *集合* ，而不是一次添加一个。 一般情况下，任务集合的构造方式是在循环访问一组输入文件或作业的参数时定义任务。

可在单个调用中添加的任务集合的最大大小取决于所使用的批处理 API。

### <a name="apis-allowing-collections-of-up-to-100-tasks"></a>允许最多100任务的集合的 Api

这些批处理 Api 会将集合限制为100个任务。 此限制可能会较小，具体取决于任务的大小 (例如，如果任务) 有大量资源文件或环境变量。

- [REST API](/rest/api/batchservice/task/addcollection)
- [Python API](/python/api/azure-batch/azure.batch.operations.TaskOperations)
- [Node.js API](/javascript/api/@azure/batch/task)

使用这些 Api 时，需要提供逻辑来划分任务数以满足集合限制，并在发生任务添加故障时处理错误和重试。 如果任务集合太大，以致无法添加，则请求会生成错误，并在减少任务后重试。

### <a name="apis-allowing-collections-of-larger-numbers-of-tasks"></a>允许收集大量任务的 Api

其他批处理 Api 支持更大的任务集合，只受提交客户端上 RAM 可用性的限制。 对于较低级别的 Api，这些 Api 透明地处理将任务集合划分为 "区块"，并会重试任务添加失败。

- [.NET API](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync)
- [Java API](/java/api/com.microsoft.azure.batch.protocol.tasks.addcollectionasync)
- 包含 Batch CLI 模板的 [Azure Batch CLI 扩展](batch-cli-templates.md)
- [Python SDK 扩展](https://pypi.org/project/azure-batch-extensions/)

## <a name="increase-throughput-of-task-submission"></a>增大用于提交任务的吞吐量

可能需要一段时间才能将大型任务集合添加到作业。 例如，通过 .NET API 添加20000任务最多可能需要一分钟。 根据批处理 API 和工作负载，可以通过修改以下一项或多项来改善任务吞吐量。

### <a name="task-size"></a>任务大小

添加大型任务比添加较小任务的时间要长。 若要减小集合中每个任务的大小，可以简化任务命令行、减少环境变量的数目，或者更有效地处理任务执行要求。

例如，不要使用大量的资源文件，而是使用池上的 [启动任务](jobs-and-tasks.md#start-task) 安装任务依赖项，或者使用 [应用程序包](batch-application-packages.md) 或 [Docker 容器](batch-docker-container-workloads.md)。

### <a name="number-of-parallel-operations"></a>并行操作的数目

根据批处理 API，可以通过增加 Batch 客户端的最大并发操作数来提高吞吐量。 在 .NET API 中使用 [BatchClientParallelOptions.MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) 属性，或者在 Batch Python SDK 扩展中使用 [TaskOperations.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations) 等方法的 `threads` 参数来配置此项设置。 （此属性在本机 Batch Python SDK 中不可用。）

默认情况下，此属性设置为1，但你可以将其设置为更高的值以提高操作的吞吐量。 提高吞吐量的代价是会消耗网络带宽，并在一定程度上降低 CPU 的性能。 最高可按 `MaxDegreeOfParallelism` 或 `threads` 的 100 倍提高任务吞吐量。 在实践中，应将并发操作的数量设置为低于100。

 包含 Batch 模板的 Azure Batch CLI 扩展会根据可用核心数自动增加并发操作数目，但无法在 CLI 中配置此属性。

### <a name="http-connection-limits"></a>HTTP 连接限制

在添加大量任务时，有许多并发 HTTP 连接可以限制 Batch 客户端的性能。 某些 Api 限制 HTTP 连接数。 例如，使用 .NET API 进行开发时，[ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) 属性默认设置为 2。 我们建议将该值增大到接近或大于并行操作数目。

## <a name="example-batch-net"></a>示例：Batch .NET

以下 C# 代码片段演示了在使用 Batch .NET API 添加大量任务时要配置的设置。

若要提高任务吞吐量，请增大 [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) 的 [MaxDegreeOfParallelism](/dotnet/api/microsoft.azure.batch.batchclientparalleloptions.maxdegreeofparallelism) 属性值。 例如：

```csharp
BatchClientParallelOptions parallelOptions = new BatchClientParallelOptions()
  {
    MaxDegreeOfParallelism = 15
  };
...
```

使用 [AddTaskAsync](/dotnet/api/microsoft.azure.batch.cloudjob.addtaskasync) 或 [AddTask](/dotnet/api/microsoft.azure.batch.cloudjob.addtask
) 方法的相应重载将任务集合添加到作业。 例如：

```csharp
// Add a list of tasks as a collection
List<CloudTask> tasksToAdd = new List<CloudTask>(); // Populate with your tasks
...
await batchClient.JobOperations.AddTaskAsync(jobId, tasksToAdd, parallelOptions);
```

## <a name="example-batch-cli-extension"></a>示例：Batch CLI 扩展

使用包含 [Batch CLI 模板](batch-cli-templates.md)的 Azure Batch CLI 扩展，创建包含[任务工厂](https://github.com/Azure/azure-batch-cli-extensions/blob/master/doc/taskFactories.md)的作业模板 JSON 文件。 任务工厂为单个任务定义中的某个作业配置相关任务的集合。  

下面是一维参数扫描作业的示例作业模板，其中包含大量任务 (在本例中为 250000) 。 任务命令行是一个简单的 `echo` 命令。

```json
{
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime":"PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

若要使用模板运行作业，请参阅[使用 Azure Batch CLI 模板和文件传输](batch-cli-templates.md)。

## <a name="example-batch-python-sdk-extension"></a>示例：Batch Python SDK 扩展

若要使用 Azure Batch Python SDK 扩展，请先安装 Python SDK 和扩展：

```
pip install azure-batch
pip install azure-batch-extensions
```

设置使用该 SDK 扩展的 `BatchExtensionsClient`：

```python

client = batch.BatchExtensionsClient(
    base_url=BATCH_ACCOUNT_URL, resource_group=RESOURCE_GROUP_NAME, batch_account=BATCH_ACCOUNT_NAME)
...
```

创建要添加到作业的任务集合。 例如：

```python
tasks = list()
# Populate the list with your tasks
...
```

使用 [task.add_collection](/python/api/azure-batch/azure.batch.operations.TaskOperations) 添加任务集合。 设置 `threads` 参数以增加并发操作数目：

```python
try:
    client.task.add_collection(job_id, threads=100)
except Exception as e:
    raise e
```

Batch Python SDK 扩展还支持使用任务工厂的 JSON 规范将任务参数添加到作业。 例如，为前面 Batch CLI 模板示例中所示的参数扫描配置作业参数：

```python
parameter_sweep = {
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "myjob",
            "poolInfo": {
                "poolId": "mypool"
            },
            "taskFactory": {
                "type": "parametricSweep",
                "parameterSets": [
                    {
                        "start": 1,
                        "end": 250000,
                        "step": 1
                    }
                ],
                "repeatTask": {
                    "commandLine": "/bin/bash -c 'echo Hello world from task {0}'",
                    "constraints": {
                        "retentionTime": "PT1H"
                    }
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
...
job_json = client.job.expand_template(parameter_sweep)
job_parameter = client.job.jobparameter_from_json(job_json)
```

将作业参数添加到作业。 设置 `threads` 参数以增加并发操作数目：

```python
try:
    client.job.add(job_parameter, threads=50)
except Exception as e:
    raise e
```

## <a name="next-steps"></a>后续步骤

- 详细了解如何使用包含 [Batch CLI 模板](batch-cli-templates.md)的 Azure Batch CLI 扩展。
- 详细了解 [Batch Python SDK 扩展](https://pypi.org/project/azure-batch-extensions/)。
- 阅读 [Azure Batch 的最佳实践](best-practices.md)。
