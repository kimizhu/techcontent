---
title: Service Fabric 群集资源管理器 - 管理集成 | Azure
description: 概述群集资源管理器与 Service Fabric 管理之间的集成点。
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
wacn.date: 11/17/2016
ms.author: masnider
---

# 群集资源管理器与 Service Fabric 群集管理的集成
Service Fabric 群集资源管理器不是负责处理管理操作（如应用程序升级）的主要 Service Fabric 组件，但会参与此类操作。群集资源管理器帮助管理的第一种方式是从资源和均衡的立场跟踪所需群集及其中服务的状态，并且在无法将群集放入所需配置时发出运行状况报告（例如，容量不足时，或者有关服务放置位置的规则发生冲突时）。集成的另一部分与升级的工作方式有关：群集资源管理器将改变其行为。下面我们将讨论这两种情况。

## 运行状况集成
群集资源管理器不断跟踪为服务定义的规则以及群集和节点中可用的容量，并在不满足这些规则或者容量不足时发出运行状况警告和错误。例如，如果节点超出容量并且资源管理器无法更正此情况，则群集资源管理器将发出运行状况警告，指出哪个节点超出容量，以及警告是针对哪些指标。

有关资源管理器何时发出运行状况警告的另一个示例是你定义了放置约束（例如 "NodeColor == Blue"），而资源管理器检测到违反该约束的情况。对于自定义约束以及自动实施的默认约束（例如容错域和升级域约束），我们将采取这两种措施。

下面是此类运行状况报告的示例。在这种情况下，运行状况报告适用于系统服务的分区之一，因为该分区的副本暂时打包成少量的升级域，这类似于连续失败时发生的情况：

	PS C:\Users\User > Get-WindowsFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'

	PartitionId           : 00000000-0000-0000-0000-000000000001
	AggregatedHealthState : Warning
	UnhealthyEvaluations  :
                        	Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

	ReplicaHealthStates   :
                        	ReplicaId             : 130766528804733380
                        	AggregatedHealthState : Ok

                        	ReplicaId             : 130766528804577821
                        	AggregatedHealthState : Ok

                        	ReplicaId             : 130766528854889931
                        	AggregatedHealthState : Ok

                        	ReplicaId             : 130766528804577822
                        	AggregatedHealthState : Ok

                        	ReplicaId             : 130837073190680024
                        	AggregatedHealthState : Ok

	HealthEvents          :
                        	SourceId              : System.PLB
                        	Property              : ReplicaConstraintViolation_UpgradeDomain
                        	HealthState           : Warning
                        	SequenceNumber        : 130837100116930204
                        	SentAt                : 8/10/2015 7:53:31 PM
                        	ReceivedAt            : 8/10/2015 7:53:33 PM
                        	TTL                   : 00:01:05
                        	Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        	violating the Constraint: UpgradeDomain Details: Node -- 3d1a4a68b2592f55125328cd0f8ed477  Policy -- Packing
                        	RemoveWhenExpired     : True
                        	IsExpired             : False
                        	Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM

下面是此运行状况消息指出的情况：

1.	所有副本本身运行状况正常（这是 Service Fabric 的第一要务）
2.	当前违反了升级域分发约束（意味着特定升级域拥有此分区的过多副本）
3.	哪个节点包含造成违规的副本（具有以下 ID 的节点：3d1a4a68b2592f55125328cd0f8ed477）
4.	何时发生这种情形 (8/10/2015 7:13:02 PM)

这是在生产环境中触发的警报的绝佳数据，可让你知道发生了需要调查的问题。例如，在此情况下，我们想要查看是否可以找出资源管理器为何不觉得除了将副本打包到升级域中以外还有任何选择。这可能是因为其他升级域中的所有节点都已关闭，并且没有足够的其他备用域，或者如果有足够的域打开，但其他因素（例如，服务中存在某种放置策略或容量不足）导致这些升级域中的节点无效。

假设你要创建一个服务，或者资源管理器正尝试查找一个用于放置某些服务的位置，但似乎没有任何可行的解决方案。其中的原因很多，但通常不外乎是以下两种情况之一：

