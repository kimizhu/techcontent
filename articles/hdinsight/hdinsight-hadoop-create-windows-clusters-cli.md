---
title: 使用 Azure CLI 在 HDInsight 中创建基于 Windows 的 Hadoop 群集
description: 了解如何使用 Azure CLI 创建 Azure HDInsight 群集。
services: hdinsight
documentationCenter: 
tags: azure-portal
authors: mumian
manager: paulettm
editor: cgronlun

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
wacn.date: 12/16/2016
ms.author: jgao
---

# 使用 Azure CLI 在 HDInsight 中创建基于 Windows 的 Hadoop 群集

[!INCLUDE [选择器](../../includes/hdinsight-selector-create-clusters.md)]

[!INCLUDE [azure-sdk-developer-differences](../../includes/azure-sdk-developer-differences.md)]

了解如何使用 Azure CLI 创建 HDInsight 群集。有关其他群集创建工具和功能，请单击本页面顶部的相应选项卡，或参阅[群集创建方法](./hdinsight-provision-clusters-v1.md#cluster-creation-methods)。

###先决条件：

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

在开始按照本文中的说明操作前，必须具备以下条件：

- **Azure 订阅**。请参阅[获取 Azure 试用版](https://www.azure.cn/pricing/1rmb-trial/)。
- **Azure CLI** - 有关安装和配置信息，请参阅[安装和配置 Azure CLI](../xplat-cli-install.md)。

##连接到 Azure

使用以下命令连接到 Azure：

        azure config mode asm
        azure login -e AzureChinaCloud

    > [!NOTE] 如果想用 Azure CLI 管理 Azure 中国的 HDInsight 群集，请安装 Azure CLI 0.9.x，而不是最新的 0.10.x.

要获取帮助，请使用 **-h** 开关。例如：

    azure hdinsight cluster create -h
    
##创建群集

必须先获取 Azure Blob 存储帐户，然后才能创建 HDInsight 群集。要创建 HDInsight 群集，必须指定以下信息：

- **HDInsight 群集名称**

- **位置**：支持 HDInsight 群集的 Azure 数据中心之一。有关支持位置的列表，请参阅 [HDInsight 定价](https://www.azure.cn/pricing/details/hdinsight/)。

- **默认存储帐户**：HDInsight 使用 Azure Blob 存储容器作为默认文件系统。需要先拥有 Azure 存储帐户，然后才能创建 HDInsight 群集。

    要创建新 Azure 存储帐户：
    
        azure storage account create "<Azure Storage Account Name>" -l "<Azure Location>" --type LRS

    > [!NOTE] 存储帐户必须与 HDInsight 共置于同一数据中心。存储帐户类型不能为 ZRS，因为 ZRS 不支持表。

    有关使用 Azure 经典管理门户创建 Azure 存储帐户的信息，请参阅 [创建、管理或删除存储帐户][azure-create-storageaccount]。
    
    如果已拥有存储帐户但不知道帐户名称和帐户密钥，可使用以下命令检索相关信息：
    
        -- Lists Storage accounts
        azure storage account list
        -- Shows a Storage account
        azure storage account show "<Storage Account Name>"
        -- Lists the keys for a Storage account
        azure storage account keys list "<Storage Account Name>"

    有关使用 Azure 经典管理门户获取信息的详细信息，请参阅 [创建、管理或删除存储帐户][azure-create-storageaccount] 中的“查看、复制和重新生成存储访问密钥”部分。

- **(可选)默认 Blob 容器**：如果容器不存在，可使用 **azure hdinsight cluster create** 命令创建。如果选择预先创建容器，可以使用以下命令：

    azure storage container create --account-name "<Storage Account Name>" --account-key <Storage Account Key> [ContainerName]

准备好存储帐户后，即可创建群集：

    azure hdinsight cluster create -c <HDInsight Cluster Name> -l <Location> --osType Windows --version <Cluster Version> --clusterType <Hadoop | HBase | Spark | Storm> --workerNodeCount 2 --headNodeSize Large --workerNodeSize Large --defaultStorageAccountName <Azure Storage Account Name>.blob.core.chinacloudapi.cn --defaultStorageAccountKey "<Default Storage Account Key>" --defaultStorageContainer <Default Blob Storage Container> --userName admin --password "<HTTP User Password>" --rdpUserName <RDP Username> --rdpPassword "<RDP User Password" --rdpAccessExpiry "03/01/2016"

## 另请参阅

- [Azure HDInsight 入门](./hdinsight-hadoop-tutorial-get-started-windows-v1.md) - 了解如何开始使用 HDInsight 群集
- [以编程方式提交 Hadoop 作业](./hdinsight-submit-hadoop-jobs-programmatically.md) - 了解如何以编程方式将作业提交到 HDInsight
- [使用 Azure CLI 管理 HDInsight 中的 Hadoop 群集](./hdinsight-administer-use-command-line.md)
- [将适用于 Mac、Linux 和 Windows 的 Azure CLI 与 Azure 服务管理配合使用](../virtual-machines-command-line-tools.md)

<!---HONumber=Mooncake_Quality_Review_1202_2016-->