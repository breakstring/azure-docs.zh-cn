---
title: 使用托管标识建立到 Azure SQL 数据库的连接
titleSuffix: Azure Cognitive Search
description: 了解如何使用托管标识建立到 Azure SQL 数据库的索引器连接
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: b940da2cf754e7e1cac91df6b517ecebe55e8c40
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358416"
---
# <a name="set-up-an-indexer-connection-to-azure-sql-database-using-a-managed-identity"></a>使用托管标识建立到 Azure SQL 数据库的索引器连接

本页介绍如何使用托管标识设置到 Azure SQL 数据库的索引器连接，而不是在数据源对象连接字符串中提供凭据。

在详细了解此功能之前，建议先了解什么是索引器以及如何为数据源设置索引器。 有关详细信息，请访问以下链接：

* [索引器概述](search-indexer-overview.md)
* [Azure SQL 索引器](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>使用托管标识设置连接

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - 打开系统分配的托管标识

启用系统分配的托管标识后，Azure 将为搜索服务创建一个标识，该标识可用于对同一租户和订阅中的其他 Azure 服务进行身份验证。 然后，你可以在 Azure 基于角色的访问控制中使用此标识， (允许在索引过程中访问数据的 Azure RBAC) 分配。

![打开系统分配的托管标识](./media/search-managed-identities/turn-on-system-assigned-identity.png "打开系统分配的托管标识")

选择“保存”后，将看到已分配给搜索服务的对象 ID。

![对象 ID](./media/search-managed-identities/system-assigned-identity-object-id.png "对象 ID")

### <a name="2---provision-azure-active-directory-admin-for-sql-server"></a>2 - 为 SQL Server 预配 Azure Active Directory 管理

在下一步连接到数据库时，需要连接到对数据库具有管理员访问权限的 Azure Active Directory (Azure AD) 帐户，以便授予搜索服务访问数据库的权限。

按照[此处](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-database)的说明，授予 Azure AD 帐户对数据库的管理员访问权限。

### <a name="3---assign-the-search-service-permissions"></a>3 - 分配搜索服务权限

按照以下步骤分配读取数据库的搜索服务权限。

1. 连接到 Visual Studio

    ![连接到 Visual Studio](./media/search-managed-identities/connect-with-visual-studio.png "连接到 Visual Studio")

2. 用 Azure AD 帐户进行身份验证

    ![身份验证](./media/search-managed-identities/visual-studio-authentication.png "Authenticate")

3. 执行以下命令：

    请给搜索服务名称加上括号。
    
    ```
    CREATE USER [your search service name here] FROM EXTERNAL PROVIDER;
    EXEC sp_addrolemember 'db_datareader', [your search service name here];
    ```

    ![新查询](./media/search-managed-identities/visual-studio-new-query.png "新建查询")

    ![执行查询](./media/search-managed-identities/visual-studio-execute-query.png "执行查询")

>[!NOTE]
> 如果完成此步骤后更改了步骤 1 中的搜索服务标识，则必须删除角色成员身份并删除 SQL 数据库中的用户，然后通过再次完成步骤 3 再次添加权限。
> 通过运行以下命令可以删除角色成员身份和用户：
> ```
> sp_droprolemember 'db_datareader', [your search service name];
> DROP USER IF EXISTS [your search service name];
> ```

### <a name="4---add-a-role-assignment"></a>4 - 添加角色分配

在此步骤中，你将授予 Azure 认知搜索服务从 SQL 服务器读取数据的权限。

1. 在 Azure 门户中导航到 Azure SQL Server 页面。
2. 选择“访问控制(标识和访问管理)”
3. 依次选择“添加”和“添加角色分配” 

    ![添加角色分配](./media/search-managed-identities/add-role-assignment-sql-server.png "添加角色分配")

4. 选择相应的“读取者”角色。
5. 将“分配访问权限至”保留为“Azure AD 用户、组或服务主体” 
6. 搜索你的搜索服务，选中，然后选择“保存”

    ![添加读取者角色分配](./media/search-managed-identities/add-role-assignment-sql-server-reader-role.png "添加读取者角色分配")

### <a name="5---create-the-data-source"></a>5 - 创建数据源

[REST API](/rest/api/searchservice/create-data-source)、Azure 门户和[.net SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection)支持托管标识连接字符串。 下面是一个示例，说明如何使用 [REST API](/rest/api/searchservice/create-data-source) 和托管标识连接字符串创建数据源，以便为 Azure SQL 数据库中的数据编制索引。 对于 REST API、.NET SDK 和 Azure 门户，托管标识连接字符串格式是相同的。

使用 [REST API](/rest/api/searchservice/create-data-source)创建数据源时，数据源必须具有以下必需属性：

* **name** 是搜索服务中数据源的唯一名称。
* “类型”为 `azuresql`
* **凭据**
    * 使用托管标识进行身份验证时，凭据格式与不使用托管标识时不同。 在这里，你将提供初始目录或数据库名称以及没有帐户密钥或密码的 ResourceId。 ResourceId 必须包括 Azure SQL 数据库的订阅 ID、SQL 数据库的资源组和 SQL 数据库的名称。 
    * 托管标识连接字符串格式：
        * Initial Catalog|Database=database name;ResourceId=/subscriptions/your subscription ID/resourceGroups/your resource group name/providers/Microsoft.Sql/servers/your SQL Server name/;Connection Timeout=connection timeout length; **
* “容器”指定要编制索引的表或视图的名称。

如何使用 [REST API](/rest/api/searchservice/create-data-source) 创建 Azure SQL 数据源对象的示例：

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-datasource",
    "type" : "azuresql",
    "credentials" : { "connectionString" : "Database=sql-database;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azure-sql-resource-group/providers/Microsoft.Sql/servers/sql-server-search-demo;Connection Timeout=30;" },
    "container" : { "name" : "my-table" }
} 
```

### <a name="6---create-the-index"></a>6 - 创建索引

索引指定文档、属性和其他构造中可以塑造搜索体验的字段。

下面介绍如何使用可搜索的 `booktitle` 字段创建索引：   

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

有关创建索引的详细信息，请参阅[创建索引](/rest/api/searchservice/create-index)

### <a name="7---create-the-indexer"></a>7 - 创建索引器

索引器将数据源与目标搜索索引关联，并提供自动执行数据刷新的计划。

创建索引和数据源后，便可以准备创建索引器。

Azure SQL 索引器的索引器定义示例：

```
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-indexer",
    "dataSourceName" : "sql-datasource",
    "targetIndexName" : "my-target-index",
    "schedule" : { "interval" : "PT2H" }
}
```    

此索引器每隔两小时运行一次（已将计划间隔设置为“PT2H”）。 若要每隔 30 分钟运行一次索引器，可将间隔设置为“PT30M”。 支持的最短间隔为 5 分钟。 计划是可选的 - 如果省略，则索引器在创建后只运行一次。 但是，可以随时根据需要运行索引器。   

有关创建索引器 API 的更多详细信息，请参阅[创建索引器](/rest/api/searchservice/create-indexer)。

有关定义索引器计划的详细信息，请参阅[如何为 Azure 认知搜索计划索引器](search-howto-schedule-indexers.md)。

## <a name="troubleshooting"></a>疑难解答

如果在索引器尝试连接数据源时出现错误，指出不允许客户端访问服务器，请查看[常见索引器错误](./search-indexer-troubleshooting.md)。

## <a name="see-also"></a>另请参阅

详细了解 Azure SQL 索引器：
* [Azure SQL 索引器](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)