1.	某个暂时性状态导致无法正确放置此服务实例或副本
2.	此服务的要求配置不当，以致无法满足其要求。

在每种情况下，都会看见群集资源管理器提供的运行状况报告，其中包含可帮助判断当前状况以及为何无法放置服务的信息。我们将此过程称为“约束消除序列”。在此过程中，系统将逐步了解配置的约束如何影响服务，并记录约束消除的因素。这样，当无法放置服务时，便可以看到哪些节点已被消除及其原因。

## 约束类型
我们讨论可以在这些运行状况报告中看见的各种约束及其检查的内容。请注意，大多数的情况下，不会看到其中的某些约束消除节点，因为约束默认位于软式或优化级别（本文后面内容中提供了约束优先级的相关详细信息）。但是，可以看到与约束有关的运行状况消息，了解它们是否是设置为硬式约束，或它们是否在极少见的情况下确实造成节点被消除，因此此处提供了这些运行状况消息以提供完整信息：

-	ReplicaExclusionStatic 和 ReplicaExclusionDynamic – 这是内部约束，表示在搜索过程中，我们遇到来自相同分区的两个有状态副本，或无状态实例必须放置在相同节点上（不允许这样做）的情况。ReplicaExclusionStatic 和 ReplicaExclusionDynamic 几乎是完全相同的规则。ReplicaExclusionDynamic 约束表示“我们无法将此副本放在此处，因为唯一提议的解决方案已在此处放置一个副本”。这与 ReplicaExclusionStatic 排除不同，后者指出不是提议的冲突，而是实际的冲突 – 节点上已经有一个副本。让人困惑吗？ 是的。这是否很重要？ 不是。只要知道，如果看到的约束消除序列包含 ReplicaExclusionStatic 或 ReplicaExclusionDynamic 约束，群集资源管理器就会认为没有足够的节点可放置所有副本。其他约束通常告诉我们，一开始节点太少会造成哪种结果。
-	PlacementConstraint：如果你看到此消息，则表示我们已消除一些节点，因为它们不符合服务的放置约束。我们在此消息中描绘当前配置的放置约束。如果有提供任何放置约束，这通常就是正常的，但是，如果放置约束中有 bug，造成消除太多节点，就会在此处看到该结果。
-	NodeCapacity：如果你看到此约束，则表示我们无法将副本放在指定的节点上，因为这会导致节点超出容量。
-	Affinity：此约束表示我们无法将副本放在受影响的节点上，因为这会导致违反相关性约束。
-	FaultDomain 和 UpgradeDomain：如果将副本放在指定的节点上会导致副本打包在特定的容错域或升级域中，此约束将消除节点。[容错域与升级域约束及最终行为](./service-fabric-cluster-resource-manager-cluster-description.md)中的主题提供了几个介绍此约束的示例
-	PreferredLocation：通常你看不到这个会导致节点从解决方案中删除的约束，因为该约束默认情况下仅用于优化。此外，首选的位置约束通常只出现在升级期间（用于将副本移回到开始升级时的位置），然而也有可能会出现在其他情况下。

###<a name="constraint-priorities"></a> 约束优先级
在所有约束中，你可能认为：“嘿 – 我认为放置约束在系统中是最重要的。如果能够确保不违反放置约束，我愿意违反其他约束，甚至包括相关性和容量的约束。”

其实我们可以这样做！ 可以使用几个不同级别的强制措施来配置约束，但这些措施都可归结为“硬”(0)、“软”(1)、“优化”(2) 和“关闭”(-1)。默认情况下，我们会将大多数约束定义为硬约束（举例来说，大部分用户通常不会将容量视为可以放宽松的要素），而大多数约束不是硬约束就是软约束。不过，在复杂的情况下，事情可能有所不同。例如，假如想要确保始终违反相关性以解决节点容量问题，可以将相关性约束的优先级设置为“软”(1)，并将容量约束保持设置为“硬”(0)。不同的约束优先级也是你为何经常看到某些约束违规警告的原因 - 因为我们愿意暂时让某些约束保持宽松（违规）。请注意，这些级别并不是真正表示始终或绝对不会违反给定的约束，只是指定了偏好的强制实施顺序，因此当无法满足所有约束时，我们可以做出适当的取舍。

