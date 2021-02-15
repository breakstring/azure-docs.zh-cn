---
title: 排查 Azure IoT 中心 500xxx 内部错误
description: 了解如何修复 500xxx 内部错误
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1333a135f3e123757e268513f73e8329537e630b
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538198"
---
# <a name="500xxx-internal-errors"></a>500xxx 内部错误

本文介绍 **500xxx 内部错误** 的原因和解决方案。

## <a name="symptoms"></a>症状

向 IoT 中心发出的请求失败并出现错误，该错误以 500 和/或某种类型的“服务器错误”开头。 一些可能的原因如下：

* **500001 ServerError** ：IoT 中心遇到服务器端问题。

* **500008 GenericTimeout** ：IoT 中心在超时之前无法完成连接请求。

* **ServiceUnavailable (无错误代码)** ：IoT 中心遇到了内部错误。

* **InternalServerError (无错误代码)** ：IoT 中心遇到了内部错误。

## <a name="cause"></a>原因

500xxx 错误响应可能有多种原因。 在所有情况下，此问题很可能是暂时性的。 尽管 IoT 中心团队会努力维持 [SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/)，但一小部分 IoT 中心节点偶尔会遇到暂时性故障。 如果设备尝试连接到有问题的节点，则你会收到此错误。

## <a name="solution"></a>解决方案

若要缓解 500xxx 错误，请从设备发出重试命令。 若要[自动管理重试](./iot-hub-reliability-features-in-sdks.md#connection-and-retry)，请确保使用最新版本的 [Azure IoT SDK](./iot-hub-devguide-sdks.md)。 有关暂时性故障处理和重试的最佳做法，请参阅[暂时性故障处理](/azure/architecture/best-practices/transient-faults)。  如果问题仍然出现，请检查[资源运行状况](./iot-hub-azure-service-health-integration.md#check-health-of-an-iot-hub-with-azure-resource-health)和 [Azure 状态](https://status.azure.com/)，以确定 IoT 中心是否存在已知的问题。 也可使用[手动故障转移功能](./tutorial-manual-failover.md)。 如果不存在已知的问题，但问题仍然出现，请[联系支持人员](https://azure.microsoft.com/support/options/)以做进一步调查。
