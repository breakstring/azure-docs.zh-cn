---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 3f1a24db459cabd65d9ce17b89105c3b9ab8abb7
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188214"
---
1. 启动 Visual Studio 2019。

1. 确保“.NET 跨平台开发”  工作负荷可用。 在 Visual Studio 菜单栏中选择“工具”   >   “获取工具和功能”，以打开 Visual Studio 安装程序。 如果此工作负荷已启用，请关闭对话框。

   ![Visual Studio 安装程序的屏幕截图，其中突出显示了“工作负荷”选项卡](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-net-core-workload.png)

   否则，请选择“.NET Core 跨平台开发”旁边的框，  然后选择对话框右下角的“修改”。  安装新功能将花费一些时间。

1. 创建新的 Visual C# .NET Core 控制台应用。 在“新建项目”对话框中，从左侧窗格中展开“已安装” > “Visual C#” > “.NET Core”。     然后，选择“控制台应用(.NET Core)”  。 在项目名称处，输入 helloworld  。

   ![“新建项目”对话框屏幕截图](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "创建 Visual C# 控制台应用 (.NET Core)")

1. 安装并引用[语音 SDK NuGet 包](https://aka.ms/csspeech/nuget)。 在解决方案资源管理器中，右键单击该解决方案，并选择“为解决方案管理 NuGet 包”  。

   ![解决方案资源管理器的屏幕截图，其中突出显示了“为解决方案管理 NuGet 包”](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "管理解决方案 NuGet 程序包")

1. 在右上角的“包源”字段中，选择“nuget.org”   。搜索 `Microsoft.CognitiveServices.Speech` 包，并将其安装到“helloworld”项目中  。

   ![屏幕截图显示“管理解决方案包”对话框。](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "安装 NuGet 包")

1. 接受显示的许可证即可开始安装 NuGet 包。

   ![“接受许可证”对话框的屏幕截图](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "接受许可证")

安装此包后，在包管理器控制台中会显示一条确认消息。