下面列出了不同约束的配置和默认优先级值：

ClusterManifest.xml

        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>

在此处可以发现，有针对升级域和容错域定义的约束，升级域约束为“软”约束。此外，还有一个奇怪的具有优先级的“PreferredLocation”约束。这是什么？

首先，我们要将服务分散在容错域与升级域之间作为 Resource Manager 引擎内的约束的想法建模。在过去的某些时候，我们需要严格规定容错域与升级域的位置，而在某些情况下出现某种问题时，我们完全可以忽略其位置（虽然是暂时性的），因此一般而言，我们很高兴能够在设计上做出选择并且约束基础结构具有弹性。大多数情况下，升级域约束都是软约束，也就是说，如果资源管理器需要暂时将一些副本打包到容错域或升级域进行处理，例如在升级过程中，或者出现一些并发失败或其他约束违规（对于硬约束），那么，这就不会出现问题。这种情况通常不会发生，除非系统中发生的许多失败或其他扰乱因素阻止正确放置。如果已正确配置环境，则稳定的状态始终是完全遵循升级域。

PreferredLocation 约束稍有不同，因此它是唯一设置为“Optimization”的约束。我们将在升级过程中使用此约束，以尝试将服务放回到升级之前它们所在的位置。在实践中，有许多不同的原因会导致这种做法不可行，但优化的效果仍然不错，因此我们使用了此约束。我们将在讨论群集资源管理器如何帮助升级时详细介绍此约束

## 升级
在应用程序和群集升级期间，群集资源管理器也有助于确保成功升级，同时确保规则和群集性能不受到影响。

### 保持强制实施规则
规则是需要注意的重点 – 在升级期间仍强制实施一些严格控制（如放置约束）。你可能认为这是理所当然的，但我们仍要做出澄清。这样做的好处是如果你想要确保特定工作负荷不在特定的节点上运行，则即使是在升级过程中，也会自动强制实施这些规则。如果环境高度受约束，这会导致升级花费很长时间，因为如果服务（或它所在的节点）需要关闭进行更新，则服务只有少数几个选项可用。

### 智能替换
开始升级时，资源管理器将获取当前群集排列方式的快照，并尝试返回可陈述何时完成升级的信息。幕后的原因很简单 – 首先它确保在升级过程中每个服务副本或实例只有一些转换（将受影响的节点移出，然后移回）。然后，它确保升级本身对于群集的布局没有太大影响；如果在升级前就已安排好群集，则升级后也会安排妥当，至少不会让事情变得更糕。

### 降低流动
升级期间还会发生另一种情况，那就是群集资源管理器对正在升级的实体关闭平衡。因此，如果你有两个不同的应用程序实例并在其中一个实例上开始升级，则该应用程序实例的平衡将会暂停，但另一个不会。阻止反应式平衡可防止对升级本身做出不必要的反应（“哎呀！ 这是一个空节点！ 最好在其中填充各种内容！”），因此会阻止群集中的服务额外进行大量的移动，而这种移动必须在完成升级后服务需要移回节点时撤消。如果有问题的升级是群集升级，则整个群集在升级期间将暂停均衡（约束检查 – 确保强制实施规则 – 保持活动，只禁用主动重新均衡）。

### 宽松规则
在升级期间，即使群集整体相当受限或已满，通常你还是想要完成升级。在升级期间能够管理群集容量甚至比群集升级期间通常有 5% 到 20% 的容量关闭更为重要，该工作负荷通常必须转到某个位置。这就是[缓冲容量](./service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity)概念真正派上用场的地方 – 尽管在正常操作期间遵守缓冲容量（留出一些可用空间），但群集资源管理器将在升级期间填满总容量（占用缓冲区）。

## 后续步骤
- 参阅 [Service Fabric 群集资源管理器简介](./service-fabric-cluster-resource-manager-introduction.md)，帮助自己入门

<!---HONumber=Mooncake_1017_2016-->