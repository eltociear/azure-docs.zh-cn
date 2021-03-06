---
title: SAP HANA 扩展，在 RHEL 上使用 Azure NetApp 文件进行待机。微软文档
description: 红帽企业 Linux 上的 SAP NetWeaver 高可用性指南，用于 SAP 应用程序的 Azure NetApp 文件
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/10/2020
ms.author: radeltch
ms.openlocfilehash: ace30b044682cb4e62a0f81da90f21a77be1edd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372969"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-red-hat-enterprise-linux"></a>使用红帽企业 Linux 上的 Azure NetApp 文件，在 Azure VM 上部署具有备用节点的 SAP HANA 横向扩展系统 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2455582
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


本文介绍如何通过将[Azure NetApp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)用于共享存储卷，在 Azure 红帽企业 Linux 虚拟机 （VM） 上具有待机状态的横向扩展配置中部署高可用性 SAP HANA 系统。  

在示例配置、安装命令等中，HANA 实例为**03，HANA**系统 ID 为**HN1**。 这些示例基于 SAP 7.6 的 HANA 2.0 SP4 和红帽企业 Linux。 

在开始之前，请参阅以下 SAP 说明和文件：

* [Azure NetApp 文件文档][anf-azure-doc] 
* SAP 说明[1928533]包括：  
  * 支持部署 SAP 软件的 Azure VM 大小列表
  * Azure VM 大小的重要容量信息
  * 支持的 SAP 软件、操作系统 (OS) 和数据库组合
  * 微软 Azure 上 Windows 和 Linux 所需的 SAP 内核版本
