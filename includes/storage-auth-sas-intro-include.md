---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/20/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: aec1faa4de1149f08fb6fbc1cc5bf3aa2ab6becd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "75371764"
---
使用共享访问签名 (SAS)，可以授予对存储帐户中容器和 blob 的有限访问权限。 创建 SAS 时，需要指定其约束条件，包括允许客户端访问哪些 Azure 存储资源、它们对这些资源具有哪些权限，以及 SAS 的有效期。

每个 SAS 均使用密钥进行签名。 可通过以下两种方式之一对 SAS 进行签名：

- 使用 Azure Active Directory (Azure AD) 凭据创建的密钥。 使用 Azure AD 凭据签名的 SAS 是用户委托 SAS。
- 使用存储帐户密钥。 服务 SAS 和帐户 SAS 均使用存储帐户密钥进行签名。  

用户委托 SAS 为使用存储帐户密钥签名的 SAS 提供更高的安全性。 Microsoft 建议尽可能使用用户委托 SAS。 有关详细信息，请参阅[向具有共享访问签名的数据授予有限的访问权限 (SAS)](../articles/storage/common/storage-sas-overview.md)。
