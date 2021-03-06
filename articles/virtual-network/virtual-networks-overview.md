---
title: Azure 虚拟网络 | Azure
description: 了解 Azure 中的虚拟网络。
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn

ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
wacn.date: 12/26/2016
ms.author: jdial
---

# 虚拟网络
Azure 虚拟网络 (VNet) 是你自己的网络在云中的表示形式。它是对专用于你的订阅的 Azure 云进行的逻辑隔离。你可以完全控制该网络中的 IP 地址块、DNS 设置、安全策略和路由表。你还可以进一步将 VNet 细分成各个子网，并启动 Azure IaaS 虚拟机 (VM) 和/或[云服务（PaaS 角色实例）](../cloud-services/cloud-services-choose-me.md)。此外，还可以使用 Azure 中提供的[连接选项](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site)之一将虚拟网络连接到本地网络。实际上，你可以将网络扩展到 Azure，对 IP 地址块进行完全的控制，并享受企业级 Azure 带来的好处。

若要更好地了解 VNet，请看下图，其中显示了简化的本地网络。

![本地网络](./media/virtual-networks-overview/figure01.png)

上图显示了通过路由器连接到公共 Internet 的本地网络。你还可以看到路由器与托管 DNS 服务器和 Web 服务器场的外围网络之间的防火墙。Web 服务器场使用向 Internet 公开的硬件负载均衡器进行负载均衡，并使用内部子网中的资源。内部子网由另一个防火墙与外围网络隔开，并托管 Active Directory 域控制器服务器、数据库服务器和应用程序服务器。

可以在 Azure 中托管同一网络，如下图所示。

![Azure 虚拟网络](./media/virtual-networks-overview/figure02.png)

请注意 Azure 基础结构如何起着路由器作用，允许从 VNet 访问公共 Internet 而无需进行任何配置。防火墙可由应用于每个单独子网的网络安全组 (NSG) 替代。而物理负载均衡器可由 Azure 中面向 Internet 的负载均衡器和内部负载均衡器替代。

> [!NOTE]
在 Azure 中有两种部署模式：经典（也称为服务管理）和 Azure 资源管理器 (ARM)。不能将经典 VNet 添加到地缘组，或创建为区域 VNet。如果地缘组中有一个 VNet，建议[将它迁移到区域 VNet](./virtual-networks-migrate-to-regional-vnet.md)。
> 

