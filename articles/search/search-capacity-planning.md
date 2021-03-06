---
title: 调整查询和索引工作负载的容量
titleSuffix: Azure Cognitive Search
description: 在 Azure 认知搜索中调整分区和副本计算机资源，其中每个资源按照可计费搜索单位定价。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 270ff3c3e8e4cffbb1f4b1987ee497530d0c0982
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546260"
---
# <a name="adjust-capacity-in-azure-cognitive-search"></a>调整 Azure 认知搜索中的容量

[在预配搜索服务和](search-create-service-portal.md)锁定特定定价层之前，请花几分钟时间了解服务中副本和分区的角色，以及如何调整服务以适应资源需求的峰值和下降。

容量是[您选择的层](search-sku-tier.md)（层确定硬件特征）和投影工作负载所需的副本和分区组合的函数。 根据层和调整的大小，添加或减少容量可能需要 15 分钟到几个小时。 

修改副本和分区的分配时，建议使用 Azure 门户。 门户对低于层的最大限制的允许组合实施限制。 但是，如果需要基于脚本或基于代码的预配方法[，Azure PowerShell](search-manage-powershell.md)或[管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/services)是替代解决方案。

## <a name="terminology-replicas-and-partitions"></a>术语：副本和分区

|||
|-|-|
|*分区* | 为读/写操作（例如在重建或刷新索引时）提供索引存储和 I/O。 每个分区都有总索引的一个共享。 如果分配三个分区，则索引分为三个分区。 |
|*副本* | 是搜索服务的实例，主要用于对查询操作进行负载均衡。 每个副本都是索引的一个副本。 如果分配了三个副本，则将有三个索引副本可用于服务查询请求。|

## <a name="when-to-add-nodes"></a>何时添加节点

最初，服务被分配了由一个分区和一个副本组成的最小级别的资源。 

单个服务必须具有足够的资源才能处理所有工作负荷（索引和查询）。 两个工作负载都没有在后台运行。 您可以安排查询请求自然不太频繁的时间编制索引，但服务不会将一个任务优先于另一个任务。 此外，当内部更新服务或节点时，一定量的冗余会平滑查询性能。

