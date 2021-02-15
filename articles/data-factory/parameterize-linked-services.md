---
title: 参数化 Azure 数据工厂中的链接服务
description: 了解如何参数化 Azure 数据工厂中的链接服务，并在运行时传递动态值。
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/15/2021
author: dcstwh
ms.author: weetok
ms.openlocfilehash: fbb7cb6fd0041ab77e649b14712907d916eaa449
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372920"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>参数化 Azure 数据工厂中的链接服务

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

现在可以参数化链接服务并在运行时传递动态值。 例如，如果要连接到同一逻辑 SQL Server 上的不同数据库，则现在可以在链接服务定义中参数化数据库名称。 这样就不需为逻辑 SQL Server 上的每个数据库创建链接服务。 也可以参数化链接服务定义中的其他属性 - 例如，用户名。

可以使用 Azure 门户中的数据工厂 UI 或编程接口来参数化链接服务。

> [!TIP]
> 我们建议不要参数化密码或机密。 而应将所有连接字符串都存储在 Azure Key Vault 中，并参数化 *机密名称*。

> [!Note]
> 存在用于在参数名称中使用 "-" 的开放 bug，我们建议使用名称而不使用 "-"，直到 bug 得到解决。

有关此功能的 7 分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-linked-service-types"></a>支持的链接服务类型

可以将任何类型的链接服务参数化。
在 UI 上创作链接服务时，数据工厂会为以下类型的链接服务提供内置参数化体验。 在“链接服务创建/编辑”边栏选项卡中，可以找到新参数的选项并添加动态内容。

- Amazon Redshift
- Amazon S3
- Azure Cosmos DB (SQL API)
- Azure Database for MySQL
- Azure Databricks
- Azure Key Vault
- Azure SQL 数据库
- Azure SQL 托管实例
- Azure Synapse Analytics 
- MySQL
- Oracle
- SQL Server
- 泛型 HTTP
- 泛型 REST

对于不在上述列表中的其他链接服务类型，可以通过在 UI 上编辑 JSON 来参数化该链接服务：

- 在“链接服务创建/编辑”边栏选项卡中 -> 展开底部的“高级”-> 选中“以 JSON 格式指定动态内容”复选框 -> 指定链接服务 JSON 有效负载。 
- 或者，在创建没有参数化的链接服务后，转到[管理中心](author-visually.md#management-hub) ->“链接服务”-> 查找特定的链接服务 -> 单击“代码”（“{}”按钮）以编辑 JSON。 

请参考 [JSON 示例](#json)来添加 ` parameters` 节，以定义参数并使用 ` @{linkedService().paraName} ` 来引用参数。

## <a name="data-factory-ui"></a>数据工厂 UI

![将动态内容添加到“链接服务”定义](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![新建参数](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
