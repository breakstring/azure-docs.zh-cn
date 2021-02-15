---
title: 在 Microsoft Azure Stack Edge Pro GPU 设备上运行 GPU 模块 |Microsoft Docs
description: 描述如何通过 Azure 门户在 Azure Stack 边缘 Pro 设备上配置和运行 GPU 上的模块。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/04/2021
ms.author: alkohli
ms.openlocfilehash: d172ce98ba93360c621a91fb0e2a55d022470943
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935542"
---
# <a name="configure-and-run-a-module-on-gpu-on-azure-stack-edge-pro-device"></a>在 Azure Stack Edge Pro 设备上的 GPU 上配置和运行模块

Azure Stack Edge Pro 设备包含一个或多个图形处理单元 (GPU) 。 Gpu 是 AI 计算的常用选项，因为它们提供并行处理功能，并且在图像呈现时速度比中央处理单元 (Cpu) 的速度更快。 有关 Azure Stack Edge Pro 设备中包含的 GPU 的详细信息，请参阅 [Azure Stack Edge pro 设备技术规范](azure-stack-edge-gpu-technical-specifications-compliance.md)。

本文介绍如何在 Azure Stack Edge Pro 设备上配置和运行 GPU 上的模块。 在本文中，你将使用为 Nvidia T4 Gpu 编写的公开可用容器模块 **位数** 。 此过程可用于配置 Nvidia 为这些 Gpu 发布的任何其他模块。


## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

1. 你可以访问已启用 GPU 的1节点 Azure Stack Edge Pro 设备。 此设备已使用 Azure 中的资源激活。  

## <a name="configure-module-to-use-gpu"></a>配置模块以使用 GPU

若要将模块配置为使用 Azure Stack Edge Pro 设备上的 GPU 来运行模块，<!--Can it be simplified? "To configure a module to be run by the GPU on your Azure Stack Edge Pro device,"?--> 请按照以下步骤操作。

1. 在 Azure 门户中，请参阅与设备关联的资源。

2. 在 " **概述**" 中，选择 **IoT Edge**。

    ![配置模块以使用 GPU 1](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-1.png)

3. 在 " **启用 IoT Edge 服务**" 中，选择 " **添加**"。

   ![配置模块以使用 GPU 2](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-2.png)

4. 在 " **创建 IoT Edge 服务**" 中，输入 IoT 中心资源的设置：

   |字段   |值    |
   |--------|---------|
   |订阅      | Azure Stack Edge 资源使用的订阅。 |
   |资源组    | Azure Stack Edge 资源使用的资源组。 |
   |IoT 中心           | 选择 " **新建** " 或 " **使用现有**"。 <br> 默认会使用标准层 (S1) 来创建 IoT 资源。 若要使用免费层 IoT 资源，请创建一个资源，然后选择现有的资源。 <br> 在每种情况下，IoT 中心资源都会使用 Azure Stack Edge 资源所用的同一订阅和资源组。     |
   |名称              | 如果不想使用为新的 IoT 中心资源提供的默认名称，请输入其他名称。 |

   完成设置后，请选择 " **查看 + 创建**"。 查看 IoT 中心资源的设置，并选择 " **创建**"。

   ![开始使用计算 2](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-3.png)

   IoT 中心资源的资源创建花费几分钟时间。 创建资源后， **概述** 表明 IoT Edge 服务现在正在运行。

   ![开始使用计算 3](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-4.png)

5. 若要确认已配置边缘计算角色，请选择 " **属性**"。

   ![开始使用计算 4](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-5.png)

6. 在 " **属性**" 中，选择 **IoT Edge 设备** 的链接。

   ![配置模块以使用 GPU 6](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-2.png)

   在右侧窗格中，可以看到与 Azure Stack Edge Pro 设备关联的 IoT Edge 设备。 此设备对应于创建 IoT 中心资源时创建的 IoT Edge 设备。
 
7. 选择此 IoT Edge 设备。

   ![配置模块以使用 GPU 7](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-3.png)

8. 选择“设置模块”。

   ![配置模块以使用 GPU 8](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-4.png)

9. 选择 " **+ 添加** "，然后选择 " **+ IoT Edge 模块**"。 

    ![配置模块以使用 GPU 9](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-5.png)

10. 在 " **添加 IoT Edge 模块** " 选项卡中：

    1. 提供 **映像 URI**。 你将在此处使用公开提供的 Nvidia 模块 **数字** 。 
    
    2. 将 **重启策略** 设置为 " **始终**"。
    
    3. 将 **所需状态** 设置为 " **正在运行**"。
    
    ![配置模块以使用 GPU 10](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-6.png)

11. 在 " **环境变量** " 选项卡中，提供变量的名称和相应的值。 

    1. 要使当前模块在此设备上使用一个 GPU，请使用 NVIDIA_VISIBLE_DEVICES。 

    2. 将该值设置为 0 或 1。 值0或1，可确保设备至少使用一个 GPU 作为此模块。 如果将此值设置为0，则表示此模块正在使用设备上的两个 Gpu。

       ![配置模块以使用 GPU 11](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-7.png)

       有关可与 Nvidia GPU 一起使用的环境变量的详细信息，请参阅 [nvidia 容器运行时](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec)。

    > [!NOTE]
    > GPU 只能映射到一个模块。 但是，模块可以使用其中一个，无论是不使用 Gpu。

12. 输入模块的名称。 此时，你可以选择提供容器 create 选项和修改模块克隆设置，或者，如果已完成，请选择 " **添加**"。 

    ![配置模块以使用 GPU 12](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-8.png)

13. 确保该模块正在运行，然后选择 " **查看 + 创建**"。

    ![配置模块以使用 GPU 13](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-9.png)

14. 在 " **查看** " 和 "创建" 选项卡中，将显示所选的部署选项。 查看选项，然后选择 " **创建**"。
    
    ![配置模块以使用 GPU 14](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-10.png)

15. 记下模块的 **运行时状态** 。
    
    ![配置模块以使用 GPU 15](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-11.png)

    部署模块需要几分钟时间。 选择 " **刷新** "，应会看到运行 **时状态** 更新为 " **正在运行**"。

    ![配置模块以使用 GPU 16](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-12.png)


## <a name="next-steps"></a>后续步骤

- 详细了解 [可与 NVIDIA GPU 一起使用的环境变量](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec)。
