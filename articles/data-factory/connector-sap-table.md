---
title: 从 SAP 表复制数据
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 SAP 表复制到支持的接收器数据存储。
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/01/2021
ms.openlocfilehash: e4f756631b51ce9c5fba32939d1c6651e7b328d0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378513"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 SAP 表复制数据

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure 数据工厂中的复制活动从 SAP 表复制数据。 有关详细信息，请参阅[复制活动概述](copy-activity-overview.md)。

>[!TIP]
>若要了解 ADF 对 SAP 数据集成方案的总体支持，请参阅[使用 Azure 数据工厂进行 SAP 数据集成](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf)白皮书，其中包含各 SAP 连接器的详细介绍、比较和指导。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 SAP 表连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 SAP 表复制到任何受支持的接收器数据存储。 有关复制活动支持作为源或接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 SAP 表连接器支持：

- 在以下产品中从 SAP 表复制数据：

  - SAP ERP 中心组件 (SAP ECC) 7.01 或更高版本（包含在 2015 年之后发布的最新 SAP 支持包堆栈中）。
  - SAP Business Warehouse (SAP BW) 版本 7.01 或更高版本（位于最新的 SAP 支持包堆栈中，该堆栈是 2015 年以后发布的）。
  - SAP S/4HANA。
  - SAP Business Suite 版本 7.01 或更高版本（位于最新的 SAP 支持包堆栈中，该堆栈是 2015 年以后发布的）中的其他产品。

- 从 SAP 透明表、共用表、聚集表和视图复制数据。
- 使用基本身份验证或安全网络通信 (SNC)（如果已配置 SNC）复制数据。
- 连接到 SAP 应用程序服务器或 SAP 消息服务器。
- 通过默认或自定义 RFC 检索数据。

版本 7.01 或更高版本指 SAP NetWeaver 版本，而不是 SAP ECC 版本。 例如，SAP ECC 6.0 EHP 7 的 NetWeaver 版本一般 >=7.4。 如果你不确定自己的环境，请在 SAP 系统中执行以下步骤来确认版本：

1. 使用 SAP GUI 连接到 SAP 系统。 
2. 转到“系统” -> “状态” 。 
3. 检查 SAP_BASIS 的版本，确保它等于或大于 701。  
      ![检查 SAP_BASIS](./media/connector-sap-table/sap-basis.png)

## <a name="prerequisites"></a>先决条件

若要使用此 SAP 表连接器，需要：

- 安装自承载集成运行时（3.17 或更高版本）。 有关详细信息，请参阅[创建和配置自承载集成运行时](create-self-hosted-integration-runtime.md)。

- 从 SAP 网站下载 64 位 [SAP Connector for Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html)，并将其安装到自承载集成运行时计算机上。 安装期间，请在“可选安装步骤”窗口中确保选择“将程序集安装到 GAC”选项。  

  ![安装 SAP Connector for .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- 在数据工厂 SAP 表连接器中使用的 SAP 用户必须拥有以下权限：

  - 使用远程函数调用 (RFC) 目标的授权。
  - 对 S_SDSAUTH 授权对象拥有“执行”活动权限。

## <a name="get-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下部分详细介绍了用于定义特定于 SAP 表连接器的数据工厂实体的属性。

## <a name="linked-service-properties"></a>链接服务属性

SAP BW Open Hub 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| `type` | `type` 属性必须设置为 `SapTable`。 | 是 |
| `server` | SAP 实例所在的服务器的名称。<br/>用于连接到 SAP 应用程序服务器。 | 否 |
| `systemNumber` | SAP 系统的系统编号。<br/>用于连接到 SAP 应用程序服务器。<br/>允许的值：用字符串表示的两位十进制数。 | 否 |
| `messageServer` | SAP 消息服务器的主机名。<br/>用于连接到 SAP 消息服务器。 | 否 |
| `messageServerService` | 消息服务器的服务名称或端口号。<br/>用于连接到 SAP 消息服务器。 | 否 |
| `systemId` | 表所在的 SAP 系统的 ID。<br/>用于连接到 SAP 消息服务器。 | 否 |
| `logonGroup` | SAP 系统的登录组。<br/>用于连接到 SAP 消息服务器。 | 否 |
| `clientId` | SAP 系统中客户端的 ID。<br/>允许的值：用字符串表示的三位十进制数。 | 是 |
| `language` | SAP 系统使用的语言。<br/>默认值为 `EN`。| 否 |
| `userName` | 有权访问 SAP 服务器的用户名。 | 是 |
| `password` | 用户的密码。 使用 `SecureString` 类型标记此字段可以安全地将其存储在数据工厂中，或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| `sncMode` | 用于访问表所在的 SAP 服务器的 SNC 激活指示符。<br/>若要使用 SNC 连接到 SAP 服务器，可以使用此属性。<br/>允许的值为 `0`（关闭，默认值）或 `1`（打开）。 | 否 |
| `sncMyName` | 用于访问表所在的 SAP 服务器的发起方 SNC 名称。<br/>当 `sncMode` 打开时适用。 | 否 |
| `sncPartnerName` | 用于访问表所在的 SAP 服务器的通信合作伙伴 SNC 名称。<br/>当 `sncMode` 打开时适用。 | 否 |
| `sncLibraryPath` | 用于访问表所在的 SAP 服务器的外部安全产品库。<br/>当 `sncMode` 打开时适用。 | 否 |
| `sncQop` | 要应用的保护级别的 SNC 质量。<br/>当 `sncMode` 打开时适用。 <br/>允许的值为 `1`（身份验证）、`2`（完整性）、`3`（隐私）、`8`（默认值）和 `9`（最大值）。 | 否 |
| `connectVia` | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如前面的[先决条件](#prerequisites)中所述，需要安装自承载集成运行时。 |是 |

### <a name="example-1-connect-to-an-sap-application-server"></a>示例 1：连接到 SAP 应用程序服务器

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-2-connect-to-an-sap-message-server"></a>示例 2：连接到 SAP 消息服务器

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>示例 3：使用 SNC 进行连接

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关用于定义数据集的各个部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)。 以下部分提供 SAP 表数据集支持的属性的列表。

支持使用以下属性从/向 SAP BW Open Hub 链接服务复制数据。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| `type` | `type` 属性必须设置为 `SapTableResource`。 | 是 |
| `tableName` | 要从中复制数据的 SAP 表的名称。 | 是 |

### <a name="example"></a>示例

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)。 以下部分提供 SAP 表源支持的属性的列表。

