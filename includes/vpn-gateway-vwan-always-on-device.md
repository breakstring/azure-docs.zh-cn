---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 34e841a5f17d589c4fbef54a4a8674a99ac6c640
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028085"
---
必须满足以下要求才能成功建立设备隧道：

* 该设备必须是已加入域且运行 Windows 10 企业版或教育版 1809 或更高版本的计算机。
* 只可针对 Windows 内置 VPN 解决方案配置该隧道；结合计算机证书身份验证使用 IKEv2 建立该隧道。
* 对于每台设备，只能配置一个设备隧道。

1. 按照[点到站点 VPN 客户端](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md)一文中所述，在 Windows 10 客户端上安装客户端证书。 证书需位于本地计算机存储中。
1. 按照[这些说明](/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration)创建 VPN 配置文件，并在 LOCAL SYSTEM 帐户的上下文中配置设备隧道。

### <a name="configuration-example-for-device-tunnel"></a>设备隧道的配置示例

配置虚拟网络网关并在 Windows 10 客户端的本地计算机存储中安装客户端证书后，使用以下示例配置客户端设备隧道：

1. 复制以下文本，并将其另存为 ***devicecert.ps1** _。

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. 复制以下文本，并将其作为 _*_VPNProfile.xml_*_ 保存在与 _ * devicecert.ps1 * * 相同的文件夹中。 编辑以下文本，使之与你的环境相匹配。

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers> <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address> <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address> <= IP of resource in the vnet or the vnet address space`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
   </Authentication>  
   <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
    <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
   <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
     </NativeProfile> 
     <!-- use host routes(/32) to prevent routing conflicts -->  
     <Route>  
   <Address>192.168.3.5</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
     <Route>  
   <Address>192.168.3.4</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. 从 [Sysinternals](/sysinternals/downloads/psexec) 下载 **PsExec**，并将文件解压缩到 **C:\PSTools**。
1. 在管理员命令提示符下，运行以下命令启动 PowerShell：

   ```
   PsExec.exe Powershell for 32-bit Windows
   PsExec64.exe Powershell for 64-bit Windows
   ```

   ![屏幕截图显示一个命令提示符窗口，其中包含用于启动64位版本的 PowerShell 的命令。](./media/vpn-gateway-vwan-always-on-device/powershell.png)
1. 在 PowerShell 中，切换到 **devicecert.ps1** 和 **VPNProfile.xml** 所在的文件夹，然后运行以下命令：

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![屏幕截图显示已使用 devicesert 脚本运行 MachineCertTest 的 PowerShell 窗口。](./media/vpn-gateway-vwan-always-on-device/machinecerttest.png)
1. 运行 **rasphone**。

   ![屏幕截图显示了一个已选择 rasphone 的 "运行" 对话框。](./media/vpn-gateway-vwan-always-on-device/rasphone.png)
1. 找到 **MachineCertTest** 条目并单击“连接”。 

   ![屏幕截图显示 "网络连接" 对话框，其中选择了 MachineCertTest 并显示了 "连接" 按钮。](./media/vpn-gateway-vwan-always-on-device/connect.png)
1. 如果连接成功，请重新启动计算机。 隧道将自动连接。