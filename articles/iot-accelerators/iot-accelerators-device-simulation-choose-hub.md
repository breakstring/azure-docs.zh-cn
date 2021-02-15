---
title: 将现有 IoT 中心与设备模拟解决方案配合使用-Azure |Microsoft Docs
description: 本文介绍如何配置设备模拟解决方案加速器以使用现有的 IoT 中心。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 1f89e23d7bb279e7cce5c104060cc7898517f8b7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009358"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>将现有的 IoT 中心与设备模拟解决方案加速器配合使用

部署设备模拟时，还可以选择部署要在模拟中使用的 IoT 中心。 此选项部署[具有单个缩放单元的 S2 层 IoT 中心](../iot-hub/iot-hub-scaling.md)。 如果你部署了此可选 IoT 中心，仍然可以选择针对另一个 IoT 中心进行模拟运行。

如果选择不部署可选 IoT 中心，则必须为运行的所有模拟使用你自己的中心。

如果还没有 IoT 中心，则在 [Azure 门户](https://portal.azure.com)中新建一个即可。

若要使用预先存在的 IoT 中心，则需要 iothubowner 共享访问策略的连接字符串。 可从 [Azure 门户](https://portal.azure.com)获取此连接字符串：

1. 在门户的 IoT 中心配置页上，单击“共享访问策略”。

1. 单击“iothubowner”。

1. 复制主连接字符串或辅助连接字符串。

[![获取连接字符串](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

使用配置模拟时复制的连接字符串：

![配置模拟](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation.png)

### <a name="next-steps"></a>后续步骤

在此操作方法指南中，你已了解如何在模拟中使用现有的 IoT 中心。 接下来，你可能想要了解如何为模拟[创建高级设备模型](iot-accelerators-device-simulation-advanced-device.md)。