通常，搜索应用程序需要比分区更多的副本，尤其是当服务操作偏向查询工作负载时。 [高可用性](#HA)部分将解释原因。

> [!NOTE]
> 添加更多副本或分区会增加运行服务的成本，并且可能会引入对结果排序方式的细微变化。 请务必查看[定价计算器](https://azure.microsoft.com/pricing/calculator/)以了解添加更多节点的计费影响。 [下图](#chart)可以帮助您交叉引用特定配置所需的搜索单元数。 有关其他副本如何影响查询处理的详细信息，请参阅[排序结果](search-pagination-page-layout.md#ordering-results)。

## <a name="how-to-allocate-replicas-and-partitions"></a>如何分配副本和分区

1. 登录到 [Azure 门户](https://portal.azure.com/)，并选择搜索服务。

1. 在“设置”中，打开“规模”页以修改副本和分区。******** 

   以下屏幕截图显示了预配有一个副本和分区的标准服务。 底部的公式指示正在使用多少个搜索单位 (1)。 如果单位价格为 $100（非实际价格），则运行此服务的每月成本平均为 $100。

   ![显示当前值的“规模”页](media/search-capacity-planning/1-initial-values.png "显示当前值的“规模”页")

1. 使用滑块增加或减少分区数。 底部的公式指示正在使用多少个搜索单位。

   此示例各使用一个副本和分区将容量翻倍。 请注意搜索单位计数；现在有 4 个搜索单位，因为计费公式是副本数乘以分区数 (2 x 2)。 将容量翻倍不仅仅会使运行服务的成本翻倍。 如果搜索单位的成本是 $100，则新的每月费用将是 $400。

   有关每个层的当前单位成本，请访问[定价页](https://azure.microsoft.com/pricing/details/search/)。

   ![添加副本和分区](media/search-capacity-planning/2-add-2-each.png "添加副本和分区")

1. 单击“保存”以确认所做的更改。****

   ![确认对规模和计费所做的更改](media/search-capacity-planning/3-save-confirm.png "确认对规模和计费所做的更改")

   更改容量需要花费几个小时才能完成。 一旦启动更改过程，就无法将其取消；系统不会实时监视副本和分区的调整。 但是，在更改过程中，会一直显示以下消息。

   ![门户中的状态消息](media/search-capacity-planning/4-updating.png "门户中的状态消息")

> [!NOTE]
> 预配服务后，无法将其升级到更高层。 必须在新层中创建搜索服务，并重新加载索引。 有关服务预配的帮助，请参阅[在门户中创建 Azure 认知搜索服务](search-create-service-portal.md)。
>
> 此外，分区和副本由服务在内部进行独占管理。 没有处理器相关性的概念，也没有将工作负载分配给特定节点的概念。
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>分区和副本组合

“基本”服务可以包含一个分区以及最多三个副本，上限为三个 SU。 唯一可调整的资源是副本。 至少需要两个副本才能实现查询的高可用性。

所有“标准”和“存储优化”搜索服务可以采用副本和分区的以下组合，但不能超过 36 个 SU 的限制。 

|   | **1 个分区** | **2 个分区** | **3 个分区** | **4 个分区** | **6 个分区** | **12 个分区** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 个副本** |1 个 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 个副本** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 个副本** |3 SU |6 SU |9 SU |12 SU |18 SU |36 个 SU |
| **4 个副本** |4 SU |8 SU |12 SU |16 SU |24 SU |不可用 |
| **5 副本** |5 SU |10 SU |15 SU |20 SU |30 SU |不可用 |
| **6 个副本** |6 SU |12 SU |18 SU |24 SU |36 个 SU |不可用 |
| **12 副本** |12 SU |24 SU |36 个 SU |不可用 |不可用 |不可用 |

Azure 网站上详细说明了 SU、定价和容量。 有关详细信息，请参阅 [Pricing Details](https://azure.microsoft.com/pricing/details/search/)（定价详细信息）。

> [!NOTE]
> 副本数和分区数必须能被 12 整除（具体而言，为 1、2、3、4、6、12）。 这是因为，Azure 认知搜索将每个索引预先分割为 12 个分片，以便将其平均分散到所有分区。 例如，如果服务有三个分区，而你创建了新索引，则每个分区将包含该索引的四个分片。 Azure 认知搜索为索引分片的方法属于实现细节，在将来的版本中可能发生变化。 尽管目前的分区数为 12，但请不要料想将来该数字永远都是 12。
>

<a id="HA"></a>

## <a name="high-availability"></a>高可用性

由于扩展的过程比较简单而且相对较快，因此我们通常建议从一个分区以及一个或两个副本开始，并随着不断构建查询卷而进行扩展。 查询工作负荷主要是在副本上运行。 如果需要更高的吞吐量或高可用性，也许需要增加副本。

针对高可用性的一般建议是：

* 对于只读工作负荷（查询），需要有两个副本才能实现高可用性

* 对于读/写工作负荷（查询以及添加、更新或删除单个文档时的索引编制），需有三个或更多个副本才能实现高可用性

Azure 认知搜索的服务级别协议 (SLA) 针对查询操作，以及由文档添加、更新或删除操作构成的索引更新。

基本层最多能有一个分区和三个副本。 如果希望灵活地立即响应对索引编制和查询吞吐量的需求波动，请考虑使用标准层中的一个。  如果你发现存储要求的增长速度大大超过了可用的吞吐量，请考虑使用某个“存储优化”层。

## <a name="disaster-recovery"></a>灾难恢复

目前没有任何内置的机制可实现灾难恢复。 添加分区或副本并不是实现灾难恢复目标的正确策略。 最常见的方法是通过在另一区域中设置另一个搜索服务，在服务级别添加冗余。 与索引重建期间的可用性一样，重定向或故障转移逻辑必须来自代码。

## <a name="estimate-replicas"></a>估计副本

在生产服务上，应为 SLA 目的分配三个副本。 如果查询性能降低，可以添加副本，以便将索引的其他副本联机，以支持更大的查询工作负荷，并在多个副本上加载平衡请求。

我们不提供有关需要多少副本来容纳查询负载的指南。 查询性能取决于查询和竞争工作负载的复杂性。 尽管添加副本会明显提高性能，但结果不一定有线性改善：添加三个副本并不保证带来三倍的吞吐量。

有关估计解决方案的 QPS 的指导，请参阅["缩放"以了解性能和](search-performance-optimization.md)[监视器查询](search-monitor-queries.md)

## <a name="estimate-partitions"></a>估计分区

[您选择的层](search-sku-tier.md)决定了分区大小和速度，并且每个层都围绕一组适合各种方案的特征进行了优化。 如果选择更高端的层，则可能需要的分区可能比使用 S1 时要少。 通过自定向测试需要回答的一个问题是，更大、更昂贵的分区是否比在较低层预配的服务上提供两个更便宜的分区产生更好的性能。

需要以近乎实时的速度刷新数据的搜索应用程序，需要的分区数在比例上要多于副本。 添加分区可将读/写操作分配到更多的计算资源。 此外，还能提供更多磁盘空间来存储更多的索引和文档。

索引越大，查询所需的时间就越长。 因此，可能发现，每次增加分区都需要按比例少量增加副本。 查询和查询卷的复杂性影响查询执行的速度。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [选择 Azure 认知搜索的定价层](search-sku-tier.md)