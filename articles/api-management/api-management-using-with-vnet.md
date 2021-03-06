---
title: 如何在虚拟网络中使用 Azure API 管理
description: 了解如何在 Azure API 管理中设置与虚拟网络的连接并通过它访问 Web 服务。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/09/2020
ms.author: apimpm
ms.openlocfilehash: 462a44f7766e0ec52ba7156d6de5ae5261e21376
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547365"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>如何在虚拟网络中使用 Azure API 管理
使用 Azure 虚拟网络 (VNET) 可将多个 Azure 资源置于可以控制其访问权限但无法通过 Internet 路由的网络中。 然后，可以使用各种 VPN 技术将这些网络连接到本地网络。 若要了解有关 Azure 虚拟网络的详细信息，请先了解以下信息：[Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)。

可以将 Azure API 管理部署到虚拟网络 (VNET) 内部，以便它可以访问该网络中的后端服务。 可以将开发人员门户和 API 网关配置为可以从 Internet 访问或只能在虚拟网络内访问。

> [!NOTE]
> API 导入文档 URL 必须托管在可公开访问的 Internet 地址上。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>先决条件

若要执行本文中所述的步骤，必须具有：

+ 一个有效的 Azure 订阅。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ 一个 APIM 实例。 有关详细信息，请参阅[创建 Azure API 管理实例](get-started-create-service-instance.md)。

## <a name="enable-vnet-connection"></a><a name="enable-vpn"> </a>启用 VNET 连接

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>使用 Azure 门户启用 VNET 连接

