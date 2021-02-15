---
title: 本地调试 PowerShell Azure Functions
description: 了解如何在本地运行时调试 PowerShell 函数。
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: a668024db126c82f96756555aba513b77f7d7366
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422954"
---
# <a name="debug-powershell-azure-functions-locally"></a>本地调试 PowerShell Azure Functions

Azure Functions 允许你将函数作为 PowerShell 脚本进行开发。

你可以使用以下标准开发工具，在本地调试 PowerShell 函数：

* [Visual Studio Code](https://code.visualstudio.com/)： Microsoft 的免费、轻型和开源文本编辑器，其中提供了 powershell 扩展，提供完整的 powershell 开发体验。
* PowerShell 控制台：使用与调试任何其他 PowerShell 进程相同的命令进行调试。

[Azure Functions Core Tools](functions-run-local.md) 支持 Azure Functions 的本地调试，包括 PowerShell 函数。

## <a name="example-function-app"></a>示例函数应用

本文中使用的函数应用有一个 HTTP 触发的函数，并且包含以下文件：

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

此函数应用与完成 [PowerShell 快速入门](./create-first-function-vs-code-powershell.md)时获取的应用类似。

中的函数代码 `run.ps1` 类似于以下脚本：

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>设置附加点

若要调试任何 PowerShell 函数，该函数需要停止才能附加调试器。 该 `Wait-Debugger` cmdlet 将停止执行并等待调试器。

>[!NOTE]
>使用 PowerShell 7 时，无需 `Wait-Debugger` 在代码中添加调用。

只需添加对 `Wait-Debugger` 语句上方的 cmdlet 的调用 `if` ，如下所示：

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

调试从语句开始 `if` 。 

`Wait-Debugger`现在，可以使用 Visual Studio Code 或 PowerShell 控制台调试函数。

## <a name="debug-in-visual-studio-code"></a>在 Visual Studio Code 中调试

若要在 Visual Studio Code 中调试 PowerShell 函数，则必须安装以下各项：

* [适用于 Visual Studio Code 的 PowerShell 扩展](/powershell/scripting/components/vscode/using-vscode)
* [适用于 Visual Studio Code 的 Azure Functions 扩展](./create-first-function-cli-powershell.md)
* [PowerShell Core 6.2 或更高版本](/powershell/scripting/install/installing-powershell-core-on-windows)

安装这些依赖项后，加载现有的 PowerShell 函数项目，或者 [创建第一个 Powershell 函数项目](./create-first-function-vs-code-powershell.md)。

>[!NOTE]
> 如果你的项目没有所需的配置文件，则系统会提示你添加它们。

### <a name="set-the-powershell-version"></a>设置 PowerShell 版本

PowerShell Core 与 Windows PowerShell 并行安装。 将 PowerShell Core 设置为 powershell 版本即可用于 Visual Studio Code 的 PowerShell 扩展。

1. 按 F1 显示命令托盘，然后搜索 `Session` 。

1. 选择 **PowerShell：显示会话菜单** 。

1. 如果 **当前会话** 不是 **PowerShell core 6** ，请选择 " **切换到： PowerShell core 6** "。

打开 PowerShell 文件后，会看到该版本在窗口右下角显示为绿色。 选择此文本还会显示 "会话" 菜单。 若要了解详细信息，请参阅 [选择用于扩展的 PowerShell 版本](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension)。

### <a name="start-the-function-app"></a>启动函数应用

验证 `Wait-Debugger` 是否在要附加调试器的函数中进行了设置。  `Wait-Debugger`添加后，可以使用 Visual Studio Code 调试函数应用。

选择 " **调试** " 窗格，并 **附加到 PowerShell 函数** 。

![调试器](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

还可以按 F5 键开始调试。

启动调试操作执行以下任务：

* `func extensions install`在终端中运行以安装函数应用所需的任何 Azure Functions 扩展。
* `func host start`在终端中运行以启动函数主机中的函数应用。
* 将 PowerShell 调试器附加到函数运行时中的 PowerShell 运行空间。

>[!NOTE]
> 需要确保 PSWorkerInProcConcurrencyUpperBound 设置为1，以确保 Visual Studio Code 中的调试体验正确。 这是默认值。

函数应用运行后，需要一个单独的 PowerShell 控制台来调用 HTTP 触发的函数。

在这种情况下，PowerShell 控制台就是客户端。 `Invoke-RestMethod`用于触发函数。

在 PowerShell 控制台中运行以下命令：

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

你会注意到响应不会立即返回。 这是因为， `Wait-Debugger` 已附加调试器，并且 PowerShell 执行会尽快进入中断模式。 这是由于 BreakAll 的 [概念](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place)，稍后将对此进行说明。 按下按钮后 `continue` ，调试器现在会在行的后面立即中断 `Wait-Debugger` 。

此时，已附加调试器，可以执行所有正常的调试器操作。 有关在 Visual Studio Code 中使用调试器的详细信息，请参阅 [官方文档](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions)。

继续并完全调用脚本后，你会注意到：

* 执行此操作的 PowerShell 控制台 `Invoke-RestMethod` 返回了结果
* Visual Studio Code 中的 PowerShell 集成控制台正在等待脚本执行

以后调用相同的函数时，PowerShell 扩展中的调试器将在之后立即中断 `Wait-Debugger` 。

## <a name="debugging-in-a-powershell-console"></a>在 PowerShell 控制台中调试

>[!NOTE]
> 本部分假设你已阅读 [Azure Functions Core Tools 文档](functions-run-local.md) ，并了解如何使用 `func host start` 命令启动函数应用。

`cd`在函数应用的目录中打开控制台，并运行以下命令：

```sh
func host start
```

在运行和的函数应用的情况 `Wait-Debugger` 下，你可以附加到进程。 需要两个以上的 PowerShell 控制台。

其中一个控制台充当客户端。 从此开始，调用 `Invoke-RestMethod` 以触发函数。 例如，你可以运行以下命令：

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

你会注意到，它不会返回响应，这是的结果 `Wait-Debugger` 。 PowerShell 运行空间现在正在等待要附加的调试程序。 接下来，我们来吧。

在其他 PowerShell 控制台中，运行以下命令：

```powershell
Get-PSHostProcessInfo
```

此 cmdlet 将返回一个表，该表类似于以下输出：

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

请记下 `ProcessId` 表中的项，并将其 `ProcessName` 作为 `dotnet` 。 此过程是函数应用。

接下来，运行以下代码片段：

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

启动后，调试器将中断，并显示类似于以下输出的内容：

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

此时，你会在 [PowerShell 调试器](/powershell/module/microsoft.powershell.core/about/about_debuggers)中的断点处停止。 在这里，你可以执行所有常见的调试操作：逐过程执行、逐过程执行、继续、退出以及其他操作。 若要查看控制台中可用的完整调试命令集，请运行 `h` 或 `?` 命令。

还可以通过 cmdlet 在此级别设置断点 `Set-PSBreakpoint` 。

继续并完全调用脚本后，你会注意到：

* 你执行的 PowerShell 控制台 `Invoke-RestMethod` 现在返回结果。
* 你执行的 PowerShell 控制台 `Debug-Runspace` 正在等待脚本执行。

您可以再次调用相同的函数 (`Invoke-RestMethod` 例如) ，调试器会在命令后立即中断 `Wait-Debugger` 。

## <a name="considerations-for-debugging"></a>调试注意事项

在调试函数代码时，请注意以下问题。

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` 可能导致调试器在意外的位置中断

PowerShell 扩展使用 `Debug-Runspace` ，后者又依赖于 PowerShell 的 `BreakAll` 功能。 此功能告知 PowerShell 在执行的第一个命令处停止。 此行为使你有机会在调试的运行空间内设置断点。

在实际调用脚本之前，Azure Functions 运行时运行几个命令 `run.ps1` ，因此调试器在或内可能会中断 `Microsoft.Azure.Functions.PowerShellWorker.psm1` `Microsoft.Azure.Functions.PowerShellWorker.psd1` 。

如果发生此中断，请运行 `continue` 或 `c` 命令跳过此断点。 然后，在预期的断点处停止。

## <a name="troubleshooting"></a>故障排除

如果在调试过程中遇到困难，应检查以下各项：

| 检查 | 操作 |
|------|------|
| `func --version`从终端运行。 如果收到一个 `func` 找不到的错误，则可能是本地变量中缺少 ( # A0) 的核心工具 `path` 。| [重新安装核心工具](functions-run-local.md#v2)。|  
| 在 Visual Studio Code 中，默认终端需要有权访问 func.exe。 请确保未使用未安装核心工具的默认终端，如适用于 Linux 的 Windows 子系统 (WSL) 。  | 将 Visual Studio Code 中的默认 shell 设置为 PowerShell 7 (推荐的) 或 Windows PowerShell 5.1。|
  

## <a name="next-steps"></a>后续步骤

若要了解有关使用 PowerShell 开发函数的详细信息，请参阅 [Azure Functions powershell 开发人员指南](functions-reference-powershell.md)。
