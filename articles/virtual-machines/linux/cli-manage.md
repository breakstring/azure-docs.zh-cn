---
title: 常用的 Azure CLI 命令
description: 了解一些常用 Azure CLI 命令，以便开始在 Azure 资源管理器模式下管理 VM
author: RicksterCDN
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 05/12/2017
ms.author: rclaus
ms.openlocfilehash: 5a9dd8aaeed0642461e4244a72a3dab5c96a77b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87372240"
---
# <a name="common-azure-cli-commands-for-managing-azure-resources"></a>用于管理 Azure 资源的常用 Azure CLI 命令

可以通过 Azure CLI 在 macOS、Linux 和 Windows 上创建和管理 Azure 资源。 本文详述了一些最常用于创建和管理虚拟机 (VM) 的命令。

本文需要 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 也可以通过浏览器使用 [Cloud Shell](../../cloud-shell/quickstart.md)。

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Azure CLI 中的基本 Azure 资源管理器命令
有关特定命令行开关和选项的详细帮助，可以通过键入 `az <command> <subcommand> --help` 来使用联机命令帮助和选项。

### <a name="create-vms"></a>创建 VM
| 任务 | Azure CLI 命令 |
| --- | --- |
| 创建资源组 | `az group create --name myResourceGroup --location eastus` |
| 创建 Linux VM | `az vm create --resource-group myResourceGroup --name myVM --image ubuntults` |
| 创建 Windows VM | `az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter` |

### <a name="manage-vm-state"></a>管理 VM 状态
| 任务 | Azure CLI 命令 |
| --- | --- |
| 启动 VM | `az vm start --resource-group myResourceGroup --name myVM` |
| 停止 VM | `az vm stop --resource-group myResourceGroup --name myVM` |
| 释放 VM | `az vm deallocate --resource-group myResourceGroup --name myVM` |
| 重新启动 VM | `az vm restart --resource-group myResourceGroup --name myVM` |
| 重新部署 VM | `az vm redeploy --resource-group myResourceGroup --name myVM` |
| 删除 VM | `az vm delete --resource-group myResourceGroup --name myVM` |

### <a name="get-vm-info"></a>获取 VM 信息
| 任务 | Azure CLI 命令 |
| --- | --- |
| 列出 VM | `az vm list` |
| 获取有关 VM 的信息 | `az vm show --resource-group myResourceGroup --name myVM` |
| 获取 VM 资源的使用情况 | `az vm list-usage --location eastus` |
| 获取所有可用 VM 大小 | `az vm list-sizes --location eastus` |

## <a name="disks-and-images"></a>磁盘和映像
| 任务 | Azure CLI 命令 |
| --- | --- |
| 将数据磁盘添加到 VM | `az vm disk attach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk --size-gb 128 --new` |
| 从 VM 中删除数据磁盘 | `az vm disk detach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk` |
| 调整磁盘大小 | `az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 256` |
| 拍摄磁盘快照 | `az snapshot create --resource-group myResourceGroup --name mySnapshot --source myDataDisk` |
| 创建 VM 的映像 | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| 从映像创建 VM | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>后续步骤
有关 CLI 命令的其他示例，请参阅[使用 Azure CLI 创建和管理 Linux VM](tutorial-manage-vm.md) 教程。
