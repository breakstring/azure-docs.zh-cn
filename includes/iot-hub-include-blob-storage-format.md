---
title: include 文件
description: include 文件
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b20c97bf919073b01f564b59b3a55eccb7614005
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89043027"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> 可以将数据以 [Apache Avro](https://avro.apache.org/) 格式（默认）或 JSON 格式（预览版）写入 Blob 存储。 
>    
> JSON 格式编码的功能现处于预览版状态，IoT 中心可在任意区域使用（美国东部、美国西部和欧洲西部除外）。 编码格式只能在配置 Blob 存储终结点时设置。 不能更改已设置的终结点的格式。 使用 JSON 编码时，必须在消息系统属性中将 contentType 设置为 JSON，将 contentEncoding 设置为 UTF-8。 
>
> 若要更详细地了解如何使用 Blob 存储终结点，请参阅[有关如何路由到存储的指南](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint)。
>