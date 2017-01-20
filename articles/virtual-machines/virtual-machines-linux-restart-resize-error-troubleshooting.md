---
title: VM 重新启动或大小调整问题 | Azure
description: 排查在 Azure 中重新启动或调整现有 Linux 虚拟机时遇到的 Resource Manager 部署问题
services: virtual-machines-linux, azure-resource-manager
documentationCenter: 
authors: Deland-Han
manager: felixwu
editor: 
tags: top-support-issue

ms.service: virtual-machines-linux
ms.topic: support-article
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.workload: required
ms.date: 09/09/2016
wacn.date: 12/26/2016
ms.author: delhan
---

# 排查在 Azure 中重新启动或调整现有 Linux 虚拟机时遇到的 Resource Manager 部署问题

> [!div class="op_single_selector"]
- [经典](./virtual-machines-linux-classic-restart-resize-error-troubleshooting.md)
- [Resource Manager](./virtual-machines-linux-restart-resize-error-troubleshooting.md)

在尝试启动已停止的 Azure 虚拟机 (VM)，或调整现有 Azure VM 的大小时，经常遇到的错误是分配失败。当群集或区域没有可用的资源或无法支持所请求的 VM 大小时，就会发生此错误。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## 收集审核日志

若要开始故障排除，请收集审核日志，以识别与问题相关的错误。以下链接包含有关该过程的详细信息：

[使用 Azure 门户预览对资源组部署进行故障排除](../azure-resource-manager/resource-manager-troubleshoot-deployments-portal.md)

[使用 Resource Manager 执行审核操作](../azure-resource-manager/resource-group-audit.md)

## 问题：启动已停止的 VM 时发生错误

尝试启动已停止的 VM，但出现分配失败错误。

### 原因

必须在托管云服务的原始群集上尝试发出启动已停止 VM 的请求。但是，群集没有可用的空间来完成该请求。

### 解决方法

*	停止可用性集中的所有 VM 并重新启动每个 VM。

  1. 单击“资源组”> _你的资源组_ >“资源”> _你的可用性集_ >“虚拟机”> _你的虚拟机_ >“停止”。

  2. 所有 VM 停止后，选择每个已停止的 VM 并单击“启动”。

*	稍后重试重新启动请求。

## 问题：调整现有 VM 的大小时发生错误

尝试调整现有 VM 的大小，但出现分配失败错误。

### 原因

必须在托管云服务的原始群集上尝试发出调整 VM 大小的请求。但是，群集不支持请求的 VM 大小。

### 解决方法

* 使用更小的 VM 大小来重试请求。

* 如果无法更改请求的 VM 大小：

  1. 停止可用性集中的所有 VM。

    * 单击“资源组”> _你的资源组_ >“资源”> _你的可用性集_ >“虚拟机”> _你的虚拟机_ >“停止”。

  2. 所有 VM 停止后，将所需的 VM 调整到更大的大小。
  3. 选择已调整大小的 VM，单击“启动”，然后启动每个已停止的 VM。

## 后续步骤

如果你在 Azure 中创建新的 Linux VM 时遇到问题，请参阅[排查在 Azure 中新建 Linux 虚拟机时遇到的部署问题](./virtual-machines-linux-troubleshoot-deployment-new-vm.md)。

<!---HONumber=Mooncake_Quality_Review_1215_2016-->