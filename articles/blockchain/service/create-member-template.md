---
title: 使用 Azure 资源管理器模板创建 Azure 区块链服务成员
description: 了解如何使用 Azure 资源管理器模板创建 Azure 区块链服务成员。
services: azure-resource-manager
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs, references_regions
ms.date: 09/16/2020
ms.openlocfilehash: e9893336f2e6633519853aceecc945ee6bf0bf4b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91292755"
---
# <a name="quickstart-create-an-azure-blockchain-service-member-using-an-arm-template"></a>快速入门：使用 ARM 模板创建 Azure 区块链服务成员

本快速入门将使用 Azure 资源管理器模板（ARM 模板）在 Azure 区块链服务中部署新的区块链成员和联盟。 Azure 区块链服务成员是专用联盟区块链网络中的区块链节点。 预配成员时，可以创建或加入联盟网络。 至少需要一个联盟网络的成员。 参与者所需的区块链成员数取决于方案。 联盟参与者可能有一个或多个区块链成员，或者与其他参与者共享成员。 有关联盟的详细信息，请参阅 [Azure 区块链服务联盟](consortium.md)。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/201-blockchain-asaservice/)。

:::code language="json" source="~/quickstart-templates/201-blockchain-asaservice/azuredeploy.json":::

该模板中定义的 Azure 资源：

* [Microsoft.Blockchain/blockchainMembers](/azure/templates/microsoft.blockchain/blockchainmembers)

## <a name="deploy-the-template"></a>部署模板

1. 选择以下链接登录到 Azure 并打开一个模板。

    [![部署到 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

1. 指定 Azure 区块链服务成员的设置。

    设置 | 说明
    --------|------------
    订阅 | 选择要用于你的服务的 Azure 订阅。 如果有多个订阅，请选择要计费的资源所在的订阅。
    资源组 | 可以创建新的资源组名称，也可以选择订阅中现有的资源组名称。
    区域 | 选择区域以创建资源组。 联盟中所有成员的位置必须相同。 可用于部署的位置有 westeurope、eastus、southeastasia、westeurope、northeurope、westus2 和 japaneast。 功能在某些地区可能不可用。 Azure 区块链数据管理器在以下 Azure 区域提供：“美国东部”和“西欧”。
    Bc 成员名称 | 为该 Azure 区块链服务成员选择一个唯一名称。 区块链成员名称只能包含小写字母和数字。 第一个字符必须是字母。 值长度必须介于 2 到 20 个字符之间。
    联盟名称 | 输入唯一名称。 有关联盟的详细信息，请参阅 [Azure 区块链服务联盟](consortium.md)。
    成员密码 | 成员的默认事务节点的密码。 连接到区块链成员的默认事务节点公共终结点时，请使用密码进行基本身份验证。
    联盟管理帐户密码 | 联盟帐户密码用于加密为成员创建的 Ethereum 帐户的私钥。 它用于联盟管理。
    SKU 层级 | 你的新服务的定价层级。 在“标准”层级和“基本”层级之间进行选择 。 使用“基本”层进行开发、测试和概念证明。 使用“标准”层进行生产级部署。 如果使用区块链数据管理器或发送大量专用事务，也使用标准层。 不支持在创建成员后在“基本”定价层和“标准”定价层之间进行切换。
    SKU 名称 | 新服务的节点配置和成本。 对于基本层，使用 B0；对于标准层，使用 S0。
    位置 | 选择要创建成员的位置。 默认情况下，使用资源组位置 `[resourceGroup().location]`。 联盟中所有成员的位置必须相同。 可用于部署的位置有 westeurope、eastus、southeastasia、westeurope、northeurope、westus2 和 japaneast。 功能在某些地区可能不可用。 Azure 区块链数据管理器在以下 Azure 区域提供：“美国东部”和“西欧”。

1. 选择“查看 + 创建”以验证和部署模板。

  此处使用 Azure 门户来部署模板。 还可以使用 Azure PowerShell、Azure CLI 和 REST API。 若要了解其他部署方法，请参阅[部署模板](../../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="review-deployed-resources"></a>查看已部署的资源

你可以使用 Azure 门户查看已部署的 Azure 区块链服务成员的详细信息。 在门户中，前往包含 Azure 区块链服务成员的资源组。 选择创建的区块链成员。

![Azure 门户中的已部署 Azure 区块链成员概述详细信息](./media/create-member-template/deployed-member.png)

## <a name="clean-up-resources"></a>清理资源

你可以将所创建的区块链成员用于下一快速入门或教程。 不再需要这些资源时，可以通过删除为本快速入门创建的资源组来删除这些资源。

若要删除资源组，请执行以下操作：

1. 在 Azure 门户中，导航至左侧导航窗格中的“资源组”，然后选择要删除的资源组。
2. 选择“删除资源组”。 输入资源组名称确认删除并选择“删除”。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已部署了一个 Azure 区块链服务成员和一个新的联盟。 请尝试学习下一个快速入门，使用适用于 Ethereum 的 Azure 区块链开发工具包将内容附加到 Azure 区块链服务成员。

> [!div class="nextstepaction"]
> [使用 Visual Studio Code 连接到 Azure 区块链服务](connect-vscode.md)