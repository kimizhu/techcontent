---
title: 使用 Azure 导入/导出工具 - v1 | Azure
description: 了解如何使用导入/导出工具为导入作业准备硬盘驱动器，以修复导入作业或导出作业。
author: renashahmsft
manager: aungoo
editor: tysonn
services: storage
documentationcenter: ''

ms.assetid: f77535bb-d577-438a-bdd3-e15a82e0c543
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
wacn.date: 12/29/2016
ms.author: renash
---

# 使用 Azure 导入/导出工具 

使用 Azure 导入/导出工具 (WAImportExport.exe) 可以创建和管理 Azure 导入/导出服务的作业，将大量数据传入或传出 Azure Blob 存储。

本文档适用于最新版本的 Azure 导入/导出工具。有关该工具的 v1 版本的用法信息，请参阅[使用 Azure 导入/导出工具 v1](./storage-import-export-tool-how-to-v1.md)。

以下文章介绍了如何使用该工具实现以下目的：

- 安装和设置导入/导出工具。
- 为作业准备硬盘驱动器，以便将数据从驱动器导入 Azure Blob 存储。
- 使用复制日志文件查看作业状态。
- 修复导入作业。
- 修复导出作业。
- 排查在使用 Azure 导入/导出工具的过程中遇到的问题。

<!---HONumber=Mooncake_1226_2016-->