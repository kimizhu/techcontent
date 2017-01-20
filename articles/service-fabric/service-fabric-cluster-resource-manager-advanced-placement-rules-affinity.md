---
title: Service Fabric 群集资源管理器 - 相关性 | Azure
description: 概述如何配置 Service Fabric 服务的相关性
services: service-fabric
documentationCenter: .net
authors: masnider
manager: timlt
editor: 

ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
wacn.date: 10/24/2016
ms.author: masnider
---

# 在 Service Fabric 中配置和使用服务相关性

相关性是一个控件，主要用于帮助简化将已存在且较大型的单体式应用程序转换到云和微服务领域。也就是说，在某些情况下它也可以用作提升服务性能的合法优化，不过这可能会产生一定的副作用。

假设你正在将某个较大型应用，或者在设计时未将微服务纳入考虑的应用引入 Service Fabric。这种过渡实际上很常见，我们曾经有多个客户（内部和外部）处于这种局面。首先将整个应用提升到该环境，将它打包并运行。然后，开始将其分解成不同的、可彼此通信的较小服务。

然后就是“惊叹”。“惊叹”通常是由下列原因之一导致的：

1. 单体式应用中的某个组件 X 原本与我们刚刚转换成独立服务的组件 Y 之间具有未记录的依赖关系。由于这些组件现在群集中不同的节点上运行，因此这种依赖关系已破坏。
2.	这些组件通过（本地命名管道 | 共享内存 | 磁盘上的文件）进行通信，但我确实需要能够独立更新它，以便能够更快地结束任务。稍后我将删除硬依赖性。
3.	一切都没问题，但事实上，这两个组件之间的对话非常频繁且性能敏感。将它们移到独立的服务时，整体应用程序性能将受到严重影响，或者延迟会增大。因此，整个应用程序不符合预期。

在这种情况下，我们不想要失去重构工作，也不想回到单体结构，但确实需要某种意义上的定位。这种情况会持续到可以重新设计组件，使其像服务一样自然运行，或持续到可以通过其他某种方式达到性能预期（如果可能）为止。

怎么办？ 嗯，可以尝试启用相关性。

## 如何配置相关性
若要设置相关性，可以定义两个不同服务之间的相关性关系。可以将相关性想象成在一个服务上“指向”另一个服务，同时假设“这个服务只有在那个服务正在运行时才能运行”。 有时我们将这种相关性称为父子关系（将子级指向父级）。相关性确保将一个服务的副本或实例放置在与另一个服务的副本或实例相同的节点上。

    ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
    affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
    affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
    serviceDescription.Correlations.Add(affinityDescription);
    await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);

## 不同的相关性选项
相关性通过多种相互关联的架构之一来表示，有两种不同的模式。相关性的最常见模式是所谓的 NonAlignedAffinity 模式。在 NonAlignedAffinity 下，不同服务的副本或实例放在同一个节点上。另一种模式是 AlignedAffinity。对齐的相关性仅适用于有状态服务。配置两个有状态服务实现对齐的相关性，可确保这些服务的主副本与其他服务的主副本位于相同的节点上。它还能确保这些服务的每个辅助副本对位于相同的节点上。也可以针对有状态服务配置 NonAlignedAffinity（但不太常见）。使用 NonAlignedAffinity 时，会将两个有状态服务的不同副本共置在相同的节点上，但不尝试对齐它们的主副本或辅助副本。

![相关性模式及其影响][Image1]  

### 尽力而为的所需状态
相关性与单体式体系结构之间有一些差异。产生这些差异的原因有许多是因为相关性关系是以尽力而为的方式获取。具有相关性关系的服务是本质上不同的实体，可能失败或者被单独移动。还有一些原因会破坏相关性关系。例如，相关性关系中只有某些服务对象的容量限制能够适用于给定的节点。在这种情况下，即使有相关性关系，但由于存在其他限制，也无法强制实施这种关系。如果以后可以强制实施所有其他约束和相关性，则会自动更正相关性约束违规。

### 链形与星形
我们目前无法创建相关性关系的模型链。这意味着，如果有一个服务是某一个相关性关系中的子级，则该服务不能是另一个相关性关系中的父级。如果想要为这种关系的建模，需要有效地将它建模为星形而不是链形。为此，请改为将最下层子级的父级设置为“中间”子级的父级。根据服务的排列方式，这可能需要创建“占位符”服务作为多个子项的父项。

![相关性关系上下文中的链形与星形][Image2]  

目前关于相关性关系的另一个要注意事项是它们是双向的。这意味着，“相关性”规则只强制子级就是父级的所在之处。例如，如果父级突然故障转移到另一个节点，则在群集资源管理器注意到子级不是与父级位于相同位置之前，实际上不觉得有任何不妥之处；关系不立即强制实施。

### 分区支持
对于相关性，要注意的最后一点是，不支持分区父级的相关性关系。我们最终可能会支持这一点，但目前并不允许。

## 后续步骤
- 有关可用于配置服务的其他选项的详细信息，请查看 [Learn about configuring Services](./service-fabric-cluster-resource-manager-configure-services.md)（了解如何配置服务）中提供的其他群集资源管理器配置的相关主题
- 用户使用相关性的许多原因（例如将服务限制于少量的计算机，以及尝试聚合服务集合的负载）可以通过应用程序组获得更好的支持。请查看 [Application Groups](./service-fabric-cluster-resource-manager-application-groups.md)（应用程序组）

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png

<!---HONumber=Mooncake_1017_2016-->