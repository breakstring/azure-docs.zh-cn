---
title: 将功能标志添加到 .NET Framework 应用的快速入门 | Microsoft Docs | Microsoft Docs
description: 有关将功能标志添加到 .NET Framework 应用以及在 Azure 应用程序配置中管理这些标志的快速入门
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 10/19/2020
ms.author: alkemper
ms.openlocfilehash: 513c826e11ff9dfe6ea94349c67620da9d1bba48
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932041"
---
# <a name="quickstart-add-feature-flags-to-a-net-framework-app"></a>快速入门：向 .NET Framework 应用添加功能标志

在本快速入门中，你要将 Azure 应用程序配置合并到 .NET Framework 应用中，以创建功能管理的端到端实现。 可以使用应用程序配置服务集中存储所有功能标志并控制其状态。 

.NET 功能管理库使用功能标志支持扩展该框架。 这些库在 .NET 配置系统的基础上构建。 它们通过其 .NET 配置提供程序与应用程序配置集成。

## <a name="prerequisites"></a>先决条件

- Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/dotnet)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.8](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>创建应用配置存储区

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. 选择“功能管理器” > “+添加”以添加名为 `Beta` 的功能标志。  

    > [!div class="mx-imgBorder"]
    > ![启用名为 Beta 的功能标志](media/add-beta-feature-flag.png)

    暂时不定义 `label`。

## <a name="create-a-net-console-app"></a>创建 .NET 控制台应用

1. 启动 Visual Studio，并选择“文件” > “新建” > “项目”    。

1. 在 **创建新项目** 中，针对“控制台”  项目类型进行筛选，然后单击“控制台应用(.NET Framework)”  。 单击“下一步”。 

1. 在 **配置新项目** 中，输入项目名称。 在“Framework”  下，选择“.NET Framework 4.8”  或更高版本。 单击“创建”。 

## <a name="connect-to-an-app-configuration-store"></a>连接到应用程序配置存储区

1. 右键单击项目，然后选择“管理 NuGet 包”  。 在“浏览”选项卡中，搜索以下 NuGet 包并将其添加到项目中  。 如果无法找到，请选中“包括预发行版”复选框  。

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration.AzureAppConfiguration
    Microsoft.FeatureManagement
    ```

1. 打开文件 *Program.cs* 并添加以下语句：

    ```csharp
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    using Microsoft.FeatureManagement;
    using System.Threading.Tasks;
    ```

1. 更新 `Main` 方法以连接到应用程序配置，指定 `UseFeatureFlags` 选项以检索功能标志。 若启用 `Beta` 功能标志，将显示一条消息。

    ```csharp
        public static async Task Main(string[] args)
        {         
            IConfigurationRoot configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                           .UseFeatureFlags();
                }).Build();

            IServiceCollection services = new ServiceCollection();

            services.AddSingleton<IConfiguration>(configuration).AddFeatureManagement();

            using (ServiceProvider serviceProvider = services.BuildServiceProvider())
            {
                IFeatureManager featureManager = serviceProvider.GetRequiredService<IFeatureManager>();

                if (await featureManager.IsEnabledAsync("Beta"))
                {
                    Console.WriteLine("Welcome to the beta!");
                }
            }

            Console.WriteLine("Hello World!");
            Console.WriteLine("Press any key to continue ...");
            Console.Read();
        }
    ```

## <a name="build-and-run-the-app-locally"></a>在本地生成并运行应用

1. 将名为 ConnectionString 的环境变量设置为应用程序配置存储区的连接字符串  。 如果使用 Windows 命令提示符，请运行以下命令：

    ```console
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    如果使用 Windows PowerShell，请运行以下命令：

    ```powershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. 重启 Visual Studio 以便使所做更改生效。 

1. 按 Ctrl+F5 生成并运行控制台应用。

    ![启用了功能标志的应用](./media/quickstarts/dotnet-app-feature-flag.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已经在应用程序配置中创建了一个功能标志，并将其用于 .NET Framework 控制台应用。 若要了解如何在不重启应用程序的情况下动态更新功能标志和其他配置值，请继续学习下一教程。

> [!div class="nextstepaction"]
> [启用动态配置](./enable-dynamic-configuration-dotnet.md)
