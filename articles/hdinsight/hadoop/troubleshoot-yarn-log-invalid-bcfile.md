---
title: 无法读取 Azure HDInsight 中的 Apache Yarn 日志
description: 在与 Azure HDInsight 群集交互时，对问题的步骤和可能的解决方案进行故障排除。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f7861ae4980f13fbd87780ab2d26fa3376f33532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776191"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>方案：无法读取 Azure HDInsight 中的 Apache Yarn 日志

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

从存储帐户找到的 Apache Yarn 日志是不可读的。 文件解析器不工作并生成以下错误消息：

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>原因

Apache Yarn 日志聚合为`IndexFile`格式，文件解析器不支持该格式。

## <a name="resolution"></a>解决方法

1. 在 Web 浏览器中，导航到 `https://CLUSTERNAME.azurehdinsight.net`，其中 `CLUSTERNAME` 是群集的名称。

1. 从安巴里 UI，导航到**YARN** > **配置** > **Advanced** > **高级纱线站点**。

1. 对于 WASB 存储：的`yarn.log-aggregation.file-formats`默认值为`IndexedFormat,TFile`。 将值更改为`TFile`。

1. 对于 ADLS 存储：的`yarn.nodemanager.log-aggregation.compression-type`默认值为`gz`。 将值更改为`none`。

1. 保存更改并重新启动所有受影响的服务。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport)- 用于改善客户体验的官方 Microsoft Azure 帐户连接。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”****，或打开“帮助 + 支持”**** 中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
