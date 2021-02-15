---
title: 启动、停止和删除 Azure 春季 Cloud 应用程序 |Microsoft Docs
description: 如何启动、停止和删除 Azure Spring Cloud 应用程序
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 479daa9a94247ada30c54a9c5df3471035765087
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90908281"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>启动、停止和删除 Azure 春季云应用程序

本文适用于：✔️ Java ✔️ C#

本指南说明如何使用 Azure 门户或 Azure CLI 在 Azure 春季云中更改应用程序的状态。

## <a name="using-the-azure-portal"></a>使用 Azure 门户

部署应用程序后，可以使用 Azure 门户启动、停止和删除应用程序。

1. 转到 Azure 门户中的 Azure Spring Cloud 服务实例。
1. 选择“应用程序仪表板”**** 选项卡。
1. 选择要更改其状态的应用程序。
1. 在该应用程序的 " **概述** " 页上，选择 " **启动/停止**"、" **重新启动**" 或 " **删除**"。

## <a name="using-the-azure-cli"></a>使用 Azure CLI

> [!NOTE]
> 可以使用可选参数，并使用 Azure CLI 配置默认值。 阅读 [我们的参考文档](/cli/azure/ext/spring-cloud/spring-cloud?view=azure-cli-latest&preserve-view=true)，详细了解 Azure CLI。  

首先，为 Azure CLI 安装 Azure 春季云扩展，如下所示：

```azurecli
az extension add --name spring-cloud
```

接下来，选择以下任一 Azure CLI 操作：

* 若要启动应用程序：

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 若要停止应用程序：

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 若要重启应用程序：

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* 若要删除应用程序：

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
