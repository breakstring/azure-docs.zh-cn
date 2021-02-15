---
title: 使用 Azure 门户配置文件上传 | Microsoft Docs
description: 如何使用 Azure 门户配置 IoT 中心，以便从连接的设备上传文件。 包括有关配置目标 Azure 存储帐户的信息。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: robinsh
ms.openlocfilehash: da28bfa31c74ff33a200967267500033dd6a9b1b
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2020
ms.locfileid: "92535869"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>使用 Azure 门户配置 IoT 中心文件上传

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>文件上传

若要使用 [IoT 中心的文件上传功能](iot-hub-devguide-file-upload.md)，必须先将 Azure 存储帐户与中心关联。 选择“文件上传”，以显示正在修改的 IoT 中心的文件上传属性列表。 

![在门户中查看 IoT 中心文件上传设置](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **存储容器** ：使用 Azure 门户在当前 Azure 订阅中选择 Azure 存储帐户中的 blob 容器，以便与 IoT 中心关联。 如有必要，可以在“存储帐户”  边栏选项卡上创建 Azure 存储帐户，并在“容器”  边栏选项卡上创建 blob 容器。 IoT 中心会自动生成对此 Blob 容器具有写入权限的 SAS URI，以供设备上传文件时使用。

   ![在门户中查看用于文件上传的存储容器](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **接收已上传文件的通知** ：通过切换来启用或禁用文件上传通知。

* **SAS TTL** ：此设置是 IoT 中心返回给设备的 SAS URI 生存时间。 默认设置为一小时，但可以使用滑块自定义为其他值。

* **文件通知设置默认 TTL** ：文件上传通知到期前的生存时间。 默认设置为一天，但可以使用滑块自定义为其他值。

* **文件通知最大传送数** ：IoT 中心将尝试传送文件上传通知的次数。 默认设置为 10，但可以使用滑块自定义为其他值。

   ![在门户中配置 IoT 中心文件上传](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>后续步骤

有关 IoT 中心的文件上传功能的详细信息，请参阅 IoT 中心开发人员指南中的[从设备上传文件](iot-hub-devguide-file-upload.md)。

若要了解有关如何管理 Azure IoT 中心的详细信息，请参阅以下链接：

* [批量管理 IoT 设备](iot-hub-bulk-identity-mgmt.md)
* [监视 IoT 中心](monitor-iot-hub.md)

若要进一步探索 IoT 中心的功能，请参阅：

* [IoT 中心开发人员指南](iot-hub-devguide.md)
* [使用 Azure IoT Edge 将 AI 部署到边缘设备](../iot-edge/quickstart-linux.md)
* [从根本上保护 IoT 解决方案](../iot-fundamentals/iot-security-ground-up.md)