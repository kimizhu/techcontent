<!-- Ibiza Portal: tested -->

---
title: 使用模板部署常用应用程序框架 | Azure
description: 使用 Azure Resource Manager 模板在 Windows 虚拟机创建常用应用程序框架，以便安装 Active Directory、Docker，等等。
services: virtual-machines-windows
documentationCenter: virtual-machines
authors: squillace
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/29/2016
wacn.date: 10/25/2016
ms.author: rasquill
---

# 使用 Azure Resource Manager 模板部署常用应用程序框架

工作负荷通常要求多项资源按设计运行。利用 Azure Resource Manager 模板，你不仅可以定义应用程序的配置方式，而且可以定义资源的部署方式，以便为配置的应用程序提供支持。本文介绍库中最常用的模板以及如何使用 Azure PowerShell 或 Azure CLI 来部署它们。你也可以[这个主题的 Linux 版本](./virtual-machines-linux-app-frameworks.md)。

> [!NOTE]
>Azure 具有用于创建和处理资源的两个不同的部署模型：[资源管理器和经典](../azure-resource-manager/resource-manager-deployment-model.md)。这篇文章介绍如何使用资源管理器部署模型，Azure 建议大多数新部署使用管理器部署模型代替经典部署模型。

[!INCLUDE [virtual-machines-common-app-frameworks](../../includes/virtual-machines-common-app-frameworks.md)]

<!---HONumber=Mooncake_0411_2016-->