---
title: 指定指标和放置设置
description: 了解如何通过指定指标、放置约束和其他放置策略来描述 Service Fabric 服务。
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: d4dcd319000edb204ba188ed14b4c797dba5cd38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610091"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>配置 Service Fabric 服务的群集 Resource Manager 设置
使用 Service Fabric 群集资源管理器可以精细地控制用于管控每个命名服务的规则。 每个命名服务均可为它在群集中的分配方式指定规则。 每个命名服务还可以定义要报告的指标集，包括指标集对该服务的重要程度。 配置服务的过程分为 3 个不同的任务：

1. 配置放置约束
2. 配置指标
3. 配置高级放置策略和其他规则（不太常见）

## <a name="placement-constraints"></a>放置约束
放置约束可用来控制服务实际可在群集中的哪些节点上运行。 通常为特定的命名服务实例或受限于在特定类型节点上运行的类型的所有服务。 放置约束是可扩展的。 创建服务时，可以按照节点类型定义任何属性组，然后为其选择约束。 运行时，还可以更改服务的放置约束。 这样，可以对群集或服务要求的更改进行响应。 还可以在群集中动态更新给定节点的属性。 有关放置约束以及如何对其进行配置的详细信息，请参阅[此文](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>指标
指标是给定命名服务需要的资源组。 服务的指标配置包括默认情况下，该服务的每个有状态副本或无状态实例消耗多少资源。 指标还包括一个权重，表示均衡该指标对于服务的重要程度，以便于权衡利弊。

## <a name="advanced-placement-rules"></a>高级放置规则
其他类型的放置规则在较不常见的方案中有用。 一些示例如下：
- 有助于在地理上分发群集的约束
- 某些应用程序体系结构

其他放置规则通过关联或策略来配置。

## <a name="next-steps"></a>后续步骤
- 指标是 Service Fabric 群集资源管理器在群集中管理消耗和容量的方式。 要了解有关指标以及如何配置指标的更多[，请查看本文](service-fabric-cluster-resource-manager-metrics.md)
- 相关性是可以针对服务配置的一种模式。 它并不常用，但如果需要，可以参阅[此处](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- 可以在服务上配置许多不同的放置规则以处理其他方案。 可以在[此处](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)了解这些不同的放置策略
- 从头开始并[获取 Service Fabric 群集 Resource Manager 简介](service-fabric-cluster-resource-manager-introduction.md)
- 若要了解群集 Resource Manager 如何管理和均衡群集中的负载，请查看有关[平衡负载](service-fabric-cluster-resource-manager-balancing.md)的文章
- 群集 Resource Manager 提供许多用于描述群集的选项。 要了解有关它们的更多，请查看本文介绍[服务结构群集](service-fabric-cluster-resource-manager-cluster-description.md)
