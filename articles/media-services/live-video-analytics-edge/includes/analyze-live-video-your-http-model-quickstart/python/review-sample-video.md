---
ms.openlocfilehash: 3c0d6dbba6ecda7ef87e4978d522da27cb6f9833
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2021
ms.locfileid: "98061163"
---
设置 Azure 资源时，一个高速公路车流量短视频被复制到 Azure 中用作 IoT Edge 设备的 Linux VM 上。 此快速入门使用视频文件来模拟实时流。

打开一个应用程序，例如 [VLC 媒体播放器](https://www.videolan.org/vlc/)。 选择“Ctrl+N”，然后粘贴[视频](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv)的链接以开始播放。 可以看到许多车辆在高速公路上行驶的镜头。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

在本快速入门中，你在 IoT Edge 上使用实时视频分析来检测车辆和人员等对象。 将关联的推理事件发布到 IoT Edge 中心。
