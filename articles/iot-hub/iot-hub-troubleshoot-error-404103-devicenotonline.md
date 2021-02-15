---
title: 排查 Azure IoT 中心错误 404103 DeviceNotOnline
description: 了解如何修复错误 404103 DeviceNotOnline
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: e648428f924cfc33421c8591c41f7ac85b05a033
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "76960810"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

本文介绍 **404103 DeviceNotOnline** 错误的原因和解决方案。

## <a name="symptoms"></a>症状

即使设备处于联机状态，对设备执行直接方法也会失败，并显示错误 **404103 DeviceNotOnline**。 

## <a name="cause"></a>原因

如果你知道设备处于联机状态，但仍收到错误，很可能是因为直接方法回调未在设备上注册。

## <a name="solution"></a>解决方案

若要为直接方法回调正确配置设备，请参阅[在设备上处理直接方法](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device)。