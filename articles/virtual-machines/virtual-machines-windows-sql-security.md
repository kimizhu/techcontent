---
title: Azure 中 SQL Server 的安全注意事项 | Azure
description: 本主题使用通过经典部署模型创建的资源，并提供了有关保护 Azure 虚拟机中运行的 SQL Server 的一般指南。
services: virtual-machines-windows
documentationCenter: na
authors: rothja
manager: jhubbard
editor: ''
tags: azure-service-management

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 11/15/2016
wacn.date: 12/30/2016
ms.author: jroth
---

# Azure 虚拟机中 SQL Server 的安全注意事项

本主题包括总体安全准则，可帮助建立对 Azure VM 中 SQL Server 实例的安全访问。但是，为了确保更好地保护 Azure 中的 SQL Server 数据库实例，我们建议你除了采取 Azure 的安全最佳做法外，还实施传统的本地安全做法。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Azure 遵守多个行业法规和标准，使你能够使用在虚拟机中运行的 SQL Server 构建合规的解决方案。有关 Azure 合规性的信息，请参阅 [Azure 信任中心](https://www.trustcenter.cn/)。

以下列表中列出了配置和连接到 Azure VM 中的 SQL Server 实例时应考虑的安全建议。

## 有关管理帐户的注意事项：

- 创建一个唯一的本地管理员帐户，不要命名为 **Administrator**。

- 对所有帐户使用复杂的强密码。有关如何创建强密码的详细信息，请参阅[创建强密码的提示](http://windows.microsoft.com/windows-vista/Tips-for-creating-a-strong-password)一文。

- 默认情况下，Azure 在 SQL Server 虚拟机安装期间会选择 Windows 身份验证。因此，会禁用 **SA** 登录名，并由安装程序分配密码。我们建议不应使用或启用 **SA** 登录名。以下是可在需要 SQL 登录名时使用的备用策略：
    - 创建一个具有 sysadmin 成员身份的 SQL 帐户。
    - 如果必须使用 **SA** 登录名，请启用该登录名，将其重命名并分配一个新密码。
    - 前面提到的这两个选项都需要将身份验证模式更改为 **SQL Server 和 Windows 身份验证模式**。有关详细信息，请参阅[更改服务器身份验证模式](https://msdn.microsoft.com/zh-cn/library/ms188670.aspx)。

## 有关保护与 Azure 虚拟机的连接的注意事项：

- 请考虑使用 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)（而不是公共 RDP 端口）来管理虚拟机。

- 使用[网络安全组](../virtual-network/virtual-networks-nsg.md) (NSG) 以允许或拒绝与虚拟机的网络通信。如果你想要使用 NSG，但已有了终结点 ACL，则请先删除该终结点 ACL。有关如何执行此操作的信息，请参阅[使用 PowerShell 管理终结点的访问控制列表 (ACL)](../virtual-network/virtual-networks-acl-powershell.md)。

- 如果你在使用终结点，当你不使用它们时，请删除虚拟机上的任何终结点。有关在终结点上使用 ACL 的说明，请参阅[管理终结点上的 ACL](./virtual-machines-windows-classic-setup-endpoints.md#manage-the-acl-on-an-endpoint)。

- 为 Azure 虚拟机中的 SQL Server 数据库引擎实例启用加密连接选项。使用签名证书配置 SQL Server 实例。有关详细信息，请参阅[为数据库引擎启用加密连接](https://msdn.microsoft.com/zh-cn/library/ms191192.aspx)和[连接字符串语法](https://msdn.microsoft.com/zh-cn/library/ms254500.aspx)。

- 如果只应从特定网络访问虚拟机，请使用 Windows 防火墙将访问限制到特定 IP 地址或网络子网。

## 后续步骤

如果你还对有关性能的最佳实践感兴趣，请参阅 [Azure 虚拟机中 SQL Server 的性能最佳实践](./virtual-machines-windows-sql-performance.md)。

有关其他与在 Azure VM 中运行 SQL Server 相关的主题，请参阅 [Azure 虚拟机上的 SQL Server 概述](./virtual-machines-windows-sql-server-iaas-overview.md)。

<!---HONumber=Mooncake_Quality_Review_1215_2016-->