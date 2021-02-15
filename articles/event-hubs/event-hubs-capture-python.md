---
title: 从 Python 应用读取 Azure 事件中心捕获的数据（最新版）
description: 本文介绍如何编写 Python 代码，以捕获发送到事件中心的数据，并从 Azure 存储帐户读取捕获的事件数据。
ms.topic: quickstart
ms.date: 01/04/2021
ms.openlocfilehash: acc2ce04add5fd837e9edc789e9616a9f04fb4b9
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883191"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-by-using-python-azure-eventhub"></a>使用 Python 在 Azure 存储中捕获事件中心数据并读取该数据 (azure-eventhub)

可以配置事件中心，以便在 Azure 存储帐户或 Azure Data Lake Storage Gen 1 或 Gen 2 中捕获发送到事件中心的数据。 本文介绍如何编写 Python 代码，以将事件发送到事件中心，并从 **Azure Blob 存储** 读取捕获的数据。 有关此功能的详细信息，请参阅[事件中心捕获功能概述](event-hubs-capture-overview.md)。

本快速入门使用 [Azure Python SDK](https://azure.microsoft.com/develop/python/) 来演示捕获功能。 *sender.py* 以 JSON 格式将模拟的环境遥测数据发送到事件中心。 事件中心配置为使用捕获功能将此数据成批地写入到 Blob 存储。 *capturereader.py* 应用读取这些 Blob，并为每个设备创建一个追加文件。 然后该应用将数据写入 CSV 文件。

在本快速入门中，请执行以下操作： 

> [!div class="checklist"]
> * 在 Azure 门户中创建 Azure Blob 存储帐户和容器。
> * 使用 Azure 门户创建事件中心命名空间。
> * 创建已启用捕获功能的事件中心，并将其连接到存储帐户。
> * 使用 Python 脚本将数据发送到事件中心。
> * 使用另一个 Python 脚本通过事件中心捕获功能读取并处理文件。

## <a name="prerequisites"></a>先决条件

- 安装有 PIP 和以下包的 Python。 本文中的代码已针对这些版本进行测试。 
    - Python 3.7
    - azure-eventhub 5.2.0
    - azure-storage-blob 12.6.0
    - avro-python3 1.10.1
- Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/)。  
- 有效的事件中心命名空间和事件中心。
[创建事件中心命名空间，并在该命名空间中创建事件中心](event-hubs-create.md)。 请记下事件中心命名空间的名称、事件中心的名称，以及命名空间的主访问密钥。 若要获取访问密钥，请参阅[获取事件中心连接字符串](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 默认密钥名称为 *RootManageSharedAccessKey*。 对于本快速入门，只需获取主密钥。 不需要连接字符串。  
- Azure 存储帐户、存储帐户中的 Blob 容器，以及存储帐户的连接字符串。 如果没有这些项，请执行以下操作：  
    1. [创建 Azure 存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal)  
    1. [在存储帐户中创建 Blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
    1. [获取存储帐户的连接字符串](../storage/common/storage-configure-connection-string.md)

    请务必记下连接字符串和容器名称，因为稍后在本快速入门中需要用到。  
- 为事件中心启用捕获功能。 为此，请按照[使用 Azure 门户启用事件中心捕获](event-hubs-capture-enable-through-portal.md)中的说明操作。 选择在前一步骤中创建的存储帐户和 Blob 容器。 也可以在创建事件中心时启用该功能。  

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>创建用于将事件发送到事件中心的 Python 脚本
在本部分，你将创建一个向事件中心发送 200 个事件（10 个设备 * 20 个事件）的 Python 脚本。 这些事件是以 JSON 格式发送的示例环境读数。 

1. 打开常用的 Python 编辑器，如 [Visual Studio Code][Visual Studio Code]。
2. 创建名为 *sender.py* 的脚本。 
3. 将以下代码粘贴到 *sender.py* 中。 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # This script simulates the production of events for 10 devices.
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # Create a producer client to produce and publish events to the event hub.
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # For each device, produce 20 events. 
        event_data_batch = producer.create_batch() # Create a batch. You will add events to the batch later. 
        for dev in devices:
            # Create a dummy reading.
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # Convert the reading into a JSON string.
            event_data_batch.add(EventData(s)) # Add event data to the batch.
        producer.send_batch(event_data_batch) # Send the batch of events to the event hub.
    
    # Close the producer.    
    producer.close()
    ```
4. 替换脚本中的以下值：  
    * 将 `EVENT HUBS NAMESPACE CONNECTION STRING` 替换为事件中心命名空间的连接字符串。  
    * 将 `EVENT HUB NAME` 替换为事件中心的名称。  
5. 运行脚本以将事件发送到事件中心。  
6. 在 Azure 门户中，可以验证事件中心是否已收到消息。 在“指标”部分切换到“消息”视图。 刷新页面以更新图表。 可能需要等待几秒，页面才会显示已收到消息。 

    [![验证事件中心是否已收到消息](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>创建用于读取捕获文件的 Python 脚本
在此示例中，捕获的数据存储在 Azure Blob 存储中。 本部分中的脚本从 Azure 存储帐户读取捕获的数据文件，并生成可让你轻松打开和查看的 CSV 文件。 将在应用程序的当前工作目录中看到 10 个文件。 这些文件包含 10 台设备的环境读数。 

1. 在 Python 编辑器中，创建名为 *capturereader.py* 的脚本。 此脚本读取捕获的文件，并为每个设备创建一个文件，用于仅写入该设备的数据。
2. 将以下代码粘贴到 *capturereader.py* 中。 
   
    ```python
    import os
    import string
    import json
    import uuid
    import avro.schema
    
    from azure.storage.blob import ContainerClient, BlobClient
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    
    
    def processBlob2(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed_json = json.loads(reading["Body"])
            if not 'id' in parsed_json:
                return
            if not parsed_json['id'] in dict:
                list = []
                dict[parsed_json['id']] = list
            else:
                list = dict[parsed_json['id']]
                list.append(parsed_json)
        reader.close()
        for device in dict.keys():
            filename = os.getcwd() + '\\' + str(device) + '.csv'
            deviceFile = open(filename, "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
    
    def startProcessing():
        print('Processor started using path: ' + os.getcwd())
        # Create a blob container client.
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # List all the blobs in the container.
        for blob in blob_list:
            # Content_length == 508 is an empty file, so process only content_length > 508 (skip empty files).        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # Create a blob client for the blob.
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # Construct a file name based on the blob name.
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # Open the file to write. Create it if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # Write blob contents into the file.
                processBlob2(cleanName) # Convert the file into a CSV file.
                os.remove(cleanName) # Remove the original downloaded file.
                # Delete the blob from the container after it's read.
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
3. 请将 `AZURE STORAGE CONNECTION STRING` 替换为 Azure 存储帐户的连接字符串。 在本快速入门中创建的容器的名称为 *capture*。 如果为该容器使用了不同的名称，请将 *capture* 替换为存储帐户中容器的名称。 

## <a name="run-the-scripts"></a>运行脚本
1. 打开其路径中包含 Python 的命令提示符，并运行以下命令，安装 Python 必备组件包：
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```

    > [!NOTE]
    > 本文中的代码已针对这些版本进行测试。 
    > - Python 3.7
    > - azure-eventhub 5.2.0
    > - azure-storage-blob 12.6.0
    > - avro-python3 1.10.1
2. 将目录更改为保存 *sender.py* 和 *capturereader.py* 的目录，然后运行以下命令：
   
   ```
   python sender.py
   ```
   
   此命令将启动一个新的 Python 进程，用于运行发送程序。
3. 等待几分钟让捕获运行，然后在原始命令窗口中输入以下命令：
   
   ```
   python capturereader.py
   ```

   此捕获处理器使用本地目录下载存储帐户和容器中的所有 Blob。 它将处理任何不为空的内容，并将结果以 CSV 文件的形式写入到本地目录。

## <a name="next-steps"></a>后续步骤
查看 [GitHub 上的 Python 示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)。 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: ./event-hubs-about.md
