<properties
   pageTitle="资源管理器体系结构 | Azure"
   description="Service Fabric 群集资源管理器的体系结构概述。"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>  


<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   wacn.date="01/17/2017"
   ms.author="masnider"/>  


# 群集资源管理器体系结构概述
为了管理群集中的资源，Service Fabric 群集资源管理器必须包含一些相关的信息。它必须了解目前存在哪些服务，以及这些服务正在使用的资源的当前（默认）数量。同时必须了解群集中节点的实际容量，以及可整体用于群集中和特定节点中剩余可用的资源数量。给定服务的资源消耗量可随时间更改，服务实际上通常关注多个资源。在许多不同的服务中，可能有实际资源被测量并报告，例如内存与磁盘耗用量等指标，以及（实际上更常见的）逻辑指标 - 例如“WorkQueueDepth”或“TotalRequests”等参数。逻辑和实际指标可能跨许多不同类型的服务使用，或可能仅特定对几项服务使用。

## 其他注意事项
群集所有者与操作者偶尔与服务作者不同，或者，至少是带着不同帽子的同一组人，例如，在开发服务时，了解一些与资源相关的必要事项，以及部署不同组件的理想方式，但是身为要在生产环境中为该服务处理实时站点事件的人员，有其他要执行的作业且需要不同的工具。此外，群集和服务本身都不是以静态方式设置的：群集中的节点数目可以放大和缩小、不同大小的节点可以进出，并且服务可以即时更改其资源分配，以及创建和删除。升级或其他管理操作可以在整个群集中运行，当然事情可能随时失败。

## 群集资源管理器组件和数据流
群集资源管理器将必须知道和整体群集有关的许多事项，以及独立服务的要求和组成服务的无状态实例或有状态副本的要求。为实现此目的，我们拥有群集资源管理器的代理，它们在单独的节点上运行以聚合本地资源耗用信息，集中式容错群集资源管理器服务聚合关于服务和群集的所有信息，并根据其当前配置来反应。群集资源管理器服务（和所有其他系统服务）的容错是通过和我们用于服务时完全相同的机制来实现，也就是使用服务状态的复制来仲裁群集中某些数目的副本（通常为 7）。

![资源平衡器体系结构][Image1]  


让我们以上图作为示例。在运行时，有一大堆可能发生的更改：例如，假设服务消耗的资源数量中有一些更改、有些服务失败、有些节点添加或离开群集等更改。特定节点上的所有更改进行汇总，并定期发送到群集资源管理器服务（1，2），它们在其中再次聚合、分析和存储。每隔几秒钟，服务就查看所有更改，并确定是否需要任何操作 (3)。例如，它可能注意到节点已添加到群集且是空的，并确定要将某些服务移到这些节点。它可能也注意到特定节点是超载的，或者某些服务已失败（或删除），在其他节点上释放资源。

让我们看看下一张图表，了解其中发生的情况。假设群集资源管理器确定需要更改。它与其他系统服务（尤其是故障转移管理器）进行协调，以进行必要的更改。然后将更改请求发送到适当的节点 (4)。在此方案中，我们假设资源管理器注意到节点 5 已超载，因此确定要将服务 B 从 N5 移到 N4。重新配置 (5) 结束时，群集看起来像这样：

![资源平衡器体系结构][Image2]

## 后续步骤
- 群集资源管理器提供许多用于描述群集的选项。若要详细了解这些选项，请查看这篇有关[描述 Service Fabric 群集](/documentation/articles/service-fabric-cluster-resource-manager-cluster-description/)的文章

[Image1]: ./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]: ./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png

<!---HONumber=Mooncake_Quality_Review_0117_2017-->