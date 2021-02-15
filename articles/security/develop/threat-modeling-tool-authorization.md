---
title: 授权 - Microsoft 威胁建模工具 - Azure | Microsoft 文档
description: 在 Threat Modeling Tool 中了解有关授权缓解的信息。 请参阅潜在威胁列表和缓解说明。
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-csharp
ms.openlocfilehash: b2ad38e518fa4b924992355990ea3eb06a338ebe
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94693152"
---
# <a name="security-frame-authorization--mitigations"></a>安全框架：授权 | 缓解措施 
| 产品/服务 | 文章 |
| --------------- | ------- |
| **计算机信任边界** | <ul><li>[确保配置适当的 ACL 来限制对设备中的数据进行未经授权的访问](#acl-restricted-access)</li><li>[确保将特定于用户的敏感应用程序内容存储在用户配置文件目录中](#sensitive-directory)</li><li>[确保以最低特权运行部署的应用程序](#deployed-privileges)</li></ul> |
| **Web 应用程序** | <ul><li>[处理业务逻辑流时有序执行步骤顺序](#sequential-logic)</li><li>[实施频率限制机制来防止枚举](#rate-enumeration)</li><li>[确保实施适当的授权并遵循最低特权原则](#principle-least-privilege)</li><li>[业务逻辑和资源访问授权决策不应该基于传入的请求参数](#logic-request-parameters)</li><li>[确保不可通过强行浏览来枚举或访问内容和资源](#enumerable-browsing)</li></ul> |
| **Database** | <ul><li>[确保使用最低特权帐户连接到数据库服务器](#privileged-server)</li><li>[实施行级别安全性 RLS 来防止租户访问彼此的数据](#rls-tenants)</li><li>[Sysadmin 角色应该只包括必要的有效用户](#sysadmin-users)</li></ul> |
| **IoT 云网关** | <ul><li>[使用最低特权令牌连接到云网关](#cloud-least-privileged)</li></ul> |
| **Azure 事件中心** | <ul><li>[使用拥有仅限发送权限的 SAS 密钥来生成设备令牌](#sendonly-sas)</li><li>[不要使用可提供事件中心直接访问权限的访问令牌](#access-tokens-hub)</li><li>[使用拥有所需最低权限的 SAS 密钥连接到事件中心](#sas-minimum-permissions)</li></ul> |
| **Azure Document DB** | <ul><li>[尽可能使用资源令牌连接到 Azure Cosmos DB](#resource-docdb)</li></ul> |
| **Azure 信任边界** | <ul><li>[使用 Azure RBAC 启用对 Azure 订阅的精细访问管理](#grained-rbac)</li></ul> |
| **Service Fabric 信任边界** | <ul><li>[使用 Azure RBAC 限制客户端对群集操作的访问](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[根据需要执行安全建模并使用字段级别安全性](#modeling-field)</li></ul> |
| **Dynamics CRM 门户** | <ul><li>[执行门户帐户的安全建模并注意门户的安全模型不同于 CRM 的其他组件](#portal-security)</li></ul> |
| **Azure 存储** | <ul><li>[针对 Azure 表存储中的一系列实体授予精细权限](#permission-entities)</li><li>[使用 azure 资源管理器为 azure 存储帐户启用 azure RBAC) 的基于角色的访问控制 (](#rbac-azure-manager)</li></ul> |
| **移动客户端** | <ul><li>[实施隐式越狱或 root 检测](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[WCF 中的弱类引用](#weak-class-wcf)</li><li>[WCF - 实施授权控制](#wcf-authz)</li></ul> |
| **Web API** | <ul><li>[在 ASP.NET Web API 中实施适当的授权机制](#authz-aspnet)</li></ul> |
| **IoT 设备** | <ul><li>[如果设备支持需要不同权限级别的各种操作，应在设备中执行授权检查](#device-permission)</li></ul> |
| **IoT 现场网关** | <ul><li>[如果现场网关支持需要不同权限级别的各种操作，应在现场网关中执行授权检查](#field-permission)</li></ul> |

## <a name="ensure-that-proper-acls-are-configured-to-restrict-unauthorized-access-to-data-on-the-device"></a><a id="acl-restricted-access"></a>确保配置适当的 ACL 来限制对设备中的数据进行未经授权的访问

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 计算机信任边界 | 
| **SDL 阶段**               | 部署 |  
| **适用的技术** | 泛型 |
| **属性**              | 空值  |
| **参考**              | 空值  |
| **步骤** | 确保配置适当的 ACL 来限制对设备中的数据进行未经授权的访问|

## <a name="ensure-that-sensitive-user-specific-application-content-is-stored-in-user-profile-directory"></a><a id="sensitive-directory"></a>确保将特定于用户的敏感应用程序内容存储在用户配置文件目录中

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 计算机信任边界 | 
| **SDL 阶段**               | 部署 |  
| **适用的技术** | 泛型 |
| **属性**              | 空值  |
| **参考**              | 空值  |
| **步骤** | 确保将特定于用户的敏感应用程序内容存储在用户配置文件目录中。 这是为了防止计算机的多个用户访问彼此的数据。|

## <a name="ensure-that-the-deployed-applications-are-run-with-least-privileges"></a><a id="deployed-privileges"></a>确保以最低特权运行部署的应用程序

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 计算机信任边界 | 
| **SDL 阶段**               | 部署 |  
| **适用的技术** | 泛型 |
| **属性**              | 空值  |
| **参考**              | 空值  |
| **步骤** | 确保以最低特权运行部署的应用程序。 |

## <a name="enforce-sequential-step-order-when-processing-business-logic-flows"></a><a id="sequential-logic"></a>处理业务逻辑流时执行有序步骤顺序

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 空值  |
| **参考**              | 空值  |
| **步骤** | 若要验证真实的用户是否能够从头到尾运行此阶段，可以强制要求应用程序只按步骤顺序依序处理业务逻辑流并按照真实的人类时间处理所有步骤，而不会无序处理、跳过步骤、由其他用户处理步骤，或者过快提交事务。|

## <a name="implement-rate-limiting-mechanism-to-prevent-enumeration"></a><a id="rate-enumeration"></a>实施速率限制机制来防止枚举

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 空值  |
| **参考**              | 空值  |
| **步骤** | 确保敏感标识符是随机的。 针对匿名页面实施 CAPTCHA 控制。 确保错误和异常不会透露特定的数据|

## <a name="ensure-that-proper-authorization-is-in-place-and-principle-of-least-privileges-is-followed"></a><a id="principle-least-privilege"></a>确保实施适当的授权并遵循最低特权原则

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 空值  |
| **参考**              | 空值  |
| **步骤** | <p>原则是只向用户帐户授予用户在工作时最起码需要的特权。 例如，备份用户不需要安装软件，因此，备份用户只拥有运行备份以及与备份相关的应用程序的权限。 其他任何权限（例如安装新软件）会被阻止。 该原则也适用于个人计算机用户，他们通常使用普通用户帐户工作，只在绝对有必要时才打开受密码保护的特权帐户（即超级用户）。 </p><p>也可以向 Web 应用程序应用此原则。 我们不完全依赖于使用会话的基于角色的身份验证方法，而可以通过基于数据库的身份验证系统向用户分配特权。 以后我们仍要使用会话来确定用户是否正确登录，只是暂时向该用户分配特定的角色和特权，目的是验证该用户在系统上有权执行哪些操作。 另外，此方法的一个较大优势是，每当需要向用户分配较少的特权时，可以即时应用更改，因为这种分配不依赖于会话，否则必须先使会话过期。</p>|

## <a name="business-logic-and-resource-access-authorization-decisions-should-not-be-based-on-incoming-request-parameters"></a><a id="logic-request-parameters"></a>业务逻辑和资源访问授权决策不应该基于传入的请求参数

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 空值  |
| **参考**              | 空值  |
| **步骤** | 每当需要检查用户是否仅有权查看特定的数据时，应该在服务器端处理访问限制。 在登录时，userID 应存储在会话变量内部，并且应该使用它来检索数据库中的用户数据 |

### <a name="example"></a>示例
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
现在，攻击者无法篡改和更改应用程序操作，因为用于检索数据的标识符在服务器端处理。

## <a name="ensure-that-content-and-resources-are-not-enumerable-or-accessible-via-forceful-browsing"></a><a id="enumerable-browsing"></a>确保通过强行浏览无法枚举或访问内容和资源

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web 应用程序 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 空值  |
| **参考**              | 空值  |
| **步骤** | <p>不应在 web 根目录中保留敏感的静态文件和配置文件。 对于不需要公开的内容，应该应用适当的访问控制或删除内容本身。</p><p>此外，攻击者通常会将强行浏览与暴力破解技术相结合，尝试访问尽可能多的 URL 来枚举服务器上的目录和文件，从而收集信息。 攻击者可能会检查现有常见文件的所有变体。 例如，搜索密码文件可以找到 psswd.txt、password.htm、password.dat 等文件和其他变体。</p><p>为了缓解此问题，应该包含暴力破解企图检测功能。</p>|

## <a name="ensure-that-least-privileged-accounts-are-used-to-connect-to-database-server"></a><a id="privileged-server"></a>确保使用最低特权帐户连接到数据库服务器

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 数据库 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 空值  |
| **参考**              | [SQL 权限层次结构](/sql/relational-databases/security/permissions-hierarchy-database-engine)、[SQL 安全对象](/sql/relational-databases/security/securables) |
| **步骤** | 应该使用最低特权帐户连接到数据库。 应用程序登录名应限制为在数据库中使用，并且只应执行选定的存储过程。 应用程序登录名不应该拥有对表的直接访问权限。 |

## <a name="implement-row-level-security-rls-to-prevent-tenants-from-accessing-each-others-data"></a><a id="rls-tenants"></a>实施行级别安全性 RLS 来防止租户访问彼此的数据

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 数据库 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | SQL Azure、OnPrem |
| **属性**              | SQL 版本 - V12，SQL 版本 - MsSQL2016 |
| **参考**              | [SQL Server 行级别安全性 (RLS)](/sql/relational-databases/security/row-level-security) |
| **步骤** | <p>行级别安全性使客户能够根据执行查询的用户的特征（例如，组成员身份或执行上下文），控制对数据库表中的行的访问。</p><p>行级别安全性 (RLS) 简化了应用程序中安全性的设计和编码。 使用 RLS 可针对数据行访问实施限制。 例如，确保工作人员只能访问与其部门相关的数据行，或者将客户限制为只能访问与其公司相关的数据。</p><p>访问限制逻辑位于数据库层中，而不是在另一个应用层中远离数据。 数据库系统会在每次尝试从任何层进行数据访问时应用访问限制。 这样就会通过减少安全系统的外围应用，使安全系统变得更加可靠和稳健。</p><p>|

请注意，RLS 作为现成可用的数据库功能，仅适用于 SQL Server 2016 及更高版本、Azure SQL 数据库和 SQL 托管实例。 如果未实施这项现成的 RLS 功能，应确保使用视图和过程来限制数据访问

## <a name="sysadmin-role-should-only-have-valid-necessary-users"></a><a id="sysadmin-users"></a>Sysadmin 角色应该只包括必要的有效用户

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 数据库 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 空值  |
| **参考**              | [SQL 权限层次结构](/sql/relational-databases/security/permissions-hierarchy-database-engine)、[SQL 安全对象](/sql/relational-databases/security/securables) |
| **步骤** | SysAdmin 固定服务器角色的成员应该受到严格限制，永远不应包含应用程序使用的帐户。  请检查该角色中的用户列表，并删除所有不必要的帐户|

## <a name="connect-to-cloud-gateway-using-least-privileged-tokens"></a><a id="cloud-least-privileged"></a>使用最低特权令牌连接到云网关

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | IoT 云网关 | 
| **SDL 阶段**               | 部署 |  
| **适用的技术** | 泛型 |
| **属性**              | 网关选项 - Azure IoT 中心 |
| **参考**              | [Iot 中心访问控制](../../iot-hub/iot-hub-devguide.md) |
| **步骤** | 向连接到云网关（IoT 中心）的各种组件提供最低特权。 典型示例 – 设备管理/预配组件使用 registryread/write，事件处理器 (ASA) 使用服务连接。 各个设备使用设备凭据建立连接|

## <a name="use-a-send-only-permissions-sas-key-for-generating-device-tokens"></a><a id="sendonly-sas"></a>使用仅有发送权限的 SAS 密钥生成设备令牌

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Azure 事件中心 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 空值  |
| **参考**              | [事件中心身份验证和安全模型概述](../../event-hubs/authenticate-shared-access-signature.md) |
| **步骤** | SAS 密钥用于生成各个设备令牌。 为给定的发布者生成设备令牌时，请使用拥有仅限发送权限的 SAS 密钥|

## <a name="do-not-use-access-tokens-that-provide-direct-access-to-the-event-hub"></a><a id="access-tokens-hub"></a>不要使用可提供事件中心直接访问权限的访问令牌

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Azure 事件中心 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 空值  |
| **参考**              | [事件中心身份验证和安全模型概述](../../event-hubs/authenticate-shared-access-signature.md) |
| **步骤** | 不应该将授予事件中心直接访问权限的令牌提供给设备。 对设备使用仅授予发布者访问权限的最低特权令牌有助于识别设备，如果发现该设备是恶意设备或者是遭到入侵的设备，则阻止该设备。|

## <a name="connect-to-event-hub-using-sas-keys-that-have-the-minimum-permissions-required"></a><a id="sas-minimum-permissions"></a>使用具有所需最低权限的 SAS 密钥连接到事件中心

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Azure 事件中心 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 空值  |
| **参考**              | [事件中心身份验证和安全模型概述](../../event-hubs/authenticate-shared-access-signature.md) |
| **步骤** | 向连接到事件中心的各种后端应用程序提供最低特权。 为每个后端应用程序单独生成 SAS 密钥并只向它们提供所需的权限 - 发送、接收或管理。|

## <a name="use-resource-tokens-to-connect-to-cosmos-db-whenever-possible"></a><a id="resource-docdb"></a>尽可能使用资源令牌连接到 Cosmos DB

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Azure Document DB | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 空值  |
| **参考**              | 空值  |
| **步骤** | 资源令牌与 Azure Cosmos DB 权限资源关联，可捕获数据库用户与该用户对某个特定 Azure Cosmos DB 应用程序资源（例如，集合、文档）的权限之间的关系。 如果无法信任客户端（例如，移动或桌面客户端等最终用户应用程序）对主密钥或只读密钥的处理，请始终使用资源令牌访问 Azure Cosmos DB。通过可以安全存储这些密钥的后端应用程序使用主密钥或只读密钥。|

## <a name="enable-fine-grained-access-management-to-azure-subscription-using-azure-rbac"></a><a id="grained-rbac"></a>使用 Azure RBAC 启用对 Azure 订阅的精细访问管理

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Azure 信任边界 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 空值  |
| **参考**              | [添加或删除 Azure 角色分配以管理对 Azure 订阅资源的访问权限](../../role-based-access-control/role-assignments-portal.md)  |
| **步骤** | Azure 基于角色的访问控制 (Azure RBAC) 可用于对 Azure 进行细致的访问管理。 使用 Azure RBAC，只能授予用户执行其作业所需的访问权限量。|

## <a name="restrict-clients-access-to-cluster-operations-using-service-fabric-rbac"></a><a id="cluster-rbac"></a>使用 Service Fabric RBAC 限制客户端对群集操作的访问权限

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Service Fabric 信任边界 | 
| **SDL 阶段**               | 部署 |  
| **适用的技术** | 泛型 |
| **属性**              | 环境 - Azure |
| **参考**              | [Service Fabric Service Fabric 客户端的基于角色的访问控制](../../service-fabric/service-fabric-cluster-security-roles.md) |
| **步骤** | <p>Azure Service Fabric 针对连接到 Service Fabric 群集的客户端支持两种不同的访问控制类型：管理员和用户。 访问控制可让群集管理员针对不同的用户组限制特定群集操作的访问权限，使群集更加安全。</p><p>管理员拥有对管理功能（包括读取/写入功能）的完全访问权限。 默认情况下，用户只有管理功能的读取访问权限（例如查询功能），以及解析应用程序和服务的能力。</p><p>可在创建群集时为每个角色提供不同的证书，以指定两个客户端角色（管理员和客户端）。</p>|

## <a name="perform-security-modeling-and-use-field-level-security-where-required"></a><a id="modeling-field"></a>根据需要执行安全建模并使用字段级别安全性

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Dynamics CRM | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 空值  |
| **参考**              | 空值  |
| **步骤** | 根据需要执行安全建模并使用字段级别安全性|

## <a name="perform-security-modeling-of-portal-accounts-keeping-in-mind-that-the-security-model-for-the-portal-differs-from-the-rest-of-crm"></a><a id="portal-security"></a>执行门户帐户的安全建模并注意门户的安全模型不同于 CRM 的其他组件

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Dynamics CRM 门户 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 空值  |
| **参考**              | 空值  |
| **步骤** | 执行门户帐户的安全建模并注意门户的安全模型不同于 CRM 的其他组件|

## <a name="grant-fine-grained-permission-on-a-range-of-entities-in-azure-table-storage"></a><a id="permission-entities"></a>针对 Azure 表存储中的一系列实体授予精细权限

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Azure 存储 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | StorageType - 表 |
| **参考**              | [如何使用 SAS 来委派对 Azure 存储帐户中对象的访问权限](../../storage/blobs/security-recommendations.md#identity-and-access-management) |
| **步骤** | 在某些业务方案中，Azure 表存储可能需要存储面向不同当事方的敏感数据。 例如，与不同国家/地区相关的敏感数据。 在这种情况下，可以通过指定分区键和行键范围来构造 SAS 签名，使用户能够访问与特定国家/地区相关的数据。| 

## <a name="enable-azure-role-based-access-control-azure-rbac-to-azure-storage-account-using-azure-resource-manager"></a><a id="rbac-azure-manager"></a>使用 azure 资源管理器为 azure 存储帐户启用 azure RBAC) 的基于角色的访问控制 (

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Azure 存储 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 空值  |
| **参考**              | [如何使用 Azure RBAC) 的 Azure 基于角色的访问控制来保护存储帐户 (](../../storage/blobs/security-recommendations.md) |
| **步骤** | <p>创建新的存储帐户时，可以选择经典或 Azure 资源管理器部署模型。 在 Azure 中创建资源的经典模型只允许以孤注一掷的方式访问订阅，并访问存储帐户。</p><p>通过 Azure 资源管理器模型，可将存储帐户放在资源组中，使用 Azure Active Directory 控制对该特定存储帐户的管理平面的访问。 例如，可以授权特定用户访问存储帐户密钥，而其他用户可以查看有关存储帐户的信息，但无法访问存储帐户密钥。</p>|

## <a name="implement-implicit-jailbreak-or-rooting-detection"></a><a id="rooting-detection"></a>实施“隐式越狱”或“获取 root 权限”检测

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | 移动客户端 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 空值  |
| **参考**              | 空值  |
| **步骤** | <p>如果手机经过 root 或越狱，应用程序应保护自身的配置和用户数据。 Root/越狱隐含着未经授权访问的风险，而普通用户不会在他们自己的手机上进行这种访问。 因此，应用程序应在启动时运行隐式检测逻辑，检测手机是否经过 root。</p><p>该检测逻辑只需访问正常情况下只能由 root 用户访问的文件，例如：</p><ul><li>/system/app/Superuser.apk</li><li>/sbin/su</li><li>/system/bin/su</li><li>/system/xbin/su</li><li>/data/local/xbin/su</li><li>/data/local/bin/su</li><li>/system/sd/xbin/su</li><li>/system/bin/failsafe/su</li><li>/data/local/su</li></ul><p>如果应用程序可以访问其中的任何文件，则表示应用程序正以 root 用户身份运行。</p>|

## <a name="weak-class-reference-in-wcf"></a><a id="weak-class-wcf"></a>WCF 中的弱类引用

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | WCF | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型、NET Framework 3 |
| **属性**              | 空值  |
| **参考**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10))、[巩固王国](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **步骤** | <p>系统使用弱类引用，这可能会允许攻击者执行未经授权的代码。 程序引用未唯一标识的用户定义类。 当 .NET 加载这种弱标识的类时，CLR 类型加载程序会在以下位置按指定的顺序搜索该类：</p><ol><li>如果该类型的程序集已知，加载程序将搜索配置文件的重定向位置、GAC、使用配置信息的当前程序集，以及应用程序基目录</li><li>如果程序集未知，加载程序将搜索当前程序集、mscorlib，以及 TypeResolve 事件处理程序返回的位置</li><li>可以使用类型转发机制和 AppDomain.TypeResolve 事件等挂钩来修改此 CLR 搜索顺序</li></ol><p>如果攻击者通过创建同名的替代类并将它放置在 CLR 首先加载的替代位置来利用 CLR 搜索顺序，CLR 会在无意中执行攻击者提供的代码</p>|

### <a name="example"></a>示例
以下 WCF 配置文件中的 `<behaviorExtensions/>` 元素指示 WCF 将自定义行为类添加到特定的 WCF 扩展。
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
使用完全限定的（强）名称可唯一标识某个类型，进一步提高系统的安全性。 在 machine.config 和 app.config 文件中注册类型时，请使用完全限定的程序集名称。

### <a name="example"></a>示例
以下 WCF 配置文件中的 `<behaviorExtensions/>` 元素指示 WCF 将强引用自定义行为类添加到特定的 WCF 扩展。
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""Microsoft.ServiceModel.Samples.MyBehaviorSection, MyBehavior,
            Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```

## <a name="wcf-implement-authorization-control"></a><a id="wcf-authz"></a>WCF - 实施授权控制

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | WCF | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型、NET Framework 3 |
| **属性**              | 空值  |
| **参考**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10))、[巩固王国](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **步骤** | <p>此服务不使用授权控制。 当客户端调用特定的 WCF 服务时，WCF 将提供多种授权方案，用于验证调用方是否有权在服务器上执行服务方法。 如果没有为 WCF 服务启用授权控制，经过身份验证的用户可以实现特权升级。</p>|

### <a name="example"></a>示例
以下配置指示 WCF 在执行服务时不要检查客户端的授权级别：
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""None"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
使用服务授权方案来验证服务方法的调用方是否也有权执行此操作。 WCF 提供两种模式，允许定义自定义的授权方案。 UseWindowsGroups 模式使用 Windows 角色和用户进行身份验证，UseAspNetRoles 模式使用 ASP.NET 角色提供程序（例如 SQL Server）进行身份验证。

### <a name="example"></a>示例
以下配置指示 WCF 在执行 Add 服务之前确保客户端属于管理员组：
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""UseWindowsGroups"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
然后，按如下所示声明该服务：
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a name="implement-proper-authorization-mechanism-in-aspnet-web-api"></a><a id="authz-aspnet"></a>在 ASP.NET Web API 中实施适当的授权机制

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | Web API | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型、MVC5 |
| **属性**              | 不适用，标识提供者 - ADFS，标识提供程序 - Azure AD |
| **参考**              | [ASP.NET Web API 中的身份验证和授权](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) |
| **步骤** | <p>如果应用程序依赖于 Azure AD 或 ADFS 声明，则可从这些声明派生应用程序用户的角色信息，因为标识提供者或应用程序本身可以提供此信息。 在上述任何情况下，自定义授权实现应验证用户角色信息。</p><p>如果应用程序依赖于 Azure AD 或 ADFS 声明，则可从这些声明派生应用程序用户的角色信息，因为标识提供者或应用程序本身可以提供此信息。 在上述任何情况下，自定义授权实现应验证用户角色信息。</p>

### <a name="example"></a>示例
```csharp
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
public class ApiAuthorizeAttribute : System.Web.Http.AuthorizeAttribute
{
        public async override Task OnAuthorizationAsync(HttpActionContext actionContext, CancellationToken cancellationToken)
        {
            if (actionContext == null)
            {
                throw new Exception();
            }

            if (!string.IsNullOrEmpty(base.Roles))
            {
                bool isAuthorized = ValidateRoles(actionContext);
                if (!isAuthorized)
                {
                    HandleUnauthorizedRequest(actionContext);
                }
            }

            base.OnAuthorization(actionContext);
        }

public bool ValidateRoles(actionContext)
{
   //Authorization logic here; returns true or false
}

}
```
所有需要保护的控制器和操作方法应使用上述特性进行修饰。
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a name="perform-authorization-checks-in-the-device-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="device-permission"></a>如果设备支持需要不同权限级别的各种操作，应在设备中执行授权检查

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | IoT 设备 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 空值  |
| **参考**              | 空值  |
| **步骤** | <p>设备应向调用方授权，让调用方检查自身是否拥有所需的权限来执行请求的操作。 例如，假设设备是可以通过云监视的智能门锁，提供远程锁门等功能。</p><p>仅当门卡持有人的身体与门靠近时，该智能门锁才提供解锁功能。 在这种情况下，实现远程命令和控制时，不应该让云网关提供任何开门功能，因为它无权发送开门的命令。</p>|

## <a name="perform-authorization-checks-in-the-field-gateway-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="field-permission"></a>如果现场网关支持需要不同权限级别的各种操作，应在现场网关中执行授权检查

| 标题                   | 详细信息      |
| ----------------------- | ------------ |
| **组件**               | IoT 现场网关 | 
| **SDL 阶段**               | 构建 |  
| **适用的技术** | 泛型 |
| **属性**              | 空值  |
| **参考**              | 空值  |
| **步骤** | 现场网关应向调用方授权，让调用方检查自身是否拥有所需的权限来执行请求的操作。 例如，用于配置现场网关的管理用户接口/API 的权限，应该与连接到现场网关的设备的权限不同。|