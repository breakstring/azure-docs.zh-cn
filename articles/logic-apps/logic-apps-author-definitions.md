---
title: 创建、编辑或扩展逻辑应用 JSON 工作流定义
description: 如何在 Azure 逻辑应用中编写、编辑和扩展逻辑应用的 JSON 工作流定义
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: 9163071237041d7c8510a644c573e3763434bb0c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014391"
---
# <a name="create-edit-or-extend-json-for-logic-app-workflow-definitions-in-azure-logic-apps"></a>在 Azure 逻辑应用中为逻辑应用工作流定义创建、编辑或扩展 JSON

在 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)中使用自动化工作流创建企业集成解决方案时，基础逻辑应用定义会将简单的声明性 JavaScript 对象表示法 (JSON) 以及[工作流定义语言 (WDL) 架构](../logic-apps/logic-apps-workflow-definition-language.md)用于说明和验证。 这些格式使得逻辑应用定义更易于阅读和理解，无需让用户详细了解代码。
若要自动创建和部署逻辑应用，可在 [Azure 资源管理器模板](../azure-resource-manager/templates/overview.md)中将逻辑应用定义包含为 [Azure 资源](../azure-resource-manager/management/overview.md)。
若要创建、管理和部署逻辑应用，可以使用 [Azure PowerShell](/powershell/module/az.logicapp)、[Azure CLI](../azure-resource-manager/templates/deploy-cli.md) 或 [Azure 逻辑应用 REST API](/rest/api/logic/)。

