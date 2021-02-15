---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9f1c30b303bd1fe02e0685c7d848be92073ca2f6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016857"
---
#### <a name="to-add-a-storage-account-credential-in-the-same-azure-subscription-as-the-storsimple-device-manager-service"></a>在 StorSimple Device Manager 服务所在的同一 Azure 订阅中添加存储帐户凭据

1. 转到 StorSimple Device Manager 服务。 在“配置”部分中，单击“存储帐户凭据”。

    ![请参阅存储帐户凭据](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct1.png)

2. 在“存储帐户凭据”边栏选项卡上，单击“+ 添加”。

    ![添加存储帐户凭据](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct2.png)

3. 在“添加存储帐户凭据”边栏选项卡中，执行以下步骤：

    1. 在服务所在的同一 Azure 订阅中添加存储帐户凭据时，请确保选中“当前”。

    2. 从“存储帐户”下拉列表中，选择一个现有存储帐户。

    3. 将根据所选的存储帐户显示 **位置**（灰显并且在此处无法更改）。

    4. 选择 " **启用 SSL 模式** "，为你的设备和云之间的网络通信创建一个安全通道。 如果仅在私有云中操作，请禁用“启用 SSL”。

        ![“添加存储帐户凭据”边栏选项卡](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct3.png)

    5. 单击“添加”为存储帐户凭据启动作业创建。 成功创建存储帐户凭据后，将收到通知。

        ![存储帐户凭据的成功通知](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct5.png)

新创建的存储帐户凭据会显示在“存储帐户凭据”列表下。

![列出存储帐户凭据](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct6.png)

