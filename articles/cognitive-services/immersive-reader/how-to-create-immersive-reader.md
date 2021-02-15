---
title: 创建沉浸式阅读器资源
titleSuffix: Azure Cognitive Services
description: 本文将介绍如何使用自定义子域创建新的沉浸式读者资源，然后在 Azure 租户中配置 Azure AD。
services: cognitive-services
author: rwaller
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: rwaller
ms.openlocfilehash: 2503355a24a7452ca1ff9886a80f2956897889c4
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630389"
---
# <a name="create-an-immersive-reader-resource-and-configure-azure-active-directory-authentication"></a>创建沉浸式读者资源并配置 Azure Active Directory 身份验证

本文提供了一个脚本，该脚本将创建一个沉浸式读者资源并配置 Azure Active Directory (Azure AD) 身份验证。 每次创建沉浸式读者资源时，无论是使用此脚本还是在门户中，都必须使用 Azure AD 权限进行配置。 此脚本将为你提供帮助。

此脚本旨在为你创建和配置所有必要的沉浸式读取器和 Azure AD 资源，只需一步即可实现。 不过，你也可以仅为现有沉浸式读者资源配置 Azure AD 身份验证，例如，在 Azure 门户中已创建了一个身份验证。

对于某些客户，可能需要创建多个沉浸式读者资源，以便进行开发与生产，或可能用于部署服务的多个不同区域。 对于这些情况，可以返回并多次使用该脚本来创建不同的沉浸式读者资源，并使用 Azure AD 的权限对其进行配置。

此脚本设计为灵活。 它首先查找订阅中的现有沉浸式读者和 Azure AD 资源，仅在必要时才根据需要创建这些资源。 如果是第一次创建沉浸式读者资源，则脚本会执行所需的一切。 如果只想使用它为门户中创建的现有沉浸式读者资源配置 Azure AD，它也会执行此操作。 它还可用于创建和配置多个沉浸式读者资源。

## <a name="set-up-powershell-environment"></a>设置 PowerShell 环境

1. 首先打开 [Azure Cloud Shell](../../cloud-shell/overview.md)。 请确保在左上角下拉列表中或键入时，将 Cloud Shell 设置为 PowerShell `pwsh` 。

