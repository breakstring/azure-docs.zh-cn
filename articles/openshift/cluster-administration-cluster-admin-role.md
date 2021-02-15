---
title: Azure Red Hat OpenShift 群集管理员角色 |Microsoft Docs
description: Azure Red Hat OpenShift 群集管理员角色的分配和使用
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: f8b84461cc96c64ce2f97a8b4bb69246f5a0dccc
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221173"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift 客户管理员角色

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 将在年6月 30 2022 日停用。 支持创建新的 Azure Red Hat OpenShift 3.11 群集持续到30年 11 2020 月30日。 停用后，剩余的 Azure Red Hat OpenShift 3.11 群集将关闭，以防出现安全漏洞。
> 
> 按照本指南 [创建 Azure Red Hat OpenShift 4 群集](tutorial-create-cluster.md)。
> 如果有特定问题， [请](mailto:arofeedback@microsoft.com)联系我们。

你是 Azure Red Hat OpenShift 群集的群集管理员。 你的帐户已增加权限，并可访问所有用户创建的项目。

当你的帐户有与之绑定的客户-管理员群集授权角色时，它可以自动管理项目。

> [!Note] 
> 客户管理员群集群集角色与群集管理员群集角色不同。

例如，可以执行与一组谓词相关联的操作， (`create`) 对一组 () 的资源名称执行操作 `templates` 。 若要查看这些角色及其谓词和资源集的详细信息，请运行以下命令：

`$ oc get clusterroles customer-admin-cluster -o yaml`

谓词名称并不一定直接映射到 `oc` 命令。 它们更常见地等同于你可以执行的 CLI 操作类型。 

例如， `list` 如果使用谓词，则可以显示资源名称 () 的所有对象的列表 `oc get` 。 `get`如果你知道特定对象的名称 () ，则谓词意味着可以显示该对象的详细信息 `oc describe` 。

## <a name="configure-the-customer-administrator-role"></a>配置客户管理员角色

只有在创建群集期间，才能通过提供标志来配置客户管理群集的群集角色 `--customer-admin-group-id` 。 此字段当前无法在 Azure 门户中进行配置。 若要了解如何配置 Azure Active Directory 和 Administrators 组，请参阅 [Azure Red Hat OpenShift 的 Azure Active Directory 集成](howto-aad-app-configuration.md)。

## <a name="confirm-membership-in-the-customer-administrator-role"></a>确认客户管理员角色中的成员资格

若要确认客户管理员组中的成员身份，请尝试 OpenShift CLI 命令 `oc get nodes` 或 `oc projects` 。 `oc get nodes` 如果你具有客户管理员-群集角色，将显示节点的列表，并且如果你只有客户管理员-项目角色，则会显示权限错误。 `oc projects` 将显示群集中的所有项目，而不是只显示您正在使用的项目。

若要进一步浏览群集中的角色和权限，可以使用 [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) 命令。

## <a name="next-steps"></a>后续步骤

配置客户-管理员群集的群集角色：
> [!div class="nextstepaction"]
> [Azure Active Directory Azure Red Hat OpenShift 集成](howto-aad-app-configuration.md)
