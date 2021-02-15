---
title: 字段上的 Azure 流分析 JobConfig.js
description: 本文列出了用于在 Visual Studio Code 中创建作业的文件 JobConfig.js的 Azure 流分析的支持字段。
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 02/14/2020
ms.openlocfilehash: 0eebd0b62e973572a40b7b141ae908046700ba3c
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020479"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>字段上的 Azure 流分析 JobConfig.js

使用 [Visual Studio Code 创建 Azure 流分析作业](quick-create-visual-studio-code.md)的文件 *JobConfig.js* 支持以下字段。

```json
{
    "DataLocale": "string",
    "OutputErrorPolicy": "string",
    "EventsLateArrivalMaxDelayInSeconds": "integer",
    "EventsOutOfOrderMaxDelayInSeconds": "integer",
    "EventsOutOfOrderPolicy": "string",
    "StreamingUnits": "integer",
    "CompatibilityLevel": "string",
    "UseSystemAssignedIdentity": "boolean",
    "GlobalStorage": {
        "AccountName": "string",
        "AccountKey": "string",
    },
    "DataSourceCredentialDomain": "string",
    "ScriptType": "string",
    "Tags": {}
}
```

|名称|类型|必选|Value|
|----|----|--------|-----|
|DataLocale|string|否|流分析作业的数据区域设置。 值应为受支持的的名称。 如果未指定，则默认为 "en-us"。|
|OutputErrorPolicy|string|否|指示要应用于出现错误的事件的策略，该策略不能写入到外部存储，因为 (缺少列值、错误类型或大小) 的列值。 -停止或删除|
|EventsLateArrivalMaxDelayInSeconds|integer|否|最大可容忍延迟时间，以秒为单位。 支持的范围为-1 到 1814399 (20.23：59：59天) ，-1 用于指定无限期等待。 如果该属性不存在，则将其解释为具有值-1。|
|EventsOutOfOrderMaxDelayInSeconds|integer|否|可按顺序将有序事件调整为按顺序返回的最大可容忍延迟（以秒为单位）。|
|EventsOutOfOrderPolicy|string|否|指示要应用于输入事件流中未按顺序到达的事件的策略。 -调整或删除|
|StreamingUnits|integer|是|指定流式处理作业使用的流式处理单位数。|
|CompatibilityLevel|string|否|控制流式处理作业的某些运行时行为。 -可接受的值为 "1.0"、"1.1"、"1.2"|
|UseSystemAssignedIdentity|boolean|否|设置为 true 可使此作业使用托管 Azure Active Directory 标识作为其自身与其他 Azure 服务进行通信。|
|GlobalStorage|string|否|全局存储帐户用于存储与流分析作业相关的内容，例如 SQL 引用数据快照。|
|GlobalStorage. AccountKey|string|否|全局存储帐户的相应键。|
|DataSourceCredentialDomain|string|否|凭据本地存储的保留属性。|
|ScriptType|字符串|是|保留的属性，用于指示此源文件的类型。 可接受的值为 JobConfig.js上的 "JobConfig"。|
|标记|JSON 键值对|否|标记是名称/值对，可让你通过将相同的标记应用到多个资源和资源组，对资源进行分类并查看合并的账单。 标记名称不区分大小写，但标记值区分大小写。|

## <a name="next-steps"></a>后续步骤

* [在 Visual Studio Code 中创建 Azure 流分析作业](quick-create-visual-studio-code.md)
* [通过 Visual Studio Code 使用示例数据在本地测试流分析查询](visual-studio-code-local-run.md)
* [使用 Visual Studio Code 对实时流输入进行本地测试流分析查询](visual-studio-code-local-run-live-input.md) 
*[使用 CI/CD npm 包部署 Azure 流分析作业](./cicd-overview.md)