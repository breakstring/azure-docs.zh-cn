---
title: 教程 - 删除 Azure Red Hat OpenShift 群集
description: 在本教程中，了解如何使用 Azure CLI 删除 Azure Red Hat OpenShift 群集
author: sakthi-vetrivel
ms.custom: fasttrack-edit
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 372f531154528698da05c7a20c5d842b593259d1
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072246"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-4-cluster"></a>教程：删除 Azure Red Hat OpenShift 4 群集

在本教程（由三个教程组成的系列教程的第三部分）中，运行 OpenShift 4 的 Azure Red Hat OpenShift 群集会被删除。 学习如何：

> [!div class="checklist"]
> * 删除 Azure Red Hat OpenShift 群集


## <a name="before-you-begin"></a>开始之前

在之前的教程中，创建了 Azure Red Hat OpenShift 群集并使用 OpenShift Web 控制台与它建立了连接。 如果尚未完成这些步骤，因此想要逐一完成它们，请先阅读[教程 1 - 创建 Azure Red Hat Openshift 4 群集](tutorial-create-cluster.md)。

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.6.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="sign-in-to-azure"></a>登录 Azure

如果在本地运行 Azure CLI，请运行 `az login` 以登录到 Azure。

```bash
az login
```

如果你有权访问多个订阅，请运行 `az account set -s {subscription ID}`（将 `{subscription ID}` 替换为要使用的订阅）。

## <a name="delete-the-cluster"></a>删除群集

在之前的教程中，设置了以下变量。

```bash
CLUSTER=yourclustername
RESOURCEGROUP=yourresourcegroup
```

使用这些值来删除群集：

```bash
az aro delete --resource-group $RESOURCEGROUP --name $CLUSTER
```

系统将提示你确认是否要删除该群集。 通过 `y` 进行确认后，删除群集需要几分钟。 此命令完成后，整个资源组以及其中所有资源都将被删除，包括该群集。

## <a name="next-steps"></a>后续步骤

本教程的此部分介绍了如何：
> [!div class="checklist"]
> * 删除 Azure Red Hat OpenShift 4 群集

详细了解如何将 OpenShift 与官方 [Red Hat OpenShift 文档](https://docs.openshift.com/container-platform/4.6/welcome/index.html)结合使用
