---
title: Azure 防火墙日志和指标的概述
description: 可以使用防火墙日志来监视 Azure 防火墙。 此外，可以使用活动日志来审核对 Azure 防火墙资源执行的操作。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/22/2020
ms.author: victorh
ms.openlocfilehash: 89c6700d5df3bcef1332121c3cf7d8f720fe054c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76315025"
---
# <a name="azure-firewall-logs-and-metrics"></a>Azure 防火墙日志和指标

可以使用防火墙日志来监视 Azure 防火墙。 此外，可以使用活动日志来审核对 Azure 防火墙资源执行的操作。

可通过门户访问其中部分日志。 可将日志发送到 [Azure Monitor 日志](../azure-monitor/insights/azure-networking-analytics.md)、存储和事件中心，并使用 Azure Monitor 日志或其他工具（例如 Excel 和 Power BI）对其进行分析。

指标是能够为近实时方案提供支持的轻型数据，因此，它们特别适合用于警报和快速检测问题。

## <a name="diagnostic-logs"></a>诊断日志

 以下诊断日志适用于 Azure 防火墙：

* **应用程序规则日志**

   仅当为每个 Azure 防火墙启用了应用程序规则日志时，才会将此日志保存到存储帐户、流式传输到事件中心和/或发送到 Azure Monitor 日志。 每当建立与某个配置的应用程序规则匹配的新连接，就会为接受/拒绝的连接生成一条日志。 如以下示例中所示，数据以 JSON 格式记录：

   ```
   Category: application rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **网络规则日志**

   仅当为每个 Azure 防火墙启用了网络规则日志时，才会将此日志保存到存储帐户、流式传输到事件中心和/或发送到 Azure Monitor 日志。 每当建立与某个配置的网络规则匹配的新连接，就会为接受/拒绝的连接生成一条日志。 如以下示例中所示，数据以 JSON 格式记录：

   ```
   Category: network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

可通过三种方式存储日志：

* 存储帐户****：如果日志存储时间较长并且希望能根据需要随时查看，则最好使用存储帐户。
* 事件中心****：若要集成其他安全信息和事件管理 (SEIM) 工具，获取资源警报，则事件中心是很好的选择。
* **Azure 监视器日志**：Azure 监视器日志最适合用于应用程序的总体实时监视或查看趋势。

## <a name="activity-logs"></a>活动日志

   默认情况下会收集活动日志条目，可在 Azure 门户中查看这些条目。

   可以使用 [Azure 活动日志](../azure-resource-manager/management/view-activity-logs.md)（以前称为操作日志和审核日志）查看提交到 Azure 订阅的所有操作。

## <a name="metrics"></a>指标

Azure Monitor 中的指标是数字值，用于描述系统某些方面在特定时间的情况。 指标每分钟收集一次，可用于警报，因为可对其频繁采样。 可以使用相对简单的逻辑快速激发警报。

以下指标适用于 Azure 防火墙：

- **应用程序规则命中计数** - 应用程序规则的命中次数。

    单位：计数

- **网络规则命中计数** - 网络规则的命中次数。

    单位：计数

- **处理的数据量** - 遍历防火墙的数据量。

    单位：字节

- **防火墙运行状况状态**- 指示基于 SNAT 端口可用性的防火墙运行状况。

    单位：百分比

   此指标有两个维度：
  - 状态：可能的值是 *"正常**"、降级*、*不正常*。
  - 原因：指示防火墙相应状态的原因。 

     如果使用 SNAT 端口> 95%，则它们被视为已耗尽，运行状况为 50%，状态 =**降级**和原因=**SNAT 端口**。 防火墙持续处理流量，现有连接不受影响。 但是，新连接可能不会间歇性地建立。

     如果 sNAT 端口< 95% 使用，则防火墙被视为正常，运行状况显示为 100%。

     如果未报告 SNAT 端口使用情况，则运行状况显示为 0%。 

- **SNAT 端口利用率** - 防火墙利用的 SNAT 端口数百分比。

    单位：百分比

   将更多公共 IP 地址添加到防火墙时，将有更多的 SNAT 端口可用，从而减小 SNAT 端口利用率。 此外，当防火墙出于各种原因而横向扩展时（例如，增加 CPU 或吞吐量），也有更多的 SNAT 端口可用。 因此，实际上，给定比例的 SNAT 端口利用率可能会下降，而无需添加任何公共 IP 地址，只是因为服务已扩展。您可以直接控制可用公共 IP 地址的数量，以增加防火墙上的可用端口。 但无法直接控制防火墙缩放。 目前，只为前五个公共 IP 地址添加了 SNAT 端口。   


## <a name="next-steps"></a>后续步骤

- 若要了解如何监视 Azure 防火墙日志和指标，请参阅[教程：监视 Azure 防火墙日志](tutorial-diagnostics.md)。

- 若要详细了解 Azure Monitor 中的指标，请参阅 [Azure Monitor 中的指标](../azure-monitor/platform/data-platform-metrics.md)。
