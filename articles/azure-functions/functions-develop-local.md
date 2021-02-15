---
title: 在本地开发和运行 Azure Functions
description: 了解如何在本地计算机上编写 Azure Functions 代码并对其进行测试，然后再 Azure Functions 上运行它们。
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 9c37d51abcc8d612b777b845515cf07666369d4f
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168128"
---
# <a name="code-and-test-azure-functions-locally"></a>在本地对 Azure Functions 进行编码和测试

尽管你可以在 [Azure 门户]中开发和测试 Azure Functions，但许多开发人员更偏爱本地开发体验。 在 Functions 中，可以轻松使用你偏好的代码编辑器和开发工具在本地计算机上开发和测试函数。 本地函数可以连接到实时 Azure 服务，你可以在本地计算机上使用完整的 Functions 运行时调试函数。

## <a name="local-development-environments"></a>本地开发环境

在本地计算机开发函数的方式取决于[语言](supported-languages.md)和工具偏好。 下表中的环境支持本地开发：

|环境                              |语言         |说明|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [C # (类库) ](functions-dotnet-class-library.md)， [c # 脚本 () ](functions-reference-csharp.md)， [JavaScript](functions-reference-node.md)， [PowerShell](./create-first-function-vs-code-powershell.md)， [Python](functions-reference-python.md) | [适用于 VS Code 的 Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)在 VS Code 中添加了 Functions 支持。 需要 Core Tools。 使用 2.x 版 Core Tools 时，支持 Linux、MacOS 和 Windows 上的开发。 若要了解详细信息，请参阅[使用 Visual Studio Code 创建第一个函数](./create-first-function-vs-code-csharp.md)。 |
| [命令提示符或终端](functions-run-local.md) | [C # (类库) ](functions-dotnet-class-library.md)， [c # 脚本 () ](functions-reference-csharp.md)， [JavaScript](functions-reference-node.md)， [PowerShell](functions-reference-powershell.md)， [Python](functions-reference-python.md) | [Azure Functions Core Tools] 提供核心运行时和模板用于创建函数，以实现本地开发。 版本 2.x 支持 Linux、MacOS 和 Windows 上的开发。 所有环境依赖于 Core Tools 提供本地 Functions 运行时。 |
| [Visual Studio 2019](functions-develop-vs.md) | [C#（类库）](functions-dotnet-class-library.md) | Azure Functions 工具包含在 [Visual Studio 2019](https://www.visualstudio.com/vs/) 和更高版本的 **Azure 开发** 工作负荷中。 可以编译类库中的函数，并将 .dll 文件发布到 Azure。 包含用于本地测试的 Core Tools。 有关详细信息，请参阅[使用 Visual Studio 开发 Azure Functions](functions-develop-vs.md)。 |
| [Maven](./create-first-function-cli-java.md)（不同的） | [Java](functions-reference-java.md) | 与 Core Tools 集成以实现 Java 函数的开发。 版本 2.x 支持 Linux、MacOS 和 Windows 上的开发。 有关详细信息，请参阅[使用 Java 和 Maven 创建第一个函数](./create-first-function-cli-java.md)。 还支持使用 [Eclipse](functions-create-maven-eclipse.md) 和 [IntelliJ IDEA](functions-create-maven-intellij.md) 进行开发 |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

其中每个本地开发环境允许创建函数应用项目，并使用预定义的 Functions 模板创建新函数。 每个环境使用 Core Tools，使你能够在自己的计算机上针对实际的 Functions 运行时测试和调试函数，就像对其他任何应用执行此操作一样。 还可以将函数应用项目从其中的任何环境发布到 Azure。

## <a name="next-steps"></a>后续步骤

+ 若要详细了解如何使用 Visual Studio 2019 在本地开发编译的 C# 函数，请参阅[使用 Visual Studio 开发 Azure Functions](functions-develop-vs.md)。
+ 若要详细了解如何在 Mac、Linux 或 Windows 计算机上使用 VS Code 本地开发函数，请参阅[从 VS Code 部署 Azure Functions](/azure/developer/javascript/tutorial-vscode-serverless-node-01)。
+ 若要详细了解如何通过命令提示符或终端开发函数，请参阅[使用 Azure Functions Core Tools](functions-run-local.md)。

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure 门户]: https://portal.azure.com
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows