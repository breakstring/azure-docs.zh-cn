---
title: 使用 Azure 服务总线的 .NET 多层应用程序 | Microsoft 文档
description: 本 .NET 教程可帮助你在 Azure 中开发使用服务总线队列在各层之间进行通信的多层应用。
ms.devlang: dotnet
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 40529df5195a29fbf2ff4887311932c2ffbf471d
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029889"
---
# <a name="net-multi-tier-application-using-azure-service-bus-queues"></a>使用 Azure 服务总线队列创建 .NET 多层应用程序

使用 Visual Studio 和免费的 Azure SDK for .NET，可以轻松针对 Microsoft Azure 进行开发。 本教程指导完成创建使用在本地环境中运行的多个 Azure 资源的应用程序的步骤。

学习以下技能：

* 如何通过单个下载和安装来使计算机能够进行 Azure 开发。
* 如何使用 Visual Studio 针对 Azure 进行开发。
* 如何使用 Web 角色和辅助角色在 Azure 中创建多层应用程序。
* 如何使用服务总线队列在各层之间进行通信。

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

在本教程中，将生成多层应用程序并在 Azure 云服务中运行它。 前端为 ASP.NET MVC Web 角色，后端为使用服务总线队列的辅助角色。 可以创建与前端相同的多层应用程序，作为要部署到 Azure 网站而不是云服务的 Web 项目。 还可以试用 [.NET 本地/云混合应用程序](../azure-relay/service-bus-dotnet-hybrid-app-using-service-bus-relay.md)教程。

以下屏幕截图显示已完成的应用程序。

![应用程序“提交”页的屏幕截图。][0]

## <a name="scenario-overview-inter-role-communication"></a>方案概述：角色间通信
若要提交处理命令，以 Web 角色运行的前端 UI 组件必须与以辅助角色运行的中间层逻辑进行交互。 此示例使用服务总线消息传送在各层之间进行通信。

在 Web 层和中间层之间使用服务总线消息传送将分离这两个组件。 与直接消息传送（即 TCP 或 HTTP）不同，Web 层不会直接连接到中间层，而是将工作单元作为消息推送到服务总线，服务总线以可靠方式保留这些工作单元，直到中间层准备好使用和处理它们。

服务总线提供了两个实体以支持中转消息传送：队列和主题。 通过队列，发送到队列的每个消息均由一个接收方使用。 主题支持发布/订阅模式，在该模式中，每个已发布消息都将提供给在该主题中注册的订阅。 每个订阅都以逻辑方式保留自己的消息队列。 此外，还可以使用筛选规则配置订阅，这些规则可将传递给订阅队列的消息集限制为符合筛选条件的消息集。 以下示例使用服务总线队列。

![显示 Web 角色、服务总线和辅助角色之间通信的关系图。][1]

与直接消息传送相比，此通信机制具有多项优势：

* **暂时分离。** 使用异步消息传送模式，生产者和使用者不需要在同一时间联机。 服务总线可靠地存储消息，直到使用方准备好接收它们。 这会允许分布式应用程序的组件断开连接，例如，为进行维护而自动断开，或因组件故障断开连接，而不会影响系统的整体性能。 此外，使用方应用程序可能只需在一天的特定时段内联机。
* **负载量。** 在许多应用程序中，系统负载随时间而变化，而每个工作单元所需的处理时间通常为常量。 使用队列在消息创建者与使用者之间中继意味着，只需将使用方应用程序（辅助）预配为适应平均负载而非最大负载。 队列深度将随传入负载的变化而加大和减小。 这会直接根据为应用程序加载提供服务所需的基础结构的数目来节省成本。
* **负载均衡。** 随着负载增加，可添加更多的工作进程以从队列中读取。 每条消息仅由一个辅助进程处理。 另外，可通过此基于拉取的负载均衡来以最合理的方式使用辅助计算机，即使这些辅助计算机具有不同的处理能力（因为它们以其最大速率拉取消息）也是如此。 此模式通常称为 *使用者竞争* 模式。
  
  ![显示 Web 角色、服务总线和两个辅助角色之间通信的关系图。][2]

