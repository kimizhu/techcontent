---
title: 捕获 Linux VM 的映像 | Azure
description: 了解如何使用经典部署模型捕获基于 Linux 的 Azure 虚拟机 (VM) 的映像。
services: virtual-machines-linux
documentationCenter: 
authors: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/31/2016
wacn.date: 10/24/2016
ms.author: iainfou
---

# How to capture a classic Linux virtual machine as an image（如何捕获用作映像的经典 Linux 虚拟机）

> [!IMPORTANT]
> Azure 具有用于创建和处理资源的两个不同的部署模型：[资源管理器和经典](../azure-resource-manager/resource-manager-deployment-model.md)。本文介绍使用经典部署模型。Azure 建议大多数新部署使用 Resource Manager 模型。了解如何[使用 Resource Manager 模型执行这些步骤](./virtual-machines-linux-capture-image.md)。

本文将演示如何捕获运行 Linux 的经典 Azure 虚拟机，以用作映像来创建其他虚拟机。此映像包括操作系统磁盘和附加到虚拟机的数据磁盘。它不包括网络配置，因此在使用此映像创建其他虚拟机时需要进行网络配置。

Azure 在“映像”下存储映像，以及任何已上载的映像。有关映像的详细信息，请参阅[关于 Azure 中的虚拟机映像][]。

## 开始之前

这些步骤假定已使用经典部署模型创建了 Azure 虚拟机并配置了操作系统，包括附加任何数据磁盘。如果需要创建 VM，请阅读[如何创建 Linux 虚拟机][]。

## 捕获虚拟机

1. 使用所选 SSH 客户端[连接到虚拟机](./virtual-machines-linux-mac-create-ssh-keys.md)。

2. 在 SSH 窗口中，键入以下命令。`waagent` 的输出结果可能会因此实用程序的版本而略有差异：

    `sudo waagent -deprovision+user`  

    此命令将尝试清除系统并使其适用于重新预配。此操作执行以下任务：

    - 删除 SSH 主机密钥（如果在配置文件中 Provisioning.RegenerateSshHostKeyPair 为“y”）
    - 清除 /etc/resolv.conf 中的 nameserver 配置
    - 从 /etc/shadow 中删除 `root` 用户的密码（如果在配置文件中 Provisioning.DeleteRootPassword 为“y”）
    - 删除缓存的 DHCP 客户端租赁
    - 将主机名重置为 localhost.localdomain
    - 删除上次预配的用户帐户（从 /var/lib/waagent 获得）**和关联数据**。

    >[!NOTE]
    > 取消预配会删除文件和数据，目的是使映像“一般化”。仅在需要捕获以用作新映像模板的虚拟机上运行此命令。无法确保映像中的所有敏感信息均已清除，或者说无法确保该映像适合再分发给第三方。

3. 键入 **y** 继续。添加 `-force` 参数即可免除此确认步骤。

4. 键入 **Exit** 关闭 SSH 客户端。

    >[!NOTE]
    > 剩余步骤假定已在客户端计算机上[安装 Azure CLI](../xplat-cli-install.md)。以下所有步骤也可以在 [Azure 经典管理门户][]中执行。

5. 从客户端计算机中打开 Azure CLI 并登录到你的 Azure 订阅。有关详细信息，请阅读[从 Azure CLI 连接到 Azure 订阅](../xplat-cli-connect.md)。

6. 请确保你是在服务管理模式下：

    `azure config mode asm`  

7. 使用以下命令关闭已在前面的步骤中预配的虚拟机：

    `azure vm shutdown <your-virtual-machine-name>`  

    >[!NOTE]
    > 可以使用 `azure vm list` 找出在订阅中创建的所有虚拟机

8. 在虚拟机停止后，使用以下命令捕获映像：

    `azure vm capture -t <your-virtual-machine-name> <new-image-name>`  

    键入你需要的映像名称以替换 _new-image-name_。此命令创建通用 OS 映像。`-t` 子命令将删除原始虚拟机。

9. 新映像现在会出现在映像列表中，可以用于配置任何新的虚拟机。你可以使用以下命令来查看它：

    `azure vm image list`  

    在 [Azure 经典管理门户][]中，它会显示在“映像”列表中。

    ![成功捕获映像](./media/virtual-machines-linux-classic-capture-image/VMCapturedImageAvailable.png)  

## 后续步骤
该映像已就绪，可用于创建虚拟机了。可以使用 Azure CLI 命令 `azure vm create` 并提供所创建的映像名称。有关此命令的详细信息，请参阅 [Using the Azure CLI with Classic deployment model](../virtual-machines-command-line-tools.md)（将 Azuer CLI 与经典部署模型配合使用）。此外，也可以使用 [Azure 经典管理门户][]来创建自定义虚拟机，只需使用“从库中”方法并选择所创建的映像即可。如需更多详细信息，请参阅[如何创建自定义虚拟机][]。

**另请参阅：**[Azure Linux 代理用户指南](./virtual-machines-linux-agent-user-guide.md)

[Azure 经典管理门户]: http://manage.windowsazure.cn
[关于 Azure 中的虚拟机映像]: ./virtual-machines-linux-classic-about-images.md
[如何创建自定义虚拟机]: ./virtual-machines-linux-classic-create-custom.md
[How to Attach a Data Disk to a Virtual Machine]: ./virtual-machines-linux-classic-attach-disk.md
[如何创建 Linux 虚拟机]: ./virtual-machines-linux-classic-create-custom.md

<!---HONumber=Mooncake_1017_2016-->