### <a name="sap-table-as-source"></a>SAP 表作为源

支持使用以下属性从 SAP 表复制数据：

| 属性                         | 说明                                                  | 必选 |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | `type` 属性必须设置为 `SapTableSource`。         | 是      |
| `rowCount`                         | 要检索的行数。                              | 否       |
| `rfcTableFields`                 | 要从 SAP 表复制的字段（列）。 例如，`column0, column1`。 | 否       |
| `rfcTableOptions`                | 用于筛选 SAP 表中的行的选项。 例如，`COLUMN0 EQ 'SOMEVALUE'`。 另请参阅本文稍后提供的 SAP 查询运算符表。 | 否       |
| `customRfcReadTableFunctionModule` | 可用于从 SAP 表读取数据的自定义 RFC 函数模块。<br>可以使用自定义 RFC 函数模块来定义如何从 SAP 系统检索数据并将其返回到数据工厂。 必须为自定义函数模块实现一个接口（导入、导出、表），类似于数据工厂使用的默认接口 `/SAPDS/RFC_READ_TABLE2`。<br>数据工厂 | 否       |
| `partitionOption`                  | 要从 SAP 表中读取的分区机制。 支持的选项包括： <ul><li>`None`</li><li>`PartitionOnInt`（在左侧用零填充正常整数或整数值，例如 `0000012345`）</li><li>`PartitionOnCalendarYear`（采用“YYYY”格式的 4 位数）</li><li>`PartitionOnCalendarMonth`（采用“YYYYMM”格式的 6 位数）</li><li>`PartitionOnCalendarDate`（采用“YYYYMMDD”格式的 8 位数）</li><li>`PartitionOntime`（采用“HHMMSS”格式的 6 位数，例如 `235959`）</li></ul> | 否       |
| `partitionColumnName`              | 用于将数据分区的列的名称。                | 否       |
| `partitionUpperBound`              | `partitionColumnName` 中指定的用于继续分区的列的最大值。 | 否       |
| `partitionLowerBound`              | `partitionColumnName` 中指定的用于继续分区的列的最小值。 （注意：当分区选项为 `PartitionOnInt` 时，`partitionLowerBound` 不能为“0”） | 否       |
| `maxPartitionsNumber`              | 要将数据拆分成的最大分区数。     | 否       |
| `sapDataColumnDelimiter` | 单个字符，将用作传递给 SAP RFC 的分隔符，以用于拆分输出数据。 | 否 |

