---
title: 任务运行时环境变量
description: Azure Batch Analytics 的任务运行时环境变量指南和参考。
ms.topic: conceptual
ms.date: 12/30/2020
ms.openlocfilehash: c1d9ffb3fe6775b061863656adcb7f45f8840997
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97830881"
---
# <a name="azure-batch-runtime-environment-variables"></a>Azure Batch 运行时环境变量

[Azure Batch 服务](https://azure.microsoft.com/services/batch/)在计算节点上设置以下环境变量。 可以在任务命令行中引用这些环境变量，也可在命令行运行的程序和脚本中引用它们。

要详细了解如何将环境变量和 Batch 结合使用，请参阅[任务环境设置](jobs-and-tasks.md#environment-settings-for-tasks)。

## <a name="environment-variable-visibility"></a>环境变量的可见性

这些环境变量仅在 **任务用户**（即执行任务的节点上的用户帐户）的上下文中可见。 [远程连接](./error-handling.md#connect-to-compute-nodes)到计算节点时，你将看不到这些变量远程桌面协议 (RDP) 或安全外壳 (SSH) 并列出环境变量。 这是因为，用于远程连接的用户帐户与任务使用的帐户不同。

要获取环境变量的当前值，请在 Windows 计算节点上启动 `cmd.exe`，或在 Linux 节点上启动 `/bin/sh`：

`cmd /c set <ENV_VARIABLE_NAME>`

`/bin/sh -c "printenv <ENV_VARIABLE_NAME>"`

## <a name="command-line-expansion-of-environment-variables"></a>环境变量的命令行扩展

计算节点上的任务执行的命令行不在 shell 下运行。 这意味着这些命令行无法以本机方式使用 shell 功能，例如环境变量扩展 (包括 `PATH`) 。 若要使用此类功能，必须在命令行中调用 shell。 例如，在 Windows 计算节点上启动 `cmd.exe` 或在 Linux 节点上启动 `/bin/sh`：

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c "MyTaskApplication $MY_ENV_VAR"`

## <a name="environment-variables"></a>环境变量

| 变量名称                     | 说明                                                              | 可用性 | 示例 |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | 任务所属的 Batch 帐户名。                  | 所有任务。   | mybatchaccount |
| AZ_BATCH_ACCOUNT_URL            | Batch 帐户的 URL。 | 所有任务。 | `https://myaccount.westus.batch.azure.com` |
| AZ_BATCH_APP_PACKAGE            | 所有应用包环境变量的前缀。 例如，如果应用程序“FOO”版本 1 已安装到池上，则环境变量为 AZ_BATCH_APP_PACKAGE_FOO_1（Linux 上）或 AZ_BATCH_APP_PACKAGE_FOO#1（Windows 上）。 AZ_BATCH_APP_PACKAGE_FOO_1 指向将包下载到的位置（文件夹）。 使用应用包的默认版本时，使用不带版本号的 AZ_BATCH_APP_PACKAGE 环境变量。 如果在 Linux 中，应用程序包名称为“Agent-linux-x64”且版本为“1.1.46.0”，则环境变量实际为：AZ_BATCH_APP_PACKAGE_agent_linux_x64_1_1_46_0（使用下划线和小写）。 有关详细信息，请参阅 [执行安装的应用程序](batch-application-packages.md#execute-the-installed-applications) 了解更多详细信息。 | 包含关联应用包的任何任务。 如果节点本身拥有应用程序包，则还可用于所有任务。 | AZ_BATCH_APP_PACKAGE_FOO_1 (Linux) 或 AZ_BATCH_APP_PACKAGE_FOO#1 (Windows) |
| AZ_BATCH_AUTHENTICATION_TOKEN   | 一种身份验证令牌，用于授予对一组有限的 Batch 服务操作的访问权限。 仅当[添加任务](/rest/api/batchservice/task/add#request-body)时设置 [authenticationTokenSettings](/rest/api/batchservice/task/add#authenticationtokensettings) 时，才会显示此环境变量。 令牌值在 Batch API 中用作凭据以创建 Batch 客户端，例如在 [BatchClient.Open() .NET API](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_) 中。 | 所有任务。 | OAuth2 访问令牌 |
| AZ_BATCH_CERTIFICATES_DIR       | [任务工作目录](files-and-directories.md)内的目录，在其中存储用于 Linux 计算节点的证书。 此环境变量不适用于 Windows 计算节点。                                                  | 所有任务。   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_HOST_LIST              | 分配给[多实例任务](batch-mpi.md)的节点列表，采用 `nodeIP,nodeIP` 格式。 | 多实例主要和子任务。 | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | 指定当前节点是否为[多实例任务](batch-mpi.md)的主节点。 可能值为 `true` 和 `false`。| 多实例主要和子任务。 | `true` |
| AZ_BATCH_JOB_ID                 | 任务所属的作业的 ID。 | 除启动任务以外的所有任务。 | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | 节点上的作业准备[任务目录](files-and-directories.md)的完整路径。 | 除启动任务和作业准备任务之外的所有任务。 仅当使用作业准备任务来配置作业时才适用。 | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | 节点上的作业准备[任务工作目录](files-and-directories.md)的完整路径。 | 除启动任务和作业准备任务之外的所有任务。 仅当使用作业准备任务来配置作业时才适用。 | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_MASTER_NODE            | 在其上运行[多实例任务](batch-mpi.md)的主要任务的计算节点的 IP 地址和端口。 请勿对 MPI 和 NCCL 通信使用此处指定的端口 - 它被保留用于 Azure Batch 服务。 改用变量 MASTER_PORT，方式是使用通过命令行参数传入的值设置（端口 6105 是适合的默认选项），或者使用 AML 设置的值（若已设置）。 | 多实例主要和子任务。 | `10.0.0.4:6000` |
| AZ_BATCH_NODE_ID                | 任务分配到的节点的 ID。 | 所有任务。 | tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_IS_DEDICATED      | 如果为 `true`，则当前节点是一个专用节点。 如果为 `false`，则它是[低优先级节点](batch-low-pri-vms.md)。 | 所有任务。 | `true` |
| AZ_BATCH_NODE_LIST              | 分配给[多实例任务](batch-mpi.md)的节点列表，采用 `nodeIP;nodeIP` 格式。 | 多实例主要和子任务。 | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_NODE_MOUNTS_DIR        | 所有装载目录所在的节点级别[文件系统装载](virtual-file-mount.md)位置的完整路径。 Windows 文件共享使用驱动器号，因此对 Windows 来说，装载驱动器是设备和驱动器的组成部分。  |  如果用户知道已装载目录的装载权限，则包含启动任务的所有任务都可由该用户操作。 | 例如在 Ubuntu 中，位置为 `/mnt/batch/tasks/fsmounts` |
| AZ_BATCH_NODE_ROOT_DIR          | 节点上所有[批处理目录](files-and-directories.md)的根目录的完整路径。 | 所有任务。 | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | 节点上[共享目录](files-and-directories.md)的完整路径。 节点上执行的所有任务具有此目录的读取/写入权限。 在其他节点上执行的任务没有对此目录（它不是“共享”的网络目录）的远程访问权限。 | 所有任务。 | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | 节点上的[启动任务目录](files-and-directories.md)的完整路径。 | 所有任务。 | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | 运行任务的池的 ID。 | 所有任务。 | batchpool001 |
| AZ_BATCH_TASK_DIR               | 节点上的[任务目录](files-and-directories.md)的完整路径。 此目录包含任务的 `stdout.txt` 和 `stderr.txt`，以及 AZ_BATCH_TASK_WORKING_DIR。 | 所有任务。 | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | 当前任务的 ID。 | 除启动任务以外的所有任务。 | task001 |
| AZ_BATCH_TASK_SHARED_DIR | 对[多实例任务](batch-mpi.md)的主要任务和每个子任务来说相同的目录路径。 路径存在于运行多实例任务的每个节点上，并可供在该节点上运行的任务命令读取/写入 ([协调命令](batch-mpi.md#coordination-command) 和 [应用程序命令](batch-mpi.md#application-command)。 在其他节点上执行的子任务或主要任务不具有对此目录的远程访问权限（该目录不是“共享”的网络目录）。 | 多实例主要和子任务。 | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_TASK_WORKING_DIR       | 节点上的[任务工作目录](files-and-directories.md)的完整路径。 当前正在运行的任务具有对此目录的读取/写入权限。 | 所有任务。 | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | 分配给[多实例任务](batch-mpi.md)的节点和每节点内核数的列表。 使用 `numNodes<space>node1IP<space>node1Cores<space>` 格式列出了节点和内核<br/>`node2IP<space>node2Cores<space> ...`，其中节点数后跟一个或多个节点 IP 地址和每个节点的内核数。 |  多实例主要和子任务。 |`2 10.0.0.4 1 10.0.0.5 1` |

## <a name="next-steps"></a>后续步骤

- 了解如何 [使用带批处理的环境变量](jobs-and-tasks.md#environment-settings-for-tasks)。
- 详细了解 [Batch 中的文件和目录](files-and-directories.md)
- 了解 [多实例任务](batch-mpi.md)。
