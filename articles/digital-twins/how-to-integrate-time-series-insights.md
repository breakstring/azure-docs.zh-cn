---
title: 与 Azure 时序见解集成
titleSuffix: Azure Digital Twins
description: 请参阅如何设置从 Azure 数字孪生到 Azure 时序见解的事件路由。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 1/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 951c52cdba191aa291061259e1c15b9190513770
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092697"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>将 Azure 数字孪生与 Azure 时序见解集成

本文介绍如何将 Azure 数字孪生与 [Azure 时序见解 (TSI) ](../time-series-insights/overview-what-is-tsi.md)集成。

本文中所述的解决方案将允许你收集和分析有关 IoT 解决方案的历史数据。 Azure 数字孪生非常适合用于将数据馈送到时序见解中，因为它支持关联多个数据流，并可在将信息发送到时序见解之前将信息标准化。 

## <a name="prerequisites"></a>先决条件

若要设置与时序见解的关系，你需要具有 **Azure 数字孪生实例**。 应设置此实例，使其能够基于数据更新数字输出信息，因为您需要多次更新一次克隆信息，才能看到时序见解中跟踪的数据。 

如果尚未设置此设置，则可以按照 Azure 数字孪生教程创建它 [*：连接端到端解决方案*](./tutorial-end-to-end.md)。 本教程将指导你设置 Azure 数字孪生实例，该实例适用于虚拟 IoT 设备，用于触发数字克隆更新。

## <a name="solution-architecture"></a>解决方案体系结构

你将通过以下路径向 Azure 数字孪生附加时序见解。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="在端到端方案中显示 Azure 服务的视图，突出显示时序见解" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>对孪生体更新通知创建路由和筛选器

每当更新了克隆的状态时，Azure 数字孪生实例就可以发出 [双子次更新事件](how-to-interpret-event-data.md) 。 在本部分中，将创建一个 Azure 数字孪生 [**事件路由**](concepts-route-events.md) ，该路由会将这些更新事件定向到 Azure [事件中心](../event-hubs/event-hubs-about.md) 以供进一步处理。

Azure 数字孪生 [*教程：连接端到端解决方案*](./tutorial-end-to-end.md) 演练了这样一种方案：温度计用于更新代表房间的数字克隆上的温度属性。 此模式依赖于克隆的更新，而不是从 IoT 设备转发遥测，这使你可以灵活地更改基础数据源，而无需更新时序见解逻辑。

1. 首先，创建一个事件中心命名空间，该命名空间将从你的 Azure 数字孪生实例接收事件。 你可以使用以下 Azure CLI 说明，或使用 Azure 门户： [*快速入门：使用 Azure 门户创建事件中心*](../event-hubs/event-hubs-create.md)。 若要查看哪些区域支持事件中心，请访问 [*按区域提供的 Azure 产品*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs)。

    ```azurecli-interactive
    az eventhubs namespace create --name <name for your Event Hubs namespace> --resource-group <resource group name> -l <region>
    ```

2. 在命名空间中创建一个事件中心，用于接收克隆的更改事件。 指定事件中心的名称。

    ```azurecli-interactive
    az eventhubs eventhub create --name <name for your Twins event hub> --resource-group <resource group name> --namespace-name <Event Hubs namespace from above>
    ```

