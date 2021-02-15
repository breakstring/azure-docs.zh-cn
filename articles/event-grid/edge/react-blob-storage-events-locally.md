---
title: 响应 Blob 存储模块事件-Azure 事件网格 IoT Edge |Microsoft Docs
description: 响应 Blob 存储模块事件
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 230e158a970f8c815b1575403c013e30749124c5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005055"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>教程：在 IoT Edge (Preview 上对 Blob 存储事件做出反应) 
本文介绍如何在 IoT 模块上部署 Azure Blob 存储，该模块将充当事件网格发布者，以将 Blob 创建和 Blob 删除事件发送到事件网格。  

有关 IoT Edge 上的 Azure Blob 存储的概述，请参阅 [IoT Edge 及其功能上的 Azure Blob 存储](../../iot-edge/how-to-store-data-blob.md) 。

> [!WARNING]
> IoT Edge 与事件网格的集成中的 Azure Blob 存储处于预览阶段

若要完成本教程，您需要：

* **Azure 订阅** -如果你还没有帐户，请创建一个 [免费帐户](https://azure.microsoft.com/free) 。 
* **Azure IoT 中心和 IoT Edge 设备** -按照适用于 [Linux](../../iot-edge/quickstart-linux.md) 或 [Windows 设备](../../iot-edge/quickstart.md) 的快速入门中的步骤（如果尚未安装）。

## <a name="deploy-event-grid-iot-edge-module"></a>部署事件网格 IoT Edge 模块

可以通过多种方式将模块部署到 IoT Edge 设备，并且所有这些模块都适用于 IoT Edge 上的 Azure 事件网格。 本文介绍了在 Azure 门户中 IoT Edge 部署事件网格的步骤。

>[!NOTE]
> 在本教程中，您将部署不带持久性的事件网格模块。 这意味着，在您重新部署该模块时，将删除您在本教程中创建的任何主题和订阅。 有关如何设置持久性的详细信息，请参阅以下文章： [在 Linux 中持久保存状态](persist-state-linux.md) 或 [在 Windows 中保持状态](persist-state-windows.md)。 对于生产工作负荷，我们建议您安装带有持久性的事件网格模块。


### <a name="select-your-iot-edge-device"></a>选择 IoT Edge 设备

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 导航到 IoT 中心。
1. 从 "**自动设备管理**" 部分的菜单中选择 " **IoT Edge** "。 
1. 从设备列表中单击目标设备的 ID
1. 选择“设置模块”  。 使页面保持打开状态。 你将继续执行下一节中的步骤。

### <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，其中描述了要部署的模块、数据在模块间的流动方式以及模块孪生的所需属性。 Azure 门户提供部署清单的创建向导，无需你手动构建 JSON 文档。  它分为三步：添加模块、指定路由和评审部署  。

### <a name="add-modules"></a>添加模块

1. 在 "**部署模块**" 部分，选择 "**添加**"
1. 从下拉列表中的模块类型中，选择 " **IoT Edge 模块**
1. 提供容器的名称、图像、容器创建选项：

   * **名称**： eventgridmodule
   * **映像 URI**：`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **容器创建选项**：

    ```json
        {
          "Env": [
           "inbound__serverAuth__tlsPolicy=enabled",
           "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    

 1. 单击“保存” 
 1. 在将 Azure 事件网格订户模块一起部署之前，请继续阅读下一节。

    >[!IMPORTANT]
    > 在本教程中，您将学习如何部署事件网格模块，以允许 HTTP/HTTPs 请求和禁用客户端身份验证。 对于生产工作负荷，建议仅启用启用了客户端身份验证的 HTTPs 请求和订阅服务器。 有关如何安全配置事件网格模块的详细信息，请参阅 [安全性和身份验证](security-authentication.md)。
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>部署事件网格订阅服务器 IoT Edge 模块

本部分介绍如何部署另一个 IoT 模块，该模块将充当可传递事件的事件处理程序。

### <a name="add-modules"></a>添加模块

1. 在 " **部署模块** " 部分，再次选择 " **添加** "。 
1. 从下拉列表中的模块类型中，选择 " **IoT Edge 模块**
1. 提供容器的 "名称"、"映像" 和 "容器" 创建选项：

   * **名称**：订阅服务器
   * **映像 URI**：`mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **容器创建选项**：无
1. 单击“保存” 
1. 转到下一节以添加 Azure Blob 存储模块

## <a name="deploy-azure-blob-storage-module"></a>部署 Azure Blob 存储模块

本部分介绍如何部署 Azure Blob 存储模块，该模块将充当事件网格发布程序发布 Blob 创建和删除的事件。

### <a name="add-modules"></a>添加模块

1. 在 "**部署模块**" 部分，选择 "**添加**"
2. 从下拉列表中的模块类型中，选择 " **IoT Edge 模块**
3. 提供容器的 "名称"、"映像" 和 "容器" 创建选项：

   * **名称**： azureblobstorageoniotedge
   * **映像 URI**： mcr.microsoft.com/azure-blob-storage:latest
   * **容器创建选项**：

   ```json
       {
         "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>",
           "EVENTGRID_ENDPOINT=http://<event grid module name>:5888"
         ],
         "HostConfig":{
           "Binds":[
               "<storage mount>"
           ],
           "PortBindings":{
             "11002/tcp":[{"HostPort":"11002"}]
           }
         }
       }
   ```

   > [!IMPORTANT]
   > - Blob 存储模块可以使用 HTTPS 和 HTTP 发布事件。 
   > - 如果已为 EventGrid 启用基于客户端的身份验证，请确保将 EVENTGRID_ENDPOINT 的值更新为允许使用 https，如下所示： `EVENTGRID_ENDPOINT=https://<event grid module name>:4438` 。
   > - 另外，将另一个环境变量添加 `AllowUnknownCertificateAuthority=true` 到上述 Json。 通过 HTTPS 与 EventGrid 通信时， **AllowUnknownCertificateAuthority** 允许存储模块信任自签名的 EventGrid 服务器证书。

4. 使用以下信息更新复制的 JSON：

   - 请将 `<your storage account name>` 替换为容易记住的名称。 帐户名长度应为 3 到 24 个字符，并带有小写字母和数字。 不含空格。

   - 将 `<your storage account key>` 替换为 64 字节 base64 密钥。 你可以使用 [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 等工具生成密钥。 你将使用这些凭据从其他模块访问 blob 存储。

   - 替换 `<event grid module name>` 为事件网格模块的名称。
   - 根据容器操作系统替换 `<storage mount>`。
     - 对于 Linux 容器， **我的容量：/blobroot**
     - 对于 Windows 容器，**我的卷： C：/BlobRoot**

5. 单击“保存” 
6. 单击 " **下一步** " 以继续转到 "路由" 部分

    > [!NOTE]
    > 如果使用 Azure VM 作为边缘设备，请添加入站端口规则，以允许在本教程中使用的主机端口上的入站流量：4438、5888、8080和11002。 有关添加规则的说明，请参阅 [如何打开 VM 的端口](../../virtual-machines/windows/nsg-quickstart-portal.md)。

### <a name="setup-routes"></a>安装路由

保留默认路由，然后选择 " **下一步** " 继续查看部分

### <a name="review-deployment"></a>评审部署

1. "查看" 部分显示基于你在上一节中所做的选择创建的 JSON 部署清单。 确认你看到以下四个模块： **$edgeAgent**、 **$edgeHub**、 **eventgridmodule**、 **订阅服务器** 和所有正在部署的 **azureblobstorageoniotedge** 。
2. 审阅部署信息，然后选择“提交”。

## <a name="verify-your-deployment"></a>验证部署

1. 提交部署后，返回到 IoT 中心的“IoT Edge”页。
2. 选择部署目标 **IoT Edge 设备** ，以打开其详细信息。
3. 在 "设备详细信息" 中，验证 "eventgridmodule"、"订阅服务器" 和 "azureblobstorageoniotedge" 模块是否作为 **"部署" 中指定** 的和 " **设备" 报告**。

   可能需要等待一段时间，该模块才会在设备上启动并向 IoT 中心发回报告。 刷新页面以查看更新的状态。

## <a name="publish-blobcreated-and-blobdeleted-events"></a>发布 BlobCreated 和 BlobDeleted 事件

1. 此模块自动创建主题 **MicrosoftStorage**。 验证它是否存在
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

    示例输出：

    ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage",
            "name": "MicrosoftStorage",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/MicrosoftStorage/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
    ```

    > [!IMPORTANT]
    > - 对于 HTTPS 流，如果通过 SAS 密钥启用了客户端身份验证，则应将前面指定的 SAS 密钥添加为标头。 因此，卷请求将为： `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - 对于 HTTPS 流，如果通过证书启用了客户端身份验证，则卷曲请求将为： `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. 订户可以注册发布到主题的事件。 若要接收任何事件，需要为 **MicrosoftStorage** 主题创建事件网格订阅。
    1. 创建具有以下内容的 blobsubscription.js。 有关有效负载的详细信息，请参阅我们的 [API 文档](api.md)

       ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       >[!NOTE]
       > **EndpointType** 属性指定订阅服务器是 **Webhook**。  **EndpointUrl** 指定订阅服务器侦听事件的 URL。 此 URL 对应于之前部署的 Azure Function 示例。

    2. 运行以下命令以创建主题的订阅。 确认显示 HTTP 状态代码为 `200 OK` 。

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - 对于 HTTPS 流，如果通过 SAS 密钥启用了客户端身份验证，则应将前面指定的 SAS 密钥添加为标头。 因此，卷请求将为： `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - 对于 HTTPS 流，如果通过证书启用了客户端身份验证，则卷曲请求将为：`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

    3. 运行以下命令以验证是否已成功创建订阅。 应返回 HTTP 状态代码 200 OK。

       ```sh
       curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       示例输出：

       ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription5",
          "properties": {
            "Topic": "MicrosoftStorage",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       > [!IMPORTANT]
       > - 对于 HTTPS 流，如果通过 SAS 密钥启用了客户端身份验证，则应将前面指定的 SAS 密钥添加为标头。 因此，卷请求将为： `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - 对于 HTTPS 流，如果通过证书启用了客户端身份验证，则卷曲请求将为： `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. 下载 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/) 并 [将其连接到本地存储](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>验证事件传递

### <a name="verify-blobcreated-event-delivery"></a>验证 BlobCreated 事件传递

1. 将文件作为块 blob 上传到 Azure 存储资源管理器的本地存储，该模块将自动发布创建事件。 
2. 查看创建事件的订阅服务器日志。 按照以下步骤 [验证事件传递](pub-sub-events-webhook-local.md#verify-event-delivery)

    示例输出：

    ```json
            Received Event:
            {
              "id": "d278f2aa-2558-41aa-816b-e6d8cc8fa140",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobCreated",
              "eventTime": "2019-10-01T21:35:17.7219554Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "PutBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "ef1c387b-4c3c-4ac0-8e04-ff73c859bfdc",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

### <a name="verify-blobdeleted-event-delivery"></a>验证 BlobDeleted 事件传递

1. 使用 Azure 存储资源管理器从本地存储中删除 blob，模块将自动发布删除事件。 
2. 查看订阅服务器日志以查找删除事件。 按照以下步骤 [验证事件传递](pub-sub-events-webhook-local.md#verify-event-delivery)

    示例输出：
    
    ```json
            Received Event:
            {
              "id": "ac669b6f-8b0a-41f3-a6be-812a3ce6ac6d",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobDeleted",
              "eventTime": "2019-10-01T21:36:09.2562941Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "DeleteBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "2996bbfb-c819-4d02-92b1-c468cc67d8c6",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

恭喜！ 您已经完成了本教程。 以下各节提供了有关事件属性的详细信息。

### <a name="event-properties"></a>事件属性

下面列出了受支持的事件属性及其类型和说明。 

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| 主题 | string | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| subject | string | 事件主题的发布者定义路径。 |
| eventType | string | 此事件源的一个注册事件类型。 |
| EventTime | string | 基于提供程序 UTC 时间的事件生成时间。 |
| id | 字符串 | 事件的唯一标识符。 |
| 数据 | object | Blob 存储事件数据。 |
| dataVersion | string | 数据对象的架构版本。 发布者定义架构版本。 |
| metadataVersion | string | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

数据对象具有以下属性：

| 属性 | 类型 | 说明 |
| -------- | ---- | ----------- |
| api | string | 触发事件的操作。 可以为下列值之一： <ul><li>BlobCreated-允许的值为： `PutBlob` 和 `PutBlockList`</li><li>BlobDeleted-允许的值为 `DeleteBlob` 、 `DeleteAfterUpload` 和 `AutoDelete` 。 <p>此 `DeleteAfterUpload` 事件是在自动删除 blob 时生成的，因为 deleteAfterUpload 所需的属性设置为 true。 </p><p>`AutoDelete` 当 blob 自动删除，因为 deleteAfterMinutes 所需的属性值过期，将生成事件。</p></li></ul>|
| ClientRequestId | 字符串 | 用于存储 API 操作的客户端提供的请求 ID。 此 ID 可用于在日志中使用 "客户端请求 id" 字段与 Azure 存储诊断日志关联，可以使用 "x-客户端请求 id" 标头在客户端请求中提供。 有关详细信息，请参阅 [日志格式](/rest/api/storageservices/storage-analytics-log-format)。 |
| requestId | 字符串 | 用于存储 API 操作的服务生成的请求 ID。 可用于通过 Azure 存储诊断日志中的“request-id-header”字段关联到这些日志，并且由“x-ms-request-id”标头中的初始化 API 调用返回。 请参阅[日志格式](/rest/api/storageservices/storage-analytics-log-format)。 |
| eTag | string | 可用于根据条件执行操作的值。 |
| contentType | string | 为 Blob 指定的内容类型。 |
| contentLength | integer | Blob 大小，以字节为单位。 |
| blobType | string | Blob 的类型。 有效值为“BlockBlob”或“PageBlob”。 |
| url | string | Blob 的路径。 <br>如果客户端使用 Blob REST API，则 url 将采用以下结构： *\<storage-account-name\> . blob.core.windows.net/ \<container-name\> / \<file-name\>*。 <br>如果客户端使用 Data Lake Storage REST API，则 url 将具有以下结构： *\<storage-account-name\> . dfs.core.windows.net/ \<file-system-name\> / \<file-name\>*。 |


## <a name="next-steps"></a>后续步骤

请参阅 Blob 存储文档中的以下文章：

- [筛选 Blob 存储事件](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [使用 Blob 存储事件的建议做法](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

在本教程中，已通过在 Azure Blob 存储中创建或删除 blob 来发布事件。 请参阅其他教程，了解如何将事件转发给云 (Azure 事件中心或 Azure IoT 中心) ： 

- [将事件转发到 Azure 事件网格](forward-events-event-grid-cloud.md)
- [将事件转发到 Azure IoT 中心](forward-events-iothub.md)
