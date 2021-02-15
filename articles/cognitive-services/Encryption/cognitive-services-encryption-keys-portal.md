---
title: 认知服务 Customer-Managed 密钥
titleSuffix: Cognitive Services
description: 了解如何使用 Azure 门户通过 Azure Key Vault 来配置客户管理的密钥。 使用客户管理的密钥可以创建、轮换、禁用和撤销访问控制。
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: fca3fc80411432f2e82a68faa3b6f297b8ca5f9c
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521996"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-for-cognitive-services"></a>为认知服务配置客户管理的密钥 Azure Key Vault

启用用于认知服务 Azure Key Vault Customer-Managed 密钥的过程因产品而异。 使用以下链接了解特定于服务的说明：

## <a name="vision"></a>影像

* [静态数据加密自定义视觉](../Custom-Vision-Service/encrypt-data-at-rest.md)
* [静态数据的人脸服务加密](../Face/encrypt-data-at-rest.md)
* [格式识别静态数据的加密](../form-recognizer/encrypt-data-at-rest.md)

## <a name="language"></a>语言

* [语言理解服务静态数据加密](../LUIS/encrypt-data-at-rest.md)
* [静态数据加密 QnA Maker](../QnAMaker/encrypt-data-at-rest.md)
* [翻译器的静态数据加密](../translator/encrypt-data-at-rest.md)

## <a name="decision"></a>决策

* [静态数据加密内容审查器](../Content-Moderator/encrypt-data-at-rest.md)
* [静态数据加密 Personalizer](../personalizer/encrypt-data-at-rest.md)

## <a name="next-steps"></a>后续步骤

* [什么是 Azure Key Vault？](../../key-vault/general/overview.md)
* [认知服务客户管理的密钥请求表单](https://aka.ms/cogsvc-cmk)