---
title: 准备 Azure 容器技术资产
description: 帮助你在 Azure 市场上配置容器产品/服务的技术资源和指南。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 014bcd6fc519c267cdf17e9e98b850425c25ead6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459333"
---
# <a name="prepare-your-azure-container-technical-assets"></a>准备 Azure 容器技术资产

本文提供技术资源和建议，以帮助你在 Azure 市场上创建容器产品/服务。

## <a name="before-you-begin"></a>开始之前

有关快速入门、教程和示例的信息，请参阅 [Azure 容器实例文档](../container-instances/index.yml)。

## <a name="fundamental-technical-knowledge"></a>基础技术知识

设计、生成和测试这些资产需要一些时间，并在 Azure 平台和用于生成套餐的技术方面有一定的专业知识。

除了解决方案领域以外，工程团队还应该了解以下 Microsoft 技术：

- 基本了解 [Azure 服务](https://azure.microsoft.com/services/)
- 如何[设计和架构 Azure 应用程序](https://azure.microsoft.com/solutions/architecture/)
- [Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)、[Azure 存储](https://azure.microsoft.com/services/?filter=storage)和 [Azure 网络](https://azure.microsoft.com/services/?filter=networking)的实践知识
- [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)的实践知识
- [JSON](https://www.json.org/) 的实践知识。

## <a name="suggested-tools"></a>建议的工具

选择以下一种或两种脚本环境来帮助管理容器映像：

- [Azure PowerShell](/powershell/azure/)
- [Azure CLI](/cli/azure/)。

我们建议将以下工具添加到开发环境：

- [Azure 存储浏览器](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - 扩展：[Azure 资源管理器工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - 扩展：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - 扩展：[Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)。

查看 [Azure 开发人员工具](https://azure.microsoft.com/)页中的可用工具。 如果使用的是 Visual Studio，请查看 [Visual Studio Marketplace](https://marketplace.visualstudio.com/) 中可用的工具。

## <a name="create-the-container-image"></a>创建容器映像

不能将映像从本地注册表部署到 Azure 容器实例。

- 如果本地注册表中已有工作容器，请创建 Azure 注册表，并将容器映像上传到 Azure 容器注册表。 若要了解详细信息，请参阅 [教程：通过 Azure 容器注册表任务在云中构建和部署容器映像](../container-registry/container-registry-tutorial-quick-task.md)。

- 如果尚无容器映像，并且需要容器化现有应用程序或创建新的基于容器的应用程序，请克隆 GitHub 中的应用程序源代码，从应用程序源创建容器映像，并在本地 Docker 环境中测试映像。 若要了解详细信息，请参阅 [教程：创建用于部署到 Azure 容器实例的容器映像](../container-instances/container-instances-tutorial-prepare-app.md)。

## <a name="next-steps"></a>后续步骤

- [创建容器产品/服务](create-azure-container-offer.md)