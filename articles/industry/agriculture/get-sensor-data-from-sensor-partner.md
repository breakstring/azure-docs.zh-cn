---
title: 获取合作伙伴提供的传感器数据
description: 本文介绍如何获取合作伙伴提供的传感器数据。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 57baa00f7f9b64212d0aee8b0a2efeca814abeb3
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168522"
---
# <a name="get-sensor-data-from-sensor-partners"></a>从传感器合作伙伴获取传感器数据

Azure FarmBeats 可帮助你将 IoT 设备和传感器中的流数据引入 Datahub。 目前支持以下传感器设备合作伙伴。

  ![FarmBeats 合作伙伴](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

将设备数据与 Azure FarmBeats 集成可帮助你获取在场中部署到数据中心的 IoT 传感器的基本数据。 数据可通过 FarmBeats 加速器进行可视化。 通过使用 FarmBeats，可将数据用于数据合成，并使用机器学习/人工智能 (ML/AI) 模型构建。

若要启动传感器数据流，请确保以下各项：

-  你已在 Azure Marketplace 中安装 FarmBeats。
-  您决定要在您的场中安装的传感器和设备。
-  如果计划使用 "土料湿气传感器"，请使用 FarmBeats 土潮湿传感器放置图来获取有关传感器数量的建议，以及确切地放置传感器的位置。 有关详细信息，请参阅 [生成映射](generate-maps-in-azure-farmbeats.md)。
- 你可以在你的服务器场中购买和部署设备合作伙伴的设备或传感器。 请确保可以通过设备合作伙伴的解决方案访问传感器数据。

## <a name="enable-device-integration-with-farmbeats"></a>启用设备与 FarmBeats 的集成

开始流式处理传感器数据后，可以开始将数据导入到 FarmBeats 系统的过程。 向设备提供商提供以下信息以启用与 FarmBeats 的集成：

 - API 终结点
 - 租户 ID
 - 客户端 ID
 - 客户端机密
 - EventHub 连接字符串

按照以下步骤生成以上信息：

> [!NOTE]
> 需要在 Azure 上完成这些步骤才能访问部署 FarmBeats 的 Azure 订阅。

1. 登录 https://portal.azure.com/ 。

2. **如果你使用的是 FarmBeats 版本1.2.7 或更高版本，请跳过步骤 a、b 和 c，然后转到步骤3。** 可以通过选择 FarmBeats UI 右上角的 " **设置** " 图标来检查 FarmBeats 版本。

      a.  中转到**Azure Active Directory**  >  **应用注册**

      b. 选择在 FarmBeats 部署过程中创建的 **应用注册** 。 它的名称与你的 FarmBeats datahub 相同。

      c. 选择 " **公开 API** > 选择" **添加客户端应用程序** "并输入 **04B07795-8ddb-461a-bbee-02f9e1bf7b46** 并检查 **授权作用域**。 这将授予对 Azure CLI 的访问权限 (Cloud Shell) 执行以下步骤：

3. 打开 Cloud Shell。 此选项位于 Azure 门户右上角工具栏中。

    ![Azure 门户工具栏](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. 确保环境设置为 **PowerShell**。 默认情况下，它设置为 Bash。

    ![PowerShell 工具栏设置](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. 中转到主目录。

    ```azurepowershell-interactive
    cd
    ```

6. 运行以下命令。 这会连接经过身份验证的帐户以用于 Azure AD 请求

    ```azurepowershell-interactive
    Connect-AzureAD
    ```

7. 运行以下命令。 这会将脚本下载到主目录。

    ```azurepowershell-interactive

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

8. 运行以下脚本。 该脚本要求提供租户 ID，该 ID 可从**Azure Active Directory**  >  **概述**"页获取。

    ```azurepowershell-interactive

    ./generatePartnerCredentials.ps1

    ```

> [!NOTE]
> 1. Datahub API 终结点名称必须为小写。
> 2. 如果要复制 Datahub API 终结点的 farmbeats 网站名称 url，请确保没有尾随斜杠 (/) 。

9. 按照屏幕上的说明来捕获 **API 终结点**、 **租户 ID**、 **客户端 ID**、 **客户端密钥**和 **EventHub 连接字符串**的值。

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>使用生成的凭据集成设备数据

现在，你已获得了上一节中生成的以下信息。
 - API 终结点
 - EventHub 连接字符串
 - 客户端 ID
 - 客户端机密
 - 租户 ID

你将需要向设备合作伙伴提供此链接，以链接 FarmBeats。 若要执行相同操作，请参阅设备合作伙伴门户。 例如，如果你使用的设备来自 Davis 乐器、Teralytic 或 Pessl 乐器 (Metos.at) 请按照下面所述，执行相应的页面：

1. [Davis 仪器](https://weatherlink.github.io/azure-farmbeats/setup)

2. [Teralytic](https://app.teralytic.com/)

3. [Pessl 仪器](https://ng.fieldclimate.com/user-api-services)

设备提供程序确认集成成功。 确认后，可以在 Azure FarmBeats 上查看所有设备和传感器。

## <a name="view-devices-and-sensors"></a>查看设备和传感器

使用以下部分查看场的设备和传感器。

### <a name="view-devices"></a>查看设备

目前，FarmBeats 支持以下设备：

- **Node**：连接了一个或多个传感器的设备。
- **网关**：一个或多个节点附加到的设备。

请执行下列步骤：

1. 在主页上，从菜单中选择 " **设备** "。
  " **设备** " 页显示设备类型、型号、状态、它所在的场和元数据的上次更新日期。 默认情况下，场列设置为 *NULL*。 你可以选择将设备分配到场。 有关详细信息，请参阅 [分配设备](#assign-devices)。
2. 选择设备以查看连接到设备的设备属性、遥测和子设备。

    ![“设备”页](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>查看传感器

请执行下列步骤：

1. 在主页上，从菜单中选择 " **传感器** "。
  " **传感器** " 页显示有关传感器类型、其连接到的服务器、父设备、端口名称、端口类型和上次更新状态的详细信息。
2. 选择传感器以查看传感器的传感器属性、活动警报和遥测数据。

    ![传感器页面](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>分配设备

传感器数据流入后，可以将其分配给部署传感器的服务器场。

1. 在主页上，从菜单中选择 " **服务器场** "。 此时会显示 " **服务器场** " 列表页。
2. 选择要向其分配设备的场，然后选择 " **添加设备**"。
3. 此时将显示 " **添加设备** " 窗口。 选择要分配给场的设备。

    ![添加设备窗口](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. 选择 " **添加设备**"。 或者，在 " **设备** " 菜单中，选择要分配给场的设备，然后选择 " **关联设备**"。
5. 在 " **关联设备** " 窗口中，从下拉列表中选择场，然后选择 " **应用到所有** " 以将场关联到所有选定的设备。

    ![关联设备窗口](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. 若要将每台设备关联到不同的服务器场，请选择 " **分配给场** " 列中的下拉箭头，然后为每个设备行选择一个场。

7. 选择 " **分配** " 完成设备分配。

### <a name="visualize-sensor-data"></a>可视化传感器数据

请执行下列步骤：

1. 在主页上，从菜单中选择 " **服务器场** " 以查看 " **服务器场** " 页。
2. 选择要查看其传感器数据的 **场** 。
3. 在 **场** 仪表板上，可以查看遥测数据。 可以查看实时遥测数据，也可以使用 **自定义范围** 查看特定的日期范围。

    ![场仪表板](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>删除传感器

请执行下列步骤：

1. 在 "主页" 页上，从菜单中选择 " **传感器** " 以查看 **传感器** 页面。
2. 选择要删除的设备，然后在确认窗口中选择 " **删除** "。

    ![突出显示 "传感器删除" 页和 "删除" 按钮的屏幕截图。](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

确认消息显示传感器已成功删除。

## <a name="delete-devices"></a>删除设备

请执行下列步骤：

1. 在主页上，从菜单中选择 " **设备** " 以查看 " **设备** " 页。
2. 选择要删除的设备，然后在确认窗口中选择 " **删除** "。

    ![“删除”按钮](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>后续步骤

现在，传感器数据流入 Azure FarmBeats 实例。 现在，了解如何为你的场 [生成地图](generate-maps-in-azure-farmbeats.md#generate-maps) 。
