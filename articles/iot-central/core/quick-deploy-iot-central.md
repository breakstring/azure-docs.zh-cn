---
title: 快速入门 - 创建 Azure IoT Central 应用程序 | Microsoft Docs
description: 快速入门 - 创建新的 Azure IoT Central 应用程序。 使用免费的定价计划或标准定价计划之一创建应用程序。
author: viv-liu
ms.author: viviali
ms.date: 11/23/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: a2d48a6f4e451e2a9b4140618c7c590293cb5ef5
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548522"
---
# <a name="quickstart---create-an-azure-iot-central-application"></a>快速入门 - 创建 Azure IoT Central 应用程序

本快速入门展示了如何创建 Azure IoT Central 应用程序。

## <a name="create-an-application"></a>创建应用程序

导航到 [Azure IoT Central 生成](https://aka.ms/iotcentral)站点。 然后使用 Microsoft 个人、工作或学校帐户登录。

若要创建一个新应用程序，可以从行业相关的 IoT Central 模板列表中选择一个模板来帮助你快速入门，或者可以使用“自定义应用”  模板从头开始创建。 在本快速入门中，使用“自定义应用程序”  模板。

若要基于“自定义应用程序”  模板新建 Azure IoT Central 应用程序，请执行以下操作：

1. 导航到“生成”  页：

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-new-application.png" alt-text="生成 IoT 应用程序页面":::

1. 选择“自定义应用程序”  并确保选择了“自定义应用程序”  模板。

1. Azure IoT Central 会根据所选应用程序模板自动建议应用程序名称  。 可以使用此名称或输入自己的友好应用程序名称。

1. Azure IoT Central 还会根据应用程序名称为你生成唯一的应用程序 URL 前缀  。 使用此 URL 访问应用程序。 如果你愿意，可以将此 URL 前缀更改为更令人难忘的内容。

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-custom.png" alt-text="Azure IoT Central 的“创建应用程序”页":::

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-billinginfo.png" alt-text="Azure IoT Central 计费信息":::

    > [!NOTE]
    > 如果你在上一页中选择了“自定义应用”，则会看到一个“应用程序模板”下拉列表。 该下拉列表可能会显示你的组织提供给你的其他模板。

    >[!IMPORTANT]
    >自定义应用程序（旧版）模板 (V2) 已停用，因为旧版应用程序模板中以前提供的所有功能现在都在最新自定义应用程序模板 (V3) 中提供。

1. 选择使用 7 天免费试用版定价计划或标准定价计划之一创建此应用程序：

    - 使用 *免费* 计划创建的应用程序在七天内是免费的，并且最多支持五台设备。 在它们过期之前，随时可将其转换为使用标准定价计划。
    - 使用标准计划创建的应用程序按设备计费，你可以选择“标准 0”、“标准 1”或“标准 2”定价计划，前两个设备是免费的  。 在 [Azure IoT Central 定价页](https://azure.microsoft.com/pricing/details/iot-central/)上详细了解免费和标准定价计划。 如果使用标准定价计划创建应用程序，需要选择“目录”  、“Azure 订阅”  和“位置”  ：
        - “目录”是将在其中创建应用程序的 Azure Active Directory  。 Azure Active Directory 包含用户标识、凭据和其他组织信息。 如果你没有 Azure Active Directory，则在创建 Azure 订阅时，系统会为你创建一个。
        - 有了 *Azure 订阅*，就可以创建 Azure 服务的实例。 IoT Central 将在订阅中预配资源。 如果没有 Azure 订阅，则可在 [Azure 注册页](https://aka.ms/createazuresubscription)上免费创建一个。 创建 Azure 订阅后，导航回“新建应用程序”页。  新订阅现在显示在“Azue 订阅”  下拉列表中。
        - “位置”是你想要创建应用程序的[地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)  。 通常，应选择物理上离设备最近的位置以获得最佳性能。 选择一个位置后，之后便不能将应用程序移到其他位置。

1. 查看“条款和条件”，并选择页面底部的“创建”  。 几分钟后，IoT Central 应用程序便就绪可用：

    :::image type="content" source="media/quick-deploy-iot-central/iotcentral-application.png" alt-text="Azure IoT Central 应用程序":::

## <a name="next-steps"></a>后续步骤

在本快速入门中，创建了 IoT Central 应用程序。 接下来，建议执行以下步骤来继续了解 IoT Central：

> [!div class="nextstepaction"]
> [将模拟设备添加到 IoT Central 应用程序](./quick-create-simulated-device.md)

如果你是设备开发人员并且想要深入了解某种代码，接下来，建议执行以下步骤：
> [!div class="nextstepaction"]
> [创建客户端应用程序并将其连接到 Azure IoT Central 应用程序](./tutorial-connect-device.md)