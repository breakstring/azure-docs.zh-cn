---
title: 了解 Azure IoT Central 应用的设备模板版本控制 | Microsoft Docs
description: 通过创建新版本循环访问设备模板，不影响实时连接设备
author: dominicbetts
ms.author: dobett
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 93545c63013c95e3db498b079061da3d9b189efd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995754"
---
# <a name="create-a-new-device-template-version"></a>创建新设备模板版本

本文适用于解决方案构建者和设备开发人员。 

设备模板包含一个架构，该架构描述设备如何与 IoT Central 进行交互。 这些交互包括遥测、属性和命令。 设备和 IoT Central 应用程序都依赖于此架构的共享了解来交换信息。 只能对架构进行有限的更改，而不会中断协定，这就是大多数架构更改需要新版本的设备模板的原因。 版本控制设备模板允许较旧的设备继续使用其所理解的架构版本，而更新或更新的设备则使用更高版本的架构。

设备模板中的架构是在设备模型及其接口中定义的。 设备模板包括其他信息，如云属性、显示自定义和视图。 如果在设备模板的这些部分中进行了更改，但未定义设备与 IoT Central 交换数据的方式，则无需对模板进行版本。

在操作员可以使用更新的设备模板之前，必须始终将其发布。 IoT Central 会阻止你在不首先对模板进行版本控制的情况下将重大更改发布到设备模板。

> [!NOTE]
> 若要详细了解如何创建设备模板，请参阅[设置和管理设备模板](howto-set-up-template.md)

## <a name="versioning-rules"></a>版本规则

本部分概述了适用于设备模板的版本控制规则。 设备模型和接口都有版本号。 以下代码片段显示了恒温器设备的设备型号。 设备模型具有单个接口。 您可以在字段末尾看到版本号 `@id` 。

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    // ...
  ]
}
```

若要在 IoT Central UI 中查看此信息，请在设备模板编辑器中选择 " **查看标识** "：

:::image type="content" source="media/howto-version-device-template/view-identity.png" alt-text="查看接口的标识以查看版本号":::

以下列表显示了确定何时必须创建新版本的规则：

* 在发布设备型号后，即使在新版本的设备模板中，也不能删除任何接口。
* 在发布设备型号后，可以在创建新版本的设备模板时添加接口。
* 在发布设备型号后，如果创建新版本的设备模板，则可以将接口替换为较新的版本。 例如，如果传感器 v1 设备模板使用恒温器 v1 接口，则可以创建使用恒温器 v2 接口的传感器 v2 设备模板。
* 接口发布后，即使在新版本的设备模板中，也不能删除任何接口内容。
* 在发布接口后，如果创建新版本的接口和设备模板，则可以向接口的内容中添加项。 可以添加到接口中的项包括遥测、属性和命令。
* 在发布接口后，如果创建新版本的接口和设备模板，则可以对接口中的现有项进行非架构更改。 接口项的非架构部分包括显示名称和语义类型。 无法更改的接口项的架构部分包括名称、功能类型和架构。

以下部分将指导你完成在 IoT Central 中修改设备模板的一些示例。

## <a name="customize-the-device-template-without-versioning"></a>自定义设备模板而不进行版本管理

无需对设备模板和接口进行版本控制，就能编辑设备功能的某些元素。 例如，其中的一些字段包括显示名称、语义类型、最小值、最大值、小数位数、颜色、单位、显示单位、注释和说明。 若要添加其中的某个自定义项：

1. 请参阅 **设备模板** 页。
1. 选择要自定义的设备模板。
1. 选择“自定义”选项卡。
1. 在设备模型中定义的所有功能都列在此处。 您可以编辑、保存和使用所有这些字段，而无需对您的设备模板进行版本。 如果要编辑的字段是只读的，则必须对设备模板进行版本更改。 选择要编辑的字段，然后输入任何新值。
1. 选择“保存”。 现在，这些值将覆盖在设备模板中最初保存并在应用程序中使用的所有内容。

## <a name="version-a-device-template"></a>版本设备模板

创建新版本的设备模板将创建模板的草稿版本，可在其中编辑设备模型。 任何已发布的接口都保持发布状态，直到它们被单独进行版本控制。 若要修改已发布的接口，请先创建新的设备模板版本。

仅当您尝试编辑在 "自定义" 部分中无法编辑的部分设备模型时，才设置设备模板的版本。

版本设备模板：

1. 请参阅 **设备模板** 页。
1. 选择要尝试版本的设备模板。
1. 选择页面顶部的 " **版本** " 按钮，并为模板指定一个新名称。 IoT Central 建议一个新名称，您可以对其进行编辑。
1. 选择“创建”  。
1. 该设备模板现在处于草稿模式。 您可以看到您的接口仍处于锁定状态。 版本要修改的任何接口。

## <a name="version-an-interface"></a>版本接口

对接口进行版本控制可以在已创建的接口中添加、更新和删除功能。

对接口进行版本：

1. 请参阅 **设备模板** 页。
1. 选择处于草稿模式的设备模板。
1. 选择要进行版本和编辑的已发布模式下的接口。
1. 选择 "接口" 页顶部的 " **版本** " 按钮。
1. 选择“创建”  。
1. 该接口现在处于草稿模式。 你可以在接口中添加或编辑功能，而不会破坏现有的自定义和视图。

## <a name="migrate-a-device-across-versions"></a>跨版本迁移设备

可以创建多个版本的设备模板。 随着时间的推移，将使用这些设备模板来连接多个设备。 可以将设备从设备模板的一个版本迁移到另一个版本。 以下步骤介绍如何迁移设备：

1. 转到 **“设备”** 页。
1. 选择需迁移到另一版本的设备。
1. 选择 **Migrate**"迁移 :::image type="content" source="media/howto-version-device-template/migrate-device.png" alt-text="&quot;：选择开始迁移设备的选项":::
1. 选择具有要将设备迁移到的版本号的设备模板，然后选择 " **迁移**"。

## <a name="next-steps"></a>后续步骤

如果你是操作员或解决方案生成器，建议下一步是了解 [如何管理你的设备](./howto-manage-devices.md)。

如果你是设备开发人员，建议下一步是了解 [Azure IoT Edge 设备和 Azure IoT Central](./concepts-iot-edge.md)。