以下各节讨论了实现此体系结构的代码。

## <a name="create-a-namespace"></a>创建命名空间

第一步是创建命名空间  并获取该命名空间的[共享访问签名 (SAS) 密钥](service-bus-sas.md)。 命名空间为每个通过服务总线公开的应用程序提供应用程序边界。 创建命名空间后，系统将生成一个 SAS 密钥。 命名空间名称与 SAS 密钥的组合为服务总线提供了用于验证应用程序访问权限的凭据。

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-web-role"></a>创建 Web 角色

在本部分中，会生成应用程序的前端。 首先，将创建应用程序显示的各种页面。
之后，将添加代码，以便将项目提交到服务总线队列并显示有关队列的状态信息。

### <a name="create-the-project"></a>创建项目

1. 使用管理员特权启动 Visual Studio：右键单击“Visual Studio”  程序图标，并单击“以管理员身份运行”  。 本文后面所述的 Azure 计算仿真程序要求以管理员权限启动 Visual Studio。
   
   在 Visual Studio 的“文件”菜单中，单击“新建”，并单击“项目”。
2. 从“Visual C#”下的“已安装模板”中，单击“云”，并单击“Azure 云服务”。 **MultiTierApp**。 然后单击“确定”  。
   
   ![“新建项目”对话框的屏幕截图，其中选择了“云”，并突出显示了“Azure 云服务 Visual C#”并添加了红色边框。][9]
3. 在 " **角色** " 窗格中，双击 " **ASP.NET Web 角色**"。
   
   ![选中 "ASP.NET" Web 角色并选择 "WebRole1" 的 "新建 Microsoft Azure 云服务" 对话框的屏幕截图。][10]
4. 将鼠标指针停留在“Azure 云服务解决方案”  下的“WebRole1”  上，单击铅笔图标，并将 Web 角色重命名为“FrontendWebRole”  。 然后单击“确定”  。 （请确保输入“Frontend”而不是“FrontEnd”，此处为小写“e”。）
   
   ![新的 "Microsoft Azure 云服务" 对话框的屏幕截图，解决方案已重命名为 FrontendWebRole。][11]
5. 从“新建 ASP.NET 项目”  对话框的“选择模板”  列表中，单击“MVC”  。
   
   ![“新建 ASP.NET 项目”对话框的屏幕截图，其中突出显示了“MVC”并添加了红色边框，还对“更改身份验证”选项添加了红色边框。][12]
6. 仍然在“新建 ASP.NET 项目”  对话框中，单击“更改身份验证”  按钮。 在“更改身份验证”对话框中，确保已选择“无身份验证”，然后单击“确定”    。 在本教程中，将部署无需用户登录名的应用。
   
    ![“更改身份验证”对话框的屏幕截图，其中选择了“无身份验证”选项并添加了红色边框。][16]
7. 返回到“新建 ASP.NET 项目”  对话框，单击“确定”  以创建项目。
8. 在 **解决方案资源管理器** 的 **FrontendWebRole** 项目中，右键单击 " **引用**"，然后单击 " **管理 NuGet 包**"。
9. 单击“浏览”  选项卡，然后搜索“WindowsAzure.ServiceBus”  。 搜索 **WindowsAzure.ServiceBus** 包，单击“安装”，并接受使用条款。
   
   ![“管理 NuGet 包”对话框的屏幕快照，其中突出显示了“WindowsAzure.ServiceBus”，并对“安装”选项添加了红色边框。][13]
   
   请注意，现已引用所需的客户端程序集并已添加部分新代码文件。
10. 在“解决方案资源管理器”  中，右键单击“模型”  ，并依次单击“添加”  和“类”  。 在“名称”  框中，键入名称“OnlineOrder.cs”  。 然后单击“添加”  。

### <a name="write-the-code-for-your-web-role"></a>为 Web 角色编写代码
在本部分，将创建应用程序显示的各种页面。

1. 在 Visual Studio 的 OnlineOrder.cs 文件中将现有命名空间定义替换为以下代码：
   
   ```csharp
   namespace FrontendWebRole.Models
   {
       public class OnlineOrder
       {
           public string Customer { get; set; }
           public string Product { get; set; }
       }
   }
   ```
2. 在“解决方案资源管理器”  中，双击“Controllers\HomeController.cs”  。 在文件顶部添加以下 **using** 语句以包括针对你刚创建的模型以及服务总线的命名空间。
   
   ```csharp
   using FrontendWebRole.Models;
   using Microsoft.ServiceBus.Messaging;
   using Microsoft.ServiceBus;
   ```
3. 仍在 Visual Studio 的 HomeController.cs 文件中，将现有命名空间定义替换为以下代码。 此代码包含用于处理将项提交到队列这一任务的方法。
   
   ```csharp
   namespace FrontendWebRole.Controllers
   {
       public class HomeController : Controller
       {
           public ActionResult Index()
           {
               // Simply redirect to Submit, since Submit will serve as the
               // front page of this application.
               return RedirectToAction("Submit");
           }
   
           public ActionResult About()
           {
               return View();
           }
   
           // GET: /Home/Submit.
           // Controller method for a view you will create for the submission
           // form.
           public ActionResult Submit()
           {
               // Will put code for displaying queue message count here.
   
               return View();
           }
   
           // POST: /Home/Submit.
           // Controller method for handling submissions from the submission
           // form.
           [HttpPost]
           // Attribute to help prevent cross-site scripting attacks and
           // cross-site request forgery.  
           [ValidateAntiForgeryToken]
           public ActionResult Submit(OnlineOrder order)
           {
               if (ModelState.IsValid)
               {
                   // Will put code for submitting to queue here.
   
                   return RedirectToAction("Submit");
               }
               else
               {
                   return View(order);
               }
           }
       }
   }
   ```
4. 在“生成”  菜单中，单击“生成解决方案”  以测试工作的准确性。
5. 现在，为前面创建的 `Submit()` 方法创建视图。 在 `Submit()` 方法（不带任何参数的 `Submit()` 的重载函数）中右键单击，并选择“添加视图”  。
   
   ![代码的屏幕截图，焦点位于 Submit 方法和一个下拉列表，其中突出显示了“添加视图”选项。][14]
6. 此时会显示一个用于创建视图的对话框。 在“模板”  列表中，选择“创建”  。 在“模型类”  列表中，选择“OnlineOrder”  类。
   
   ![“添加视图”对话框的屏幕截图，其中对“模板”和“模型类”下拉列表添加了红色边框。][15]
7. 单击“添加”  。
8. 现在，请更改应用程序的显示名称。 在“解决方案资源管理器”中，双击“views/shared\\_Layout.cshtml”文件以在 Visual Studio 编辑器中将其打开。
9. 将 **ASP.NET 应用程序** 的所有匹配项替换为 **Northwind 商贸产品**。
10. 删除“Home”  、“About”  和“Contact”  链接。 删除突出显示的代码：
    
    ![代码的截图，其中突出显示了三行 HTML 操作链接代码。][28]
11. 最后，修改提交页以包含有关队列的一些信息。 在“解决方案资源管理器”  中，双击“Views\Home\Submit.cshtml”  文件以在 Visual Studio 编辑器中将其打开。 `<h2>Submit</h2>`后面添加以下行。 `ViewBag.MessageCount` 当前为空。 稍后将填充它。
    
    ```html
    <p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>
    ```
12. 现在，已实现 UI。 可以按 **F5** 运行应用程序并确认其按预期方式运行。
    
    ![应用程序“提交”页的屏幕截图。][17]

### <a name="write-the-code-for-submitting-items-to-a-service-bus-queue"></a>编写用于将项提交到 Service Bus 队列的代码
现在，将添加用于将项提交到队列的代码。 首先，将创建一个包含服务总线队列连接信息的类。 然后，将从 Global.aspx.cs 初始化连接。 最后，将更新你之前在 HomeController.cs 中创建的提交代码以便实际将项提交到服务总线队列。

1. 在“解决方案资源管理器”  中，右键单击“FrontendWebRole”  （右键单击项目而不是角色）。 单击“添加”  ，并单击“类”  。
2. 将类命名为 **QueueConnector.cs**。 单击“添加”  以创建类。
3. 现在，将添加可封装连接信息并初始化服务总线队列连接的代码。 将 QueueConnector.cs 的全部内容替换为下面的代码，并输入 `your Service Bus namespace`（命名空间名称）和 `yourKey`（之前从 Azure 门户中获取的 **主要密钥**）的值。
   
   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using System.Web;
   using Microsoft.ServiceBus.Messaging;
   using Microsoft.ServiceBus;
   
   namespace FrontendWebRole
   {
       public static class QueueConnector
       {
           // Thread-safe. Recommended that you cache rather than recreating it
           // on every request.
           public static QueueClient OrdersQueueClient;
   
           // Obtain these values from the portal.
           public const string Namespace = "your Service Bus namespace";
   
           // The name of your queue.
           public const string QueueName = "OrdersQueue";
   
           public static NamespaceManager CreateNamespaceManager()
           {
               // Create the namespace manager which gives you access to
               // management operations.
               var uri = ServiceBusEnvironment.CreateServiceUri(
                   "sb", Namespace, String.Empty);
               var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                   "RootManageSharedAccessKey", "yourKey");
               return new NamespaceManager(uri, tP);
           }
   
           public static void Initialize()
           {
               // Using Http to be friendly with outbound firewalls.
               ServiceBusEnvironment.SystemConnectivity.Mode =
                   ConnectivityMode.Http;
   
               // Create the namespace manager which gives you access to
               // management operations.
               var namespaceManager = CreateNamespaceManager();
   
               // Create the queue if it does not exist already.
               if (!namespaceManager.QueueExists(QueueName))
               {
                   namespaceManager.CreateQueue(QueueName);
               }
   
               // Get a client to the queue.
               var messagingFactory = MessagingFactory.Create(
                   namespaceManager.Address,
                   namespaceManager.Settings.TokenProvider);
               OrdersQueueClient = messagingFactory.CreateQueueClient(
                   "OrdersQueue");
           }
       }
   }
   ```
4. 现在，请确保 **Initialize** 方法会被调用。 在“解决方案资源管理器”  中，双击“Global.asax\Global.asax.cs”  。
5. 在 **Application_Start** 方法的末尾添加以下代码行。
   
   ```csharp
   FrontendWebRole.QueueConnector.Initialize();
   ```
6. 最后，更新之前创建的 Web 代码以便将项提交到队列。 在“解决方案资源管理器”  中，双击“Controllers\HomeController.cs”  。
7. 更新 `Submit()` 方法（不包含任何参数的重载），如下所示，获取队列的消息计数。
   
   ```csharp
   public ActionResult Submit()
   {
       // Get a NamespaceManager which allows you to perform management and
       // diagnostic operations on your Service Bus queues.
       var namespaceManager = QueueConnector.CreateNamespaceManager();
   
       // Get the queue, and obtain the message count.
       var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
       ViewBag.MessageCount = queue.MessageCount;
   
       return View();
   }
   ```
8. 更新 `Submit(OnlineOrder order)` 方法（包含一个参数的重载），如下所示，将订单信息提交到队列。
   
   ```csharp
   public ActionResult Submit(OnlineOrder order)
   {
       if (ModelState.IsValid)
       {
           // Create a message from the order.
           var message = new BrokeredMessage(order);
   
           // Submit the order.
           QueueConnector.OrdersQueueClient.Send(message);
           return RedirectToAction("Submit");
       }
       else
       {
           return View(order); 
       }
   }
   ```
9. 现在，可以重新运行应用程序。 每提交订单时，消息计数都会增大。
   
   ![应用程序的“提交”页的屏幕截图，其中显示消息计数递增到 1。][18]

## <a name="create-the-worker-role"></a>创建辅助角色
现在，将创建用于处理订单提交的辅助角色。 此示例使用“服务总线队列的辅助角色”  Visual Studio 项目模板。 已从门户中获取所需的凭据。

1. 确保已将 Visual Studio 连接到 Azure 帐户。
2. 在 Visual Studio 的 **解决方案资源管理器** 中，右键单击 **MultiTierApp** 项目下的 "**角色**" 文件夹。
3. 单击“添加”  ，并单击“新建辅助角色项目”  。 此时会显示“添加新角色项目”  对话框。
   
   ![“解决方案资源管理器”窗格的屏幕截图，其中突出显示了“新建辅助角色项目”选项和“添加”选项。][26]
4. 在“添加新角色项目”  对话框中，单击“服务总线队列的辅助角色”  。
   
   ![“添加新角色项目”对话框的屏幕截图，其中突出显示了“服务总线队列的辅助角色”选项，并对其添加了红色边框。][23]
5. 在“名称”  框中，将项目命名为“OrderProcessingRole”  。 然后单击“添加”  。
6. 将在“创建服务总线命名空间”部分的步骤 9 中获取的连接字符串复制到剪贴板。
7. 在“解决方案资源管理器”中，右键单击在步骤 5 中创建的“OrderProcessingRole”（确保右键单击“角色”下的“OrderProcessingRole”而不是类）。 然后单击“属性”  。
8. 在“属性”对话框的“设置”选项卡中，在“Microsoft.ServiceBus.ConnectionString”的“值”框内单击，并粘贴在步骤 6 中复制的终结点值。
   
   ![“属性”对话框的屏幕截图，其中选择了“设置”选项卡，并对“Microsoft.ServiceBus.ConnectionString”表行添加了红色边框。][25]
9. 从队列中处理订单时，创建一个 **OnlineOrder** 类来表示这些订单。 可以重用已创建的类。 在“解决方案资源管理器”  中，右键单击“OrderProcessingRole”  类（右键单击类图标，而不是角色）。 单击“添加”  ，并单击“现有项”  。
10. 浏览到 **FrontendWebRole\Models** 的子文件夹，然后双击“OnlineOrder.cs”  以将其添加到此项目中。
11. 在 **WorkerRole.cs** 中，将 **QueueName** 变量的值 `"ProcessingQueue"` 更改为 `"OrdersQueue"`，如以下代码所示。
    
    ```csharp
    // The name of your queue.
    const string QueueName = "OrdersQueue";
    ```
12. 在 WorkerRole.cs 文件顶部添加以下 using 语句。
    
    ```csharp
    using FrontendWebRole.Models;
    ```
13. 在 `Run()` 函数中，在 `OnMessage()` 调用的内部，将 `try` 子句的内容替换为以下代码。
    
    ```csharp
    Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
    // View the message as an OnlineOrder.
    OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
    Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
    receivedMessage.Complete();
    ```
14. 已完成此应用程序。 可以测试整个应用程序，方法是右键单击“解决方案资源管理器”中的 MultiTierApp 项目，选择“设置为启动项目”  ，然后按 F5。 请注意，消息计数不会递增，因为辅助角色会处理队列中的项并将其标记为完成。 可以通过查看 Azure 计算模拟器 UI 来查看辅助角色的跟踪输出。 可通过右击任务栏的通知区域中的模拟器图标并选择“显示计算模拟器 UI”  来执行此操作。
    
    ![单击模拟器图标时显示的内容的屏幕截图。 选项列表中列有“显示计算模拟器 UI”。][19]
    
    ![Microsoft Azure 计算模拟器 (Express) "对话框的屏幕截图。][20]

## <a name="next-steps"></a>后续步骤
若要了解有关 Service Bus 的详细信息，请参阅以下资源：  

* [服务总线队列入门][sbacomqhowto]
* [服务总线服务页][sbacom]  

若要了解有关多层方案的详细信息，请参阅：  

* [使用存储表、队列和 Blob 的 .NET 多层应用程序][mutitierstorage]  

[0]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-app.png
[1]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
[2]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
[9]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
[10]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
[11]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
[12]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
[13]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
[14]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
[15]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
[16]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-14.png
[17]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-app.png
[18]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-app2.png

[19]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
[20]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
[23]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
[25]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
[26]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
[28]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png

[sbacom]: https://azure.microsoft.com/services/service-bus/  
[sbacomqhowto]: service-bus-dotnet-get-started-with-queues.md  
[mutitierstorage]: https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
