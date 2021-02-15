---
title: 在 Azure 中使用 Python 创建和管理 Windows VM
description: 了解如何使用 Python 在 Azure 中创建和管理 Windows VM。
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 06/22/2017
ms.author: cynthn
ms.custom: devx-track-python
ms.openlocfilehash: d99f9326b3113742fdc0c14a4ae39d552df47edd
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730150"
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>在 Azure 中使用 Python 创建和管理 Windows VM

[Azure 虚拟机](overview.md) (VM) 需要多个支持性 Azure 资源。 本文介绍如何使用 Python 创建、管理和删除 VM 资源。 学习如何：

> [!div class="checklist"]
> * 创建 Visual Studio 项目
> * 安装包
> * 创建凭据
> * 创建资源
> * 执行管理任务
> * 删除资源
> * 运行应用程序

完成这些步骤大约需要 20 分钟。

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

1. 如果尚未安装，请安装 [Visual Studio](/visualstudio/install/install-visual-studio)。 在“工作负荷”页上选择“Python 开发”，然后单击“安装”。 在摘要中，可以看到系统已自动选择“Python 3 64 位 (3.6.0)”。 如果已安装 Visual Studio，则可以使用 Visual Studio 启动器添加 Python 工作负荷。
2. 安装并启动 Visual Studio 后, 单击“文件” > “新建” > “项目”。
3. 单击“模板” > “Python” > “Python 应用程序”，输入“myPythonProject” 作为项目名称并选择该项目的位置，然后单击“确定”。

## <a name="install-packages"></a>安装包

1. 在解决方案资源管理器的“myPythonProject”下，右键单击“Python 环境”，然后选择“添加虚拟环境”。
2. 在“添加虚拟环境”屏幕上，接受默认名称“env”，确保已选择“Python 3.6（64 位）”作为基础解释器，然后单击“创建”。
3. 右键单击所创建的 env 环境，然后单击“安装 Python 包”，并在搜索框中输入“azure”，然后按 Enter 键。

应在输出窗口中看到 azure 包已成功安装。 

## <a name="create-credentials"></a>创建凭据

在开始此步骤前，请确保具有 [Active Directory 服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)。 此外，应记下应用程序 ID、身份验证密钥和租户 ID，以便在后面的步骤中使用。

1. 打开创建的 myPythonProject.py 文件，然后添加此代码，使应用程序运行：

    ```python
    if __name__ == "__main__":
    ```

2. 若要导入所需代码，请将以下语句添加到 .py 文件的顶部：

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. 接下来在 .py 文件的导入语句后添加变量，指定代码中使用的公用值：
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    将 subscription-id 替换为订阅标识符。

4. 若要创建发出请求所需的 Active Directory 凭据，请将此函数添加到 .py 文件中的变量之后：

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    将 application-id、authentication-key 和 tenant-id 替换为前面在创建 Azure Active Directory 服务主体时收集的值。

5. 若要调用之前添加的函数，请在 .py 文件末尾处的 if 语句下添加此代码：

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>创建资源
 
### <a name="initialize-management-clients"></a>初始化管理客户端

在 Azure 中使用 Python SDK 创建和管理资源时需要用到管理客户端。 若要创建管理客户端，请在 .py 文件末尾处的 if 语句下添加此代码：

```python
resource_group_client = ResourceManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
network_client = NetworkManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
compute_client = ComputeManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
```

### <a name="create-the-vm-and-supporting-resources"></a>创建 VM 和支持资源

必须在[资源组](../../azure-resource-manager/management/overview.md)中包含所有资源。

1. 若要创建资源组，请在 .py 文件中的变量后添加此函数：

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. 若要调用之前添加的函数，请在 .py 文件末尾处的 if 语句下添加此代码：

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

使用[可用性集](tutorial-availability-sets.md)可以更方便地维护应用程序所用的虚拟机。

1. 若要创建可用性集，请在 .py 文件中的变量后添加此函数：
   
    ```python
    def create_availability_set(compute_client):
        avset_params = {
            'location': LOCATION,
            'sku': { 'name': 'Aligned' },
            'platform_fault_domain_count': 3
        }
        availability_set_result = compute_client.availability_sets.create_or_update(
            GROUP_NAME,
            'myAVSet',
            avset_params
        )
    ```

2. 若要调用之前添加的函数，请在 .py 文件末尾处的 if 语句下添加此代码：

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

与虚拟机通信需要[公共 IP 地址](../../virtual-network/public-ip-addresses.md)。

1. 若要创建虚拟机的公共 IP 地址，请在 .py 文件中的变量后添加此函数：

    ```python
    def create_public_ip_address(network_client):
        public_ip_addess_params = {
            'location': LOCATION,
            'public_ip_allocation_method': 'Dynamic'
        }
        creation_result = network_client.public_ip_addresses.create_or_update(
            GROUP_NAME,
            'myIPAddress',
            public_ip_addess_params
        )

        return creation_result.result()
    ```

2. 若要调用之前添加的函数，请在 .py 文件末尾处的 if 语句下添加此代码：

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

