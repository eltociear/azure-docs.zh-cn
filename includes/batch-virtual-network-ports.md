---
title: include 文件
description: include 文件
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/04/2020
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: e9460108499ca76d1b149b61cebe3d3081bf6544
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086249"
---
### <a name="general-requirements"></a>一般要求

* VNet 必须与用于创建池的 Batch 帐户位于同一订阅和区域中。

* 使用 VNet 的池最多可以有 4096 个节点。

* 为池指定的子网必须提供足够的未分配 IP 地址来容纳面向该池的 VM 的数量；即，池的 `targetDedicatedNodes` 和 `targetLowPriorityNodes` 属性的总和。 如果子网没有足够的未分配 IP 地址，池将分配部分计算节点，并发生调整大小错误。

* 需要通过为 VNet 提供服务的自定义 DNS 服务器解析 Azure 存储终结点。 具体而言，`<account>.table.core.windows.net`、`<account>.queue.core.windows.net` 和 `<account>.blob.core.windows.net` 形式的 URL 应当是可以解析的。

其他 VNet 要求会有所不同，具体取决于 Batch 池是使用“虚拟机”配置还是使用“云服务”配置。 若要进行新的池部署（部署到 VNet 中），建议使用“虚拟机”配置。

### <a name="pools-in-the-virtual-machine-configuration"></a>“虚拟机”配置中的池

**支持的 VNet** - 仅限基于 Azure 资源管理器的 VNet

**子网 ID** - 通过 Batch API 指定子网时，请使用子网的资源标识符。** 标识符的形式为：

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}
  ```

**权限** - 检查在 VNet 的订阅或资源组上实施的安全策略或锁定是否限制用户管理 VNet 所需的权限。

**其他网络资源** - Batch 自动在包含 VNet 的资源组中分配其他网络资源。

> [!IMPORTANT]
>对于每个 50 个专用节点（或每 20 个低优先级节点），Batch 分配：一个网络安全组 （NSG）、一个公共 IP 地址和一个负载均衡器。 这些资源受订阅的[资源配额](../articles/azure-resource-manager/management/azure-subscription-service-limits.md)限制。 对于大型池，您可能需要请求增加一个或多个这些资源的配额。

#### <a name="network-security-groups-batch-default"></a>网络安全组：批处理默认值

子网必须允许来自 Batch 服务的入站通信能够安排计算节点上的任务，以及出站通信，以便根据工作负荷的需求与 Azure 存储或其他资源进行通信。 对于虚拟机配置中的池，Batch 在附加到计算节点的网络接口 （NIC） 级别添加 NSG。 这些 NSG 配置了以下附加规则：

* 端口 29876 和 29877 上的入站 TCP 流量来自对应于服务标记`BatchNodeManagement`的 Batch 服务 IP 地址。
* 端口 22（Linux 节点）或端口 3389（Windows 节点）上允许远程访问的入站 TCP 流量。 对于 Linux 上的某些类型的多实例任务（如 MPI），还需要允许在包含 Batch 计算节点的子网络中为 IP 提供 SSH 端口 22 流量。 这可能会根据子网级 NSG 规则阻止（见下文）。
* 任何端口上通往虚拟网络的出站流量。 可以根据子网级 NSG 规则对此进行修订（见下文）。
* 任何端口到 Internet 的出站流量。 可以根据子网级 NSG 规则对此进行修订（见下文）。

> [!IMPORTANT]
> 在 Batch 配置的 NSG 中修改或添加入站或出站规则时，请务必小心。 如果 NSG 拒绝与指定子网中的计算节点通信，则 Batch 服务会将计算节点的状态设置为“不可用”****。 此外，不应将任何资源锁应用于 Batch 创建的任何资源，否则可能导致由于用户启动的操作（如删除池）而阻止资源清理。

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>网络安全组：指定子网级规则

不需要在虚拟网络子网级别指定 NSG，因为 Batch 配置自己的 NSG（见上文）。 如果 NSG 与部署 Batch 计算节点的子网相关联，或者希望应用自定义 NSG 规则来覆盖应用的默认值，则必须使用至少入站和出站安全规则配置此 NSG，如下所示表。

仅当需要允许从外部源远程访问计算节点时，才在端口 3389 （Windows） 或 22 （Linux） 上配置入站流量。 如果需要支持具有某些 MPI 运行时的多实例任务，则可能需要在 Linux 上启用端口 22 规则。 池计算节点的可用性并非严格要求允许这些端口上的流量。

**入站安全规则**

| 源 IP 地址 | 源服务标记 | 源端口 | 目标 | 目标端口 | 协议 | 操作 |
| --- | --- | --- | --- | --- | --- | --- |
| 空值 | `BatchNodeManagement`[服务标记](../articles/virtual-network/security-overview.md#service-tags)（如果使用区域变体，与批处理帐户位于同一区域） | * | Any | 29876-29877 | TCP | Allow |
| 用户源 IP 用于远程访问计算节点和/或计算节点子网，用于 Linux 多实例任务（如果需要）。 | 空值 | * | Any | 3389 (Windows)、22 (Linux) | TCP | Allow |

**出站安全规则**

| 源 | 源端口 | 目标 | 目标服务标记 | 目标端口 | 协议 | 操作 |
| --- | --- | --- | --- | --- | --- | --- |
| Any | * | [服务标签](../articles/virtual-network/security-overview.md#service-tags) | `Storage`（如果使用区域变体，则与批处理帐户位于同一区域） | 443 | TCP | Allow |

### <a name="pools-in-the-cloud-services-configuration"></a>“云服务”配置中的池

**支持的 VNet** - 仅限经典 VNet

**子网 ID** - 通过 Batch API 指定子网时，请使用子网的资源标识符。** 标识符的形式为：

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**权限** - `Microsoft Azure Batch` 服务主体必须为指定的 VNet 提供 `Classic Virtual Machine Contributor` 基于角色的访问控制 (RBAC) 角色。

#### <a name="network-security-groups"></a>网络安全组

子网必须允许来自 Batch 服务的入站通信，才能在计算节点上计划任务，必须允许出站通信，才能与 Azure 存储或其他资源通信。

不需指定 NSG，因为 Batch 将入站通信配置为只能从 Batch IP 地址到池节点。 但是，如果指定的子网具有关联的 NSG 和/或防火墙，则配置入站和出站安全规则，如以下各表中所示。 如果 NSG 拒绝与指定子网中的计算节点通信，则 Batch 服务会将计算节点的状态设置为“不可用”****。

如果需要允许 RDP 访问池节点，请为 Windows 端口 3389 配置入站流量。 不配置它也可使用池节点。

**入站安全规则**

| 源 IP 地址 | 源端口 | 目标 | 目标端口 | 协议 | 操作 |
| --- | --- | --- | --- | --- | --- |
Any <br /><br />虽然这需要有效地“全部允许”，但 Batch 服务会在每个节点级别应用 ACL 规则，以筛选掉所有非 Batch 服务 IP 地址。 | * | Any | 10100、20100、30100 | TCP | Allow |
| 可选，允许 RDP 访问计算节点。 | * | Any | 3389 | TCP | Allow |

**出站安全规则**

| 源 | 源端口 | 目标 | 目标端口 | 协议 | 操作 |
| --- | --- | --- | --- | --- | --- |
| Any | * | Any | 443  | Any | Allow |