1. 将以下代码片段复制并粘贴到 shell 中。

    ```azurepowershell-interactive
    function Create-ImmersiveReaderResource(
        [Parameter(Mandatory=$true, Position=0)] [String] $SubscriptionName,
        [Parameter(Mandatory=$true)] [String] $ResourceName,
        [Parameter(Mandatory=$true)] [String] $ResourceSubdomain,
        [Parameter(Mandatory=$true)] [String] $ResourceSKU,
        [Parameter(Mandatory=$true)] [String] $ResourceLocation,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupName,
        [Parameter(Mandatory=$true)] [String] $ResourceGroupLocation,
        [Parameter(Mandatory=$true)] [String] $AADAppDisplayName="ImmersiveReaderAAD",
        [Parameter(Mandatory=$true)] [String] $AADAppIdentifierUri,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecret,
        [Parameter(Mandatory=$true)] [String] $AADAppClientSecretExpiration
    )
    {
        $unused = ''
        if (-not [System.Uri]::TryCreate($AADAppIdentifierUri, [System.UriKind]::Absolute, [ref] $unused)) {
            throw "Error: AADAppIdentifierUri must be a valid URI"
        }

        Write-Host "Setting the active subscription to '$SubscriptionName'"
        $subscriptionExists = Get-AzSubscription -SubscriptionName $SubscriptionName
        if (-not $subscriptionExists) {
            throw "Error: Subscription does not exist"
        }
        az account set --subscription $SubscriptionName

        $resourceGroupExists = az group exists --name $ResourceGroupName
        if ($resourceGroupExists -eq "false") {
            Write-Host "Resource group does not exist. Creating resource group"
            $groupResult = az group create --name $ResourceGroupName --location $ResourceGroupLocation
            if (-not $groupResult) {
                throw "Error: Failed to create resource group"
            }
            Write-Host "Resource group created successfully"
        }

        # Create an Immersive Reader resource if it doesn't already exist
        $resourceId = az cognitiveservices account show --resource-group $ResourceGroupName --name $ResourceName --query "id" -o tsv
        if (-not $resourceId) {
            Write-Host "Creating the new Immersive Reader resource '$ResourceName' (SKU '$ResourceSKU') in '$ResourceLocation' with subdomain '$ResourceSubdomain'"
            $resourceId = az cognitiveservices account create `
                            --name $ResourceName `
                            --resource-group $ResourceGroupName `
                            --kind ImmersiveReader `
                            --sku $ResourceSKU `
                            --location $ResourceLocation `
                            --custom-domain $ResourceSubdomain `
                            --query "id" `
                            -o tsv

            if (-not $resourceId) {
                throw "Error: Failed to create Immersive Reader resource"
            }
            Write-Host "Immersive Reader resource created successfully"
        }

        # Create an Azure Active Directory app if it doesn't already exist
        $clientId = az ad app show --id $AADAppIdentifierUri --query "appId" -o tsv
        if (-not $clientId) {
            Write-Host "Creating new Azure Active Directory app"
            $clientId = az ad app create --password $AADAppClientSecret --end-date "$AADAppClientSecretExpiration" --display-name $AADAppDisplayName --identifier-uris $AADAppIdentifierUri --query "appId" -o tsv

            if (-not $clientId) {
                throw "Error: Failed to create Azure Active Directory app"
            }
            Write-Host "Azure Active Directory app created successfully."
            Write-Host "NOTE: To manage your Active Directory app client secrets after this Immersive Reader Resource has been created please visit https://portal.azure.com and go to Home -> Azure Active Directory -> App Registrations -> $AADAppDisplayName -> Certificates and Secrets blade -> Client Secrets section" -ForegroundColor Yellow
        }

        # Create a service principal if it doesn't already exist
        $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv
        if (-not $principalId) {
            Write-Host "Creating new service principal"
            az ad sp create --id $clientId | Out-Null
            $principalId = az ad sp show --id $AADAppIdentifierUri --query "objectId" -o tsv

            if (-not $principalId) {
                throw "Error: Failed to create new service principal"
            }
            Write-Host "New service principal created successfully"
        }

        # Sleep for 5 seconds to allow the new service principal to propagate
        Write-Host "Sleeping for 5 seconds"
        Start-Sleep -Seconds 5

        Write-Host "Granting service principal access to the newly created Immersive Reader resource"
        $accessResult = az role assignment create --assignee $principalId --scope $resourceId --role "Cognitive Services User"
        if (-not $accessResult) {
            throw "Error: Failed to grant service principal access"
        }
        Write-Host "Service principal access granted successfully"

        # Grab the tenant ID, which is needed when obtaining an Azure AD token
        $tenantId = az account show --query "tenantId" -o tsv

        # Collect the information needed to obtain an Azure AD token into one object
        $result = @{}
        $result.TenantId = $tenantId
        $result.ClientId = $clientId
        $result.ClientSecret = $AADAppClientSecret
        $result.Subdomain = $ResourceSubdomain

        Write-Host "Success! " -ForegroundColor Green -NoNewline
        Write-Host "Save the following JSON object to a text file for future reference:"
        Write-Output (ConvertTo-Json $result)
    }
    ```

