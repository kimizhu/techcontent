- 虚拟网络可以在相同或不同的 Azure 区域（位置）中。

- 云服务或负载均衡终结点不能跨虚拟网络，即使它们连接在一起，也是如此。

- 将多个 Azure 虚拟网络连接在一起不需要任何本地 VPN 网关，除非需要跨界连接。

- VNet 到 VNet 通信支持连接虚拟网络。它不支持连接不在虚拟网络中的虚拟机或云服务。

- VNet 到 VNet 连接需要 RouteBased（以前称为动态路由）VPN 类型的 Azure VPN 网关。

- 虚拟网络连接可与多站点 VPN 同时使用，最多可以将一个虚拟网络 VPN 网关的 10 个（默认/标准网关）或 30 个（高性能网关）VPN 隧道连接到其他虚拟网络或本地站点。

- 虚拟网络和本地网络站点的地址空间不得重叠。地址空间重叠将会导致创建 VNet 到 VNet 连接失败。

- 不支持一对虚拟网络之间存在冗余隧道。

- 虚拟网络的所有 VPN 隧道共享 Azure VPN 网关上的可用带宽，以及 Azure 中的相同 VPN 网关运行时间 SLA。

- VNet 到 VNet 流量通过 Microsoft 网络而不是 Internet 传输。

- 同一区域中的 VNet 到 VNet 流量双向均为免费；跨区域 VNet 到 VNet 出口流量根据源区域的出站 VNet 间数据传输费率收费。有关详细信息，请参阅[定价页](https://www.azure.cn/pricing/details/vpn-gateway/)。

<!---HONumber=Mooncake_0815_2016-->