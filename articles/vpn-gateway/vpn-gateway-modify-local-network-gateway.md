---
title: 修改本地网络网关 IP 地址前缀和网关 IP |Azure
description: 本文介绍如何更改本地网络网关的 IP 地址前缀
services: vpn-gateway
documentationCenter: na
authors: cherylmc
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2016
wacn.date: 01/05/2017
ms.author: cherylmc
---

# 使用 PowerShell 修改本地网络网关设置

有时本地网络网关 AddressPrefix 或 GatewayIPAddress 的设置会变更。以下说明有助于修改本地网络网关设置。也可在 Azure 门户预览中修改这些设置。

## 开始之前

你需要安装最新版本的 Azure Resource Manager PowerShell cmdlet。有关安装 PowerShell cmdlet 的详细信息，请参阅[如何安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs)。

## 修改 IP 地址前缀

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## 修改网关 IP 地址

[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## 后续步骤

可验证网关连接。请参阅[验证网关连接](./vpn-gateway-verify-connection-resource-manager.md)。

<!---HONumber=Mooncake_0822_2016-->