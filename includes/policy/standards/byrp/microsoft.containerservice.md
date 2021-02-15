---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 188803a87c24dfd33bf11c801cecd52e1b4182d6
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2021
ms.locfileid: "99093207"
---
## <a name="azure-security-benchmark"></a>Azure 安全基准

[Azure 安全基准](../../../../articles/security/benchmarks/overview.md)提供有关如何在 Azure 上保护云解决方案的建议。 若要查看此服务如何完全映射到 Azure 安全基准，请参阅 [Azure 安全基准映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

若要查看所有 Azure 服务的可用 Azure Policy 内置项如何映射到此合规性标准，请参阅 [Azure Policy 法规遵从性 - Azure 安全基准](../../../../articles/governance/policy/samples/azure-security-benchmark.md)。

|域 |控制 ID |控制标题 |策略<br /><sub>（Azure 门户）</sub> |策略版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|网络安全 |NS-1 |实现内部流量的安全性 |[应在 Kubernetes 服务上定义经授权的 IP 范围](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
|网络安全 |NS-4 |防止应用程序和服务受到外部网络攻击 |[应在 Kubernetes 服务上定义经授权的 IP 范围](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
|特权访问 |PA-7 |遵循 Just Enough Administration（最小特权原则） |[应在 Kubernetes 服务中使用基于角色的访问控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|数据保护 |DP-4 |加密传输中的敏感信息 |[在 Kubernetes 群集中强制实施 HTTPS 入口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a5b4dca-0b6f-4cf5-907c-56316bc1bf3d) |[5.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/IngressHttpsOnly.json) |
|安全状况和漏洞管理 |PV-2 |维护 Azure 服务的安全配置 |[应在群集上安装并启用用于 Kubernetes 服务 (AKS) 的 Azure Policy 加载项](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a15ec92-a229-4763-bb14-0ea34a568f8d) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AKS_AzurePolicyAddOn_Audit.json) |
|安全状况和漏洞管理 |PV-2 |维护 Azure 服务的安全配置 |[不允许 Kubernetes 群集中有特权容器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95edb821-ddaf-4404-9732-666045e056b4) |[5.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerNoPrivilege.json) |
|安全状况和漏洞管理 |PV-2 |维护 Azure 服务的安全配置 |[确保容器 CPU 和内存资源限制不超过 Kubernetes 群集中指定的限制](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe345eecc-fa47-480f-9e88-67dcc122b164) |[5.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerResourceLimits.json) |
|安全状况和漏洞管理 |PV-2 |维护 Azure 服务的安全配置 |[确保容器仅侦听 Kubernetes 群集中允许的端口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F440b515e-a580-421e-abeb-b159a61ddcbc) |[5.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerAllowedPorts.json) |
|安全状况和漏洞管理 |PV-2 |维护 Azure 服务的安全配置 |[确保只有允许使用的容器映像才在 Kubernetes 群集中运行](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffebd0533-8e55-448f-b837-bd0e06f16469) |[5.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerAllowedImages.json) |
|安全状况和漏洞管理 |PV-2 |维护 Azure 服务的安全配置 |[确保服务只在 Kubernetes 群集中侦听允许使用的端口](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F233a2a17-77ca-4fb1-9b6b-69223d272a44) |[5.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ServiceAllowedPorts.json) |
|安全状况和漏洞管理 |PV-2 |维护 Azure 服务的安全配置 |[Kubernetes 群集容器不得共享主机进程 ID 命名空间或主机 IPC 命名空间](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a1ee2f-2a2a-4576-bf2a-e0e36709c2b8) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/BlockHostNamespace.json) |
|安全状况和漏洞管理 |PV-2 |维护 Azure 服务的安全配置 |[Kubernetes 群集容器只应使用允许的 AppArmor 配置文件](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F511f5417-5d12-434d-ab2e-816901e72a5e) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/EnforceAppArmorProfile.json) |
|安全状况和漏洞管理 |PV-2 |维护 Azure 服务的安全配置 |[Kubernetes 群集容器只应使用允许的功能](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc26596ff-4d70-4e6a-9a30-c2506bd2f80c) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerAllowedCapabilities.json) |
|安全状况和漏洞管理 |PV-2 |维护 Azure 服务的安全配置 |[Kubernetes 群集容器应使用只读根文件系统运行](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf49d893-a74c-421d-bc95-c663042e5b80) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ReadOnlyRootFileSystem.json) |
|安全状况和漏洞管理 |PV-2 |维护 Azure 服务的安全配置 |[Kubernetes 群集 Pod hostPath 卷只应使用允许的主机路径](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F098fc59e-46c7-4d99-9b16-64990e543d75) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AllowedHostPaths.json) |
|安全状况和漏洞管理 |PV-2 |维护 Azure 服务的安全配置 |[Kubernetes 群集 Pod 和容器只应使用批准的用户 ID 和组 ID 运行](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AllowedUsersGroups.json) |
|安全状况和漏洞管理 |PV-2 |维护 Azure 服务的安全配置 |[Kubernetes 群集 Pod 只应使用批准的主机网络和端口范围](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/HostNetworkPorts.json) |
|安全状况和漏洞管理 |PV-2 |维护 Azure 服务的安全配置 |[Kubernetes 群集不得允许容器特权提升](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c6e92c9-99f0-4e55-9cf2-0c234dc48f99) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerNoPrivilegeEscalation.json) |
|安全状况和漏洞管理 |PV-7 |快速自动修复软件漏洞 |[Kubernetes 服务应升级到不易受攻击的 Kubernetes 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Azure 安全基准 v1

[Azure 安全基准](../../../../articles/security/benchmarks/overview.md)提供有关如何在 Azure 上保护云解决方案的建议。 若要查看此服务如何完全映射到 Azure 安全基准，请参阅 [Azure 安全基准映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

若要查看所有 Azure 服务的可用 Azure Policy 内置项如何映射到此合规性标准，请参阅 [Azure Policy 法规遵从性 - Azure 安全基准](../../../../articles/governance/policy/samples/azure-security-benchmark.md)。

|域 |控制 ID |控制标题 |策略<br /><sub>（Azure 门户）</sub> |策略版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|网络安全 |1.1 |在虚拟网络上使用网络安全组或 Azure 防火墙来保护资源 |[应在 Kubernetes 服务上定义经授权的 IP 范围](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
|数据保护 |4.6 |使用 Azure RBAC 控制对资源的访问 |[应在 Kubernetes 服务中使用基于角色的访问控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|漏洞管理 |5.3 |部署第三方软件修补程序自动化管理解决方案 |[Kubernetes 服务应升级到不易受攻击的 Kubernetes 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark"></a>CIS Microsoft Azure 基础基准

若要查看所有 Azure 服务的可用 Azure Policy 内置项如何映射到此合规性标准，请参阅 [Azure Policy 法规遵从性 - CIS Microsoft Azure 基础基准 1.1.0](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md)。
有关此符合性标准的详细信息，请参阅 [CIS Microsoft Azure 基础基准](https://www.cisecurity.org/benchmark/azure/)。

|域 |控制 ID |控制标题 |策略<br /><sub>（Azure 门户）</sub> |策略版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|其他安全注意事项 |8.5 |在 Azure Kubernetes 服务中启用基于角色的访问控制 (RBAC) |[应在 Kubernetes 服务中使用基于角色的访问控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC 级别 3

若要查看各项 Azure 服务可用的 Azure Policy 内置项如何映射到此合规性标准，请参阅 [Azure Policy 法规合规性 - CMMC 级别 3](../../../../articles/governance/policy/samples/cmmc-l3.md)。
有关此合规性标准的详细信息，请参阅[网络安全成熟度模型认证 (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf)。

|域 |控制 ID |控制标题 |策略<br /><sub>（Azure 门户）</sub> |策略版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|访问控制 |AC.1.001 |仅限授权用户、代表授权用户执行操作的进程以及设备（包括其他信息系统）访问信息系统。 |[Kubernetes 群集 Pod 只应使用批准的主机网络和端口范围](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/HostNetworkPorts.json) |
|访问控制 |AC.1.001 |仅限授权用户、代表授权用户执行操作的进程以及设备（包括其他信息系统）访问信息系统。 |[应在 Kubernetes 服务中使用基于角色的访问控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|访问控制 |AC.1.002 |仅限授权用户有权执行的事务和函数类型访问信息系统。 |[Kubernetes 群集 Pod 只应使用批准的主机网络和端口范围](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/HostNetworkPorts.json) |
|访问控制 |AC.1.002 |仅限授权用户有权执行的事务和函数类型访问信息系统。 |[应在 Kubernetes 服务中使用基于角色的访问控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|访问控制 |AC.2.007 |对特定安全功能和特权帐户等内容采用最小特权原则。 |[应在 Kubernetes 服务中使用基于角色的访问控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|访问控制 |AC.2.016 |根据批准的授权控制 CUI 流。 |[应在 Kubernetes 服务中使用基于角色的访问控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|配置管理 |CM.2.062 |采用最少功能原则，将组织系统配置为仅提供基本功能。 |[应在 Kubernetes 服务中使用基于角色的访问控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|配置管理 |CM.3.068 |限制、禁用或阻止使用不必要的程序、函数、端口、协议和服务。 |[Kubernetes 群集 Pod 只应使用批准的主机网络和端口范围](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/HostNetworkPorts.json) |
|风险评估 |RM.2.143 |根据风险评估修正漏洞。 |[Kubernetes 服务应升级到不易受攻击的 Kubernetes 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |
|系统和通信保护 |SC.1.175 |监视、控制和保护组织系统的外部边界和关键内部边界的通信（即组织系统传输或接收的信息）。 |[Kubernetes 群集 Pod 只应使用批准的主机网络和端口范围](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/HostNetworkPorts.json) |
|系统和通信保护 |SC.3.177 |采用经 FIPS 验证的加密模块来保护 CUI 的机密性。 |[Azure Kubernetes 服务群集中的操作系统和数据磁盘都应使用客户托管的密钥进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d7be79c-23ba-4033-84dd-45e2a5ccdd67) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AKS_CMK_Deny.json) |
|系统和通信保护 |SC.3.183 |默认拒绝和例外允许（即全部拒绝和例外允许）网络通信流量。 |[Kubernetes 群集 Pod 只应使用批准的主机网络和端口范围](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/HostNetworkPorts.json) |
|系统和信息完整性 |SI.1.210 |及时识别、报告和更正信息及信息系统缺陷。 |[Kubernetes 服务应升级到不易受攻击的 Kubernetes 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

若要查看所有 Azure 服务的可用 Azure Policy 内置项如何映射到此合规性标准，请参阅 [Azure Policy 法规合规性 - HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md)。
有关此合规性标准的详细信息，请参阅 [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)。

|域 |控制 ID |控制标题 |策略<br /><sub>（Azure 门户）</sub> |策略版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|特权管理 |1149.01c2System.9 - 01.c |组织可使获得授权的用户能够在组织规定允许自行决定的情况下确定业务合作伙伴的访问权限，并采用手动流程或自动化机制来帮助用户进行信息共享/协作决策，从而促进信息共享。 |[应在 Kubernetes 服务中使用基于角色的访问控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|特权管理 |1153.01c3System.35 - 01.c |所有未明确要求的文件系统访问权限都被禁用，只有获得授权的用户才能访问，且他们只能访问履行用户工作职责所明确需要的文件系统访问权限。 |[应在 Kubernetes 服务中使用基于角色的访问控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|职责分离 |1229.09c1Organizational.1 - 09.c |职责分离用于限制未经授权或无意中修改信息和系统的风险。 |[应在 Kubernetes 服务中使用基于角色的访问控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="nist-sp-800-171-r2"></a>NIST SP 800-171 R2

若要查看所有 Azure 服务的可用 Azure Policy 内置项如何映射到此合规性标准，请参阅 [Azure Policy 法规遵从性 - NIST SP 800-171 R2](../../../../articles/governance/policy/samples/nist-sp-800-171-r2.md)。
有关此符合性标准的详细信息，请参阅 [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final)。

|域 |控制 ID |控制标题 |策略<br /><sub>（Azure 门户）</sub> |策略版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|系统和信息完整性 |3.14.1 |及时识别、报告和更正系统缺陷。 |[Kubernetes 服务应升级到不易受攻击的 Kubernetes 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |

