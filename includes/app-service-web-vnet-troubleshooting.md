---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 652d42d6e2d9e909c3a03bd82a3a36f91bc73807
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419547"
---
该功能易于设置，但这并不意味着您的体验将没有问题。 如果在访问所需终结点时遇到问题，可以使用一些实用程序从应用控制台测试连接。 可以使用两种控制台。 一个是库杜控制台，另一个是 Azure 门户中的控制台。 要从你的应用程序到达库杜控制台，请转到**工具** > **库杜**。 您还可以在 [站点名称]scm.azuresite.net 上访问 Kudo 控制台。 网站加载后，转到**调试控制台**选项卡。要从应用转到 Azure 门户托管的控制台，请转到**工具** > **控制台**。

#### <a name="tools"></a>工具
由于安全限制 **，ping、nslook****和** **tracert**的工具无法通过控制台工作。 为了填补空白，添加了两个单独的工具。 为了测试 DNS 功能，我们添加了一个名为**nameresolver.exe**的工具。 语法为：

    nameresolver.exe hostname [optional: DNS Server]

可以使用 nameresolver 来检查应用所需的主机名。 这样，您可以测试 DNS 中是否有错误配置或可能没有对 DNS 服务器的访问权限。 通过查看环境变量WEBSITE_DNS_SERVER和WEBSITE_DNS_ALT_SERVER，可以查看应用在控制台中使用的 DNS 服务器。

您可以使用下一个工具测试与主机和端口组合的 TCP 连接。 该工具名为 **tcpping**，语法为：

    tcpping.exe hostname [optional: port]

**tcpping** 实用程序会告知是否可访问特定主机和端口。 仅当有应用程序在主机和端口组合处侦听，并且应用具有从应用到指定主机和端口的网络访问权限时，它才能显示成功。

#### <a name="debug-access-to-virtual-network-hosted-resources"></a>调试对虚拟网络托管资源的访问
许多因素会阻止你的应用到达特定的主机和端口。 大多数时候，这是这些事情之一：

* **存在防火墙。** 如果路上有防火墙，则达到 TCP 超时。 本例中的 TCP 超时为 21 秒。 使用 **tcpping** 工具测试连接性。 TCP 超时可能由防火墙以外的许多因素引起，但从中开始。
* **DNS 不可访问。** DNS 超时为每个 DNS 服务器 3 秒。 如果具有 2 个 DNS 服务器，则超时为 6 秒。 使用 nameresolver 查看 DNS 是否正常工作。 不能使用 nslookup，因为不使用虚拟网络配置的 DNS。 如果无法访问，则表明可能有防火墙或 NSG 在阻止对 DNS 的访问，或者该 DNS 可能已停机。

如果这些方法未解决问题，请首先检查以下因素：

**区域 VNet 集成**
* 您的目的地是否为非 RFC1918 地址，您没有将WEBSITE_VNET_ROUTE_ALL设置为 1？
* 是否有 NSG 阻止来自集成子网的传出？
* 如果要通过 Azure ExpressRoute 或 VPN，是否配置本地网关将流量路由回 Azure？ 如果可以到达虚拟网络中的终结点，但不能到达本地，请检查路由。
* 您是否有足够的权限在集成子网上设置委派？ 在区域 VNet 集成配置期间，集成子网将委派给 Microsoft.Web。 VNet 集成 UI 自动将子网委托给 Microsoft.Web。 如果您的帐户没有足够的网络权限来设置委派，则需要可以在集成子网上设置属性的人员来委派子网。 要手动委派集成子网，请访问 Azure 虚拟网络子网 UI 并为 Microsoft.Web 设置委派。

**网关所需的 VNet 集成**
* RFC 1918 范围（10.0.0.0-10.255.255/ 172.16.0.0-172.31.255.255 / 192.168.0-192.168.255.255）中的点到站点地址范围？
* 网关是否显示在门户中？ 如果网关处于关闭状态，则将其重新启动。
* 证书是否显示为同步，或者您怀疑网络配置已更改？  如果您的证书不同步，或者您怀疑对未与 ASP 同步的虚拟网络配置进行了更改，请选择 **"同步网络**"。
* 如果要通过 VPN，是否配置本地网关将流量路由回 Azure？ 如果可以到达虚拟网络中的终结点，但不能到达本地，请检查路由。
* 您是否正在尝试使用同时支持指向站点和 ExpressRoute 的共存网关？ VNet 集成不支持共存网关。

调试网络问题是一个挑战，因为您看不到阻止访问特定主机：端口组合的内容。 一些原因包括：

* 主机上安装了防火墙，阻止从点对点 IP 范围访问应用程序端口。 跨越子网通常需要公共访问。
* 目标主机已关闭。
* 您的应用程序已关闭。
* 您拥有错误的 IP 或主机名。
* 您的应用程序侦听的端口与预期不同。 可以使用终结点主机上的“netstat -aon”匹配进程 ID 和侦听端口。
* 网络安全组的配置方式使它们无法从点对点 IP 范围访问应用程序主机和端口。

你不知道你的应用实际使用的地址。 它可能是集成子网中或点到站点地址范围内的任意地址，因此你需要允许从整个地址范围进行访问。

其他调试步骤包括：

* 连接到虚拟网络中的 VM，并尝试从那里访问资源主机：端口。 要测试 TCP 访问，请使用 PowerShell 命令**测试网连接**。 语法为：

      test-netconnection hostname [optional: -Port]

* 在 VM 上启动应用程序，并使用**tcpping**从应用从控制台测试对该主机和端口的访问。

#### <a name="on-premises-resources"></a>本地资源 ####

如果应用无法到达本地资源，请检查是否可以从虚拟网络到达资源。 请使用 test-netconnection PowerShell 命令来针对 TCP 访问权限进行测试****。 如果您的 VM 无法访问本地资源，则 VPN 或 ExpressRoute 连接可能未正确配置。

如果虚拟网络托管的 VM 可以到达本地系统，但应用无法到达，则原因可能是以下原因之一：

* 您的路由未配置与本地网关中的子网或点对点地址范围。
* 您的网络安全组正在阻止对点到站点 IP 范围的访问。
* 本地防火墙正在阻止来自点到站点 IP 范围的流量。
* 您正在尝试使用区域 VNet 集成功能访问非 RFC 1918 地址。