虚拟机必须在[虚拟网络](../../virtual-network/virtual-networks-overview.md)的子网中。

1. 若要创建虚拟网络，请在 .py 文件中的变量后添加此函数：

    ```python
    def create_vnet(network_client):
        vnet_params = {
            'location': LOCATION,
            'address_space': {
                'address_prefixes': ['10.0.0.0/16']
            }
        }
        creation_result = network_client.virtual_networks.create_or_update(
            GROUP_NAME,
            'myVNet',
            vnet_params
        )
        return creation_result.result()
    ```

2. 若要调用之前添加的函数，请在 .py 文件末尾处的 if 语句下添加此代码：
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. 若要将子网添加到虚拟网络，请在 .py 文件中的变量后添加此函数：
    
    ```python
    def create_subnet(network_client):
        subnet_params = {
            'address_prefix': '10.0.0.0/24'
        }
        creation_result = network_client.subnets.create_or_update(
            GROUP_NAME,
            'myVNet',
            'mySubnet',
            subnet_params
        )

        return creation_result.result()
    ```
        
4. 若要调用之前添加的函数，请在 .py 文件末尾处的 if 语句下添加此代码：
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

虚拟机需要使用网络接口在虚拟网络上通信。

1. 若要创建网络接口，请在 .py 文件中的变量后添加此函数：

    ```python
    def create_nic(network_client):
        subnet_info = network_client.subnets.get(
            GROUP_NAME, 
            'myVNet', 
            'mySubnet'
        )
        publicIPAddress = network_client.public_ip_addresses.get(
            GROUP_NAME,
            'myIPAddress'
        )
        nic_params = {
            'location': LOCATION,
            'ip_configurations': [{
                'name': 'myIPConfig',
                'public_ip_address': publicIPAddress,
                'subnet': {
                    'id': subnet_info.id
                }
            }]
        }
        creation_result = network_client.network_interfaces.create_or_update(
            GROUP_NAME,
            'myNic',
            nic_params
        )

        return creation_result.result()
    ```

2. 若要调用之前添加的函数，请在 .py 文件末尾处的 if 语句下添加此代码：

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

创建所有支持资源后，即可创建虚拟机。

1. 若要创建虚拟机，请在 .py 文件中的变量后添加此函数：
   
    ```python
    def create_vm(network_client, compute_client):  
        nic = network_client.network_interfaces.get(
            GROUP_NAME, 
            'myNic'
        )
        avset = compute_client.availability_sets.get(
            GROUP_NAME,
            'myAVSet'
        )
        vm_parameters = {
            'location': LOCATION,
            'os_profile': {
                'computer_name': VM_NAME,
                'admin_username': 'azureuser',
                'admin_password': 'Azure12345678'
            },
            'hardware_profile': {
                'vm_size': 'Standard_DS1'
            },
            'storage_profile': {
                'image_reference': {
                    'publisher': 'MicrosoftWindowsServer',
                    'offer': 'WindowsServer',
                    'sku': '2012-R2-Datacenter',
                    'version': 'latest'
                }
            },
            'network_profile': {
                'network_interfaces': [{
                    'id': nic.id
                }]
            },
            'availability_set': {
                'id': avset.id
            }
        }
        creation_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm_parameters
        )
    
        return creation_result.result()
    ```

    > [!NOTE]
    > 本教程创建运行 Windows Server 操作系统版本的虚拟机。 若要详细了解如何选择其他映像，请参阅[使用 Windows PowerShell 和 Azure CLI 来导航和选择 Azure 虚拟机映像](../linux/cli-ps-findimage.md)。
    > 
    > 

2. 若要调用之前添加的函数，请在 .py 文件末尾处的 if 语句下添加此代码：

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>执行管理任务

在虚拟机生命周期中，可能需要运行管理任务，例如启动、停止或删除虚拟机。 此外，建议创建代码来自动执行重复或复杂的任务。

### <a name="get-information-about-the-vm"></a>获取有关 VM 的信息

