---
title: 排查 Azure IoT 中心错误 400027 ConnectionForcefullyClosedOnNewConnection
description: 了解如何修复错误 400027 ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- mqtt
- fasttrack-edit
- iot
ms.openlocfilehash: e5d1dc345c72d77be6172fb9c3a10eb2f38d186a
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506322"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

本文介绍 **400027 ConnectionForcefullyClosedOnNewConnection** 错误的原因和解决方案。

## <a name="symptoms"></a>症状

使用 .NET SDK 和 MQTT 传输类型，你的设备将与 **Communication_Error** 作为 **ConnectionStatusChangeReason** 断开连接。

设备到云的孪生操作（如读取或修补报告的属性）或直接方法调用失败，错误代码为 **400027** 。

## <a name="cause"></a>原因

另一个客户端使用相同凭据创建了与 IoT 中心的新连接，因此 IoT 中心关闭了以前的连接。 IoT 中心不允许多个客户端使用同一组凭据进行连接。

## <a name="solution"></a>解决方案

请确保每个客户端使用其自己的标识连接到 IoT 中心。