* SAP 说明[2015553]：列出 Azure 中 SAP 支持的 SAP 软件部署的先决条件
* SAP 说明 [2002167] 已建议为红帽企业 Linux 设置操作系统设置
* SAP 说明 [2009879] 包含适用于 Red Hat Enterprise Linux 的 SAP HANA 准则
* SAP 注释[2178632]：包含有关 Azure 中为 SAP 报告的所有监视指标的详细信息
* SAP 注释[2191498]： 包含 Azure 中 Linux 所需的 SAP 主机代理版本
* SAP 注释[2243692]： 包含有关 Azure 中 Linux 上的 SAP 许可的信息
* SAP 说明[1999351]： 包含用于 SAP 的 Azure 增强监视扩展的其他故障排除信息
* SAP 说明[1900823]： 包含有关 SAP HANA 存储要求的信息
* [SAP 社区 Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)： 包含 Linux 所需的所有 SAP 笔记
* [针对 Linux 上的 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* 通用 RHEL 文档
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)（高可用性附加产品概述）
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)（高可用性附加产品管理）
  * [High Availability Add-On 参考](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [红帽企业 Linux 网络指南](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
* 特定于 Azure 的 RHEL 文档：
  * [Install SAP HANA on Red Hat Enterprise Linux for Use in Microsoft Azure](https://access.redhat.com/solutions/3193782)（在 Red Hat Enterprise Linux 上安装要在 Microsoft Azure 中使用的 SAP HANA）
* [使用 Azure NetApp 文件在 Microsoft Azure 上使用 NetApp SAP 应用程序][anf-sap-applications-azure]


## <a name="overview"></a>概述

实现 HANA 高可用性的一种方法是配置主机自动故障转移。 要配置主机自动故障转移，请将一个或多个虚拟机添加到 HANA 系统，并将其配置为备用节点。 当活动节点发生故障时，备用节点会自动接管。 在 Azure 虚拟机的显示配置中，通过使用[Azure NetApp 文件上的 NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)实现自动故障转移。  

> [!NOTE]
> 备用节点需要访问所有数据库卷。 HANA 卷必须装入 NFSv4 卷。 NFSv4 协议中改进的基于文件租约的锁定机制用于`I/O`屏蔽。 

> [!IMPORTANT]
> 要构建支持的配置，必须将 HANA 数据和日志卷部署为 NFSv4.1 卷，并使用 NFSv4.1 协议装载它们。 NFSv3 不支持具有备用节点的 HANA 主机自动故障转移配置。

![SAP NetWeaver 高可用性概述](./media/sap-hana-high-availability-rhel/sap-hana-scale-out-standby-netapp-files-rhel.png)

在遵循 SAP HANA 网络建议的上图中，三个子网在一个 Azure 虚拟网络中表示： 
* 用于客户端通信
* 用于与存储系统通信
* 用于内部 HANA 节点间通信

Azure NetApp 卷位于单独的子网中，[委托给 Azure NetApp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)。  

对于此示例配置，子网是：  

  - `client`10.9.1.0/26  
  - `storage`10.9.3.0/26  
  - `hana`10.9.2.0/26  
  - `anf`10.9.0.0/26（委派子网到 Azure NetApp 文件）

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>设置 Azure NetApp 文件基础结构 

在继续设置 Azure NetApp 文件基础结构之前，请熟悉 Azure [NetApp 文件文档][anf-azure-doc]。 

Azure NetApp 文件在多个[Azure 区域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)中可用。 检查所选 Azure 区域是否提供 Azure NetApp 文件。  

有关 Azure NetApp 文件（按 Azure 区域）的可用性的信息，请参阅[Azure NetApp 文件可用性（按 Azure 区域][anf-avail-matrix]）。  

在部署 Azure NetApp 文件之前，请通过注册[Azure NetApp 文件说明][anf-register]请求载入 Azure NetApp 文件。 

### <a name="deploy-azure-netapp-files-resources"></a>部署 Azure NetApp 文件资源  

以下说明假定您已部署[Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。 Azure NetApp 文件资源和 VM（其中将装载 Azure NetApp 文件资源）必须部署在同一 Azure 虚拟网络或对等 Azure 虚拟网络中。  

1. 如果尚未部署资源，请请求载入 Azure [NetApp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)。  

2. 按照创建 NetApp 帐户中的说明在所选 Azure 区域中[创建 NetApp 帐户](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)。  

3. 按照[设置 Azure NetApp 文件容量池](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)中的说明设置 Azure NetApp 文件容量池。  

   本文中介绍的 HANA 体系结构使用*超服务级别*的单个 Azure NetApp 文件容量池。 对于 Azure 上的 HANA 工作负荷，我们建议使用 Azure NetApp 文件*超或**高级*[服务级别](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)。  

4. 将子网委派给 Azure NetApp 文件，如[将子网委派到 Azure NetApp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)中的说明中所述。  

5. 按照为[Azure NetApp 文件创建 NFS 卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)中的说明部署 Azure NetApp 文件卷。  

   部署卷时，请确保选择**NFSv4.1**版本。 在指定的 Azure NetApp 文件[子网](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)中部署卷。 
   
   请记住，Azure NetApp 文件资源和 Azure VM 必须位于同一 Azure 虚拟网络或对等 Azure 虚拟网络中。 例如 **，HN1**-data-mnt00001、HN1 -log-mnt00001 等，是卷名称，nfs://10.9.0.4/ **HN1****HN1**-数据-mnt00001、nfs://10.9.0.4/**HN1**-log-mnt00001 等，是 Azure NetApp 文件卷的文件路径。  

   * 卷**HN1**-数据 mnt00001 （nfs://10.9.0.4/**HN1**-数据 mnt00001）
   * 卷**HN1**-数据 mnt00002 （nfs://10.9.0.4/**HN1**-数据 mnt00002）
   * 卷**HN1**-log-mnt00001 （nfs://10.9.0.4/**HN1**-log mnt00001）
   * 卷**HN1**-log-mnt0002 （nfs://10.9.0.4/**HN1**-log mnt00002）
   * 卷**HN1**-共享 （nfs://10.9.0.4/**HN1**-共享）
   
   在此示例中，我们为每个 HANA 数据和日志卷使用了单独的 Azure NetApp 文件卷。 要在较小或非生产性系统上进行成本优化的配置，可以将所有数据装载放在单个卷上，并且所有日志都安装在不同的单个卷上。  

### <a name="important-considerations"></a>重要注意事项

在创建 Azure NetApp 文件以使用备用节点方案进行 SAP HANA 横向扩展时，请注意以下重要注意事项：

- 最小容量池为 4 字节 （TiB）。  
- 最小体积大小为 100 GB （GiB）。
- Azure NetApp 文件和将装载 Azure NetApp 文件卷的所有虚拟机必须位于同一 Azure 虚拟网络中或同一区域中的[对等虚拟网络中](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。  
- 所选虚拟网络必须具有委派给 Azure NetApp 文件的子网。
- Azure NetApp 文件卷的吞吐量是卷配额和服务级别的函数，如[Azure NetApp 文件的服务级别](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)中所述。 调整 HANA Azure NetApp 卷的量量时，请确保生成的吞吐量满足 HANA 系统要求。  
- 使用 Azure NetApp 文件[导出策略](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)，可以控制允许的客户端、访问类型（读写、只读等）。 
- Azure NetApp 文件功能尚未区域感知。 目前，该功能未部署在 Azure 区域中的所有可用性区域中。 请注意某些 Azure 区域的潜在延迟影响。  

> [!IMPORTANT]
> 对于 SAP HANA 工作负载，低延迟至关重要。 与 Microsoft 代表合作，确保虚拟机和 Azure NetApp 文件卷在接近的地方部署。  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Azure NetApp 文件上的 HANA 数据库的尺寸

Azure NetApp 文件卷的吞吐量是卷大小和服务级别的函数，如[Azure NetApp 文件的服务级别](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)中所述。 

在 Azure 中为 SAP 设计基础结构时，请注意 SAP 的一些最低存储要求，这转化为最低吞吐量特征：

- 在 /hana/log 上读取写入，每秒 250 MB （MB/s），具有 1-MB I/O 大小。  
- 读取 16 MB 和 64 MB I/O 大小的 /hana/数据至少 400 MB/s 的活动。  
- 使用 16 MB 和 64 MB I/O 大小为 /hana/数据写入至少 250 MB/s 的活动。 

[Azure NetApp 文件吞吐量限制](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)每 1 TiB 的卷配额是：
- 高级存储层 - 64 MiB/s  
- 超存储层 - 128 MiB/s  

为了满足 SAP 数据和日志的最低吞吐量要求以及 /hana/共享的准则，建议的大小是：

| 数据量(Volume) | 大小<br>高级存储层 | 大小<br>超存储层 | 支持的 NFS 协议 |
| --- | --- | --- | --- |
| /哈纳/日志/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/shared | 每 4 个辅助节点 1xRAM | 每 4 个辅助节点 1xRAM | v3 或 v4.1 |

使用 Azure NetApp 文件超存储层，本文中介绍的布局的 SAP HANA 配置是：

| 数据量(Volume) | 大小<br>超存储层 | 支持的 NFS 协议 |
| --- | --- | --- |
| /哈纳/日志/mnt00001 | 2 TiB | v4.1 |
| /哈纳/日志/mnt00002 | 2 TiB | v4.1 |
| /哈纳/数据/mnt00001 | 3.2 TiB | v4.1 |
| /哈纳/数据/mnt00002 | 3.2 TiB | v4.1 |
| /hana/shared | 2 TiB | v3 或 v4.1 |

> [!NOTE]
> 此处列出的 Azure NetApp 文件大小调整建议旨在满足 SAP 为其基础结构提供程序建议的最低要求。 在实际的客户部署和工作负载方案中，这些大小可能不够。 将这些建议作为起点，并根据特定工作负载的要求进行调整。  

> [!TIP]
> 您可以动态调整 Azure NetApp 文件卷的大小，而无需*卸载*卷、停止虚拟机或停止 SAP HANA。 此方法允许灵活性，以满足应用程序的预期和意外吞吐量需求。

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>通过 Azure 门户部署 Linux 虚拟机

首先，您需要创建 Azure NetApp 文件卷。 然后执行以下步骤：
1. 在[Azure 虚拟网络中](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)创建[Azure 虚拟网络子网](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)。 
1. 部署 VM。 
1. 创建其他网络接口，并将网络接口附加到相应的 VM。  

   每个虚拟机都有三个网络接口，对应于三个 Azure 虚拟网络子网`client`（`storage`和`hana`。 

   有关详细信息，请参阅在[Azure 中使用多个网络接口卡创建 Linux 虚拟机](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics)。  

> [!IMPORTANT]
> 对于 SAP HANA 工作负载，低延迟至关重要。 要实现低延迟，请与 Microsoft 代表合作，确保虚拟机和 Azure NetApp 文件卷在接近的地方部署。 当您使用 SAP HANA Azure NetApp 文件[加入新的 SAP HANA 系统](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)时，提交必要的信息。 
 
接下来的说明假定您已经创建了资源组、Azure 虚拟网络和三个 Azure 虚拟网络子网：`client`和`storage``hana`。 部署 VM 时，请选择客户端子网，以便客户端网络接口是 VM 上的主接口。 您还需要通过存储子网网关配置到 Azure NetApp 文件委派子网的显式路由。 

> [!IMPORTANT]
> 确保您选择的操作系统已针对您正在使用的特定 VM 类型对 SAP HANA 进行了 SAP 认证。 有关这些类型的 SAP HANA 认证 VM 类型和操作系统版本的列表，请访问[SAP HANA 认证的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)站点。 单击列出的 VM 类型的详细信息，获取该类型支持的 SAP HANA 操作系统版本的完整列表。  

1. 为 SAP HANA 创建可用性集。 请确保设置最大更新域。  

2. 通过执行以下步骤创建三个虚拟机（hanadb1、hanadb2、hanadb3）：**hanadb1** **hanadb2** **hanadb3**  

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在 SAP HANA 支持的 Azure 库中使用红帽企业 Linux 映像。 在本示例中，我们使用 RHEL-SAP-HA 7.6 图像。  

   b.保留“数据库类型”设置，即设置为“共享”。 选择您之前为 SAP HANA 创建的可用性集。  

   c. 选择客户端 Azure 虚拟网络子网。 选择[加速网络](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)。  

   部署虚拟机时，将自动生成网络接口名称。 在这些简单性说明中，我们将引用自动生成的网络接口，这些接口连接到客户端 Azure 虚拟网络子网，作为**hanadb1 客户端****、hanadb2 客户端**和**hanadb3 客户端**。 

3. 为`storage`虚拟网络子网创建三个网络接口，每个虚拟机一个接口（在此示例中为**hanadb1 存储****、hanadb2 存储**和**hanadb3 存储**）。  

4. 为`hana`虚拟网络子网创建三个网络接口，每个虚拟机一个接口（在此示例中为**hanadb1-hana、hanadb2-hana**和**hanadb3-hana）。** **hanadb2-hana**  

5. 通过执行以下步骤，将新创建的虚拟网络接口附加到相应的虚拟机：  

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 转到[Azure 门户](https://portal.azure.com/#home)中的虚拟机。  

    b.保留“数据库类型”设置，即设置为“共享”。 在左侧窗格中，选择 **"虚拟机**"。 筛选虚拟机名称（例如**hanadb1），** 然后选择虚拟机。  

    c. 在 **"概述"** 窗格中，选择 **"停止"** 以取消分配虚拟机。  

    d.单击“下一步”。 选择 **"网络**"，然后附加网络接口。 在**附加网络接口**下拉列表中，为`storage`和`hana`子网选择已创建的网络接口。  
    
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 选择“保存”。**** 
 
    f. 对剩余的虚拟机重复步骤 b 到 e（在我们的示例中 **，hanadb2**和**hanadb3）。**
 
    g. 将虚拟机保留为停止状态。 接下来，我们将为所有新连接的网络接口启用[加速网络](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)。  

6. 通过执行以下步骤，为 和`storage``hana`子网的其他网络接口启用加速网络：  

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在[Azure 门户](https://portal.azure.com/#home)中打开[Azure 云外壳](https://azure.microsoft.com/features/cloud-shell/)。  

    b.保留“数据库类型”设置，即设置为“共享”。 执行以下命令，为附加网络接口启用加速网络，这些接口连接到`storage`和`hana`子网。  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. 通过执行以下步骤启动虚拟机：  

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在左侧窗格中，选择 **"虚拟机**"。 筛选虚拟机名称（例如**hanadb1），** 然后选择它。  

    b.保留“数据库类型”设置，即设置为“共享”。 在 **"概述"** 窗格中，选择 **"开始**"。  

## <a name="operating-system-configuration-and-preparation"></a>操作系统配置和准备

下一节中的说明用以下内容之一进行预缀：
* **[A]**： 适用于所有节点
* **[1]**： 仅适用于节点 1
* **[2]**： 仅适用于节点 2
* **[3]**：仅适用于节点 3

通过执行以下步骤配置和准备操作系统：

1. **[A]** 维护虚拟机上的主机文件。 包括所有子网的条目。 此示例添加了`/etc/hosts`以下条目。  

    <pre><code>
    # Storage
    10.9.3.4   hanadb1-storage
    10.9.3.5   hanadb2-storage
    10.9.3.6   hanadb3-storage
    # Client
    10.9.1.5   hanadb1
    10.9.1.6   hanadb2
    10.9.1.7   hanadb3
    # Hana
    10.9.2.4   hanadb1-hana
    10.9.2.5   hanadb2-hana
    10.9.2.6   hanadb3-hana
    </code></pre>

3. **[A]** 添加网络路由，以便与 Azure NetApp 文件的通信通过存储网络接口进行。  

   在此示例中，将用于`Networkmanager`配置其他网络路由。 以下说明假定存储网络接口为`eth1`。  
   首先，确定设备`eth1`的连接名称。 在此示例中，设备的`eth1`连接名称为`Wired connection 1`。  

    <pre><code>
    # Execute as root
    nmcli connection
    # Result
    #NAME                UUID                                  TYPE      DEVICE
    #System eth0         5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  eth0
    #Wired connection 1  4b0789d1-6146-32eb-83a1-94d61f8d60a7  ethernet  eth1
    </code></pre>

   然后通过`eth1`配置到 Azure NetApp 文件委派网络的其他路由。  

    <pre><code>
    # Add the following route 
    # ANFDelegatedSubnet/cidr via StorageSubnetGW dev StorageNetworkInterfaceDevice
    nmcli connection modify <b>"Wired connection 1"</b> +ipv4.routes <b>"10.9.0.0/26 10.9.3.1"</b>
    </code></pre>

    重新启动 VM 以激活更改。  

3. **[A]** 安装 NFS 客户端包。  

    <pre><code>
    yum install nfs-utils
    </code></pre>

3. **[A]** 准备操作系统，以便使用 NFS 在 Azure NetApp 上运行 SAP HANA，如[NETApp AFF 系统上的 SAP HANA 中所述，以及 NFS 配置指南](https://www.netapp.com/us/media/tr-4435.pdf)。 为 NetApp 配置设置创建配置文件 */etc/sysctl.d/netapp-hana.conf。*  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000 net.ipv4.tcp_slow_start_after_idle=0 net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** 创建具有其他优化设置的配置文件 */etc/sysctl.d/ms-az.conf。*  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.ip_local_port_range = 40000 65300
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

5. **[A]** 调整阳光设置，如[NETApp AFF 系统上的 SAP HANA 中的建议，以及 NFS 配置指南](https://www.netapp.com/us/media/tr-4435.pdf)。  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

6. **[A]** 用于 HANA 配置的红帽。

    配置 RHEL，如 SAP 说明<https://access.redhat.com/solutions/2447641>2292690、2455582、2593824 和 。 [2292690] [2455582] [2593824]

    > [!NOTE]
    > 如果安装 HANA 2.0 SP04，则需要按照`compat-sap-c++-7`SAP 说明[2593824]中所述安装软件包，然后才能安装 SAP HANA。 

## <a name="mount-the-azure-netapp-files-volumes"></a>装载 Azure NetApp 文件卷

1. **[A]** 为 HANA 数据库卷创建装载点。  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** 在**HN1**共享上为 /usr/sap 创建特定于节点的目录。  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    mount <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** 验证 NFS 域设置。 确保域配置为默认的 Azure NetApp 文件域，即**`defaultv4iddomain.com`** 映射设置为**无人**。  

    > [!IMPORTANT]
    > 请确保在 VM 上`/etc/idmapd.conf`设置 NFS 域，以匹配 Azure NetApp 文件上的默认**`defaultv4iddomain.com`** 域配置： 。 如果 NFS 客户端（即 VM）上的域配置与 NFS 服务器（即 Azure NetApp 配置）不匹配，则安装在 VM 上的 Azure NetApp 卷上的文件权限将显示为`nobody`。  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** `nfs4_disable_idmapping`验证 。 应将其设置为**Y**。要创建位于的位置`nfs4_disable_idmapping`的目录结构，请执行装载命令。 您将无法在 /sys/模块下手动创建目录，因为访问是为内核/驱动程序保留的。  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   有关如何更改`nfs4_disable_idmapping`参数的更多详细信息，请参阅https://access.redhat.com/solutions/1749883。

6. **[A]** 装载共享的 Azure NetApp 文件卷。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** 将节点特定的卷安装在**hanadb1**上。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** 将节点特定的卷安装在**hanadb2**上。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** 将节点特定的卷安装在**hanadb3**上。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** 验证所有 HANA 卷是否都装载了 NFS 协议版本**NFSv4**。  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/data/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /usr/sap/<b>HN1</b> from 10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/shared from 10.9.0.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    </code></pre>

## <a name="installation"></a>安装  

在此示例中，使用 Azure 的备用节点在横向扩展配置中部署 SAP HANA，我们使用了 HANA 2.0 SP4。  

### <a name="prepare-for-hana-installation"></a>准备 HANA 安装

1. **[A]** 在 HANA 安装之前，设置根密码。 安装完成后，可以禁用根密码。 执行为`root`命令`passwd`。  

2. **[1]** 验证您可以通过 SSH 登录到**hanadb2**和**hanadb3，** 而无需提示输入密码。  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** 安装 HANA 2.0 SP4 所需的其他软件包。 有关详细信息，请参阅 SAP 说明[2593824](https://launchpad.support.sap.com/#/notes/2593824)。 

    <pre><code>
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1 
    </code></pre>

4. **[2]，[3]** 将 SAP HANA `data` `log`和目录的所有权更改为**hn1**adm。   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

5. **[A]** 暂时禁用防火墙，以便它不会干扰 HANA 安装。 完成 HANA 安装后，您可以重新启用它。 
   <pre><code>
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
   </code></pre>

### <a name="hana-installation"></a>HANA 安装

1. **[1]** 按照[SAP HANA 2.0 安装和更新指南](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)中的说明安装 SAP HANA。 在此示例中，我们使用主节点、一个辅助节点和一个备用节点安装 SAP HANA 横向扩展。  

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 从 HANA 安装软件目录中启动**hdblcm**程序。 使用`internal_network`参数并传递子网的地址空间，该子网用于内部 HANA 节点间通信。  

    <pre><code>
    ./hdblcm --internal_network=10.9.2.0/26
    </code></pre>

   b.保留“数据库类型”设置，即设置为“共享”。 在提示符处，输入以下值：

     * 对于**选择操作**：输入**1（** 用于安装）
     * 对于**用于安装的其他组件**：输入**2、3**
     * 对于安装路径：按 Enter（默认为 /hana/共享）
     * 对于**本地主机名**：按 Enter 接受默认值
     * 在 **"是否要将主机添加到系统？"：** 输入**y**
     * 对于逗**号分隔主机名要添加**： 输入**hanadb2，hanadb3**
     * 对于**根用户名**[根]：按 Enter 接受默认值
     * 对于主机 hanadb2 的角色：输入**1（** 对于工作人员）
     * 对于主机 hanadb2 的**主机故障转移组**[默认值]：按 Enter 接受默认值
     * 对于主机 hanadb2 [<>]<assign automatically>的**存储分区编号**：按 Enter 接受默认值
     * 对于主机 hanadb2 [默认]**的工作人员组**：按 Enter 接受默认值
     * 对于主机 hanadb3**的选定角色**：输入**2（** 用于待机）
     * 对于主机 hanadb3 的**主机故障转移组**[默认]：按 Enter 接受默认值
     * 对于主机 hanadb3 [默认]**的工作人员组**：按 Enter 接受默认值
     * 对于**SAP HANA 系统 ID**：输入**HN1**
     * 对于**实例编号**[00]： 输入**03**
     * 对于**本地主机辅助角色组**[默认]：按 Enter 接受默认值
     * 对于**选择系统使用情况 / 输入索引 [4]**： 输入**4** （用于自定义）
     * 有关**数据卷的位置**[/hana/数据/HN1]：按 Enter 接受默认值
     * 有关**日志卷的位置**[/哈纳/日志/HN1]：按 Enter 接受默认值
     * 对于**限制最大内存分配？** [n]： 输入**n**
     * 对于**主机 hanadb1 的证书主机名称1** [hanadb1]：按 Enter 接受默认值
     * 对于**主机 hanadb2 的证书主机名称2** [hanadb2]：按 Enter 接受默认值
     * 对于**主机 hanadb3 的证书主机名称3** [hanadb3]：按 Enter 接受默认值
     * 对于**系统管理员 （hn1adm） 密码**：输入密码
     * 对于**系统数据库用户（系统）密码**：输入系统的密码
     * 对于**确认系统数据库用户（系统）密码**：输入系统的密码
     * 对于**计算机重新启动后的重新启动系统？** [n]： 输入**n** 
     * 对于**是否要继续 （y/n）：** 验证摘要，如果一切看起来良好，请输入**y**


2. **[1]** 验证全局  

   显示全局.ini，并确保内部 SAP HANA 节点间通信的配置到位。 验证**通信**部分。 它应具有`hana`子网的地址空间，并且`listeninterface`应设置为`.internal`。 验证**internal_hostname_resolution**部分。 它应具有属于子网的 HANA 虚拟机的`hana`IP 地址。  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.9.2.0/26</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.9.2.4</b> = <b>hanadb1</b>
    <b>10.9.2.5</b> = <b>hanadb2</b>
    <b>10.9.2.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** 添加主机映射以确保客户端 IP 地址用于客户端通信。 添加`public_host_resolution`节 ，并从客户端子网添加相应的 IP 地址。  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.9.1.5</b>
    map_<b>hanadb2</b> = <b>10.9.1.6</b>
    map_<b>hanadb3</b> = <b>10.9.1.7</b>
   </code></pre>

4. **[1]** 重新启动 SAP HANA 以激活更改。  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** 验证客户端接口是否将使用子网中的`client`IP 地址进行通信。  

   <pre><code>
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.9.1.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.9.1.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.9.1.5</b>"
   </code></pre>

   有关如何验证配置的信息，请参阅 SAP 注释[2183363 - SAP HANA 内部网络的配置](https://launchpad.support.sap.com/#/notes/2183363)。  

5. **[A]** 重新启用防火墙。  
   - 停止 HANA
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
       </code></pre>
   - 重新启用防火墙
       <pre><code>
        # Execute as root
        systemctl start firewalld
        systemctl enable firewalld
       </code></pre>

   - 打开必要的防火墙端口

       > [!IMPORTANT]
       > 创建防火墙规则以允许 HANA 节点间通信和客户端流量。 [所有 SAP 产品的 TCP/IP 端口](https://help.sap.com/viewer/ports)上均列出了所需端口。 以下命令只是一个示例。 在这种情况下，使用系统编号为 03。

       <pre><code>
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       </code></pre>

   - 开始 HANA
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
       </code></pre>

6. 要优化基础 Azure NetApp 文件存储的 SAP HANA，设置以下 SAP HANA 参数：

   - `max_parallel_io_requests`**128**
   - `async_read_submit` **on**
   - `async_write_submit_active` **on**
   - `async_write_submit_blocks`**所有**

   有关详细信息，请参阅[NetApp AFF 系统上的 SAP HANA，以及 NFS 配置指南](https://www.netapp.com/us/media/tr-4435.pdf)。 

   从 SAP HANA 2.0 系统开始，您可以在 中`global.ini`设置参数。 有关详细信息，请参阅 SAP 说明[1999930](https://launchpad.support.sap.com/#/notes/1999930)。  
   
   对于 SAP HANA 1.0 系统版本 SPS12 和更早版本，可以在安装过程中设置这些参数，如 SAP 说明[2267798](https://launchpad.support.sap.com/#/notes/2267798)中所述。  

7. Azure NetApp 文件使用的存储具有 16 TB 的文件大小限制。 SAP HANA 不会隐式地意识到存储限制，并且在达到 16 TB 的文件大小限制时，它不会自动创建新的数据文件。 当 SAP HANA 尝试将文件扩展到 16 TB 以上时，该尝试将导致错误，并最终导致索引服务器崩溃。 

   > [!IMPORTANT]
   > 为防止 SAP HANA 尝试将数据文件增长到超出存储子系统[的 16 TB 限制](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits)，在 中`global.ini`设置以下参数。  
   > - datavolume_striping = true
   > - datavolume_striping_size_gb = 15000 有关详细信息，请参阅 SAP 说明[2400005](https://launchpad.support.sap.com/#/notes/2400005)。
   > 请注意 SAP 注释[2631285](https://launchpad.support.sap.com/#/notes/2631285)。 

## <a name="test-sap-hana-failover"></a>测试 SAP HANA 故障转移 

1. 模拟 SAP HANA 辅助节点上的节点崩溃。 请执行以下操作： 

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在模拟节点崩溃之前，将以下命令运行为**hn1**dm 以捕获环境状态：  

   <pre><code>
    # Check the landscape status
    python /usr/sap/HN1/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |

    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   b.保留“数据库类型”设置，即设置为“共享”。 要模拟节点崩溃，在辅助节点上运行以下命令作为根，在这种情况下为**hanadb2：**  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. 监视系统故障转移完成情况。 故障转移完成后，捕获状态，该状态应如下所示：  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > 当节点遇到内核死机时，通过`kernel.panic`*在所有*HANA 虚拟机上设置为 20 秒，避免 SAP HANA 故障转移的延迟。 配置在 中`/etc/sysctl`完成。 重新启动虚拟机以激活更改。 如果未执行此更改，则当节点遇到内核死机时，故障转移可能需要 10 分钟或更长时间。  

2. 通过执行以下操作终止名称服务器：

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在测试之前，通过将以下命令作为**hn1**adm 来检查环境的状态：  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
   </code></pre>

   b.保留“数据库类型”设置，即设置为“共享”。 在活动主节点上以**hn1**dm 身份运行以下命令，在这种情况下，该节点为**hanadb1：**  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    备用节点**hanadb3**将接管主节点。 以下是故障转移测试完成后的资源状态：  

    <pre><code>
     # Check the instance status
     sapcontrol -nr 03  -function GetSystemInstanceList
     GetSystemInstanceList
     OK
     hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
     hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
     hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
     hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
     # Check the landscape status
     python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
     | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
     |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
     |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
     | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
     | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
     | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
     | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
    </code></pre>

   c. 重新启动**hanadb1**上的 HANA 实例（即在同一虚拟机上，其中名称服务器已终止）。 **hanadb1**节点将重新加入环境，并将保留其备用角色。  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   在**HANadb1**上启动 SAP HANA 后，预期状态如下：  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d.单击“下一步”。 同样，在当前活动的主节点（即节点**hanadb3**上）上终止名称服务器。  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   节点**hanadb1**将恢复主节点的角色。 故障转移测试完成后，状态将如下所示：

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 在**hanadb3**上启动 SAP HANA，它将准备作为备用节点。  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   在**HANadb3**上启动 SAP HANA 后，状态如下所示：  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>后续步骤

* [适用于 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* 若要了解如何建立高可用性以及针对 Azure 上的 SAP HANA（大型实例）规划灾难恢复，请参阅 [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md)。
* 要了解如何在 Azure VM 上建立高可用性并规划 SAP HANA 的灾难恢复，请参阅[Azure 虚拟机 （VM） 上的 SAP HANA 的高可用性][sap-hana-ha]。
