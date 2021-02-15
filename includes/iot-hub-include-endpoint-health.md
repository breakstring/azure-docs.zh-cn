---
title: include 文件
description: include 文件
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 24a07109fc8f4d6ebd283dee7ee00107f0eb49b7
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557828"
---
可以使用 REST API [Get Endpoint Health](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) 获取终结点的运行状况状态。 我们建议使用与路由消息延迟相关的 [IoT 中心路由指标](../articles/iot-hub/monitor-iot-hub-reference.md#routing-metrics)来识别和调试终结点运行状况不佳或运行不正常时的错误，因为我们预计当终结点处于其中一种状态时，延迟会更高。 若要详细了解如何使用 IoT 中心指标，请参阅[监视 IoT 中心](../articles/iot-hub/monitor-iot-hub.md)。

|运行状况状态|说明|
|---|---|
|healthy|终结点按预期方式接受消息。|
|不正常|终结点未接受消息，IoT 中心正重试将消息发送到此终结点。|
|未知|IoT 中心尚未尝试将消息传递到此终结点。|
|已降级|终结点正在接受比预期慢的消息或正在从不正常状态恢复。|
|不活动|IoT 中心不再向此终结点传递消息。 重新尝试将消息发送到此终结点时失败。|