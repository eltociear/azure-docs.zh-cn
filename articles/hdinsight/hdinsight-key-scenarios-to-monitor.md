---
title: 监视群集性能 - Azure HDInsight
description: 如何在 Azure HDInsight 中监视 Apache Hadoop 群集的运行状况和性能。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/09/2020
ms.openlocfilehash: 75ac5a7fc352f877573d79a004d8da761c6f1cef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082874"
---
# <a name="monitor-cluster-performance-in-azure-hdinsight"></a>在 Azure HDInsight 中监视群集性能

监视 HDInsight 群集的运行状况和性能对于维持最佳性能和资源利用率来说至关重要。 监视还可以帮助你检测并解决群集配置错误和用户代码问题。

以下各部分介绍了如何监视和优化群集上的负载、Apache Hadoop YARN 队列，以及检测存储限制问题。

## <a name="monitor-cluster-load"></a>监视群集负载

当群集上的负载均匀分布在所有节点中时，Hadoop 群集可以提供最佳性能。 这使得处理任务在运行时可以不受个体节点上的 RAM、CPU 或磁盘资源约束。

要查看群集的节点及其加载，请登录到[Ambari Web UI，](hdinsight-hadoop-manage-ambari.md)然后选择 **"主机**"选项卡。您的主机由其完全限定的域名列出。 每个主机的运行状态由一个彩色运行状况指示器进行显示：

| Color | 描述 |
| --- | --- |
| Red | 主机上至少有一个主组件已关闭。 悬停鼠标以查看列出受影响组件的工具提示。 |
| 橙色 | 主机上至少有一个辅助组件已关闭。 悬停鼠标以查看列出受影响组件的工具提示。 |
| Yellow | Ambari 服务器超过 3 分钟没有收到来自主机的检测信号。 |
| 绿色 | 正常运行状态。 |

此外还将看到列，显示每个主机的内核数及 RAM 量、磁盘使用情况和平均负载。

![阿帕奇·安巴里主机选项卡概述](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-hosts-tab.png)

选择任意主机名，详细了解在该主机上运行的组件及其指标。 查看 CPU 使用情况、负载、磁盘使用情况、内存使用情况、网络使用情况和进程数的可选时间线，了解这些指标。

![阿帕奇·安巴里主机详细信息概述](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-host-details.png)

有关设置警报和查看指标的详细信息，请参阅[使用 Apache Ambari Web UI 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)。

## <a name="yarn-queue-configuration"></a>YARN 队列配置

Hadoop 跨其分布式平台运行各种服务。 YARN (Yet Another Resource Negotiator) 协调这些服务并分配群集资源以确保任何负载都均匀地分布在群集中。

YARN 将 JobTracker、资源管理和作业计划/监视的两种责任划分为两个守护程序：一个全局资源管理器和一个每应用程序 ApplicationMaster (AM)。

资源管理器是一个纯计划程序**，且仅仲裁所有竞争应用程序之间的可用资源。 资源管理器确保所有资源都处于使用状态，并针对各种常量（如 SLA、容量保障等）进行优化。 ApplicationMaster 处理来自于 ResourceManager 的资源，并与 NodeManager 一起执行和监视容器及其资源消耗。

当多个租户共享一个大型群集时，群集的资源将存在竞争。 CapacityScheduler 是一种可插入计划程序，通过对请求进行排队来协助资源共享。 容量计划程序还支持*分层队列*，以确保在允许其他应用程序的队列使用可用资源之前，资源在组织的子队列之间共享。

YARN 允许我们将资源分配给这些队列，并显示是否已分配所有可用资源。 若要查看有关队列的信息，请登录到 Ambari Web UI，然后从顶部菜单选择“YARN 队列管理器”****。

![阿帕奇·安巴里·亚恩队列管理器](./media/hdinsight-key-scenarios-to-monitor/apache-yarn-queue-manager.png)

YARN 队列管理器页的左侧显示队列的列表，以及分配给每个队列的容量百分比。

