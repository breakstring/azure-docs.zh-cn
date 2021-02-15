---
title: include 文件
description: include 文件
services: azure-netapp-files
author: b-juche
ms.service: azure-netapp-files
ms.topic: include
ms.date: 09/10/2019
ms.author: b-juche
ms.custom: include file
ms.openlocfilehash: e78be737ef8d191cddc2da0f738250ca04cae9a1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91672181"
---
1. 指定已批准用于 Azure NetApp 文件的订阅：
    
    ```azurecli-interactive
    az account set --subscription <subscriptionId>
    ```

1. 注册 Azure 资源提供程序： 
    
    ```azurecli-interactive
    az provider register --namespace Microsoft.NetApp --wait  
    ```