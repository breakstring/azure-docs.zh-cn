---
title: 教程：将 SQL 托管实例添加到故障转移组
titleSuffix: Azure SQL Managed Instance
description: 在本教程中，学习如何在主要和辅助 Azure SQL 托管实例之间创建故障转移组。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: sashan
ms.date: 08/27/2019
ms.openlocfilehash: 1609f188af8ffb58251edc806e19f7820a6b0869
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525715"
---
# <a name="tutorial-add-sql-managed-instance-to-a-failover-group"></a>教程：将 SQL 托管实例添加到故障转移组
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

将 Azure SQL 托管实例服务的托管实例添加到故障转移组。 在本文中，你将学习如何：

> [!div class="checklist"]
> - 创建主托管实例。
> - 创建辅助托管实例作为[故障转移组](../database/auto-failover-group-overview.md)的一部分。 
> - 测试故障转移。

  > [!NOTE]
  > - 学习本教程时，请确保按照[为 SQL 托管实例设置故障转移组的先决条件](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets)来配置资源。 
  > - 创建托管实例可能需要花费很长时间。 因此，本教程可能需要几个小时才能完成。 要详细了解预配时间，请参阅 [SQL 托管实例管理操作](sql-managed-instance-paas-overview.md#management-operations)。 
  > - 参与故障转移组的托管实例需要 [Azure ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)、全局 VNet 对等互连或两个连接的 VPN 网关。 本教程提供创建和连接 VPN 网关的步骤。 如果已配置 ExpressRoute，请跳过这些步骤。 


## <a name="prerequisites"></a>先决条件

# <a name="portal"></a>[Portal](#tab/azure-portal)
若要完成本教程，请确保做好以下准备： 

- Azure 订阅。 [创建免费帐户](https://azure.microsoft.com/free/)（如果还没有该帐户）。


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
若要完成本教程，请确保准备好以下各项：

- Azure 订阅。 [创建免费帐户](https://azure.microsoft.com/free/)（如果还没有该帐户）。
- [Azure PowerShell](/powershell/azure/)

---


## <a name="create-a-resource-group-and-primary-managed-instance"></a>创建资源组和主托管实例

此步骤将使用 Azure 门户或 PowerShell 为故障转移组创建资源组和主托管实例。 

出于性能方面的考虑，将两个托管实例部署到[配对区域](../../best-practices-availability-paired-regions.md)。 与非配对区域相比，位于异地配对区域中的托管实例具有好得多的性能。 


# <a name="portal"></a>[Portal](#tab/azure-portal) 

使用 Azure 门户创建资源组和主托管实例。 

1. 在 Azure 门户的左侧菜单中选择“Azure SQL”。 如果 Azure SQL 不在列表中，请选择“所有服务”，然后在搜索框中键入“`Azure SQL`” 。 （可选）选择“Azure SQL”旁边的星号将其收藏并将其添加为左侧导航栏中的项。 
1. 选择“+ 添加”以打开“选择 SQL 部署选项”页。 可以通过在“数据库”磁贴上选择“显示详细信息”来查看有关不同数据库的其他信息。
1. 在“SQL 托管实例”磁贴上选择“创建”。 

    ![选择“SQL 托管实例”](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. 在“创建 Azure SQL 托管实例”页面的“基本信息”选项卡上： 
    1. 在“项目详细信息”下，从下拉列表中选择你的订阅，然后选择“创建新资源组”。   键入资源组的名称，例如 `myResourceGroup`。 
    1. 在“SQL 托管实例详细信息”下，提供托管实例的名称以及要将该实例部署到的区域。 将“计算 + 存储”保留为默认值。 
    1. 在“管理员帐户”下，提供管理员登录名（例如 `azureuser`）和复杂的管理员密码。 

    ![创建辅助托管实例](./media/failover-group-add-instance-tutorial/primary-sql-mi-values.png)

1. 将其余设置保留为默认值，然后选择“查看 + 创建”，检查 SQL 托管实例设置。 
1. 选择“创建”以创建主托管实例。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 创建资源组和主托管实例。 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for SQL Managed Instance
   $resourceGroupName = "myResourceGroup-$randomIdentifier"
   $location = "eastus"
   $drLocation = "eastus2"
   
   # Set the networking values for your primary managed instance
   $primaryVNet = "primaryVNet-$randomIdentifier"
   $primaryAddressPrefix = "10.0.0.0/16"
   $primaryDefaultSubnet = "primaryDefaultSubnet-$randomIdentifier"
   $primaryDefaultSubnetAddress = "10.0.0.0/24"
   $primaryMiSubnetName = "primaryMISubnet-$randomIdentifier"
   $primaryMiSubnetAddress = "10.0.0.0/24"
   $primaryMiGwSubnetAddress = "10.0.255.0/27"
   $primaryGWName = "primaryGateway-$randomIdentifier"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   $primaryGWConnection = $primaryGWName + "-connection"
   
   
   # Set the networking values for your secondary managed instance
   $secondaryVNet = "secondaryVNet-$randomIdentifier"
   $secondaryAddressPrefix = "10.128.0.0/16"
   $secondaryDefaultSubnet = "secondaryDefaultSubnet-$randomIdentifier"
   $secondaryDefaultSubnetAddress = "10.128.0.0/24"
   $secondaryMiSubnetName = "secondaryMISubnet-$randomIdentifier"
   $secondaryMiSubnetAddress = "10.128.0.0/24"
   $secondaryMiGwSubnetAddress = "10.128.255.0/27"
   $secondaryGWName = "secondaryGateway-$randomIdentifier"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   $secondaryGWConnection = $secondaryGWName + "-connection"
   
   
   
   # Set the SQL Managed Instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for SQL Managed Instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the SQL Managed Instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server license that can be used for AHB discount
   
   # Set failover group details
   $vpnSharedKey = "mi1mi2psk"
   $failoverGroupName = "failovergroup-$randomIdentifier"
   
   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Password is" $secpasswd
   Write-host "Primary Virtual Network name is" $primaryVNet
   Write-host "Primary default subnet name is" $primaryDefaultSubnet
   Write-host "Primary SQL Managed Instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary SQL Managed Instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary SQL Managed Instance name is" $primaryInstance
   Write-host "Secondary SQL Managed Instance name is" $secondaryInstance
   Write-host "Failover group name is" $failoverGroupName
   
   # Suppress networking breaking changes warning (https://aka.ms/azps-changewarnings
   Set-Item Env:\SuppressAzurePowerShellBreakingChangeWarnings "true"
   
   # Set the subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create the resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure the primary virtual network
   Write-host "Creating primary virtual network..."
   $primaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -Name $primaryVNet `
                         -AddressPrefix $primaryAddressPrefix
   
                     Add-AzVirtualNetworkSubnetConfig `
                         -Name $primaryMiSubnetName `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                     | Set-AzVirtualNetwork
   $primaryVirtualNetwork
   
   
   # Configure the primary managed instance subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure the network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure the route table management service
   Write-host "Configuring primary MI route table management service..."
   
   $primaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'primaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryRouteTableMiManagementService
   
   # Configure the primary network security group
   Write-host "Configuring primary network security group..."
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -Name $primaryMiSubnetName `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NetworkSecurityGroup $primaryNSGMiManagementService `
                         -RouteTable $primaryRouteTableMiManagementService | `
                       Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryNSGMiManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $PrimaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $PrimaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   Write-host "Primary network security group configured successfully."
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "primaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Primary network route table configured successfully."
   
   
   # Create the primary managed instance
   
   Write-host "Creating primary SQL Managed Instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $primaryInstance `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -SubnetId $primaryMiSubnetConfigId `
                         -AdministratorCredential $mycreds `
                         -StorageSizeInGB $maxStorage `
                         -VCore $vCores `
                         -Edition $edition `
                         -ComputeGeneration $computeGeneration `
                         -LicenseType $license
   Write-host "Primary SQL Managed Instance created successfully."
   ```

本教程部分使用以下 PowerShell cmdlet：

| 命令 | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建 Azure 资源组。  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 创建虚拟网络。  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 向虚拟网络添加子网配置。 | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 获取资源组中的虚拟网络。 | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 获取虚拟网络中的子网。 | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | 创建网络安全组。 | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | 创建路由表。 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | 更新虚拟网络的子网配置。  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 更新虚拟网络。  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | 获取网络安全组。 |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| 将网络安全规则配置添加到网络安全组。 |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | 更新网络安全组。  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | 将路由添加到路由表。 |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | 更新路由表。  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | 创建托管实例。  |

---

## <a name="create-secondary-virtual-network"></a>创建辅助虚拟网络

如果使用 Azure 门户创建托管实例，则需要单独创建虚拟网络，因为主托管实例和辅助托管实例的子网不得使用重叠的范围。 如果使用 PowerShell 配置托管实例，请直接跳到步骤 3。 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

若要验证主虚拟网络的子网范围，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中导航到你的资源组，并选择主实例的虚拟网络。  
2. 在“设置”下选择“子网”，并记下“地址范围”。   辅助托管实例的虚拟网络的子网地址范围不能与此重叠。 


   ![主子网](./media/failover-group-add-instance-tutorial/verify-primary-subnet-range.png)

若要创建虚拟网络，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源”并搜索“虚拟网络”。 
1. 选择 Microsoft 发布的“虚拟网络”选项，然后在下一页选择“创建”。  
1. 填写必填字段以配置辅助托管实例的虚拟网络，然后选择“创建”。 

   下表显示了辅助虚拟网络所需的值：

    | **字段** | 值 |
    | --- | --- |
    | **名称** |  辅助托管实例要使用的虚拟网络的名称，例如 `vnet-sql-mi-secondary`。 |
    | **地址空间** | 虚拟网络的地址空间，例如 `10.128.0.0/16`。 | 
    | **订阅** | 主托管实例和资源组所在的订阅。 |
    | **区域** | 将部署辅助托管实例的位置。 |
    | **子网** | 子网的名称。 默认会提供 `default`。 |
    | **地址范围**| 子网的地址范围。 此范围必须不同于主托管实例的虚拟网络使用的子网地址范围，例如 `10.128.0.0/24`。  |
    | &nbsp; | &nbsp; |

    ![辅助虚拟网络值](./media/failover-group-add-instance-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

仅当使用 Azure 门户部署 SQL 托管实例时，才需要执行此步骤。 如果使用 PowerShell，请直接跳到步骤 3。 

---

## <a name="create-a-secondary-managed-instance"></a>创建辅助托管实例
此步骤将在 Azure 门户中创建一个辅助托管实例，同时在两个托管实例之间配置网络。 

第二个托管实例必须：
- 是空的。 
- 与主托管实例的子网和 IP 范围不同。 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

使用 Azure 门户创建辅助托管实例。 

1. 在 Azure 门户的左侧菜单中选择“Azure SQL”。 如果 Azure SQL 不在列表中，请选择“所有服务”，然后在搜索框中键入“`Azure SQL`” 。 （可选）选择“Azure SQL”旁边的星号将其收藏并将其添加为左侧导航栏中的项。 
1. 选择“+ 添加”以打开“选择 SQL 部署选项”页。 可以通过在“数据库”磁贴上选择“显示详细信息”来查看有关不同数据库的其他信息。
1. 在“SQL 托管实例”磁贴上选择“创建”。 

    ![选择“SQL 托管实例”](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. 在“创建 Azure SQL 托管实例”页面的“基本信息”选项卡上，填写必填字段来配置辅助托管实例 。 

   下表显示了辅助托管实例所需的值：
 
    | **字段** | 值 |
    | --- | --- |
    | **订阅** |  主托管实例所在的订阅。 |
    | **资源组**| 主托管实例所在的资源组。 |
    | **SQL 托管实例名称** | 新的辅助托管实例的名称，例如 `sql-mi-secondary`。  | 
    | **区域**| 辅助托管实例的位置。  |
    | **SQL 托管实例管理员登录名** | 要用于新辅助托管实例的登录名，例如 `azureuser`。 |
    | **密码** | 新辅助托管实例的管理员登录名使用的复杂密码。  |
    | &nbsp; | &nbsp; |

1. 在“网络”选项卡下，对于“虚拟网络”，请从下拉列表中选择为辅助托管实例创建的虚拟网络。 

   ![辅助托管实例网络](./media/failover-group-add-instance-tutorial/networking-settings-for-secondary-mi.png)

1. 在“其他设置”选项卡下，对于“异地复制”，请选择“用作故障转移辅助实例”旁边的“是”  。 从下拉列表中选择主托管实例。 
    
   确保排序规则和时区与主托管实例匹配。 在本教程中创建的主托管实例使用了默认的 `SQL_Latin1_General_CP1_CI_AS` 排序规则和 `(UTC) Coordinated Universal Time` 时区。 

   ![辅助托管实例网络连接](./media/failover-group-add-instance-tutorial/secondary-mi-failover.png)

1. 选择“查看 + 创建”以检查辅助托管实例的设置。 
1. 选择“创建”以创建辅助托管实例。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 创建辅助托管实例。 

   ```powershell-interactive
   # Configure the secondary virtual network
   Write-host "Configuring secondary virtual network..."
   
   $SecondaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $drlocation `
                         -Name $secondaryVNet `
                         -AddressPrefix $secondaryAddressPrefix
   
   Add-AzVirtualNetworkSubnetConfig `
                         -Name $secondaryMiSubnetName `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -AddressPrefix $secondaryMiSubnetAddress `
                       | Set-AzVirtualNetwork
   $SecondaryVirtualNetwork
   
   # Configure the secondary managed instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure the secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure the secondary route table MI management service
   Write-host "Configuring secondary route table MI management service..."
   
   $secondaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'secondaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryRouteTableMiManagementService
   
   # Configure the secondary network security group
   Write-host "Configuring secondary network security group..."
   
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -Name $secondaryMiSubnetName `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NetworkSecurityGroup $secondaryNSGMiManagementService `
                         -RouteTable $secondaryRouteTableMiManagementService `
                       | Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryToMIManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $secondaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $secondaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "secondaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Secondary network security group configured successfully."
   
   # Create the secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary SQL Managed Instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $secondaryInstance `
                     -ResourceGroupName $resourceGroupName `
                     -Location $drLocation `
                     -SubnetId $secondaryMiSubnetConfigId `
                     -AdministratorCredential $mycreds `
                     -StorageSizeInGB $maxStorage `
                     -VCore $vCores `
                     -Edition $edition `
                     -ComputeGeneration $computeGeneration `
                     -LicenseType $license `
                     -DnsZonePartner $primaryManagedInstanceId.Id
   Write-host "Secondary SQL Managed Instance created successfully."
   ```

本教程部分使用以下 PowerShell cmdlet：

| 命令 | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建 Azure 资源组。  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 创建虚拟网络。  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 向虚拟网络添加子网配置。 | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 获取资源组中的虚拟网络。 | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 获取虚拟网络中的子网。 | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | 创建网络安全组。 | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | 创建路由表。 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | 更新虚拟网络的子网配置。  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 更新虚拟网络。  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | 获取网络安全组。 |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| 将网络安全规则配置添加到网络安全组。 |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | 更新网络安全组。  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | 将路由添加到路由表。 |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | 更新路由表。  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | 创建托管实例。  |

---

## <a name="create-a-primary-gateway"></a>创建主网关 

对于要参与故障转移组的两个托管实例，必须在这两个托管实例的虚拟网络之间配置 ExpressRoute 或网关，以便能够进行网络通信。 如果选择配置 [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) 而不是连接两个 VPN 网关，请跳到[步骤 7](#create-a-failover-group)。  

本文提供了创建两个 VPN 网关并将它们连接起来的步骤，但如果你已配置 ExpressRoute，则可以改为创建故障转移组。 

> [!NOTE]
> 网关的 SKU 会影响吞吐量性能。 本教程使用最基本的 SKU (`HwGw1`) 来部署网关。 部署更高的 SKU（示例：`VpnGw3`）可实现更高的吞吐量。 有关所有可用选项，请参阅[网关 SKU](../../vpn-gateway/vpn-gateway-about-vpngateways.md#benchmark)

# <a name="portal"></a>[Portal](#tab/azure-portal)

使用 Azure 门户为主托管实例的虚拟网络创建网关。 


1. 在 [Azure 门户](https://portal.azure.com)中转到你的资源组，并选择主托管实例的“虚拟网络”资源。 
1. 在“设置”下选择“子网”，然后选择添加新的“网关子网”。   保留默认值。 

   ![为主托管实例添加网关](./media/failover-group-add-instance-tutorial/add-subnet-gateway-primary-vnet.png)

1. 创建子网网关后，在左侧导航窗格中选择“创建资源”，然后在搜索框中键入 `Virtual network gateway`。 选择“Microsoft”发布的“虚拟网络网关”资源。  

   ![创建新的虚拟网络网关](./media/failover-group-add-instance-tutorial/create-virtual-network-gateway.png)

1. 填写必填字段，为主托管实例配置网关。 

   下表显示了主托管实例的网关所需的值：
 
    | **字段** | 值 |
    | --- | --- |
    | **订阅** |  主托管实例所在的订阅。 |
    | **名称** | 虚拟网络网关的名称，例如 `primary-mi-gateway`。 | 
    | **区域** | 主托管实例所在的区域。 |
    | **网关类型** | 选择“VPN”。 |
    | **VPN 类型** | 选择“基于路由”。 |
    | **SKU**| 保留默认值 `VpnGw1`。 |
    | **虚拟网络**| 选择在第 2 部分创建的虚拟网络，例如 `vnet-sql-mi-primary`。 |
    | **公共 IP 地址**| 选择“新建”。 |
    | **公共 IP 地址名称**| 输入 IP 地址的名称，例如 `primary-gateway-IP`。 |
    | &nbsp; | &nbsp; |

1. 将其他值保留为默认值，然后选择“查看 + 创建”以检查虚拟网络网关的设置。

   ![主网关设置](./media/failover-group-add-instance-tutorial/settings-for-primary-gateway.png)

1. 选择“创建”以创建新的虚拟网络网关。 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 为主托管实例的虚拟网络创建网关。 

   ```powershell-interactive
   # Create the primary gateway
   Write-host "Adding GatewaySubnet to primary VNet..."
   Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $primaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $primaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName
   $primaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $primaryVirtualNetwork
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $primaryGWPublicIP = New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $location -AllocationMethod Dynamic
   $primaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -Subnet $primaryGatewaySubnet -PublicIpAddress $primaryGWPublicIP
   
   $primaryGateway = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $resourceGroupName `
       -Location $location -IpConfigurations $primaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $primaryGateway
   ```

本教程部分使用以下 PowerShell cmdlet：

| 命令 | 注释 |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 获取资源组中的虚拟网络。 |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 向虚拟网络添加子网配置。 | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 更新虚拟网络。  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 获取虚拟网络中的子网。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 创建公共 IP 地址。  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | 为虚拟网络网关创建 IP 配置。 |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | 创建虚拟网络网关。 |


---


## <a name="create-secondary-gateway"></a>创建辅助网关 
此步骤使用 Azure 门户为辅助托管实例的虚拟网络创建网关。 


# <a name="portal"></a>[Portal](#tab/azure-portal)

使用 Azure 门户重复上一部分中的步骤，为辅助托管实例创建虚拟网络子网和网关。 填写必填字段，为辅助托管实例配置网关。 

   下表显示了辅助托管实例的网关所需的值：

   | **字段** | 值 |
   | --- | --- |
   | **订阅** |  辅助托管实例所在的订阅。 |
   | **名称** | 虚拟网络网关的名称，例如 `secondary-mi-gateway`。 | 
   | **区域** | 辅助托管实例所在的区域。 |
   | **网关类型** | 选择“VPN”。 |
   | **VPN 类型** | 选择“基于路由”。 |
   | **SKU**| 保留默认值 `VpnGw1`。 |
   | **虚拟网络**| 为辅助托管实例选择虚拟网络，例如 `vnet-sql-mi-secondary`。 |
   | **公共 IP 地址**| 选择“新建”。 |
   | **公共 IP 地址名称**| 输入 IP 地址的名称，例如 `secondary-gateway-IP`。 |
   | &nbsp; | &nbsp; |

   ![辅助网关设置](./media/failover-group-add-instance-tutorial/settings-for-secondary-gateway.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 为辅助托管实例的虚拟网络创建网关。 

   ```powershell-interactive
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   
   Write-host "Adding GatewaySubnet to secondary VNet..."
   Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $secondaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $secondaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName
   $secondaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $secondaryVirtualNetwork
   $drLocation = $secondaryVirtualNetwork.Location
   
   Write-host "Creating secondary gateway..."
   Write-host "This will take some time."
   $secondaryGWPublicIP = New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $drLocation -AllocationMethod Dynamic
   $secondaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -Subnet $secondaryGatewaySubnet -PublicIpAddress $secondaryGWPublicIP
   
   $secondaryGateway = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $resourceGroupName `
       -Location $drLocation -IpConfigurations $secondaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   $secondaryGateway
   ```

本教程部分使用以下 PowerShell cmdlet：

| 命令 | 注释 |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 获取资源组中的虚拟网络。 |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 向虚拟网络添加子网配置。 | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 更新虚拟网络。  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 获取虚拟网络中的子网。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 创建公共 IP 地址。  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | 为虚拟网络网关创建 IP 配置。 |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | 创建虚拟网络网关。 |

---


## <a name="connect-the-gateways"></a>连接网关
此步骤在两个虚拟网络的两个网关之间创建双向连接。 


# <a name="portal"></a>[Portal](#tab/azure-portal)

使用 Azure 门户连接两个网关。 


1. 在 [Azure 门户](https://portal.azure.com)中选择“创建资源”。
1. 在搜索框中键入 `connection`，然后按 Enter 进行搜索；随后你会转到 Microsoft 发布的“连接”资源。
1. 选择“创建”以创建连接。 
1. 在“基本信息”页上选择以下值，然后选择“确定”。  
    1. 为“连接类型”选择 `VNet-to-VNet`。 
    1. 从下拉列表中选择订阅。 
    1. 在下拉列表中选择 SQL 托管实例的资源组。 
    1. 在下拉列表中选择主托管实例的位置。 
1. 在“设置”页上选择或输入以下值，然后选择“确定”： 
    1. 为“第一个虚拟网络网关”选择主网络网关，例如 `primaryGateway`。  
    1. 为“第二个虚拟网络网关”选择辅助网络网关，例如 `secondaryGateway`。 
    1. 选中“建立双向连接”旁边的复选框。 
    1. 保留默认的主连接名称，或将其重命名为所选的值。 
    1. 提供连接的“共享密钥(PSK)”，例如 `mi1m2psk`。 
    1. 选择“确定”保存设置。 

    ![创建网关连接](./media/failover-group-add-instance-tutorial/create-gateway-connection.png)

    

1. 在“查看 + 创建”页上查看双向连接的设置，然后选择“确定”以创建连接。  


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 连接两个网关。 

   ```powershell-interactive
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway to secondary gateway..."
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $primaryGateway -VirtualNetworkGateway2 $secondaryGateway -Location $location `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway to primary gateway..."
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $secondaryGateway -VirtualNetworkGateway2 $primaryGateway -Location $drLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

本教程部分使用以下 PowerShell cmdlet：

| 命令 | 注释 |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | 创建两个虚拟网络网关之间的连接。   |

---


## <a name="create-a-failover-group"></a>创建故障转移组
此步骤将创建故障转移组，并将两个托管实例添加到其中。 


# <a name="portal"></a>[Portal](#tab/azure-portal)
使用 Azure 门户创建故障转移组。 


1. 在 [Azure 门户](https://portal.azure.com)的左侧菜单中选择“Azure SQL”。 如果 Azure SQL 不在列表中，请选择“所有服务”，然后在搜索框中键入“`Azure SQL`” 。 （可选）选择“Azure SQL”旁边的星号将其收藏并将其添加为左侧导航栏中的项。 
1. 选择在第一部分中创建的主托管实例，例如 `sql-mi-primary`。 
1. 在“设置”下，导航到“实例故障转移组”，然后选择“添加组”，打开“实例故障转移组”页面   。 

   ![添加故障转移组](./media/failover-group-add-instance-tutorial/add-failover-group.png)

1. 在“实例故障转移组”页面上，键入故障转移组的名称，例如 `failovergrouptutorial`。 然后，从下拉列表中选择辅助托管实例，例如 `sql-mi-secondary`。 选择“创建”以创建故障转移组。 

   ![创建故障转移组](./media/failover-group-add-instance-tutorial/create-failover-group.png)

1. 故障转移组部署完成后，你将返回到“故障转移组”页。 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
使用 PowerShell 创建故障转移组。 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

本教程部分使用以下 PowerShell cmdlet：

| 命令 | 注释 |
|---|---|
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| 创建新的 Azure SQL 托管实例故障转移组。  |


---


## <a name="test-failover"></a>测试故障转移
此步骤将故障转移组故障转移到辅助服务器，然后使用 Azure 门户故障回复。 


# <a name="portal"></a>[Portal](#tab/azure-portal)
使用 Azure 门户测试故障转移。 


1. 在 [Azure 门户](https://portal.azure.com)中导航到你的辅助托管实例，然后在“设置”下选择“实例故障转移组”。 
1. 查看哪个托管实例是主实例，哪个托管实例是辅助实例。 
1. 选择“故障转移”，然后在有关正在断开 TDS 会话的警告中选择“是”。  

   ![将故障转移组故障转移](./media/failover-group-add-instance-tutorial/failover-mi-failover-group.png)

1. 查看哪个托管实例是主实例，哪个是辅助实例。 如果故障转移成功，这两个实例的角色应会交换。 

   ![故障转移后托管实例的角色已交换](./media/failover-group-add-instance-tutorial/mi-switched-after-failover.png)

1. 转到新的 _辅助_ 托管实例，再次选择“故障转移”，将主实例故障回复为主角色。 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
使用 PowerShell 测试故障转移。 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Fail over the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


将故障转移组还原到主服务器：

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail the primary managed instance back to the primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

本教程的此部分使用以下 PowerShell cmdlet：

| 命令 | 注释 |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | 获取或列出 SQL 托管实例故障转移组。| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | 执行 SQL 托管实例故障转移组的故障转移。 | 

---



## <a name="clean-up-resources"></a>清理资源
若要清理资源，请依次删除托管实例、虚拟群集、所有剩余资源和资源组。 

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. 在 [Azure 门户](https://portal.azure.com)中导航到你的资源组。 
1. 选择托管实例，然后选择“删除”。 在文本框中键入 `yes` 以确认你要删除该资源，然后选择“删除”。 此过程可能需要一段时间才能在后台完成，在完成之前，你将无法删除“虚拟群集”或任何其他从属资源。 在“活动”选项卡中监视删除过程，确认托管实例已被删除。 
1. 删除托管实例后，请将虚拟群集删除，方法是：在资源组中选择“虚拟群集”，然后选择“删除”。 在文本框中键入 `yes` 以确认你要删除该资源，然后选择“删除”。 
1. 删除任何剩余资源。 在文本框中键入 `yes` 以确认你要删除该资源，然后选择“删除”。 
1. 删除资源组：选择“删除资源组”，键入资源组的名称 `myResourceGroup`，然后选择“删除”。  

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

需要删除资源组两次。 首次删除资源组时将一并删除托管实例和虚拟群集，但随后会失败，并显示错误消息 `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'`。 再次运行 Remove-AzResourceGroup 命令，删除所有残留资源以及资源组。

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing SQL Managed Instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resource group..."
```

本教程部分使用以下 PowerShell cmdlet：

| 命令 | 注释 |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组。 |

---

## <a name="full-script"></a>完整脚本

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add SQL Managed Instance to a failover group")]

此脚本使用以下命令。 表中的每条命令链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建 Azure 资源组。  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 创建虚拟网络。  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | 向虚拟网络添加子网配置。 | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | 获取资源组中的虚拟网络。 | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 获取虚拟网络中的子网。 | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | 创建网络安全组。 | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | 创建路由表。 |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | 更新虚拟网络的子网配置。  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | 更新虚拟网络。  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | 获取网络安全组。 |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| 将网络安全规则配置添加到网络安全组。 |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | 更新网络安全组。  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | 将路由添加到路由表。 |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | 更新路由表。  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | 创建托管实例。  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| 返回有关 Azure SQL 数据库托管实例的详细信息。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 创建公共 IP 地址。  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | 为虚拟网络网关创建 IP 配置。 |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | 创建虚拟网络网关。 |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | 创建两个虚拟网络网关之间的连接。   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| 创建新的 SQL 托管实例故障转移组。  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | 获取或列出 SQL 托管实例故障转移组。| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | 执行 SQL 托管实例故障转移组的故障转移。 | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组。 | 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

没有适用于 Azure 门户的脚本。

---

## <a name="next-steps"></a>后续步骤

在本教程中，你已在两个托管实例之间配置了一个故障转移组。 你已了解如何：

> [!div class="checklist"]
> - 创建主托管实例。
> - 创建辅助托管实例作为[故障转移组](../database/auto-failover-group-overview.md)的一部分。 
> - 测试故障转移。

继续学习下一篇快速入门，了解如何连接到 SQL 托管实例，以及如何将数据库还原到 SQL 托管实例： 

> [!div class="nextstepaction"]
> [连接到 SQL 托管实例](connect-vm-instance-configure.md)
> [将数据库还原到 SQL 托管实例](restore-sample-database-quickstart.md)