>[!TIP]
>如果 SAP 表包含大量数据（例如几十亿行），请使用 `partitionOption` 和 `partitionSetting` 将数据拆分成小分区。 在这种情况下，将按分区读取数据，并通过单个 RFC 调用从 SAP 服务器检索每个数据分区。<br/>
<br/>
>以 `partitionOption` 和 `partitionOnInt` 为例，每个分区中的行数的计算公式为：(处于 `partitionUpperBound` 与 `partitionLowerBound` 之间的总行数)/`maxPartitionsNumber`。<br/>
<br/>
>若要并行加载数据分区以加快复制速度，并行程度由复制活动的 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 设置控制。 例如，如果将 `parallelCopies` 设置为 4，则数据工厂会根据指定的分区选项和设置并行生成并运行 4 个查询，每个查询从 SAP 表检索一部分数据。 强烈建议将 `maxPartitionsNumber` 设为 `parallelCopies` 属性值的倍数。 将数据复制到基于文件的数据存储中时，还建议将数据作为多个文件写入文件夹（仅指定文件夹名称），在这种情况下，性能优于写入单个文件。

在 `rfcTableOptions` 中，可以使用以下常用 SAP 查询运算符来筛选行：

| 运算符 | 说明 |
| :------- | :------- |
| `EQ` | 等于 |
| `NE` | 不等于 |
| `LT` | 小于 |
| `LE` | 小于或等于 |
| `GT` | 大于 |
| `GE` | 大于或等于 |
| `IN` | 例如 `TABCLASS IN ('TRANSP', 'INTTAB')` |
| `LIKE` | 例如 `LIKE 'Emma%'` |

### <a name="example"></a>示例

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="join-sap-tables"></a>联接 SAP 表

当前，SAP 表连接器仅支持一个具有默认函数模块的表。 若要获取多个表的联接数据，可以按照以下步骤利用 SAP 表连接器中的 [customRfcReadTableFunctionModule](#copy-activity-properties) 属性：

- [编写自定义函数模块](#create-custom-function-module)，该模块可以将查询作为选项，并应用自己的逻辑来检索数据。
- 对于 "自定义函数模块"，请输入自定义函数模块的名称。
- 对于 "RFC 表选项"，请指定要作为选项送进函数模块的表联接语句，如 " `<TABLE1>` `<TABLE2>` COLUMN0 上的内部联接"。

下面是一个示例：

![Sap 表联接](./media/connector-sap-table/sap-table-join.png) 

>[!TIP]
>你还可以考虑将联接的数据聚合在视图中，这受 SAP 表连接器支持。
>你还可以尝试提取相关表以加入 Azure (例如，Azure 存储空间、Azure SQL 数据库) ，然后使用数据流继续进一步的联接或筛选器。

## <a name="create-custom-function-module"></a>创建自定义函数模块

对于 SAP 表，当前我们支持复制源中的 [customRfcReadTableFunctionModule](#copy-activity-properties) 属性，这允许你利用自己的逻辑并处理数据。

作为快速指导，以下是 "自定义函数模块" 入门的一些要求：

- 定义：

    ![定义](./media/connector-sap-table/custom-function-module-definition.png) 

- 将数据导出到以下表之一：

    ![导出表1](./media/connector-sap-table/export-table-1.png) 

    ![导出表2](./media/connector-sap-table/export-table-2.png)
 
下面是有关 SAP 表连接器如何与自定义函数模块一起使用的说明：

1. 通过 SAP NCO 建立与 SAP 服务器的连接。

1. 调用 "自定义函数模块"，并将参数设置如下：

    - QUERY_TABLE：在 ADF SAP 表数据集中设置的表名称; 
    - 分隔符：在 ADF SAP 表源中设置的分隔符; 
    - ROWCOUNT/Option/Fields：在 ADF 表源中设置的行计数/聚合选项/字段。

1. 获取结果并按以下方式分析数据：

    1. 分析 "字段" 表中的值以获取架构。

        ![分析字段中的值](./media/connector-sap-table/parse-values.png)

    1. 获取输出表的值，以查看包含这些值的表。

        ![在输出表中获取值](./media/connector-sap-table/get-values.png)

    1. 获取 OUT_TABLE 中的值，分析数据，然后将其写入接收器。

## <a name="data-type-mappings-for-an-sap-table"></a>SAP 表的数据类型映射

从 SAP 表复制数据时，以下映射用于从 SAP 表数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| SAP ABAP 类型 | 数据工厂临时数据类型 |
|:--- |:--- |
| `C`（字符串） | `String` |
| `I`（整数） | `Int32` |
| `F`（浮点数） | `Double` |
| `D`（日期） | `String` |
| `T`（时间） | `String` |
| `P`（BCD Packed、货币、小数、数量） | `Decimal` |
| `N`（数字） | `String` |
| `X`（二进制和原始数据） | `String` |

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>后续步骤

有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