3. 使用发送和接收权限创建 [授权规则](/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest&preserve-view=true#az-eventhubs-eventhub-authorization-rule-create) 。 指定规则的名称。

    ```azurecli-interactive
        az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from above> --eventhub-name <Twins event hub name from above> --name <name for your Twins auth rule>
    ```

4. 创建一个将事件中心链接到 Azure 数字孪生实例的 Azure 数字孪生 [终结点](concepts-route-events.md#create-an-endpoint) 。

    ```azurecli-interactive
    az dt endpoint create eventhub -n <your Azure Digital Twins instance name> --endpoint-name <name for your Event Hubs endpoint> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace from above> --eventhub <Twins event hub name from above> --eventhub-policy <Twins auth rule from above>
    ```

5. 在 Azure 数字孪生中创建[路由](concepts-route-events.md#create-an-event-route)，将孪生体更新事件发送到终结点。 此路由中的筛选器将仅允许向您的终结点传递一条不成对的更新消息。

    >[!NOTE]
    >目前，Cloud Shell 中存在一个已知问题，该问题会影响以下命令组：`az dt route`、`az dt model` 和 `az dt twin`。
    >
    >若要解决此问题，请在运行命令之前在 Cloud Shell 中运行 `az login`，或者使用[本地 CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) 而不使用 Cloud Shell。 有关此操作的详细信息，请参阅 [*故障排除：Azure 数字孪生中的已知问题*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell)。

    ```azurecli-interactive
    az dt route create -n <your Azure Digital Twins instance name> --endpoint-name <Event Hub endpoint from above> --route-name <name for your route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

在继续之前，请记下你的 *事件中心命名空间* 和 *资源组*，因为你将在本文的后面再次使用它们来创建另一个事件中心。

## <a name="create-a-function-in-azure"></a>在 Azure 中创建一个函数

接下来，你将使用 Azure Functions 在 function app 中创建 **事件中心触发的函数** 。 可以使用端到端教程中创建的函数应用 ([*教程：连接端到端解决方案*](./tutorial-end-to-end.md)) 或你自己的解决方案。 

此函数将这些克隆的更新事件从其原始格式转换为 json 对象，仅包含来自孪生的更新和添加的值。

有关将事件中心与 Azure Functions 一起使用的详细信息，请参阅 [*Azure Functions 的 Azure 事件中心触发器*](../azure-functions/functions-bindings-event-hubs-trigger.md)。

在已发布的函数应用中，使用以下代码添加名为 **ProcessDTUpdatetoTSI** 的新函数。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

>[!NOTE]
>可能需要使用 `dotnet add package` 命令或 Visual Studio NuGet 包管理器将包添加到项目。

接下来， **发布** 新的 Azure 函数。 有关如何执行此操作的说明，请参阅 [*如何：设置用于处理数据的 Azure 函数*](how-to-create-azure-function.md#publish-the-function-app-to-azure)。

在此之前，此函数会将它创建的 JSON 对象发送到第二个事件中心，您将连接到时序见解。 你将在下一节中创建该事件中心。

稍后，你还将设置此函数将用于连接到你自己的事件中心的某些环境变量。

## <a name="send-telemetry-to-an-event-hub"></a>将遥测数据发送到事件中心

现在，你将创建另一个事件中心，并将你的函数配置为将其输出流式传输到该事件中心。 此事件中心随后会连接到时序见解。

### <a name="create-an-event-hub"></a>创建事件中心

若要创建第二个事件中心，可以使用以下 Azure CLI 说明，或使用 Azure 门户： [*快速入门：使用 Azure 门户创建事件中心*](../event-hubs/event-hubs-create.md)。

1. 从本文前面部分准备 *事件中心命名空间* 和 *资源组* 名称

2. 创建新的事件中心。 指定事件中心的名称。

    ```azurecli-interactive
    az eventhubs eventhub create --name <name for your TSI event hub> --resource-group <resource group name from earlier> --namespace-name <Event Hubs namespace from earlier>
    ```
3. 使用发送和接收权限创建 [授权规则](/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest&preserve-view=true#az-eventhubs-eventhub-authorization-rule-create) 。 指定规则的名称。

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from earlier> --eventhub-name <TSI event hub name from above> --name <name for your TSI auth rule>
    ```

## <a name="configure-your-function"></a>配置函数

接下来，需要在 function app 中设置环境变量，并在其中包含已创建的事件中心的连接字符串。

### <a name="set-the-twins-event-hub-connection-string"></a>设置孪生体事件中心连接字符串

1. 使用前面为孪生中心创建的授权规则获取孪生 [事件中心连接字符串](../event-hubs/event-hubs-get-connection-string.md)。

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <Twins event hub name from earlier> --name <Twins auth rule from earlier>
    ```

2. 使用结果中的 *primaryConnectionString* 值在 function app 中创建一个包含连接字符串的应用设置：

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<Twins event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="set-the-time-series-insights-event-hub-connection-string"></a>设置时序见解事件中心连接字符串

1. 使用上面为时序见解中心创建的授权规则获取 TSI [事件中心连接字符串](../event-hubs/event-hubs-get-connection-string.md)：

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. 使用结果中的 *primaryConnectionString* 值在 function app 中创建一个包含连接字符串的应用设置：

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<TSI event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>创建和连接时序见解实例

接下来，你将设置一个时序见解实例，以便从第二个 (TSI) 事件中心接收数据。 请按照以下步骤操作，有关此过程的更多详细信息，请参阅 [*教程：设置 Azure 时序见解 GEN2 PAYG 环境*](../time-series-insights/tutorials-set-up-tsi-environment.md)。

1. 在 Azure 门户中，开始创建时序见解环境。 
    1. 选择 **Gen2 (L1)** 定价层。
    2. 需要为此环境选择 **时序 ID** 。 时序 ID 最多可以有三个值，你将使用这些值在时序见解中搜索数据。 对于本教程，可以使用 **$dtId**。 有关选择 [*时序 id 的最佳实践，*](../time-series-insights/how-to-select-tsid.md)请阅读有关选择 ID 值的详细信息。
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="时序见解环境的创建门户 UX。从相应的下拉列表中选择订阅、资源组和位置，并为环境选择名称。" lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="时序见解环境的创建门户 UX。选择了 Gen2 (L1) 定价层，并且时序 ID 属性名称为 $dtId" lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. 选择 " **下一步：事件源** "，然后从前面选择你的 TSI 事件中心信息。 还需要创建新的事件中心使用者组。
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="时序见解环境事件源的创建门户 UX。正在使用上面的事件中心信息创建事件源。你还将创建一个新的使用者组。" lightbox="media/how-to-integrate-time-series-insights/event-source-twins.png":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>开始将 IoT 数据发送到 Azure 数字孪生

若要开始将数据发送到时序见解，需要开始更新 Azure 数字孪生中的数字克隆属性，并更改数据值。 使用 [az dt 双子端更新](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext-azure-iot-az-dt-twin-update) 命令。

如果你使用的是端到端教程 ([*教程：连接端到端解决方案*](tutorial-end-to-end.md)) 来帮助进行环境设置，你可以通过从示例运行 *devicesimulator.exe* 项目来开始发送模拟 IoT 数据。 本教程的 [*配置和运行模拟*](tutorial-end-to-end.md#configure-and-run-the-simulation) 部分介绍了相关说明。

## <a name="visualize-your-data-in-time-series-insights"></a>在时序见解中可视化数据

现在，数据应流向时序见解实例，并准备好进行分析。 请按照以下步骤来浏览传入的数据。

1. 在 [Azure 门户](https://portal.azure.com) 中打开时序见解环境 (可以在门户搜索栏) 中搜索环境的名称。 访问实例概述中所示的“时序见解资源管理器 URL”。
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="在时序见解环境的 &quot;概述&quot; 选项卡中选择 &quot;时序见解资源管理器 URL&quot;":::

2. 在资源管理器中，你将看到从左侧显示的 Azure 数字孪生的三个孪生。 选择 _**thermostat67**_，选择 **温度**，然后单击 " **添加**"。

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="选择 * * thermostat67 * *，选择 * * 温度 * *，然后单击 &quot;添加&quot;。":::

3. 你现在应看到恒温器的初始温度读数，如下所示。 将为 *room21* 和 *floor1* 更新相同的温度读数，并以串联方式可视化这些数据流。
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="在 TSI 资源管理器中将初始温度数据绘入图表。它是介于68和85之间的随机值的行":::

4. 如果允许模拟运行的时间更长，可视化效果将如下所示：
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="每个克隆的温度数据都以不同颜色的三个平行线为绘图。":::

## <a name="next-steps"></a>后续步骤

默认情况下，数字孪生存储为时序见解中的平面层次结构，但可通过模型信息和组织的多级别层次结构进行丰富。 若要了解有关此过程的详细信息，请阅读： 

* [*教程：定义和应用模型*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

你可以编写自定义逻辑，使用 Azure 数字孪生中存储的模型和图形数据自动提供此信息。 若要详细了解如何管理、升级和检索孪生图中的信息，请参阅以下参考资料：

* [*操作说明：管理数字克隆*](./how-to-manage-twin.md)
* [*操作方法：查询双子图形*](./how-to-query-graph.md)