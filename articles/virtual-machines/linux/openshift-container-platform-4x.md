---
title: 在 Azure 中部署 OpenShift 容器平台4。x
description: 在 Azure 中部署 OpenShift 容器 Platform 4.x。
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: f2fb1657c26fce3e1fdd67f36b0c6511b78dd216
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87373532"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>在 Azure 中部署 OpenShift 容器平台4。x

Azure 中的 OpenShift 容器平台 (OCP) 4.2 现在通过 Installer-Provisioned 基础结构 (IPI) 模型进行了支持。  尝试 OpenShift 4 的登录页为 [try.openshift.com](https://try.openshift.com/)。 若要在 Azure 中安装 OCP 4.2，请访问 [Red Hat OpenShift 群集管理器](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) 页。  需要使用 Red Hat 凭据才能访问此站点。


## <a name="notes"></a>注意 

 - 需要在 Azure 中安装和运行 OCP Azure Active Directory (AAD) 服务主体 (SP) 
     - 必须为 SP 授予 Azure Active Directory 关系图的 **OwnedBy** 的 API 权限
     - AAD 租户管理员必须授予管理员同意才能使此 API 权限生效
     - SP 必须向订阅授予 **参与者** 和 **用户访问管理员** 角色
 - 用于 OCP 4.x 的安装模型不同于3.x 版，并且没有可用于在 Azure 中部署 OCP 4.x 的 Azure 资源管理器模板
 - 如果在安装过程中遇到问题，请联系相应的公司 (Microsoft 或 Red Hat) 

| 问题说明 | 联系点 |
|-------------------|---------------|
| Azure 特定问题 (AAD、SP、Azure 订阅等 )                               | Microsoft |
|  (安装失败/错误、Red Hat 订阅等的特定于 OpenShift 的问题 )  |  Red Hat  |




## <a name="next-steps"></a>后续步骤

- [OpenShift 容器平台入门](https://docs.openshift.com)
