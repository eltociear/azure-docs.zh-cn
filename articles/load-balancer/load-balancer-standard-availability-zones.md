---
title: Azure 标准负载均衡器和可用性区域
titleSuffix: Azure Load Balancer
description: 使用此学习路径，开始使用 Azure 标准负载均衡器和可用性区域。
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 5a65982c5c13eb4e4273efcfd8d14910b0f35572
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197141"
---
# <a name="standard-load-balancer-and-availability-zones"></a>标准负载均衡器和可用性区域

Azure 标准负载均衡器支持[可用性区域](../availability-zones/az-overview.md)方案。 您可以使用标准负载均衡器通过将资源与区域对齐并跨区域分发来优化端到端方案中的可用性。  查看[可用性区域](../availability-zones/az-overview.md)，了解什么是可用性区域、当前支持哪些区域以及其他相关概念和产品。 可用性区域与标准负载均衡器相结合，是一个广泛而灵活的功能集，可以创建许多不同的方案。  请查看本文档了解这些[概念](#concepts)和基本场景的[设计指南](#design)。

## <a name="availability-zones-concepts-applied-to-load-balancer"></a><a name="concepts"></a> 适用于负载均衡器的可用性区域概念

负载均衡器资源本身是区域性的，而永远不会是局域性的。 可以配置的内容的粒度受前端、规则和后端池定义的每个配置的约束。
在可用性区域的上下文中，负载均衡器规则的行为和属性被描述为区域冗余或区域。  区域冗余和局域性描述属性的局域性。  在负载均衡器的上下文中，区域冗余始终意味着*多个区域*，区域意味着将服务隔离到*单个区域*。
公共负载均衡器和内部负载均衡器都支持区域冗余和局域性场景，并且两者都可根据需要跨区域定向流量（跨区域负载均衡）。** 

### <a name="frontend"></a>前端

负载均衡器前端是一种前端 IP 配置，用于引用虚拟网络资源子网中的公共 IP 地址资源或专用 IP 地址。  它构成了公开服务的负载均衡终结点。
负载均衡器资源可以同时包含具有区域和区域冗余前端的规则。 当公共 IP 资源或专用 IP 地址已保证到区域时，区域区域（或缺少其）不可改变。  如果要更改或省略公共 IP 或专用 IP 地址前端的区位，则需要在适当的区域中重新创建公共 IP。  可用性区域不会更改多个前端的约束，请查看[负载均衡器的多个前端](load-balancer-multivip-overview.md)，了解此功能的详细信息。

#### <a name="zone-redundant"></a>区域冗余 

在具有可用性区域的区域中，标准负载均衡器前端可以是区域冗余。  区域冗余意味着所有入站或出站流都由区域内的多个可用性区域同时使用单个 IP 地址提供服务。 不需要 DNS 冗余方案。 单个前端 IP 地址可以经受住区域故障，并可用于覆盖所有（未受影响的）后端池成员，而不考虑区域。 一个或多个可用性区域可能会失败，只要区域中的一个区域保持正常，数据路径即可生存。 前端的单 IP 地址由多个可用性区域中的多个独立基础结构部署同时提供。  这并不意味着无命中数据路径，但任何重试或重建都将在不受区域故障影响的其他区域中成功。   

#### <a name="optional-zone-isolation"></a>可选区域隔离

可以选择将某个前端指定为保证位于单个区域，这称为局域性前端。**  这意味着，任何入站或出站流将由某个区域中的单个局部区域提供。  前端的运行状态取决于该局部区域。  区域（保证前端所在的区域除外）中发生故障不会影响数据路径。 可以使用局域性前端来按可用性区域公开 IP 地址。  

此外，您可以直接为每个区域内的负载平衡端点使用区域前端。 还可以使用此前端来按区域负载均衡终结点公开 IP 地址，以单独监视每个区域。  或者对于公共终结点，您可以将它们与 DNS 负载平衡产品（如[流量管理器](../traffic-manager/traffic-manager-overview.md)）集成，并使用单个 DNS 名称。 然后，客户端将解析为此 DNS 名称到多个区域 IP 地址。  

如果想要混合使用这些概念（对同一个后端使用区域冗余和局域性配置），请查看 [Azure 负载均衡器的多个前端](load-balancer-multivip-overview.md)。

对于公共负载均衡器前端，向相应规则使用的前端 IP 配置引用的公共 IP 资源添加*区域*参数。

对于内部负载均衡器前端，需将 *zones* 参数添加到内部负载均衡器前端 IP 配置。 局域性前端能使负载均衡器保证某个子网中的 IP 地址位于特定的区域。

### <a name="cross-zone-load-balancing"></a>跨区域负载均衡

跨区域负载均衡是指负载均衡器能够访问任何区域中的后端终结点，并且独立于前端及其局域性。  任何负载平衡规则都可以在任何可用性区域或区域实例中定位后端实例。

您需要注意以表示可用性区域概念的方式构建方案。 例如，您需要保证在单个区域或多个区域内部署虚拟机，并将区域前端和区域后端资源对齐到同一区域。  如果仅具有区域资源，则方案将工作，但相对于可用性区域可能没有明确的故障模式。 

### <a name="backend"></a>后端

负载均衡器适用于虚拟机实例。  这些可以是独立、可用性集或虚拟机扩展集。  单个虚拟网络中的任何虚拟机实例都可以成为后端池的一部分，而不管它是否被保证为区域或哪个区域得到保证。

如果要将前端和后端与单个区域对齐并保证，则仅将虚拟机放在同一区域中，以放入相应的后端池中。

如果要跨多个区域解决虚拟机问题，只需将来自多个区域的虚拟机放入同一后端池即可。  使用虚拟机规模集时，可将一个或多个虚拟机规模集放入同一后端池。  其中的每个虚拟机规模集可以位于一个或多个区域。

### <a name="outbound-connections"></a>出站连接

相同的区域冗余和区域属性适用于[出站连接](load-balancer-outbound-connections.md)。  所有区域都提供用于出站连接的区域冗余公共 IP 地址。 区域公共 IP 地址仅由保证在其中的区域提供服务。  出站连接 SNAT 端口分配在区域故障中幸存，如果不受区域故障影响，您的方案将继续提供出站 SNAT 连接。  如果受影响的区域提供了流，则可能需要传输或为区域冗余方案重新建立连接。  受影响区域以外的区域中的流不受影响。

SNAT 端口预分配算法在可用区域或可用区域时相同。

### <a name="health-probes"></a>运行状况探测

现有运行状况探测定义将保持现有无可用区域状态。  但是，我们已经在基础结构级别扩展了运行状况模型。 

使用区域冗余前端时，Load 平衡器扩展其内部运行状况模型，以独立探测虚拟机从每个可用性区域的可访问性，并关闭可能在未经客户干预的情况下出现故障的区域之间的路径。  如果给定路径从一个区域的负载均衡器基础结构到另一个区域中的虚拟机不可用，负载均衡器可以检测并避免此故障。 可以访问此 VM 的其他区域可继续从其各自的前端为此 VM 提供服务。  因此，在故障事件期间，每个区域可能具有略有不同的新流分布，同时保护端到端服务的整体运行状况。

## <a name="design-considerations"></a><a name="design"></a>设计注意事项

负载均衡器在可用性区域上下文中具有特意的灵活性。 您可以选择与区域对齐，也可以为每个规则选择区域冗余。  提高可用性的代价可能是增大复杂性，必须经过精心设计才能获得最佳性能。  让我们了解一些重要的设计注意事项。

### <a name="automatic-zone-redundancy"></a>自动区域冗余

使用负载均衡器可以轻松地将一个 IP 用作区域冗余的前端。 区域冗余的 IP 地址能够安全地为任何区域中的局域性资源提供服务，并且可以承受一个或多个区域的故障，前提是区域中有一个局部区域保持正常。 相反，局域性前端是在单个区域中部署服务的简化设计，其命运与相应的区域相同。

区域冗余并不意味着数据路径或控制平面不可访问；它是明确的数据平面。 区域冗余流可以使用任何局部区域，客户流使用区域中所有正常的局部区域。 出现区域故障时，在该时间点使用正常区域的流量流不受影响。  在区域故障时使用区域的流量可能会受到影响，但应用程序可以恢复。 一旦 Azure 在区域故障附近聚集，这些流可以在重新传输或重新建立时在区域内的剩余正常区域中继续。

### <a name="cross-zone-boundaries"></a><a name="xzonedesign"></a>跨区域边界

请务必了解，每当端到端服务跨区域时，运行状况并不是取决于一个区域，而可能是多个区域。  因此，端到端服务可能无法通过非局域性部署获得任何可用性。

避免引入意外的跨区域依赖关系，这将在使用可用性区域时取消可用性提升。  如果应用程序由多个组件构成，而你希望能够从区域故障中恢复，则必须慎重确保在发生区域故障时，有足够的关键组件能够幸存。  例如，如果应用程序的单个关键组件只位于除幸存区域以外的其他某个区域，则该组件可能会影响整个应用程序。  另外，请考虑区域还原和应用程序的聚合方式。 您需要了解应用程序对部分失败的原因。 让我们探讨几个要点，并在研究特定的场景时，将其用作解答问题的灵感。

- 如果应用程序有两个组件，如 IP 地址和具有托管磁盘的虚拟机，并且它们在区域 1 和区域 2 中得到保证，则当区域 1 发生故障时，当区域 1 发生故障时，端到端服务将不能生存。  除非您完全了解正在创建具有潜在危险的故障模式，否则不要跨区域与区域方案。  此方案允许提供灵活性。

- 如果应用程序有两个组件，如 IP 地址和具有托管磁盘的虚拟机，并且它们分别保证为区域冗余和区域 1，则端到端服务将生存区域 2、区域 3 或两者的区域故障，除非区域 1 发生故障。  但是，如果要观察的功能只是前端的可访问性，则在一定程度上无法推测服务的运行状况。  请考虑开发一个涵盖范围更广泛的运行状况和容量模型。  可以结合使用区域冗余和局域性的概念来拓宽见解和可管理性。

- 如果应用程序在三个区域中包含两个组件（例如区域冗余的负载均衡器前端和跨区域的虚拟机规模集），则区域中不受故障影响的资源将保持可用，但在发生区域故障期间，端到端服务的容量可能会下降。 从基础结构的角度看，部署可以在一个或多个区域发生故障时幸存。这就引出了以下问题：
  - 是否了解应用程序如何推测此类故障和容量下降的原因？
  - 是否需要在服务中采取防护措施，以便在必要时强制故障转移到某个区域对？
  - 如何监视、检测和缓解此类场景？ 有时可以使用标准负载均衡器诊断功能来增强端到端服务性能的监视。 全方位地考虑哪个服务会保持可用，哪个服务可能需要增强的监视。

- 使用区域可以更轻松地识别和遏制故障。  但是，在涉及到超时、重试和退让算法等概念时，区域故障与其他故障没有不同。 即使 Azure 负载均衡器提供区域冗余的路径并且会快速尝试恢复，但在实时数据包级别看，重新传输或重新建立可能在故障初期发生，因此，必须了解应用程序应对故障的方式。 负载均衡方案将会幸存，但需要规划好以下方面：
  - 发生区域故障时，端到端服务是否能够识别此故障；如果状态丢失，应如何恢复？
  - 当区域恢复正常时，应用程序是否知道如何安全聚合？

查看[Azure 云设计模式](https://docs.microsoft.com/azure/architecture/patterns/)以提高应用程序对故障方案的恢复能力。

## <a name="next-steps"></a>后续步骤
- 了解有关[可用性区域](../availability-zones/az-overview.md)的更多信息
- 了解有关[标准负载均衡器](load-balancer-standard-overview.md)的更多
- 了解如何[使用具有区域性前端的标准负载均衡器在区域内对 VM 进行负载均衡](load-balancer-standard-public-zonal-cli.md)
- 了解如何[使用具有区域冗余前端的标准负载均衡器跨区域对 VM 进行负载均衡](load-balancer-standard-public-zone-redundant-cli.md)
- 了解[Azure 云设计模式](https://docs.microsoft.com/azure/architecture/patterns/)，以提高应用程序对故障方案的恢复能力。
