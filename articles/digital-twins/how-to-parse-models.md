---
title: 分析和验证模型
titleSuffix: Azure Digital Twins
description: 了解如何使用分析器库分析 DTDL 模型。
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q3
ms.openlocfilehash: 44cee2b47ae8f96e2852dfdb5aefe73a7af67ed6
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98731328"
---
# <a name="parse-and-validate-models-with-the-dtdl-parser-library"></a>用 DTDL 分析器库分析和验证模型

Azure 数字孪生中的[模型](concepts-models.md)是使用基于 JSON LD 的数字孪生定义语言 (DTDL) 定义的。 **建议在将模型上传到 Azure 数字孪生实例之前，先对其进行验证。**

为帮助你执行此操作，NuGet 上提供了一个 .NET 客户端 DTDL 分析库： [**DigitalTwins**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/)。 

您可以直接在 c # 代码中使用分析器库，或使用在分析器库中生成的与语言无关的代码示例项目： [**DTDL 验证器示例**](/samples/azure-samples/dtdl-validator/dtdl-validator)。

## <a name="use-the-dtdl-validator-sample"></a>使用 DTDL 验证程序示例

[**DTDL 验证**](/samples/azure-samples/dtdl-validator/dtdl-validator)程序是一个可以验证模型文档以确保 DTDL 有效的示例项目。 它基于 .NET 分析器库生成，并与语言无关。 可以通过示例链接上的 " *下载 ZIP* " 按钮获取它。

源代码显示了如何使用分析器库的示例。 可以使用验证程序示例作为命令行实用工具来验证 DTDL 文件的目录树。 它还提供交互模式。

在 DTDL 验证程序示例的文件夹中，请参阅 *readme.md* 文件，了解有关如何将示例打包到独立的可执行文件中的说明。

构建自包含包并将可执行文件添加到路径后，可以在计算机上的控制台中使用以下命令运行该验证程序：

```cmd/sh
DTDLValidator
```

利用默认选项，该示例将 `*.json` 在当前目录和所有子目录中搜索文件。 你还可以添加以下选项，使示例搜索位于所指示的目录中，以及扩展名为 *dtdl* 的文件的所有子目录：

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

可以添加该 `-i` 示例的选项以进入交互模式：

```cmd/sh
DTDLValidator -i
```

有关此示例的详细信息，请参阅源代码或运行 `DTDLValidator --help` 。

## <a name="use-the-net-parser-library"></a>使用 .NET 分析器库 

[**DigitalTwins**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/)库提供对 DTDL 定义的模型访问，本质上充当 DTDL 的 c # 反射的等效项。 此库可独立于任何 [Azure 数字孪生 SDK](how-to-use-apis-sdks.md)使用，尤其适用于在可视化或文本编辑器中进行 DTDL 验证。 在尝试将模型定义文件上传到服务之前，此方法有助于确保模型定义文件有效。

若要使用分析器库，请向其提供一组 DTDL 的文档。 通常情况下，你将从服务中检索这些模型文档，但你也可以在本地提供这些文档，前提是你的客户端在首次将其上传到服务。 

下面是使用分析器的一般工作流：
1. 从服务中检索部分或全部 DTDL 的文档。
2. 将返回的内存中 DTDL 文档传递到分析器。
3. 分析器将验证传递给它的文档集，并返回详细的错误信息。 此功能在编辑器方案中非常有用。
4. 使用分析器 Api 继续分析文档集中包含的模型。 

分析器的功能包括：
* 获取所有实现的模型接口 (接口的 `extends` 部分内容) 。
* 获取在模型中声明的所有属性、遥测、命令、组件和关系。 此命令还将获取这些定义中包含的所有元数据，并将继承 (`extends` 部分) 帐户。
* 获取所有复杂模型定义。
* 确定模型是否可从另一个模型分配。

> [!NOTE]
> [IoT 即插即用 (PnP) ](../iot-pnp/overview-iot-plug-and-play.md) 设备使用小型语法变体来描述其功能。 此语法变体是在 Azure 数字孪生中使用的 DTDL 的语义上兼容的子集。 使用分析器库时，无需知道使用哪种语法变体来创建数字输出的 DTDL。 默认情况下，分析器将始终为 PnP 和 Azure 数字孪生语法返回相同的模型。

### <a name="code-with-the-parser-library"></a>带分析器库的代码

您可以直接使用分析器库，以便在自己的应用程序中验证模型或生成动态模型驱动的 UI、面板和报表。

为了支持下面的分析器代码示例，请考虑在 Azure 数字孪生实例中定义的多个模型：

:::code language="json" source="~/digital-twins-docs-samples/models/coffeeMaker-coffeeMakerInterface-coffeeBar.json":::

下面的代码演示如何在 c # 中使用分析器库来反映这些定义：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/parseModels.cs":::

## <a name="next-steps"></a>后续步骤

编写完模型后，请参阅如何上传它们 (和执行其他管理操作) 与 DigitalTwinsModels Api 一起运行：
* [*操作说明：管理自定义模型*](how-to-manage-model.md)