---
title: 与 Azure Maps 集成
titleSuffix: Azure Digital Twins
description: 请参阅如何使用 Azure Functions 创建一个函数，该函数可使用孪生和 Azure 数字通知来更新 Azure Maps 的室内地图。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 1/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 6a654f74ff6a32ad37646021d504359c84942c12
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2021
ms.locfileid: "98573031"
---
# <a name="use-azure-digital-twins-to-update-an-azure-maps-indoor-map"></a>使用 Azure 数字孪生更新 Azure Maps 室内地图

本文逐步讲解使用 Azure 数字孪生数据更新 *室内地图* 上显示的信息所需的步骤， [Azure Maps](../azure-maps/about-azure-maps.md)。 Azure 数字孪生存储 IoT 设备关系的图形，并将遥测数据路由到不同的终结点，使其成为更新地图上信息覆盖的完美服务。

本操作说明将涉及：

1. 配置 Azure 数字孪生实例，以将克隆更新事件发送到 [Azure Functions](../azure-functions/functions-overview.md)中的函数。
2. 创建用于更新 Azure Maps 室内地图功能的函数 stateset。
3. 如何在 Azure 数字孪生图中存储地图 ID 和功能 stateset ID。

### <a name="prerequisites"></a>先决条件

* 遵循 Azure 数字孪生 [*教程：连接端到端解决方案*](./tutorial-end-to-end.md)。
    * 将使用其他终结点和路由扩展此克隆。 您还将从该教程向函数应用程序添加另一个函数。 
