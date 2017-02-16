---
title: 使用 Service Fabric 群集资源管理器配置服务 | Azure
description: 通过指定指标、放置约束和其他放置策略来描述 Service Fabric 服务。
services: service-fabric
documentationCenter: .net
authors: masnider
manager: timlt
editor: ''

ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
wacn.date: 01/17/2017
ms.author: masnider
---

# 配置 Service Fabric 服务的群集资源管理器设置
Service Fabric 群集资源管理器可让你非常精细地控制用于管控每个命名服务的规则。每个命名服务实例都可以指定应如何在群集中进行分配的规则，并且可定义一组它想要报告的指标，包括它们对于该服务的重要程度。配置服务的过程通常划分为三个不同的任务：

1. 配置放置约束
2. 配置指标
3. 配置高级放置策略（不太常见）

让我们依次讨论每项任务：

## 放置约束
放置约束可用来控制服务实际可在群集中的哪些节点上运行。我们通常看到特定的命名服务实例或受限于在特定类型节点上运行的类型的所有服务。也就是说，放置约束是可扩展的 - 可以根据节点类型定义任意的属性组合，然后在创建服务时，利用约束来选择它们。放置约束还会在服务生存期内动态更新，让你响应群集中的更改。还可以在群集中动态更新给定节点的属性。有关放置约束以及如何对其进行配置的详细信息，请参阅[此文](./service-fabric-cluster-resource-manager-cluster-description.md#placement-constraints-and-node-properties)

## 指标
指标是给定命名服务实例所需的资源集，包括该服务的每个有状态副本或无状态实例默认消耗该资源的数量相关信息。指标还包括一个权重，表示该指标对于服务的重要程度，以便于权衡利弊。

## 其他放置规则
还有其他类型的放置规则，主要用于地理分散的群集或其他较少见的方案。这些规则通过关联或策略来配置。尽管许多方案不使用这些规则，但出于完整性考虑，我们还是介绍了这些规则。

## 后续步骤
- 指标是 Service Fabric 群集资源管理器在群集中管理消耗和容量的方式。若要详细了解指标及其配置方式，请查看[此文](./service-fabric-cluster-resource-manager-metrics.md)
- 相关性是可以针对服务配置的一种模式。它并不常用，但如果需要，可以参阅[此处](./service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- 可以在服务上配置许多不同的放置规则以处理其他方案。可以在[此处](./service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)了解这些不同的放置策略
- 参阅 [Service Fabric 群集资源管理器简介](./service-fabric-cluster-resource-manager-introduction.md)，帮助自己入门
- 若要了解群集资源管理器如何管理和均衡群集中的负载，请查看有关[均衡负载](./service-fabric-cluster-resource-manager-balancing.md)的文章
- 群集资源管理器提供许多用于描述群集的选项。若要详细了解这些选项，请查看这篇有关[描述 Service Fabric 群集](./service-fabric-cluster-resource-manager-cluster-description.md)的文章

<!---HONumber=Mooncake_Quality_Review_0117_2017-->