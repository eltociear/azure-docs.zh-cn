---
title: Azure 流分析中的数据驱动调试
description: 本文介绍如何使用 Azure 门户中的作业关系图和指标对 Azure 流分析作业进行故障排除。
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 3c0c29e1793e56efae8d13cb01d57faf257d8805
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426078"
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>使用作业关系图进行数据驱动调试

使用 Azure 门户中“监视”边栏选项卡里的作业关系图，可以可视化作业管道****。 它显示了输入、输出和查询步骤。 可使用作业关系图检查每个步骤的指标，并且在解决问题时，更快速地隔离问题源。

## <a name="using-the-job-diagram"></a>使用作业关系图

在 Azure 门户的流分析作业中，在“支持和故障排除”**** 下，选择“作业关系图”****：

![包含指标的作业关系图 - 位置](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

选择每个查询步骤，在查询编辑窗格中查看相应的部分。 步骤的指标图表显示在页面下方的窗格中。

![包含指标的作业关系图 - 基本作业](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

要查看 Azure 事件中心输入的分区，请选择“. . .”**** 上下文菜单随即打开。 还可查看输入合并。

![包含指标的作业关系图 - 展开分区](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

要查看单个分区的指标图表，请选择分区节点。 指标显示在页面底部。

![包含指标的作业关系图 - 更多指标](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

要查看合并的指标图表，请选择合并节点。 下表显示没有删除或调整事件。

![包含指标的作业关系图 - 网格](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

要查看指标值和时间的详细信息，请指向图表。

![包含指标的作业关系图 - 悬停](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>使用指标进行故障排除

QueryLastProcessedTime 指标指示特定步骤收到数据的时间****。 通过查看拓扑，可以从输出处理器开始反向检查，判断哪个步骤未接收数据。 如果一个步骤未收到数据，请转到它之前的查询步骤。 检查前面的查询步骤是否具有时间范围，以及是否经过了充足的、用于输出数据的时间。 （注意：时间范围会对齐到小时。）
 
如果前一个查询步骤是输入处理器，请使用输入指标帮助解答下列针对性问题。 它们可以帮助判断作业是否正从其输入源获取数据。 如果查询已分区，请检查每个分区。
 
### <a name="how-much-data-is-being-read"></a>正在读取的数据量有多少？

*   InputEventsSourcesTotal 是已读取的数据单元的数目****。 例如，blob 的数目。
*   InputEventsTotal 是已读取的事件数目****。 此指标按分区提供。
*   InputEventsInBytesTotal 是已读取的字节数****。
*   **输入事件Last到达时间**会随着每个接收事件的排队时间进行更新。
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>时间是否在不断前进？ 如果已读取实际事件，可能不会发出停顿。

*   **InputEventsLastPunctuationTime** 表示发出停顿，从而使时间不断前进的时间。 如果不发出停顿，数据流可能会阻塞。
 
### <a name="are-there-any-errors-in-the-input"></a>输入中是否有任何错误？

*   InputEventsEventDataNullTotal 是包含 null 数据的事件计数****。
*   InputEventsSerializerErrorsTotal 是无法正确反序列化的事件计数****。
*   InputEventsDegradedTotal 是出现了问题、但该问题不是反序列化问题的事件计数****。
 
### <a name="are-events-being-dropped-or-adjusted"></a>事件是否被删除或调整？

*   InputEventsEarlyTotal 是应用程序时间戳在高水位线之前的事件数目****。
*   InputEventsLateTotal是应用程序时间戳在高水位线之后的事件数目****。
*   InputEventsDroppedBeforeApplicationStartTimeTotal 是作业开始时间之前删除的事件数目****。
 
### <a name="are-we-falling-behind-in-reading-data"></a>在读取数据上是否落后于人？

*   **囤积的输入事件数(总计)** 告知还需要为事件中心和 Azure IoT 中心输入读取多少条消息。 当此数字大于 0 时，表示作业不能以数据传入速度快速处理数据。 在这种情况下，可能需要增加流单元数和/或确保作业可以并行化。 可以在[查询并行化页](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization)上看到有关此内容的更多信息。 


## <a name="get-help"></a>获取帮助
有关更多帮助，请访问我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。 

## <a name="next-steps"></a>后续步骤
* [流分析简介](stream-analytics-introduction.md)
* [流分析入门](stream-analytics-real-time-fraud-detection.md)
* [扩展流分析作业](stream-analytics-scale-jobs.md)
* [流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