* 按照 Azure Maps [*教程操作：使用 Azure Maps Creator 创建室内地图*](../azure-maps/tutorial-creator-indoor-maps.md) ，使用 *stateset 功能* 创建 Azure Maps 室内地图。
    * [功能 statesets](../azure-maps/creator-indoor-maps.md#feature-statesets) 是 (状态) 分配给数据集功能（如房间或设备）的动态属性的集合。 在上述 Azure Maps 教程中，功能 stateset 存储将在地图上显示的房间状态。
    * 你将需要 *STATESET ID* 和 Azure Maps *订阅密钥*。

### <a name="topology"></a>拓扑

下图演示了在本教程中，室内映射集成元素的位置适用于更大的端到端 Azure 数字孪生方案。

:::image type="content" source="media/how-to-integrate-maps/maps-integration-topology.png" alt-text="在端到端方案中显示 Azure 服务的视图，突出显示室内地图集成块" lightbox="media/how-to-integrate-maps/maps-integration-topology.png":::

## <a name="create-a-function-to-update-a-map-when-twins-update"></a>创建一个函数以便在孪生更新时更新映射

首先，你将在 Azure 数字孪生中创建一个路由，以便将所有克隆的更新事件转发到事件网格主题。 然后，将使用函数读取这些更新消息，并更新 Azure Maps 中的功能 stateset。 

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>对孪生体更新通知创建路由和筛选器

每当更新了克隆的状态时，Azure 数字孪生实例就可以发出双子次更新事件。 Azure 数字孪生 [*教程：连接上面链接的端到端解决方案*](./tutorial-end-to-end.md) 演练，其中使用温度计来更新附加到房间克隆的温度属性。 你将通过订阅更新孪生的通知并使用该信息更新映射来扩展该解决方案。

此模式直接从房间逻辑上读取，而不是 IoT 设备，这使你可以灵活地更改温度的基础数据源，而无需更新映射逻辑。 例如，可以添加多个温度计或设置此空间以与其他房间共享温度计，无需更新映射逻辑。

1. 创建事件网格主题，该主题将从你的 Azure 数字孪生实例接收事件。
    ```azurecli-interactive
    az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
    ```

2. 创建终结点，将事件网格主题链接到 Azure 数字孪生。
    ```azurecli-interactive
    az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
    ```

3. 在 Azure 数字孪生中创建路由，将孪生体更新事件发送到终结点。

    >[!NOTE]
    >目前，Cloud Shell 中存在一个已知问题，该问题会影响以下命令组：`az dt route`、`az dt model` 和 `az dt twin`。
    >
    >若要解决此问题，请在运行命令之前在 Cloud Shell 中运行 `az login`，或者使用[本地 CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) 而不使用 Cloud Shell。 有关此操作的详细信息，请参阅 [*故障排除：Azure 数字孪生中的已知问题*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell)。

    ```azurecli-interactive
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Grid-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-a-function-to-update-maps"></a>创建用于更新映射的函数

从端到端教程 ([*教程：连接端到端解决方案*](./tutorial-end-to-end.md)) ，在函数应用中创建一个 **事件网格触发的函数**。 此函数将解包这些通知，并将更新发送到 Azure Maps 功能 stateset，以更新一个房间的温度。

有关参考信息，请参阅以下文档： [*Azure Functions 的 Azure 事件网格触发器*](../azure-functions/functions-bindings-event-grid-trigger.md)。

将函数代码替换为以下代码。 它仅筛选孪生空间的更新、读取更新的温度，并将该信息发送到 Azure Maps。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateMaps.cs":::

需要在 function app 中设置两个环境变量。 其中一项是你的 [Azure Maps 主要订阅密钥](../azure-maps/quick-demo-map-app.md#get-the-primary-key-for-your-account)，一个是你 [AZURE MAPS 的 stateset ID](../azure-maps/tutorial-creator-indoor-maps.md#create-a-feature-stateset)。

```azurecli-interactive
az functionapp config appsettings set --name <your-App-Service-(function-app)-name> --resource-group <your-resource-group> --settings "subscription-key=<your-Azure-Maps-primary-subscription-key>"
az functionapp config appsettings set --name <your-App-Service-(function-app)-name>  --resource-group <your-resource-group> --settings "statesetID=<your-Azure-Maps-stateset-ID>"
```

### <a name="view-live-updates-on-your-map"></a>查看地图上的实时更新

若要查看现场更新温度，请遵循以下步骤：

1. 通过运行 Azure 数字孪生教程中的 **devicesimulator.exe** 项目，开始发送模拟 IoT 数据 [*：连接端到端解决方案*](tutorial-end-to-end.md)。 有关此操作的说明，请在 [*配置和运行模拟*](././tutorial-end-to-end.md#configure-and-run-the-simulation) 部分。
2. 使用 [" **Azure Maps 室内** " 模块](../azure-maps/how-to-use-indoor-module.md) 呈现在 Azure Maps 创建者中创建的室内地图。
    1. 复制该示例中的 HTML：使用室内地图教程的 [*室内地图模块*](../azure-maps/how-to-use-indoor-module.md#example-use-the-indoor-maps-module) 部分 [*：将 Azure Maps 室内地图模块*](../azure-maps/how-to-use-indoor-module.md) 用于本地文件。
    1. 将本地 HTML 文件中的 *订阅密钥*、 *tilesetId* 和 *statesetID*  替换为你的值。
    1. 在浏览器中打开该文件。

这两个示例都发送兼容范围内的温度，因此，每隔30秒就会在地图上看到房间121更新的颜色。

:::image type="content" source="media/how-to-integrate-maps/maps-temperature-update.png" alt-text="显示房间121彩色橙色的办公地图":::

## <a name="store-your-maps-information-in-azure-digital-twins"></a>在 Azure 数字孪生中存储地图信息

现在，你已经有了更新地图信息的硬编码解决方案，可以使用 Azure 数字孪生图来存储更新室内地图所需的所有信息。 这包括每个地图和位置的 stateset ID、maps 订阅 ID 和功能 ID。 

此特定示例的解决方案涉及更新每个顶级空间，使其具有一个 stateset ID 和 maps 订阅 ID 属性，并将每个空间更新为具有功能 ID。 在初始化克隆图形时，需要设置这些值一次，然后在每个未完成的更新事件中查询这些值。

根据拓扑的配置，您可以将这三个属性存储在不同的级别，这与映射的粒度相关。

## <a name="next-steps"></a>后续步骤

若要详细了解如何管理、升级和检索孪生图中的信息，请参阅以下参考资料：

* [*操作说明：管理数字孪生*](./how-to-manage-twin.md)
* [*操作方法：查询双子图形*](./how-to-query-graph.md)
