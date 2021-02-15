---
title: include 文件
description: include 文件
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3386cb51a8a728576f6615002d6154d89ca662c1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993312"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

若要获取 registryReadWrite策略的 IoT 中心连接字符串，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，选择“资源组”。 选择中心所在的资源组，然后从资源列表中选择中心。

2. 在中心的左侧窗格上，选择“共享访问策略”。

3. 在策略列表中，选择“registryReadWrite”策略。

4. 在“共享访问密钥”下，选择“连接字符串 - 主密钥”的复制图标并保存值。

    ![显示如何检索连接字符串](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

有关 IoT 中心共享访问策略和权限的详细信息，请参阅[访问控制和权限](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions)。
