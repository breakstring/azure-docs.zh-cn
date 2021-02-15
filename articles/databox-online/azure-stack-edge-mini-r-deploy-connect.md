---
title: 教程：在 Azure 门户中连接到 Azure Stack Edge Mini R
description: 了解如何通过本地 Web UI 连接到 Azure Stack Edge Mini R 设备。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Mini R so I can use it to transfer data to Azure.
ms.openlocfilehash: fe76391a5cfce8d7d39e47131db108ab87e5aed5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463782"
---
# <a name="tutorial-connect-to-azure-stack-edge-mini-r"></a>教程：连接到 Azure Stack Edge Mini R

本教程介绍如何通过本地 Web UI 连接到 Azure Stack Edge Mini R 设备。

连接过程可能需要大约 5 分钟才能完成。

在本教程中，你将学习：

> [!div class="checklist"]
>
> * 先决条件
> * 连接到物理设备



## <a name="prerequisites"></a>先决条件

配置和设置 Azure Stack Edge 设备之前，请确保：

* 已根据[安装 Azure Stack Edge](azure-stack-edge-mini-r-deploy-install.md) 中详述的内容安装了物理设备。


## <a name="connect-to-the-local-web-ui-setup"></a>连接到本地 Web UI 设置

1. 在计算机上配置以太网适配器，以便通过静态 IP 地址 192.168.100.5 和子网 255.255.255.0 连接到 Azure Stack Edge Pro 设备。

2. 将计算机连接到设备上的端口 1。 如果将计算机直接连接到设备（不通过交换机），请使用交叉电缆或 USB 以太网适配器。 使用下图标识设备上的端口 1。

    ![Wi-Fi 布线](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)

[!INCLUDE [azure-stack-edge-gateway-delpoy-connect](../../includes/azure-stack-edge-gateway-deploy-connect.md)]


## <a name="next-steps"></a>后续步骤

本教程介绍了以下内容：

> [!div class="checklist"]
> * 先决条件
> * 连接到物理设备


若要了解如何在设备上配置网络设置，请参阅：

> [!div class="nextstepaction"]
> [配置网络](./azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)
