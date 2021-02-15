---
title: PowerShell 示例 - 列出所有应用程序代理连接器组
description: PowerShell 示例，其中列出了你目录中的所有 Azure Active Directory (Azure AD) 应用程序代理连接器组和连接器。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: f689cfd2cb2ba95bae2a2727f5b32279745eafd5
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99253232"
---
# <a name="get-all-connector-groups-and-connectors-in-the-directory"></a>获取目录中的所有连接器组和连接器

在该 PowerShell 脚本示例中，可找到你目录中的所有 Azure Active Directory (Azure AD) 应用程序代理连接器组和连接器。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

此示例需要[适用于 Graph 的 AzureAD V2 PowerShell 模块](/powershell/azure/active-directory/install-adv2) (AzureAD) 或[适用于 Graph 的 AzureAD V2 PowerShell 模块预览版](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview)。

## <a name="sample-script"></a>示例脚本

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-connectors.ps1 "Get all connector groups and connectors in the directory")]

## <a name="script-explanation"></a>脚本说明

| Command | 说明 |
|---|---|
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | 检索包含所有连接器组的列表，或检索指定的连接器组的详细信息（若指定）。 |
| [Get-AzureADApplicationProxyConnectorGroupMembers](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroupmembers) | 获取与每个连接器组关联的所有应用程序代理连接器。|

## <a name="next-steps"></a>后续步骤

要详细了解 Azure AD PowerShell 模块，请参阅 [Azure AD PowerShell 模块概述](/powershell/azure/active-directory/overview)。

有关应用程序代理的其他 PowerShell 示例，请参阅 [Azure AD 应用程序代理的 Azure AD PowerShell 示例](../application-proxy-powershell-samples.md)。
