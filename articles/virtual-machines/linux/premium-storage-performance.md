---
title: Azure 高级存储：Linux VM 性能设计 |微软文档
description: 使用 Azure 高级 SSD 托管磁盘设计高性能应用程序。 高级存储为 Azure 虚拟机上运行的 I/O 密集型工作负载提供高性能、低延迟的磁盘支持。
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 9940ee4cfce9721ac65f2b3cf1469e180adfa098
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267139"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure 高级存储：高性能设计
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> 有时，显示为磁盘性能问题的原因实际上是网络瓶颈。 在这些情况下，应优化[网络性能](../../virtual-network/virtual-network-optimize-network-bandwidth.md)。
>
> 如果你希望对磁盘进行基准测试，请参阅我们的关于[磁盘基准测试](disks-benchmarks.md)的文章。
>
> 如果 VM 支持加速网络，则应确保它已启用。 如果未启用，则可以在 [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) 和 [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms) 上已部署的 VM 上启用它。

开始之前，如果对高级存储新增，请先阅读[IaaS VM 的 Azure 磁盘类型](disks-types.md)和[高级页面 Blob 存储帐户的可伸缩性目标](../../storage/blobs/scalability-targets-premium-page-blobs.md)。


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

如果你希望对磁盘进行基准测试，请参阅我们的关于[磁盘基准测试](disks-benchmarks.md)的文章。

了解有关可用磁盘类型详细信息：[选择磁盘类型](disks-types.md)  

SQL Server 用户请阅读有关 SQL Server 性能最佳实践的文章：

* [Azure 虚拟机中 SQL 服务器的性能最佳实践](../windows/sql/virtual-machines-windows-sql-performance.md)
* [Azure Premium Storage provides highest performance for SQL Server in Azure VM](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)（Azure 高级存储为 Azure VM 中的 SQL Server 提供最高性能）