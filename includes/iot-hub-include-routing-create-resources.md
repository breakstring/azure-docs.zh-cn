---
title: include 文件
description: include 文件
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 5680bd1c3fadab8edcde1634c708ca8683c0e99c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "76021230"
---
## <a name="create-base-resources"></a>创建基础资源

在配置消息路由之前，需创建 IoT 中心、存储帐户和服务总线队列。 可以参阅适用于本教程第 1 部分的四篇文章中的一篇来创建这些资源：Azure门户、Azure 资源管理器模板、Azure CLI 或 Azure PowerShell。

为所有资源使用相同的资源组和位置。 在本教程结束后，可以通过删除资源组一次性删除所有资源。

下面是将在以下部分中执行的步骤摘要： 

1. 创建[资源组](../articles/azure-resource-manager/management/overview.md)。

2. 在 S1 层级中创建 IoT 中心。 将使用者组添加到 IoT 中心。 检索数据时，Azure 流分析使用使用者组。

   > [!NOTE]
   > 必须使用付费层中的 IoT 中心来完成本教程。 免费层只允许设置一个终结点，但本教程需要多个终结点。
   > 

3. 使用 Standard_LRS 副本创建标准 V1 存储帐户。

4. 创建服务总线命名空间和队列。

5. 为发送消息到中心的模拟设备创建设备标识。 保存测试阶段的密钥。 （如果创建资源管理器模板，则会在部署模板后执行此操作。）