## 优点
* **隔离**。VNet 彼此之间完全隔离。这使你可以为使用相同 CIDR 地址块的开发、测试和生产创建单独的网络。
* **访问公共 Internet**。默认情况下，VNet 中的所有 IaaS VM 和 PaaS 角色实例都可以访问公共 Internet。可以通过使用网络安全组 (NSG) 来控制访问。
* **访问 VNet 中的 VM**。PaaS 角色实例和 IaaS VM 可以在同一虚拟网络中启动，并可使用专用 IP 地址互相连接（即使它们位于不同子网），而无需配置网关或使用公共 IP 地址。
* **名称解析**。Azure 为部署在 VNet 中的 IaaS VM 和 PaaS 角色实例提供内部名称解析。你还可以部署自己的 DNS 服务器，并将 VNet 配置为使用这些服务器。
* **安全性**。进出虚拟机的流量以及 VNet 中的 PaaS 角色实例都可使用网络安全组进行控制。
* **连接**。可以使用网关或 VNet 对等互连使 VNet 相互连接。VNet 可以通过站点到站点 VPN 网络或 Azure ExpressRoute 连接到本地数据中心。若要了解有关站点到站点 VPN 连接的详细信息，请访问[关于 VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md#site-to-site-and-multi-site)。若要了解有关 ExpressRoute 的详细信息，请访问 [ExpressRoute 技术概述](../expressroute/expressroute-introduction.md)。若要了解有关 VNet 对等互连的详细信息，请访问 [VNet 对等互连](./virtual-network-peering-overview.md)。

  > [!NOTE]
  请确保在将任何 IaaS VM 或 PaaS 角色实例部署到 Azure 环境之前创建 VNet。基于 ARM 的 VM 需要 VNet，如果你未指定现有 VNet，Azure 将创建其 CIDR 地址块可能会与本地网络冲突的默认 VNet。使你无法将 VNet 连接到本地网络。
  >

## 子网
子网是 VNet 中的一系列 IP 地址，可以将 VNet 划分成多个子网，以方便进行组织和提高安全性。部署到 VNet 的子网（不管是相同的子网还是不同的子网）中的 VM 和 PaaS 角色实例可以互相通信，不需任何额外的配置。你还可以为子网配置路由表和 NSG。

## IP 地址
有两种类型的 IP 地址分配给 Azure 中的资源： *公共* 和 *专用* 。使用公共 IP 地址可以让 Azure 资源与 Internet 以及其他面向公众的 Azure 服务（例如 [Azure Redis 缓存](https://www.azure.cn/home/features/redis-cache/)、[Azure 事件中心](../event-hubs/index.md)）通信。专用 IP 地址允许在虚拟网络的资源之间通信，以及与那些通过 VPN 连接的资源通信，不需使用可通过 Internet 路由的 IP 地址。

若要详细了解 Azure 中的 IP 地址，请访问[虚拟网络中的 IP 地址](./virtual-network-ip-addresses-overview-arm.md)

## Azure Load Balancer
虚拟网络中的虚拟机和云服务可以通过 Azure 负载均衡器向 Internet 公开。只面向内部的业务线应用程序可以使用内部负载均衡器进行负载均衡。

* **外部负载均衡器**。可以使用外部负载均衡器为从公共 Internet 访问的 IaaS VM 和 PaaS 角色实例提供高可用性。
* **内部负载均衡器**。可以使用内部负载均衡器为从 VNet 中的其他服务访问的 IaaS VM 和 PaaS 角色实例提供高可用性。

若要了解有关 Azure 中的负载均衡的详细信息，请访问[负载均衡器概述](../load-balancer/load-balancer-overview.md)。

## 网络安全组 (NSG)
可以创建 NSG 来控制对网络接口 (NIC)、VM 和子网的入站和出站访问。每个 NSG 包含一条或多条规则，这些规则指定基于源 IP 地址、源端口、目标 IP 地址和目标端口是批准还是拒绝流量。若要了解有关 NSG 的详细信息，请访问[什么是网络安全组](./virtual-networks-nsg.md)。

## 虚拟设备
虚拟设备就是 VNet 中的另一种 VM，它运行基于软件的设备函数，如防火墙、WAN 优化或入侵检测。你可以在 Azure 中创建一个路由，以便通过虚拟设备路由 VNet 流量来使用其功能。

例如，NSG 可用于在 VNet 上提供安全性。但是，NSG 为传入和传出数据包提供第 4 层访问控制列表 (ACL)。如果你要使用第 7 层安全模型，则需要使用防火墙设备。

虚拟设备依赖于[用户定义的路由和 IP 转发](./virtual-networks-udr-overview.md)。

## 限制
一个订阅中允许的虚拟网络数是有限制的，请参阅 [Azure 网络限制](../azure-subscription-service-limits.md#networking-limits)以获取更多信息。

## 定价
在 Azure 中使用虚拟网络不需支付额外的费用。在 Vnet 中启动的计算实例将按标准费率计费，如 [Azure VM 定价](https://www.azure.cn/pricing/details/virtual-machines/)中所述。在 VNet 中使用的 [VPN 网关](https://www.azure.cn/pricing/details/vpn-gateway/)和[公共 IP 地址](https://www.azure.cn/pricing/details/reserved-ip-addresses/)也将按标准费率计费。

## 后续步骤
* [创建 VNet](./virtual-networks-create-vnet-arm-pportal.md) 和子网。
* [在 VNet 中创建 VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md)。
* 了解 [NSG](./virtual-networks-nsg.md)。
* 了解[用户定义的路由和 IP 转发](./virtual-networks-udr-overview.md)。

<!---HONumber=Mooncake_1219_2016-->