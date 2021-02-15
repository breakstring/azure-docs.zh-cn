---
title: 在 Visual Studio 中调试应用程序
description: 通过在本地开发群集上采用 Visual Studio 进行开发和调试，来提高服务的可靠性和性能。
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 0b7d08d610c883240abedc66c55abba64a74c8e3
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576309"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>使用 Visual Studio 调试 Service Fabric 应用程序
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>调试本地 Service Fabric 应用程序
可以通过在本地计算机开发群集中部署和调试 Azure Service Fabric 应用程序来节省时间和资金。 Visual Studio 2019 或 2015 可以将应用程序部署到本地群集，并将调试器自动连接到应用程序的所有实例。 必须以管理员身份运行 Visual Studio 才能连接调试器。

1. 按照[设置 Service Fabric 开发环境](service-fabric-get-started.md)中的步骤启动本地开发群集。
2. 按 **F5** 或单击“**调试**” > **开始调试**。
   
    ![显示“调试”菜单的屏幕截图。][startdebugging]
3. 通过单击“调试”  菜单中的命令来设置代码中的断点并单步执行应用程序。
   
   > [!NOTE]
   > Visual Studio 将附加到应用程序的所有实例。 单步执行代码时，断点可能被多个进程命中，从而产生并发会话。 尝试通过在线程 ID 上设置每个断点条件，或使用诊断事件，在命中后禁用断点。
   > 
   > 
4. “诊断事件”  窗口会自动打开，以便用户可实时查看诊断事件。
   
    ![查看实时诊断事件][diagnosticevents]
5. 也可以在 Cloud Explorer 中打开“**诊断事件**”窗口。  在“**Service Fabric**”下，右键单击任何节点，并选择“**查看流式跟踪”** 。
   
    ![打开“诊断事件”窗口][viewdiagnosticevents]
   
    如果要筛选对特定服务或应用程序的跟踪，请在该特定服务或应用程序上启用流式跟踪。
6. 诊断事件可以在自动生成的 **ServiceEventSource.cs** 文件中查看并从应用程序代码中进行调用。
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. “诊断事件”  窗口支持实时筛选、暂停和检查事件。  筛选是对事件消息及其内容进行的简单字符串搜索。
   
    ![实时筛选、暂停和恢复或检查事件][diagnosticeventsactions]
8. 调试服务与调试其他任何应用程序类似。 通常，将通过 Visual Studio 设置断点，以便轻松调试。 即使可靠集合在多个节点间进行复制，它们仍会实现 IEnumerable。 此实现意味着，可以在调试时使用 Visual Studio 中的结果视图来查看其中存储的内容。 为此，可以在代码中的任意位置设置断点。
   
    ![开始调试应用程序][breakpoint]


### <a name="running-a-script-as-part-of-debugging"></a>在调试过程中运行脚本
某些情况下，可能需要在启动调试会话的过程中运行脚本（例如，在不使用默认服务时）。

在 Visual Studio 中，可以将名为 **Start-Service.ps1** 的文件添加到 Service Fabric 应用程序项目 (.sfproj) 的 **Scripts** 文件夹中。 在本地群集中创建完应用程序以后，就会调用该脚本。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>调试远程 Service Fabric 应用程序
如果 Service Fabric 应用程序正在 Azure 中的 Service Fabric 群集上运行，你可以直接从 Visual Studio 远程调试这些应用程序。

> [!NOTE]
> 此功能需要 [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) 和 [Azure SDK for .NET 2.9](https://azure.microsoft.com/downloads/)。    

<!-- -->
> [!WARNING]
> 远程调试适用于开发/测试方案，而非用于生产环境中，因为它会对运行中的应用程序造成影响。

1. 在“云资源管理器”中导航到群集  。 右键单击并选择“启用调试” 
   
    ![启用远程调试][enableremotedebugging]
   
    此操作将启动在群集节点和所需的网络配置上启用远程调试扩展的过程。
2. 在“**Cloud Explorer**”中右键单击群集节点，并选择“**附加调试器**”。
   
    ![附加调试程序][attachdebugger]
3. 在“**附加到进程**”对话框中，选择想要调试的进程，并单击“**附加**”。
   
    ![选择进程][chooseprocess]
   
    想要附加到的进程名称等于服务项目程序集名称。
   
    调试器将附加到运行该进程的所有节点。
   
   * 如果调试的是无状态服务，所有节点上的该服务的所有实例都是调试会话的一部分。
   * 如果调试的是有状态服务，则只有任何分区的主副本才将处于活动状态并因此会被调试器捕获。 如果在调试会话期间移动主副本，仍在调试会话中处理该副本。
   * 若要仅捕获给定服务的相关分区或实例，可以使用条件断点，以仅中断特定的分区或实例。
     
     ![条件断点][conditionalbreakpoint]
     
     > [!NOTE]
     > 目前我们不支持使用具有相同服务可执行文件名的多个实例调试 Service Fabric 群集。
     > 
     > 
4. 应用程序调试完成后，即可在“**Cloud Explorer**”中右键单击群集并选择“**禁用调试**”来禁用远程调试扩展
   
    ![禁用远程调试][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>从远程群集节点流式传输跟踪
也可直接从远程群集节点将跟踪流式传输到 Visual studio。 借助此功能，可以流式传输在 Service Fabric 群集节点上生成的 ETW 跟踪事件。

> [!NOTE]
> 此功能需要 [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) 和 [Azure SDK for .NET 2.9](https://azure.microsoft.com/downloads/)。
> 此功能仅支持在 Azure 中运行的群集。
> 
> 

<!-- -->
> [!WARNING]
> 流跟踪适用于开发/测试方案，而非用于生产环境中，因为它会对运行中的应用程序造成影响。
> 在生产方案中，应依赖于使用 Azure 诊断转发事件。

1. 在“云资源管理器”中导航到群集  。 右键单击并选择“启用流式跟踪” 
   
    ![启用远程流跟踪][enablestreamingtraces]
   
    此操作将启动在群集节点以及所需的网络配置上启用流式跟踪扩展的过程。
2. 在“**Cloud Explorer**”中展开“**节点**”元素，右键单击想要进行流式跟踪的节点，并选择“**查看流式跟踪**”
   
    ![查看远程流跟踪][viewremotestreamingtraces]
   
    针对想要从其中查看跟踪的任意数目节点重复步骤 2。 每个节点流显示在专用窗口中。
   
    现在即可查看 Service Fabric 以及服务发出的跟踪。 如果想要筛选事件以便只显示特定的应用程序，只需在筛选器中键入应用程序的名称即可。
   
    ![查看流跟踪][viewingstreamingtraces]
3. 在群集中完成流式跟踪后，即可在“**Cloud Explorer**”中右键单击群集，并选择“**禁用流式跟踪**”来禁用远程流式跟踪。
   
    ![禁用远程流跟踪][disablestreamingtraces]

## <a name="next-steps"></a>后续步骤
* [测试 Service Fabric 服务](service-fabric-testability-overview.md)。
* [在 Visual Studio 中管理 Service Fabric 应用程序](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