![YARN 队列管理器详细信息页](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

若要更加详细地查看队列，在 Ambari 仪表板中，从左侧列表选择“YARN”**** 服务。 然后，在“快速链接”**** 下拉菜单下，选择活动节点下的“资源管理器 UI”****。

![资源管理器 UI 菜单链接](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu-link.png)

在资源管理器 UI 中，从左侧菜单中选择“计划程序”****。 “应用程序队列”** 下将显示队列的列表。 此处可看到用于每个队列的容量、作业在队列之间的分布情况，以及作业是否受资源约束。

![阿帕奇哈杜普资源管理器 UI 菜单](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

## <a name="storage-throttling"></a>存储限制

群集的性能瓶颈可能发生于存储级别。 这种类型的瓶颈通常是因为*阻塞*输入/输出 （IO） 操作，当正在运行的任务发送的 IO 超过存储服务可以处理的 IO 时，就会发生这种情况。 这种阻止将创建等待处理完当前 IO 后再进行处理的 IO 请求队列。 这些块是由于*存储限制*（这不是物理限制），而是服务级别协议 （SLA） 对存储服务施加的限制。 此限制确保单个客户端或租户无法独占服务。 SLA 限制 Azure 存储每秒 IO （IOPS） 的数量 - 有关详细信息，请参阅[标准存储帐户的可伸缩性和性能目标](../storage/common/scalability-targets-standard-account.md)。

如果使用 Azure 存储，请参阅监视与存储相关的问题（包括限制）的信息，请参阅[监视、诊断和排除 Microsoft Azure 存储](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting)。

如果群集的备份存储是 Azure 数据湖存储 （ADLS），则限制很可能是由于带宽限制。 在这种情况下，可通过观察任务日志中的限制错误来确定限制。 对于 ADLS，请参阅这些文章中相应服务的限制部分：

* [Apache Hive on HDInsight 和 Azure Data Lake Storage 性能优化指南](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [MapReduce on HDInsight 和 Azure Data Lake Storage 性能优化指南](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Apache Storm on HDInsight 和 Azure Data Lake Storage 性能优化指南](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="troubleshoot-sluggish-node-performance"></a>解决节点性能迟钝的问题

在某些情况下，由于群集上的磁盘空间较低，可能会出现迟滞。 通过以下步骤进行调查：

1. 使用[ssh 命令](./hdinsight-hadoop-linux-use-ssh-unix.md)连接到每个节点。

1. 通过运行以下命令之一检查磁盘使用情况：

    ```bash
    df -h
    du -h --max-depth=1 / | sort -h
    ```

1. 查看输出，并检查`mnt`文件夹中是否存在任何大型文件。 通常，`usercache`和`appcache`（mnt/资源/哈图普/纱线/本地/用户缓存/hive/appcache/） 文件夹包含大型文件。

1. 如果有大型文件，则当前作业导致文件增长，或者以前的作业失败可能导致此问题。 要检查此行为是否由当前作业引起，请运行以下命令：

    ```bash
    sudo du -h --max-depth=1 /mnt/resource/hadoop/yarn/local/usercache/hive/appcache/
    ```

1. 如果此命令指示特定作业，则可以选择使用类似于以下内容的命令终止作业：

    ```bash
    yarn application -kill -applicationId <application_id>
    ```

    替换为`application_id`应用程序 ID。 如果未指示特定作业，则转到下一步。

1. 完成上述命令后，或者如果未指示特定作业，则通过运行类似于以下内容的命令删除标识的大文件：

    ```bash
    rm -rf filecache usercache
    ```

有关磁盘空间问题的详细信息，请参阅[磁盘空间不足](./hadoop/hdinsight-troubleshoot-out-disk-space.md)。

> [!NOTE]  
> 如果您有要保留的大型文件，但导致磁盘空间不足问题，您必须扩展 HDInsight 群集并重新启动服务。 完成此过程并等待几分钟后，您会注意到存储已释放，并且节点的通常性能已恢复。

## <a name="next-steps"></a>后续步骤

请访问以下链接，了解有关故障排除和监视群集的详细信息：

* [分析 HDInsight 日志](hdinsight-debug-jobs.md)
* [使用 Apache Hadoop YARN 日志调试应用](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [在基于 Linux 的 HDInsight 上为 Apache Hadoop 服务启用堆转储](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
