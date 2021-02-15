---
title: 在 Azure 中重新部署 Linux 虚拟机 | Microsoft Docs
description: 在 Azure 中如何通过重新部署 Linux 虚拟机来缓解 SSH 连接问题。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: genlin
manager: dcscontentpm
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: d553fb6b2061f987e3e098ae47ebca9cd3f60984
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203399"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>将 Linux 虚拟机重新部署到新的 Azure 节点
如果在对 SSH 或应用程序访问 Azure 中 Linux 虚拟机 (VM) 进行故障排除时遇到困难，重新部署 VM 可能会有帮助。 重新部署 VM 时，将 VM 移到 Azure 基础结构中的新节点，然后重新提供支持。 所有配置选项和关联资源均保留。 本文介绍如何使用 Azure CLI 或 Azure 门户重新部署 VM。

> [!NOTE]
> 重新部署 VM 后，临时磁盘将丢失，并将更新与虚拟网络接口关联的动态 IP 地址。 


## <a name="use-the-azure-cli"></a>使用 Azure CLI
安装最新的 [Azure CLI](/cli/azure/install-az-cli2) 并使用 [az login](/cli/azure/reference-index) 登录到 Azure 帐户。

使用 [az vm redeploy](/cli/azure/vm) 重新部署 VM。 以下示例在名为“myResourceGroup”  的资源组中重新部署名为“myVM”  的 VM：

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>使用 Azure 经典 CLI

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


安装[最新的 Azure 经典 CLI](/cli/azure/install-classic-cli) 并登录到 Azure 帐户。 请确保处于资源管理器模式 (`azure config mode arm`)。

以下示例在名为“myResourceGroup”  的资源组中重新部署名为“myVM”  的 VM：

```console
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>后续步骤
如果在连接 VM 时遇到问题，可以在 [troubleshooting SSH connections](troubleshoot-ssh-connection.md)（SSH 连接故障排除）或 [detailed SSH troubleshooting steps](detailed-troubleshoot-ssh-connection.md)（详细的 SSH 故障排除步骤）中找到特定的帮助。 如果无法访问在 VM 上运行的应用程序，还可以阅读 [application troubleshooting issues](troubleshoot-app-connection.md)（应用程序故障排除问题）。