1. 转到[Azure 门户](https://portal.azure.com)以查找 API 管理实例。 搜索并选择**API 管理服务**。

2. 选择 API 管理实例。

3. 选择**虚拟网络**。
4. 配置要在虚拟网络内部署的 API 管理实例。

    ![API 管理的虚拟网络菜单][api-management-using-vnet-menu]
5. 选择所需的访问类型：

    * **关闭**：这是默认值。 API 管理未部署到虚拟网络中。

    * **外部**：API 管理网关和开发人员门户可通过外部负载均衡器从公共 Internet 访问。 网关可以访问虚拟网络中的资源。

        ![公共对等互连][api-management-vnet-public]

    * **内部**：API 管理网关和开发人员门户只能通过内部负载均衡器从虚拟网络内访问。 网关可以访问虚拟网络中的资源。

        ![专用对等互连][api-management-vnet-private]

6. 如果选择 **"外部**"或"**内部**"，您将看到预配 API 管理服务的所有区域的列表。 选择**位置**，然后选择其**虚拟网络和****子网**。 虚拟网络列表填充了在要配置的区域中设置的 Azure 订阅中可用的经典和资源管理器虚拟网络。

    > [!IMPORTANT]
    > 将 Azure API 管理实例部署到 Resource Manager VNET 时，该服务必须位于除了 Azure API 管理实例之外不包含其他资源的专用子网中。 如果尝试将 Azure API 管理实例部署到包含其他资源的 Resource Manager VNET 子网，则部署会失败。

    然后，选择“应用”****。 API 管理实例的**虚拟网络**页面将随新的虚拟网络和子网选项进行更新。

    ![选择 VPN][api-management-setup-vpn-select]

7. 在顶部导航栏中，选择 **"保存**"，然后选择 **"应用网络配置**"。

> [!NOTE]
> 每次启用或禁用 VNET 时，API 管理实例的 VIP 地址都会更改。
> 当 API 管理从**外部**移动到**内部**时，VIP 地址也会更改，反之亦然。
>

> [!IMPORTANT]
> 如果从 VNET 中删除 API 管理或更改在其中部署的 API 管理，则之前使用的 VNET 可最多 6 小时保持锁定状态。 在此期间，无法删除该 VNET 或向其部署新资源。 对于使用 api-version 2018-01-01 及更早版本的客户端，此行为是正确的。 使用 api-version 2019-01-01 及更高版本的客户端，在删除关联的 API 管理服务后，将释放 VNET。

## <a name="enable-vnet-connection-using-powershell-cmdlets"></a><a name="enable-vnet-powershell"> </a>使用 PowerShell cmdlet 启用 VNET 连接
还可以使用 PowerShell cmdlet 启用 VNET 连接。

* **在 VNET 中创建 API 管理服务**：使用 cmdlet [New-AzApi 管理](/powershell/module/az.apimanagement/new-azapimanagement)在 VNET 中创建 Azure API 管理服务。

* **在 VNET 中部署现有的 API 管理服务**：使用 cmdlet[更新-AzApi管理区域](/powershell/module/az.apimanagement/update-azapimanagementregion)在虚拟网络内移动现有的 Azure API 管理服务。

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a><a name="connect-vnet"> </a>连接到虚拟网络中托管的 Web 服务
将 API 管理服务连接到 VNET 后，访问 VNET 中的后端服务与访问公共服务无异。 在创建新的 API 或编辑现有 API 时，只需将 Web 服务的本地 IP 地址或主机名（如果为 VNET 配置了 DNS 服务器）键入“Web 服务 URL”字段。****

![从 VPN 添加 API][api-management-setup-vpn-add-api]

## <a name="common-network-configuration-issues"></a><a name="network-configuration-issues"> </a>常见网络配置问题
下面是将 API 管理服务部署到虚拟网络时可能会发生的常见错误配置问题的列表。

* **自定义 DNS 服务器设置**：API 管理服务依赖于多项 Azure 服务。 当 API 管理托管在包含自定义 DNS 服务器的 VNET 中时，API 管理需要解析这些 Azure 服务的主机名。 请根据[此](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)指南进行自定义 DNS 安装。 有关参考信息，请参阅下面的端口表和其他网络要求。

> [!IMPORTANT]
> 如果计划对 VNET 使用自定义 DNS 服务器，应在向其部署 API 管理服务**之前**完成该设置。 否则，需要在每次通过运行[应用网络配置操作](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/ApiManagementService/ApplyNetworkConfigurationUpdates)更改 DNS 服务器时更新 API 管理服务

* **API 管理所需的端口**：可以使用[网络安全组][Network Security Group]控制其中部署了 API 管理的子网的入站和出站流量。 如果其中的任一端口不可用，API 管理可能无法正常工作且不可访问。 在 VNET 中使用 API 管理时，另一个常见的错误配置问题是阻止了这些端口中的一个或多个。

<a name="required-ports"> </a>当 API 管理服务实例托管在 VNET 中时，将使用下表中的端口。

| 源 / 目标端口 | 方向          | 传输协议 |   [服务标签](../virtual-network/security-overview.md#service-tags) <br> 源/目标   | 用途 ( * )                                                 | 虚拟网络类型 |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | 入站            | TCP                | INTERNET / VIRTUAL_NETWORK            | 客户端与 API 管理的通信                      | 外部             |
| * / 3443                     | 入站            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Azure 门户和 Powershell 的管理终结点         | 外部和内部  |
| * / 443                  | 出站           | TCP                | VIRTUAL_NETWORK / Storage             | **与 Azure 存储的依赖关系**                             | 外部和内部  |
| * / 443                  | 出站           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | [Azure 活动目录](api-management-howto-aad.md)（如适用）                   | 外部和内部  |
| * / 1433                     | 出站           | TCP                | VIRTUAL_NETWORK / SQL                 | **访问 Azure SQL 终结点**                           | 外部和内部  |
| * / 5671, 5672, 443          | 出站           | TCP                | VIRTUAL_NETWORK / EventHub            | [日志到事件中心策略](api-management-howto-log-event-hubs.md)和监视代理的依赖项 | 外部和内部  |
| * / 445                      | 出站           | TCP                | VIRTUAL_NETWORK / Storage             | [对 GIT](api-management-configuration-repository-git.md)的 Azure 文件共享的依赖性                      | 外部和内部  |
| * / 1886                     | 出站           | TCP                | VIRTUAL_NETWORK / AzureCloud            | 需要为发布到资源运行状况的运行状况状态          | 外部和内部  |
| * / 443                     | 出站           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | 发布[诊断日志和指标](api-management-howto-use-azure-monitor.md)                       | 外部和内部  |
| */25                       | 出站           | TCP                | VIRTUAL_NETWORK/INTERNET            | 连接到 SMTP 中继以发送电子邮件                    | 外部和内部  |
| */587                      | 出站           | TCP                | VIRTUAL_NETWORK/INTERNET            | 连接到 SMTP 中继以发送电子邮件                    | 外部和内部  |
| * / 25028                    | 出站           | TCP                | VIRTUAL_NETWORK/INTERNET            | 连接到 SMTP 中继以发送电子邮件                    | 外部和内部  |
| * / 6381 - 6383              | 入站和出站 | TCP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | 访问 Redis 服务，用于计算机之间的[速率限制](api-management-access-restriction-policies.md#LimitCallRateByKey)策略         | 外部和内部  |
| * / *                        | 入站            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Azure 基础结构负载均衡器                          | 外部和内部  |

>[!IMPORTANT]
> “用途”为**粗体**的端口是成功部署 API 管理服务所必需的。** 不过，阻止其他端口将导致使用和监视运行中服务的能力降级。

+ **TLS 功能**：要启用 TLS/SSL 证书链构建和验证，API 管理服务需要出站网络连接到ocsp.msocsp.com、mscrl.microsoft.com和crl.microsoft.com。 如果上传到 API 管理的任何证书包含指向 CA 根的完整链，则此依赖关系不是必需的。

+ **DNS 访问**：需要端口 53 上的出站访问权限才能与 DNS 服务器通信。 如果 VPN 网关的另一端存在自定义 DNS 服务器，则必须可从承载 API 管理的子网连接该 DNS 服务器。

+ **指标和运行状况监视**：出站网络连接到 Azure 监视终结点，以便在以下域下解析：

+ **区域服务标记**"：允许向外站连接到存储、SQL 和 EventHubs 服务标记的 NSG 规则可以使用与包含 API 管理实例的区域（例如，美国西部区域 API 管理实例的 Storage.WestUS）对应的区域版本。 在多区域部署中，每个区域中的 NSG 应允许对该区域的服务标记的流量。

    | Azure 环境 | 终结点                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | <ul><li>gcs.prod.monitoring.core.windows.net（**新**）</li><li>prod.warmpath.msftcloudes.com（**弃用**）</li><li>shoebox2.metrics.microsoftmetrics.com（**新**）</li><li>shoebox2.metrics.nsatc.net（**被弃用**）</li><li>prod3.metrics.microsoftmetrics.com（**新**）</li><li>prod3.metrics.nsatc.net（**被弃用**）</li><li>prod3-black.prod3.metrics.microsoftmetrics.com（**新**）</li><li>prod3-black.prod3.metrics.nsatc.net（**被弃用**）</li><li>prod3-red.prod3.metrics.microsoftmetrics.com（**新**）</li><li>prod3-red.prod3.metrics.nsatc.net（**被弃用**）</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`.warm.ingestion.msftcloudes.com，其中 `East US 2` 是 eastus2.warm.ingestion.msftcloudes.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.microsoftmetrics.com（**新**）</li><li>shoebox2.metrics.nsatc.net（**被弃用**）</li><li>prod3.metrics.microsoftmetrics.com（**新**）</li><li>prod3.metrics.nsatc.net（**被弃用**）</li><li>prod5.prod.microsoftmetrics.com</li></ul>                                                                                                                                                                                                                                                |
    | Azure 中国世纪互联     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.microsoftmetrics.com（**新**）</li><li>shoebox2.metrics.nsatc.net（**被弃用**）</li><li>prod3.metrics.microsoftmetrics.com（**新**）</li><li>prod3.metrics.nsatc.net（**被弃用**）</li><li>prod5.prod.microsoftmetrics.com</li></ul>                                                                                                                                                                                                                                                |

>[!IMPORTANT]
> 上面带有 dns 区域 **.nsatc.net**的群集更改为 **.microsoftmetrics.com**的群集主要是 DNS 更改。 群集的 IP 地址不会更改。

+ **SMTP 中继**`smtpi-co1.msn.com`：SMTP 中继的出站网络连接，在主机下解析`smtpi-ch1.msn.com`， 和`smtpi-db3.msn.com``smtpi-sin.msn.com``ies.global.microsoft.com`

+ **开发人员门户 CAPTCHA**：开发人员门户的 CAPTCHA 的出站网络连接，在主机`client.hip.live.com`和`partner.hip.live.com`下解析。

+ **Azure 门户诊断**：要在虚拟网络内部使用 API 管理扩展时从 Azure 门户启用诊断日志流，需要允许在端口 443 上对 `dc.services.visualstudio.com` 进行出站访问。 这有助于排查使用扩展时可能遇到的问题。

+ **使用快速路由或网络虚拟设备强制将流量隧道到预处理防火墙**：常见的客户配置是定义自己的默认路由 （0.0.0.0/0），该路由强制 API 管理委派子网中的所有流量流经本地防火墙或网络虚拟设备。 此流量流一定会中断与 Azure API 管理的连接，因为已在本地阻止出站流量，或者已 NAT 到不再与各种 Azure 终结点一起工作的一组无法识别的地址。 此解决方案要求你执行多项操作：

  * 在部署 API 管理服务时所在的子网上启用服务终结点。 需为 Azure SQL、Azure 存储、Azure 事件中心和 Azure 服务总线启用[服务终结点][ServiceEndpoints]。 直接从 API 管理委托的子网启用这些服务的终结点可以让它们使用 Microsoft Azure 主干网络，为服务流量提供优化的路由。 如果将服务终结点与强制隧道 API 管理配合使用，则不会将上述 Azure 服务流量进行强制隧道传输。 其他 API 管理服务依赖项流量会通过强制隧道重定向，不能丢失，否则 API 管理服务会功能失常。
    
  * 所有控制平面流量（从 Internet 到 API 管理服务的管理终结点）都会通过特定的一组由 API 管理托管的入站 IP 进行路由。 强制流量通过隧道时，响应不会对称地映射回那些入站源 IP。 为了克服此限制，我们需要添加下述用户定义路由 ([UDR][UDRs])，以便将流量引导回 Azure，方法是将这些主机路由的目标设置为“Internet”。 用于控制平面流量的入站 IP 集记录[控制平面 IP 地址](#control-plane-ips)

  * 对于进行强制隧道传输的其他 API 管理服务依赖项，应该可以通过某种方式解析主机名并访问终结点。 其中包括：
      - 指标和运行状况监视
      - Azure 门户诊断
      - SMTP 中继
      - 开发人员门户验证码

## <a name="troubleshooting"></a><a name="troubleshooting"> </a>疑难解答
* **初始安装**：如果在某个子网中初始部署 API 管理服务未成功，建议首先在同一子网中部署一个虚拟机。 接下来，在虚拟机中部署远程桌面，并验证是否存在与 Azure 订阅中的以下每个源的连接
    * Azure 存储 Blob
    * Azure SQL Database
    * Azure 存储表

  > [!IMPORTANT]
  > 在验证连接后，在将 API 管理部署到子网中之前，请确保删除子网中部署的所有资源。

* **增量更新**：对网络进行更改时，请参阅[NetworkStatus API，](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/networkstatus)以验证 API 管理服务是否未丢失对它所依赖的任何关键资源的访问权限。 连接状态应每 15 分钟更新一次。

* **资源导航链接**：部署到资源管理器样式的 vnet 子网中时，API 管理会通过创建一个资源导航链接来保留子网。 如果子网已包含来自其他提供程序的资源，则部署将**失败**。 类似地，将 API 管理服务移动到其他子网中或删除它时，将会删除该资源导航链接。

## <a name="subnet-size-requirement"></a><a name="subnet-size"> </a>子网大小要求
Azure 会保留每个子网中的某些 IP 地址，但是这些地址不能使用。 子网的第一个和最后一个 IP 地址仅为协议一致性而保留，其他三个地址用于 Azure 服务。 有关详细信息，请参阅[在这些子网中使用 IP 地址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

除了 Azure VNET 基础结构使用的 IP 地址外，子网中的每个 Api 管理实例还会针对开发人员 SKU 使用每个高级 SKU 单位两个 IP 地址和一个单 IP 地址。 每个实例为外部负载均衡器保留一个附加的 IP 地址。 部署到内部虚拟网络时，需要内部负载均衡器的附加 IP 地址。

给定上述子网的最小大小计算，其中可以部署 API 管理为 /29，提供三个可用的 IP 地址。

API 管理的每个附加扩展单元都需要两个 IP 地址。

## <a name="routing"></a><a name="routing"> </a>路由
+ 负载均衡公共 IP 地址 (VIP) 也将保留，用于访问所有服务终结点。
+ 将使用子网 IP 范围中的一个 IP 地址 (DIP) 来访问 vnet 中的资源，并使用一个公共 IP 地址 (VIP) 来访问 vnet 外部的资源。
+ 可以在 Azure 门户中的“概述/概要”边栏选项卡上找到负载均衡公共 IP 地址。

## <a name="limitations"></a><a name="limitations"> </a>限制
* 包含 API 管理实例的子网不能包含其他任何 Azure 资源类型。
* 子网和 API 管理服务必须在同一个订阅中。
* 无法在订阅之间移动包含 API 管理实例的子网。
* 对于在内部虚拟网络模式下配置的多区域 API 管理部署，用户负责管理多个区域之间的负载均衡，因为路由归他们拥有。
* 由于平台限制，从另一个区域中的全局对等互连 VNET 中的资源到内部模式下的 API 管理服务的连接将不起作用。 有关详细信息，请参阅[一个虚拟网络中的资源无法与对等互连虚拟网络中 Azure 内部负载均衡器通信](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)

## <a name="control-plane-ip-addresses"></a><a name="control-plane-ips"> </a>控制平面 IP 地址

IP 地址按 Azure**环境**划分。 允许使用**Global**标记的入站请求时，必须与**区域**特定的 IP 地址一起列入白名单。

| **Azure 环境**|   **区域**|  **IP 地址**|
|-----------------|-------------------------|---------------|
| Azure Public| 美国中南部（全球）| 104.214.19.224|
| Azure Public| 美国中北部（全球）| 52.162.110.80|
| Azure Public| 美国中西部| 52.253.135.58|
| Azure Public| 韩国中部| 40.82.157.167|
| Azure Public| 英国西部| 51.137.136.0|
| Azure Public| 日本西部| 40.81.185.8|
| Azure Public| 美国中北部| 40.81.47.216|
| Azure Public| 英国南部| 51.145.56.125|
| Azure Public| 印度西部| 40.81.89.24|
| Azure Public| 美国东部| 52.224.186.99|
| Azure Public| 西欧| 51.145.179.78|
| Azure Public| 日本东部| 52.140.238.179|
| Azure Public| 法国中部| 40.66.60.111|
| Azure Public| 加拿大东部| 52.139.80.117|
| Azure Public| 阿拉伯联合酋长国北部| 20.46.144.85|
| Azure Public| 巴西南部| 191.233.24.179|
| Azure Public| 东南亚| 40.90.185.46|
| Azure Public| 南非北部| 102.133.130.197|
| Azure Public| 加拿大中部| 52.139.20.34|
| Azure Public| 韩国南部| 40.80.232.185|
| Azure Public| 印度中部| 13.71.49.1|
| Azure Public| 美国西部| 13.64.39.16|
| Azure Public| 澳大利亚东南部| 20.40.160.107|
| Azure Public| 澳大利亚中部| 20.37.52.67|
| Azure Public| 印度南部| 20.44.33.246|
| Azure Public| 美国中部| 13.86.102.66|
| Azure Public| 澳大利亚东部| 20.40.125.155|
| Azure Public| 美国西部 2| 51.143.127.203|
| Azure Public| 美国东部 2 EUAP| 52.253.229.253|
| Azure Public| 美国中部 EUAP| 52.253.159.160|
| Azure Public| 美国中南部| 20.188.77.119|
| Azure Public| 美国东部 2| 20.44.72.3|
| Azure Public| 北欧| 52.142.95.35|
| Azure Public| 东亚| 52.139.152.27|
| Azure Public| 法国南部| 20.39.80.2|
| Azure Public| 瑞士西部| 51.107.96.8|
| Azure Public| 澳大利亚中部 2| 20.39.99.81|
| Azure Public| 阿联酋中部| 20.37.81.41|
| Azure Public| 瑞士北部| 51.107.0.91|
| Azure Public| 南非西部| 102.133.0.79|
| Azure Public| 德国中西部| 51.116.96.0|
| Azure Public| 德国北部| 51.116.0.0|
| Azure Public| 挪威东部| 51.120.2.185|
| Azure Public| 挪威西部| 51.120.130.134|
| Azure 中国世纪互联| 中国北部（全球）| 139.217.51.16|
| Azure 中国世纪互联| 华东（全球）| 139.217.171.176|
| Azure 中国世纪互联| 中国北部| 40.125.137.220|
| Azure 中国世纪互联| 中国东部| 40.126.120.30|
| Azure 中国世纪互联| 中国北部 2| 40.73.41.178|
| Azure 中国世纪互联| 中国东部 2| 40.73.104.4|
| Azure Government| 美国弗吉尼亚州州长（全球）| 52.127.42.160|
| Azure Government| 美国得克萨斯州（全球）| 52.127.34.192|
| Azure Government| USGov Virginia| 52.227.222.92|
| Azure Government| USGov Iowa| 13.73.72.21|
| Azure Government| US Gov 亚利桑那州| 52.244.32.39|
| Azure Government| US Gov 德克萨斯州| 52.243.154.118|
| Azure Government| USDoD 中部| 52.182.32.132|
| Azure Government| 美国国防部东| 52.181.32.192|

## <a name="related-content"></a><a name="related-content"> </a>相关的内容
* [使用 Vpn 网关将虚拟网络连接到后端](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [通过不同的部署模型连接虚拟网络](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [如何使用 API 检查器跟踪 Azure API 管理中的调用](api-management-howto-api-inspector.md)
* [虚拟网络 常见问题](../virtual-network/virtual-networks-faq.md)
* [服务标记](../virtual-network/security-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/security-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/security-overview.md#service-tags