1. 运行该函数 `Create-ImmersiveReaderResource` ，并根据需要向下面的 "<PARAMETER_VALUES>" 占位符提供自己的值。

    ```azurepowershell-interactive
    Create-ImmersiveReaderResource -SubscriptionName '<SUBSCRIPTION_NAME>' -ResourceName '<RESOURCE_NAME>' -ResourceSubdomain '<RESOURCE_SUBDOMAIN>' -ResourceSKU '<RESOURCE_SKU>' -ResourceLocation '<RESOURCE_LOCATION>' -ResourceGroupName '<RESOURCE_GROUP_NAME>' -ResourceGroupLocation '<RESOURCE_GROUP_LOCATION>' -AADAppDisplayName '<AAD_APP_DISPLAY_NAME>' -AADAppIdentifierUri '<AAD_APP_IDENTIFIER_URI>' -AADAppClientSecret '<AAD_APP_CLIENT_SECRET>' -AADAppClientSecretExpiration '<AAD_APP_CLIENT_SECRET_EXPIRATION>'
    ```

    完整的命令将如下所示。 在这里，我们将每个参数都置于自己的行上，以清楚地显示整个命令。 请勿按原样复制或使用此命令。 复制并将上面的命令与你自己的值一起使用。 此示例为上面的 "<PARAMETER_VALUES>" 提供了虚拟值。 你的名称将有所不同，因为你将为这些值提供你自己的名称。

    ```
    Create-ImmersiveReaderResource
        -SubscriptionName 'MyOrganizationSubscriptionName'
        -ResourceName 'MyOrganizationImmersiveReader'
        -ResourceSubdomain 'MyOrganizationImmersiveReader'
        -ResourceSKU 'S0'
        -ResourceLocation 'westus2'
        -ResourceGroupName 'MyResourceGroupName'
        -ResourceGroupLocation 'westus2'
        -AADAppDisplayName 'MyOrganizationImmersiveReaderAADApp'
        -AADAppIdentifierUri 'https://MyOrganizationImmersiveReaderAADApp'
        -AADAppClientSecret 'SomeStrongPassword'
        -AADAppClientSecretExpiration '2021-12-31'
    ```

    | 参数 | 注释 |
    | --- | --- |
    | SubscriptionName |要用于沉浸式读者资源的 Azure 订阅的名称。 若要创建资源，您必须拥有订阅。 |
    | ResourceName |  必须为字母数字，并且可能包含 "-"，但前提是 "-" 不是第一个或最后一个字符。 长度不能超过63个字符。|
    | ResourceSubdomain |沉浸式读者资源需要自定义子域。 在调用沉浸式读卡器服务启动读取器时，SDK 将使用子域。 子域必须是全局唯一的。 子域必须为字母数字，并且可能包含 "-"，但前提是 "-" 不是第一个或最后一个字符。 长度不能超过63个字符。 如果该资源已存在，则此参数是可选的。 |
    | ResourceSKU |选项： `S0`)  (标准层) 或 `S1` (教育/非盈利组织。 请访问我们的 [认知服务定价页](https://azure.microsoft.com/pricing/details/cognitive-services/immersive-reader/) ，了解有关每个可用 SKU 的详细信息。 如果该资源已存在，则此参数是可选的。 |
    | ResourceLocation |选项： `eastus` 、 `eastus2` 、 `southcentralus` 、 `westus` 、 `westus2` 、 `australiaeast` 、 `southeastasia` 、 `centralindia` `japaneast` `northeurope` `uksouth` 、、、、 `westeurope` 。 如果该资源已存在，则此参数是可选的。 |
    | ResourceGroupName |资源是在订阅中的资源组中创建的。 提供现有资源组的名称。 如果资源组不存在，则将创建一个具有此名称的新资源组。 |
    | ResourceGroupLocation |如果资源组不存在，则需要提供要在其中创建组的位置。 若要查找位置列表，请运行 `az account list-locations` 。 使用 *名称* 属性 (在返回的结果中不包含空格) 。 如果资源组已存在，则此参数是可选的。 |
    | AADAppDisplayName |Azure Active Directory 应用程序的显示名称。 如果未找到现有 Azure AD 应用程序，则将创建一个具有此名称的新应用程序。 如果 Azure AD 应用程序已存在，则此参数是可选的。 |
    | AADAppIdentifierUri |Azure AD 应用程序的 URI。 如果未找到现有 Azure AD 应用，将创建一个具有此 URI 的新应用。 例如，`https://immersivereaderaad-mycompany`。 |
    | AADAppClientSecret |创建的密码，稍后在获取用于启动沉浸式读取器的令牌时进行身份验证。 密码长度必须至少为16个字符，至少包含1个特殊字符，且至少包含1个数字字符。 若要在创建此资源之后管理 Azure AD 应用程序客户端机密，请访问 https://portal.azure.com "> Azure Active Directory > 应用注册-> `[AADAppDisplayName]` > 证书和机密" 边栏选项卡，> 客户端密钥 "部分 (，如下面 Azure AD 的" 管理) 应用程序机密 "屏幕截图所示。 |
    | AADAppClientSecretExpiration |截止日期或日期时间，在该日期或日期时间之后 `[AADAppClientSecret]` 将过期 (例如 "2020-12-31T11：59： 59 + 00： 00" 或 "2020-12-31" ) 。 |

    管理你的 Azure AD 应用程序机密

    ![Azure 门户的 "证书和机密" 边栏选项卡](./media/client-secrets-blade.png)

1. 将 JSON 输出复制到文本文件中，以供以后使用。 输出应如下所示。

    ```json
    {
      "TenantId": "...",
      "ClientId": "...",
      "ClientSecret": "...",
      "Subdomain": "..."
    }
    ```

## <a name="next-steps"></a>后续步骤

* 查看 [Node.js 快速入门](./quickstarts/client-libraries.md?pivots=programming-language-nodejs)，了解通过 Node.js 使用沉浸式阅读器 SDK 还可以做什么
* 查看 [Android 教程](./tutorial-android.md)，了解通过 Java 或 Kotlin for Android 使用沉浸式阅读器 SDK 可执行的其他操作
* 查看 [iOS 教程](./tutorial-ios.md)，了解通过 Swift for iOS 使用沉浸式阅读器 SDK 可执行的其他操作
* 查看 [Python 教程](./tutorial-python.md)，了解通过 Python 使用沉浸式阅读器 SDK 还可以做什么
* 浏览[沉浸式阅读器 SDK ](https://github.com/microsoft/immersive-reader-sdk)和[沉浸式阅读器 SDK 参考](./reference.md)