---
title: Azure HDInsight 中的托管标识
description: 提供 Azure HDInsight 中托管标识的实现概述。
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: f2b7f6e8421a735db131bc05605936e8cb2d87eb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944129"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Azure HDInsight 中的托管标识

托管标识是在 Azure Active Directory (Azure AD) 中注册的标识，其凭据由 Azure 管理。 利用托管标识，无需在 Azure AD 中注册服务主体。 或维护凭据，如证书。

可以在 Azure HDInsight 中使用托管标识，根据需要访问 Azure AD 域服务或访问 Azure Data Lake Storage Gen2 中的文件。

有两种类型的托管标识：用户分配的托管标识和系统分配的托管标识。 Azure HDInsight 仅支持用户分配的托管标识。 HDInsight 不支持系统分配的托管标识。 用户分配的托管标识创建为独立的 Azure 资源，可将其分配到一个或多个 Azure 服务实例。 相比之下，系统分配的托管标识是在 Azure AD 中创建的，系统会自动在特定的 Azure 服务实例上直接启用它。 然后，系统分配的该托管标识的生存期将绑定到启用该托管标识的服务实例的生存期。

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight 托管标识的实现

在 Azure HDInsight 中，托管标识仅适用于内部组件的 HDInsight 服务。 目前没有任何支持的方法可用于通过 HDInsight 群集节点上安装的托管标识生成访问令牌来访问外部服务。 对于计算 VM 等某些 Azure 服务，托管标识是使用某个可用于获取访问令牌的终结点实现的。 此终结点当前在 HDInsight 节点中不可用。

如果需要启动应用程序以避免将机密/密码放入分析作业（例如 SCALA 作业），可以使用脚本操作将自己的证书分发到群集节点，然后使用该证书获取访问令牌（例如，用于访问 Azure KeyVault 的令牌）。

## <a name="create-a-managed-identity"></a>创建托管标识

可以通过以下任何方法创建托管标识：

* [Azure 门户](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

托管标识的剩余配置步骤取决于使用该托管标识的方案。

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Azure HDInsight 中的托管标识方案

Azure HDInsight 中的多种方案都会使用托管标识。 有关详细的设置和配置说明，请参阅相关文档：

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2-portal.md#create-a-user-assigned-managed-identity)
* [企业安全性套餐](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [客户管理的密钥磁盘加密](disk-encryption.md)

HDInsight 将自动续订用于这些方案的托管标识的证书。 但是，当多个不同的托管标识用于长时间运行的群集时，可能会有一个限制，即对于所有托管标识，证书续订可能不会按预期方式允许。 由于此限制，如果你计划使用长时间运行的群集（例如，运行超过 60 天），我们建议对上述所有方案使用相同的托管标识。 

如果你已经创建了具有多个不同托管标识的长时间运行的群集并且遇到以下问题之一：
 * 在 ESP 群集中，群集服务启动失败，或者纵向扩展和其他操作启动失败，并出现身份验证错误。
 * 在 ESP 群集中，更改 AAD-DS LDAPS 证书时，LDAPS 证书不会自动更新，因此 LDAP 同步和纵向扩展启动失败。
 * 对 ADLS Gen2 的 MSI 访问启动失败。
 * 在 CMK 方案中无法轮换加密密钥。

则应将上述方案所需的角色和权限分配给群集中使用的所有托管标识。 例如，如果你对 ADLS Gen2 和 ESP 群集使用了不同的托管标识，则它们都应分配有“存储 Blob 数据所有者”和“HDInsight 域服务参与者”角色，以避免遇到这些问题。

## <a name="faq"></a>常见问题

### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>如果在创建群集后删除托管标识，会发生什么情况？

需要托管标识时，群集会遇到问题。 创建群集后，当前没有办法更新或更改托管标识。 建议确保在群集运行时不删除托管标识。 或者，可以重新创建群集并分配新的托管标识。

## <a name="next-steps"></a>后续步骤

* [什么是 Azure 资源的托管标识？](../active-directory/managed-identities-azure-resources/overview.md)
