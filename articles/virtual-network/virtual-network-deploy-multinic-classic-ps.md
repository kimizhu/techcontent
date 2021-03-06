---
title: 使用 PowerShell 创建具有多个 NIC 的 VM（经典）| Azure
description: 了解如何使用 PowerShell 通过经典部署模型创建具有多个 NIC 的 VM。
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: ''
tags: azure-service-management

ms.assetid: 6e50f39a-2497-4845-a5d4-7332dbc203c5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
wacn.date: 12/26/2016
ms.author: jdial
---

# 使用 PowerShell 创建具有多个 NIC 的 VM（经典）

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

你可以在 Azure 中创建虚拟机 (VM)，然后将多个网络接口 (NIC) 附加到每个 VM。有多个 NIC 时，可跨各个 NIC 分隔不同的流量类型。例如，一个 NIC 可能会与 Internet 通信，而另一个 NIC 则只与未连接到 Internet 的内部资源通信。跨多个 NIC 分隔网络流量是许多网络虚拟设备（例如应用程序交付和 WAN 优化解决方案）所需的功能。

> [!IMPORTANT]
Azure 具有两种不同的部署模型，用于创建和处理资源：[Resource Manager 模型和经典模型](../azure-resource-manager/resource-manager-deployment-model.md)。本文介绍使用经典部署模型的情况。Azure 建议大多数新部署使用 Resource Manager 模型。了解如何使用 [Resource Manager 部署模型](./virtual-network-deploy-multinic-arm-ps.md)执行这些步骤。

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

以下步骤将名为 *IaaSStory* 的资源组用于 Web 服务器，并将名为 *IaaSStory-BackEnd* 的资源组用于数据库服务器。

## <a name="Prerequisites"></a> 先决条件

需要先创建具有此方案需要的所有资源的 *IaaSStory* 资源组，然后才能创建数据库服务器。若要创建这些资源，请完成以下步骤。若要创建虚拟网络，请完成[创建虚拟网络](./virtual-networks-create-vnet-classic-netcfg-ps.md)一文中的步骤。

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## 创建后端 VM
后端 VM 取决于以下资源的创建：

* **后端子网**。各数据库服务器将分别属于各个子网，以便隔离流量。以下脚本需要在名为 *WTestVnet* 的虚拟网络中存在此子网。
* **数据磁盘的存储帐户**。为了提高性能，数据库服务器上的数据磁盘将使用固态驱动器 (SSD) 技术，这需要高级存储帐户。请确保部署到的 Azure 位置支持高级存储。
* **可用性集**。所有数据库服务器都将添加到单个可用性集，以确保在维护期间至少有一个 VM 已启动且正在运行。

### 步骤 1 - 启动脚本
可在[此处](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1)下载所用的完整 PowerShell 脚本。按照以下步骤更改脚本，以便用于具体环境。

1. 根据在上述[先决条件](#Prerequisites)中部署的现有资源组，更改以下变量的值。

    ```powershell
    $location              = "China North"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. 根据要用于后端部署的值，更改以下变量的值。

    ```powershell
    $backendCSName         = "IaaSStory-Backend"
    $prmStorageAccountName = "iaasstoryprmstorage"
    $avSetName             = "ASDB"
    $vmSize                = "Standard_DS3"
    $diskSize              = 127
    $vmNamePrefix          = "DB"
    $dataDiskSuffix        = "datadisk"
    $ipAddressPrefix       = "192.168.2."
    $numberOfVMs           = 2
    ```

### 步骤 2 - 为 VM 创建必要的资源
需要为所有 VM 的数据磁盘创建新的云服务和存储帐户。还需要为 VM 指定映像和本地管理员帐户。若要创建这些资源，请完成以下步骤：

1. 创建新的云服务。

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. 创建新的高级存储帐户。

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```

3. 将上面创建的存储帐户设置为订阅的当前存储帐户。

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. 为 VM 选择映像。

    ```powershell
    $image = Get-AzureVMImage `
    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
    | sort PublishedDate -Descending `
    | select -ExpandProperty ImageName -First 1
    ```

5. 设置本地管理员帐户凭据。

    ```powershell
    $cred = Get-Credential -Message "Enter username and password for local admin account"
    ```

### 步骤 3 - 创建 VM
需要使用循环创建所需数量的 VM，并在循环中创建所需的 NIC 和 VM。若要创建 NIC 和 VM，请执行以下步骤。

1. 启动 `for` 循环，基于 `$numberOfVMs` 变量的值重复执行命令，从而以所需次数创建一个 VM 和两个 NIC。

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. 创建 `VMConfig` 对象，它为 VM 指定映像、大小和可用性集。

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. 将 VM 预配为 Windows VM。

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.Password
    ```

4. 设置默认 NIC，并为其分配静态 IP 地址。

    ```powershell
    Set-AzureSubnet            -SubnetNames $backendSubnetName -VM $vmConfig
    Set-AzureStaticVNetIP     -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig
    ```

5. 为每个 VM 添加第二个 NIC。

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
    -SubnetName $backendSubnetName `
    -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
    -VM $vmConfig
    ```

6. 为每个 VM 创建两个数据磁盘。

    ```powershell
    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk1Name `
    -LUN 0

    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk2Name `
    -LUN 1
    ```

7. 创建每个 VM，然后结束循环。

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### 步骤 4 - 运行脚本
既已根据需要下载并更改了脚本，可运行该脚本以创建具有多个 NIC 的后端数据库 VM。

1. 保存脚本并从 **PowerShell** 命令提示符或 **PowerShell ISE** 运行它。将看到最初的输出，如下所示。

    ```
    OperationDescription    OperationId                          OperationStatus
    --------------------    -----------                          ---------------
    New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      
    New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      

    WARNING: No deployment found in service: 'IaaSStory-Backend'.
    ```
2. 填写凭据提示中所需的信息，然后单击“确定”。此时将显示以下输出。

    ```
    New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
    New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
    ```

<!---HONumber=Mooncake_1219_2016-->