若要在 JSON 中处理逻辑应用定义，请在 Azure 门户中打开“代码视图”编辑器，或者在 Visual Studio 中将定义复制到任何所需的编辑器。
如果你不熟悉逻辑应用，请查看[如何创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

> [!NOTE]
> 某些 Azure 逻辑应用功能（例如，在逻辑应用定义中定义参数和多个触发器）只能在 JSON 中使用，而不能在逻辑应用设计器中使用。
> 因此，对于这些任务，必须在“代码视图”或其他编辑器中处理。

## <a name="edit-json---azure-portal"></a>编辑 JSON - Azure 门户

1. 登录到 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>。

2. 在左侧菜单中选择“所有服务”。 
在搜索框中查找“逻辑应用”，然后在结果中选择择自己的逻辑应用。

3. 在逻辑应用菜单中的“开发工具”  下，选择“逻辑应用代码视图”  。

   “代码视图”编辑器将会打开并显示 JSON 格式的逻辑应用定义。

## <a name="edit-json---visual-studio"></a>编辑 JSON - Visual Studio

在 Visual Studio 中处理逻辑应用定义之前，请确保已[安装所需的工具](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites)。
若要使用 Visual Studio 创建逻辑应用，请查看[快速入门：使用 Azure 逻辑应用自动完成任务和流程 - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)。

在 Visual Studio 中，可以打开以前直接通过 Azure 门户，或者在 Visual Studio 中作为 Azure 资源管理器项目创建和部署的逻辑应用。

1. 打开包含逻辑应用的 Visual Studio 解决方案或 [Azure 资源组](../azure-resource-manager/management/overview.md)项目。

2. 找到并打开逻辑应用的定义，默认情况下，该定义显示在 [资源管理器模板](../azure-resource-manager/templates/overview.md)中，其名为 **LogicApp.json**。
可以使用并自定义此模板，以部署到不同的环境中。

3. 打开逻辑应用定义和模板的快捷菜单。
选择“使用逻辑应用设计器打开”  。

   ![在 Visual Studio 解决方案中打开逻辑应用](./media/logic-apps-author-definitions/open-logic-app-designer.png)

   > [!TIP]
   > 如果你的 Visual Studio 2019 中没有此命令，请检查是否安装了 Visual Studio 的最新更新。

4. 在设计器底部，选择“代码视图”  。

   “代码视图”编辑器将会打开并显示 JSON 格式的逻辑应用定义。

5. 若要返回设计器视图，请在“代码视图”编辑器的底部选择“设计”  。

## <a name="parameters"></a>parameters

部署生命周期通常有用于开发、测试、过渡和生产的不同环境。 如果有需要在逻辑应用中不进行硬编码就重用的值，或者该值会根据部署需求而变化，则请针对工作流定义创建一个 [Azure 资源管理器模板](../azure-resource-manager/management/overview.md)，这样就还可以自动完成逻辑应用部署。

请改为按这些常规步骤来参数化这些值，或者定义和使用其参数。  然后，可以在单独的参数文件中提供这些值，通过该文件将这些值传递给模板。 这样，无需更新和重新部署逻辑应用即可更轻松地更改这些值。 如需完整的详细信息，请参阅[概述：使用 Azure 资源管理器模板将逻辑应用部署自动化](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)。

1. 在模板中，定义模板参数和工作流定义参数，以便接受分别在部署时和运行时使用的值。

   定义模板参数时所在的 parameters 节位于工作流定义外，定义工作流定义参数时所在的 parameters 节位于工作流定义内。

1. 请将硬编码的值替换为引用这些参数的表达式。 模板表达式使用的语法不同于工作流定义表达式。

   避免代码复杂化，方法是：不要在工作流定义表达式（在运行时求值）中使用模板表达式（在部署时求值）。 仅在工作流定义外部使用模板表达式。 仅在工作流定义内部使用工作流定义表达式。

   针对工作流定义参数来指定值时，可以引用模板参数，方法是：使用位于工作流定义外部但仍在逻辑应用的资源定义内部的 parameters 节。 这样，便可以将模板参数值传入工作流定义参数。

1. 请将参数的值存储在单独的[参数文件](../azure-resource-manager/templates/parameter-files.md)中，在部署时包括该文件。

## <a name="process-strings-with-functions"></a>使用函数处理字符串

逻辑应用具有各种用于处理字符串的函数。
例如，假设你想要将订单中的公司名称传递到另一个系统。
但是，你不确定是否能正确处理字符编码。
你可以对此字符串执行 base64 编码，但为了避免在 URL 中进行转义，可以改为替换几个字符。 另外，你只需公司名称的子字符串，因为不使用前五个字符。

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

以下步骤介绍此示例如何处理该字符串，从内到外进行操作：

```
"uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. 获取公司名称的 [`length()`](../logic-apps/logic-apps-workflow-definition-language.md)，以便获取字符总数。

2. 若要获取较短的字符串，请减去 `5`。

3. 现在使用 [`substring()`](../logic-apps/logic-apps-workflow-definition-language.md) 获取子字符串。
从索引 `5` 开始，提取字符串的其余部分。

4. 将此子字符串转换为 [`base64()`](../logic-apps/logic-apps-workflow-definition-language.md) 字符串。

5. 现在使用 [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) 将所有 `+` 字符替换为 `-` 字符。

6. 最后，使用 [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) 将所有 `/` 字符替换为 `_` 字符。

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>将列表项映射到属性值，然后使用映射作为参数

若要基于属性的值获得不同的结果，可以创建映射将每个属性值与结果匹配，然后使用映射作为参数。

例如，此工作流定义一些类别作为参数，并定义一个将这些类别与特定 URL 进行匹配的映射。
首先，此工作流获取文章列表。 然后，此工作流使用映射找到与每篇文章的类别匹配的 URL。

*   [`intersection()`](../logic-apps/logic-apps-workflow-definition-language.md) 函数检查类别是否与某个已知的已定义类别匹配。

*   获取匹配的类别后，此示例使用方括号从映射中拉取项：`parameters[...]`

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "https://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=https://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

## <a name="get-data-with-date-functions"></a>使用日期函数获取数据

若要从本质上不支持 *触发器* 的数据源中获取数据，可以改用日期函数处理时间和日期。
例如，此表达式可算出该工作流的步骤执行多长时间，从内到外进行操作：

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. 从 `order` 操作提取 `startTime`。
2. 使用 `utcNow()` 获取当前时间。
3. 减去 1 秒：

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md)

   可以使用其他时间单位，例如`minutes`或`hours`。

3. 现在，可以比较这两个值。

   如果第一个值小于第二个值，则意味着从首次下订单开始过去了 1 秒以上的时间。

若要设置日期格式，可以使用字符串格式化程序。 例如，若要获取 RFC1123，可以使用 [`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md)。
详细了解[日期格式设置](../logic-apps/logic-apps-workflow-definition-language.md)。

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "https://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="next-steps"></a>后续步骤

* [基于条件运行步骤（条件语句）](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [基于不同的值运行步骤（switch 语句）](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [运行并重复执行步骤（循环）](../logic-apps/logic-apps-control-flow-loops.md)
* [运行或合并并行步骤（分支）](../logic-apps/logic-apps-control-flow-branches.md)
* [基于分组的操作状态运行步骤（作用域）](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* 详细了解 [Azure 逻辑应用的工作流定义语言架构](../logic-apps/logic-apps-workflow-definition-language.md)
* 详细了解 [Azure 逻辑应用的工作流操作和触发器](../logic-apps/logic-apps-workflow-actions-triggers.md)
