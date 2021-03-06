---
title: 查看 Azure RBAC 更改的活动日志
description: 查看过去 90 天内 Azure 资源对 Azure 资源进行的活动日志，这些访问控制 （Azure RBAC） 更改。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2024bd14241184338195ed635039bae774da816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78161752"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>查看 Azure RBAC 更改的活动日志

有时，您需要有关基于 Azure 角色的访问控制 （Azure RBAC） 更改的信息，例如用于审核或故障排除目的。 每当有人更改订阅中的角色分配或角色定义时，这些更改将记录在 Azure[活动日志](../azure-monitor/platform/platform-logs-overview.md)中。 您可以查看活动日志以查看过去 90 天内的所有 Azure RBAC 更改。

## <a name="operations-that-are-logged"></a>记录的操作

以下是在活动日志中记录的 Azure RBAC 相关操作：

- 创建角色分配
- 删除角色分配
- 创建或更新自定义角色定义
- 删除自定义角色定义

## <a name="azure-portal"></a>Azure 门户

最简单的入手方式就是使用 Azure 门户查看活动日志。 以下屏幕截图显示了活动日志中角色分配操作的示例。 它还包括将日志下载为 CSV 文件的选项。

![使用门户的活动日志 - 屏幕截图](./media/change-history-report/activity-log-portal.png)

门户中的活动日志有多个筛选器。 下面是与 Azure RBAC 相关的筛选器：

| “筛选器” | “值” |
| --------- | --------- |
| 事件类别 | <ul><li>管理</li></ul> |
| Operation | <ul><li>创建角色分配</li><li>删除角色分配</li><li>创建或更新自定义角色定义</li><li>删除自定义角色定义</li></ul> |

有关活动日志的详细信息，请参阅[查看活动日志以监视对资源的操作](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)。

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

若要使用 Azure PowerShell 查看活动日志，请使用 [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) 命令。

此命令列出过去 7 天内订阅中所有角色分配的更改：

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

此命令列出过去 7 天内资源组中所有角色定义的更改：

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

此命令列出过去 7 天内订阅中所有角色分配和角色定义的更改，并在列表中显示结果：

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

## <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 查看活动日志，请使用 [az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) 命令。

此命令列出从 2 月 27 日开始的资源组中的活动日志，展望 7 天：

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2020-02-27 --offset 7d
```

此命令列出从 2 月 27 日开始的授权资源提供程序的活动日志，展望 7 天：

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2020-02-27 --offset 7d
```

## <a name="azure-monitor-logs"></a>Azure Monitor 日志

[Azure 监视器日志](../log-analytics/log-analytics-overview.md)是另一个工具，可用于收集和分析所有 Azure 资源的 Azure RBAC 更改。 Azure 监视器日志具有以下优点：

- 编写复杂查询和逻辑
- 与警报、Power BI 和其他工具集成
- 以更长的保持期保存数据
- 与其他日志（例如安全性、虚拟机和自定义日志）交叉引用

以下是开始使用的基本步骤：

1. [创建日志分析工作区](../azure-monitor/learn/quick-create-workspace.md)。

1. 为工作区[配置 Activity Log Analytics 解决方案](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution)。

1. [查看活动日志](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution)。 导航到活动日志分析解决方案概述页面的快速方法是单击 **"日志"** 选项。

   ![门户中的 Azure 监视器日志选项](./media/change-history-report/azure-log-analytics-option.png)

1. 可以选择使用[Azure 监视器日志分析](../azure-monitor/log-query/get-started-portal.md)来查询和查看日志。 有关详细信息，请参阅[开始使用 Azure 监视器日志查询](../azure-monitor/log-query/get-started-queries.md)。

以下查询返回由目标资源提供程序组织的新角色分配：

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

以下查询返回图表中显示的角色分配更改：

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![使用高级分析门户的活动日志 - 屏幕截图](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>后续步骤
* [在活动日志中查看事件](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [使用 Azure 活动日志监视订阅活动](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
