---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 09aa5affefc576606984ea7116b3d9bda4ba83d5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000533"
---
### <a name="run-multiple-containers-on-the-same-host"></a>在同一主机上运行多个容器

若要使用公开端口运行多个容器，请确保在运行每个容器时使用不同的公开端口。 例如，在端口 5000 上运行第一个容器，在端口 5001 上运行第二个容器。

可以让此容器和其他 Azure 认知服务容器一起运行在该主机上。 此外，还可以让同一认知服务容器的多个容器一起运行。
