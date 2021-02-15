---
title: 在 Azure Red Hat OpenShift 中管理资源 |Microsoft Docs
description: 在 Azure Red Hat OpenShift 群集中管理项目、模板和图像流
services: openshift
keywords: red hat openshift 项目请求配置程序
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: 0abc086553f5e903a71bcfd0b6322bcee56d2d8b
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216923"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>在 Azure Red Hat OpenShift 群集中管理项目、模板和图像流

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 将在年6月 30 2022 日停用。 支持创建新的 Azure Red Hat OpenShift 3.11 群集持续到30年 11 2020 月30日。 停用后，剩余的 Azure Red Hat OpenShift 3.11 群集将关闭，以防出现安全漏洞。
> 
> 按照本指南 [创建 Azure Red Hat OpenShift 4 群集](tutorial-create-cluster.md)。
> 如果有特定问题， [请](mailto:arofeedback@microsoft.com)联系我们。

在 OpenShift 容器平台中，项目用于对相关对象进行分组和隔离。 作为管理员，你可以向开发人员提供对特定项目的访问权限，允许他们创建自己的项目，并向他们授予对各个项目的管理权限。

## <a name="self-provisioning-projects"></a>自行预配项目

可以让开发人员创建他们自己的项目。 API 终结点负责根据名为项目请求的模板预配项目。 `oc new-project`当开发人员创建新项目时，web 控制台和命令将使用此终结点。

提交项目请求时，API 会将以下参数替换为模板：

| 参数               | 说明                                    |
| ----------------------- | ---------------------------------------------- |
| PROJECT_NAME            | 项目的名称。 必需。             |
| PROJECT_DISPLAYNAME     | 项目的显示名称。 可能为空。 |
| PROJECT_DESCRIPTION     | 项目的说明。 可能为空。  |
| PROJECT_ADMIN_USER      | 管理用户的用户名。       |
| PROJECT_REQUESTING_USER | 请求用户的用户名。           |

使用自助设置程序群集角色绑定向开发人员授予对 API 的访问权限。 默认情况下，此功能适用于所有已经过身份验证的开发人员。

## <a name="modify-the-template-for-a-new-project"></a>修改新项目的模板 

1. 以具有权限的用户身份登录 `customer-admin` 。

2. 编辑默认的项目请求模板。

   ```
   oc edit template project-request -n openshift
   ```

3. 通过添加以下批注，从 Azure Red Hat OpenShift (ARO) 更新过程中删除默认项目模板： `openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   ARO 更新过程不会更新项目请求模板。 这使客户能够自定义模板并在群集更新时保留这些自定义项。

## <a name="disable-the-self-provisioning-role"></a>禁用自行设置角色

您可以防止经过身份验证的用户组自设置新项目。

1. 以具有权限的用户身份登录 `customer-admin` 。

2. 编辑设置程序群集角色绑定。

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. 通过添加以下批注，从 ARO 更新过程中删除角色： `openshift.io/reconcile-protect: "true"` 。

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. 更改群集角色绑定，阻止 `system:authenticated:oauth` 创建项目：

   ```
   apiVersion: rbac.authorization.k8s.io/v1
   groupNames:
   - osa-customer-admins
   kind: ClusterRoleBinding
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
     labels:
       azure.openshift.io/owned-by-sync-pod: "true"
     name: self-provisioners
   roleRef:
     name: self-provisioner
   subjects:
   - kind: SystemGroup
     name: osa-customer-admins
   ```

## <a name="manage-default-templates-and-imagestreams"></a>管理默认模板和 imageStreams

在 Azure Red Hat OpenShift 中，可以禁用命名空间中任何默认模板和图像流的更新 `openshift` 。
禁用所有 `Templates` 和 `ImageStreams` 命名空间中的更新 `openshift` ：

1. 以具有权限的用户身份登录 `customer-admin` 。

2. 编辑 `openshift` 命名空间：

   ```
   oc edit namespace openshift
   ```

3. `openshift`通过添加以下批注，从 ARO 更新过程中删除命名空间：`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   通过向命名空间中的任何单个对象 `openshift` 添加批注，可以将其从更新过程中移除 `openshift.io/reconcile-protect: "true"` 。

## <a name="next-steps"></a>后续步骤

尝试教程：
> [!div class="nextstepaction"]
> [创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)