1. 若要获取有关虚拟机的信息，请在 .py 文件中将此函数添加在变量之后：

    ```python
    def get_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME, expand='instanceView')
        print("hardwareProfile")
        print("   vmSize: ", vm.hardware_profile.vm_size)
        print("\nstorageProfile")
        print("  imageReference")
        print("    publisher: ", vm.storage_profile.image_reference.publisher)
        print("    offer: ", vm.storage_profile.image_reference.offer)
        print("    sku: ", vm.storage_profile.image_reference.sku)
        print("    version: ", vm.storage_profile.image_reference.version)
        print("  osDisk")
        print("    osType: ", vm.storage_profile.os_disk.os_type.value)
        print("    name: ", vm.storage_profile.os_disk.name)
        print("    createOption: ", vm.storage_profile.os_disk.create_option.value)
        print("    caching: ", vm.storage_profile.os_disk.caching.value)
        print("\nosProfile")
        print("  computerName: ", vm.os_profile.computer_name)
        print("  adminUsername: ", vm.os_profile.admin_username)
        print("  provisionVMAgent: {0}".format(vm.os_profile.windows_configuration.provision_vm_agent))
        print("  enableAutomaticUpdates: {0}".format(vm.os_profile.windows_configuration.enable_automatic_updates))
        print("\nnetworkProfile")
        for nic in vm.network_profile.network_interfaces:
            print("  networkInterface id: ", nic.id)
        print("\nvmAgent")
        print("  vmAgentVersion", vm.instance_view.vm_agent.vm_agent_version)
        print("    statuses")
        for stat in vm_result.instance_view.vm_agent.statuses:
            print("    code: ", stat.code)
            print("    displayStatus: ", stat.display_status)
            print("    message: ", stat.message)
            print("    time: ", stat.time)
        print("\ndisks");
        for disk in vm.instance_view.disks:
            print("  name: ", disk.name)
            print("  statuses")
            for stat in disk.statuses:
                print("    code: ", stat.code)
                print("    displayStatus: ", stat.display_status)
                print("    time: ", stat.time)
        print("\nVM general status")
        print("  provisioningStatus: ", vm.provisioning_state)
        print("  id: ", vm.id)
        print("  name: ", vm.name)
        print("  type: ", vm.type)
        print("  location: ", vm.location)
        print("\nVM instance status")
        for stat in vm.instance_view.statuses:
            print("  code: ", stat.code)
            print("  displayStatus: ", stat.display_status)
    ```
2. 若要调用之前添加的函数，请在 .py 文件末尾处的 if 语句下添加此代码：

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>停止 VM

可停止虚拟机并保留其所有设置，但需继续付费；还可停止虚拟机并解除分配。 解除分配虚拟机时，也会解除分配与其关联的所有资源并将停止计费。

1. 若要停止虚拟机但不解除分配，请在 .py 文件中将此函数添加在变量之后：

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    如果要解除虚拟机分配，请将 power_off 调用更改为以下代码：

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. 若要调用之前添加的函数，请在 .py 文件末尾处的 if 语句下添加此代码：

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>启动 VM

1. 若要启动虚拟机，请在 .py 文件中将此函数添加在变量之后：

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. 若要调用之前添加的函数，请在 .py 文件末尾处的 if 语句下添加此代码：

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>重设 VM 大小

决定虚拟机大小时应考虑部署的诸多方面。 有关详细信息，请参见 [VM 大小](../sizes.md)。

1. 若要更改虚拟机大小，请在 .py 文件中将此函数添加在变量之后：

    ```python
    def update_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        vm.hardware_profile.vm_size = 'Standard_DS3'
        update_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm
        )

    return update_result.result()
    ```

2. 若要调用之前添加的函数，请在 .py 文件末尾处的 if 语句下添加此代码：

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>将数据磁盘添加到 VM

虚拟机可以有一个或多个存储为 VHD 的[数据磁盘](../managed-disks-overview.md)。

1. 若要将数据磁盘添加到虚拟机，请在 .py 文件中将此函数添加在变量之后： 

    ```python
    def add_datadisk(compute_client):
        disk_creation = compute_client.disks.create_or_update(
            GROUP_NAME,
            'myDataDisk1',
            {
                'location': LOCATION,
                'disk_size_gb': 1,
                'creation_data': {
                    'create_option': DiskCreateOption.empty
                }
            }
        )
        data_disk = disk_creation.result()
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        add_result = vm.storage_profile.data_disks.append({
            'lun': 1,
            'name': 'myDataDisk1',
            'create_option': DiskCreateOption.attach,
            'managed_disk': {
                'id': data_disk.id
            }
        })
        add_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME,
            VM_NAME,
            vm)

        return add_result.result()
    ```

2. 若要调用之前添加的函数，请在 .py 文件末尾处的 if 语句下添加此代码：

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>删除资源

由于需要为 Azure 中使用的资源付费，因此，最好删除不再需要的资源。 如果要删除虚拟机和所有支持资源，只需删除资源组。

1. 若要删除资源组和所有资源，请在 .py 文件中的变量后添加此函数：
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. 若要调用之前添加的函数，请在 .py 文件末尾处的 if 语句下添加此代码：
   
    ```python
    delete_resources(resource_group_client)
    ```

3. 保存 myPythonProject.py。

## <a name="run-the-application"></a>运行应用程序

1. 若要运行控制台应用程序，请在 Visual Studio 中单击“启动”。

2. 所有资源的状态都返回后，请按 Enter。 在状态信息中，应会看到“成功”预配状态。 创建虚拟机后，可以删除所创建的所有资源。 在按 **Enter** 开始删除资源之前，可能需要在 Azure 门户中花几分钟时间来验证资源的创建。 如果已打开 Azure 门户，则可能需要刷新边栏选项卡才能看到新资源。  

    控制台应用程序从头到尾完成运行大约需要五分钟时间。 应用程序完成运行之后，可能需要花费几分钟时间来删除所有资源和资源组。


## <a name="next-steps"></a>后续步骤

- 如果部署出现问题，请查看[使用 Azure 门户对资源组部署进行故障排除](../../azure-resource-manager/templates/deployment-history.md)
- 了解有关 [Azure Python 库](/python/api/overview/azure/)的详细信息
