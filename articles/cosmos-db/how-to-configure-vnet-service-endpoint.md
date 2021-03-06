---
title: 为 Azure Cosmos 帐户配置基于虚拟网络的访问
description: 本文档介绍为 Azure Cosmos DB 设置虚拟网络服务终结点所要执行的步骤。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mjbrown
ms.openlocfilehash: 442623880c1b95f3d7e038ae44832b74853d2c4a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366237"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>配置从虚拟网络 (VNet) 访问

可将 Azure Cosmos DB 帐户配置为仅允许从 Azure 虚拟网络的特定子网进行访问。 限制使用虚拟网络中子网的连接对 Azure Cosmos DB 帐户的访问：

1. 让该子网将其标识和虚拟网络标识发送到 Azure Cosmos DB。 在特定的子网中为 Azure Cosmos DB 启用服务终结点可实现此目的。

1. 在 Azure Cosmos DB 帐户中添加一个规则，以便将此子网指定为可从中访问帐户的源。

> [!NOTE]
> 在子网中为 Azure Cosmos DB 帐户启用服务终结点之后，抵达 Azure Cosmos DB 的流量的源将从公共 IP 切换到虚拟网络和子网。 流量切换适用于从此子网访问的任何 Azure Cosmos DB 帐户。 如果 Azure Cosmos DB 帐户包含允许此子网的基于 IP 的防火墙，则已启用服务的子网发出的请求将不再与 IP 防火墙规则相匹配，因此会遭到拒绝。
>
> 有关详细信息，请参阅本文的[从 IP 防火墙规则迁移到虚拟网络访问控制列表](#migrate-from-firewall-to-vnet)部分所述的步骤。

以下部分介绍如何为 Azure Cosmos DB 帐户配置虚拟网络服务终结点。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>使用 Azure 门户配置服务终结点

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>为现有的 Azure 虚拟网络和子网配置服务终结点

1. 在“所有资源”边栏选项卡中，找到想要保护的 Azure Cosmos DB 帐户。****

1. 从设置菜单中选择“防火墙和虚拟网络”，然后选择允许从“选定的网络”进行访问。********

1. 若要授予对现有虚拟网络子网的访问权限，请在“虚拟网络”下面选择“添加现有的 Azure 虚拟网络”。********

1. 选择要从中添加 Azure 虚拟网络的**订阅**。 选择要向其提供 Azure Cosmos DB 帐户访问权限的 Azure **虚拟网络**和**子网**。 接下来选择“启用”，以便为“Microsoft.AzureCosmosDB”启用包含服务终结点的选定网络。**** 完成后，选择 **"添加**"。

   ![选择虚拟网络和子网](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)

1. 允许从虚拟网络访问 Azure Cosmos DB 帐户之后，只允许来自此所选子网的流量。 添加的虚拟网络和子网应会显示，如以下屏幕截图所示：

   ![已成功配置虚拟网络和子网](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> 若要启用虚拟网络服务终结点，需要以下订阅权限：
>   * 使用虚拟网络的订阅：网络参与者
>   * 使用 Azure Cosmos DB 帐户的订阅：DocumentDB 帐户参与者
>   * 如果虚拟网络与 Azure Cosmos DB 帐户位于不同的订阅中，请确保包含虚拟网络的订阅还注册了 `Microsoft.DocumentDB` 资源提供程序。 若要注册资源提供程序，请参阅 [Azure 资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md)一文。

以下是向资源提供程序注册订阅的说明。

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>为新的 Azure 虚拟网络和子网配置服务终结点

1. 在“所有资源”边栏选项卡中，找到想要保护的 Azure Cosmos DB 帐户。****  

1. 从设置菜单中选择“防火墙和 Azure 虚拟网络”，然后选择允许从“选定的网络”进行访问。********  

1. 若要授予对新 Azure 虚拟网络的访问权限，请在“虚拟网络”下面选择“添加新虚拟网络”。********  

1. 提供创建新虚拟网络所需的详细信息，然后选择“创建”。**** 随后将为启用的“Microsoft.AzureCosmosDB”创建包含服务终结点的子网。

   ![为新虚拟网络选择虚拟网络和子网](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

如果 Azure Cosmos DB 帐户由 Azure 认知搜索等其他 Azure 服务使用，或者从流分析或 Power BI 访问，则可以通过选择**从全局 Azure 数据中心中的"接受连接**"来允许访问。

为确保能够从门户访问 Azure Cosmos DB 指标，需要启用“允许从 Azure 门户访问”选项。**** 有关这些选项的详细信息，请参阅[配置 IP 防火墙](how-to-configure-firewall.md)一文。 启用访问后，选择“保存”以保存设置。****

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>删除虚拟网络或子网

1. 在“所有资源”边栏选项卡中，找到为其分配了服务终结点的 Azure Cosmos DB 帐户。****  

1. 从设置菜单中选择“防火墙和虚拟网络”****。  

1. 若要删除某个虚拟网络或子网规则，请选择虚拟网络或子网旁边的“...”，然后选择“删除”。********

   ![删除虚拟网络](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

1. 单击“保存”应用所做的更改。****

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>使用 Azure PowerShell 配置服务终结点

> [!NOTE]
> 使用 PowerShell 或 Azure CLI 时，请务必在参数中指定 IP 筛选器和虚拟网络 ACL 的完整列表，而不仅仅是需要添加的项。

在 Azure PowerShell 中使用以下步骤配置 Azure Cosmos DB 帐户的服务终结点：  

1. 安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) 并[登录](https://docs.microsoft.com/powershell/azure/authenticate-azureps)。  

1. 为虚拟网络的现有子网启用服务终结点。  

   ```powershell
   $resourceGroupName = "<Resource group name>"
   $vnetName = "<Virtual network name>"
   $subnetName = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"
   $serviceEndpoint = "Microsoft.AzureCosmosDB"

   Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName | Set-AzVirtualNetworkSubnetConfig `
      -Name $subnetName `
      -AddressPrefix $subnetPrefix `
      -ServiceEndpoint $serviceEndpoint | Set-AzVirtualNetwork
   ```

1. 获取虚拟网络信息。

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. 准备宇宙数据库虚拟网络规则

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. 使用新的虚拟网络终结点配置更新 Azure Cosmos DB 帐户属性： 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. 运行以下命令，验证是否已使用上一步骤中配置的虚拟网络服务终结点更新 Azure Cosmos DB 帐户：

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a>使用 Azure CLI 配置服务终结点

Azure Cosmos 帐户可以在以后创建或更新服务终结点时针对服务终结点进行配置（如果已经为其配置了子网）。 还可以在尚未为其配置子网的 Cosmos 帐户上启用服务终结点，然后这些服务终结点在以后配置子网时开始工作。 这种灵活性允许无法同时访问 Cosmos 帐户和虚拟网络资源的管理员将其配置设为彼此独立。

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>创建新的 Cosmos 帐户并将其连接到新虚拟网络的后端子网

在此示例中，创建虚拟网络和子网时会同时为它们启用服务终结点。

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet with service endpoints enabled for Cosmos DB
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values and service endpoints
az cosmosdb create \
   -n $accountName \
   -g $resourceGroupName \
   --enable-virtual-network true \
   --virtual-network-rules $svcEndpoint
```

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>将 Cosmos 帐户独立连接并配置到后端子网

此示例旨在说明如何将 Azure Cosmos 帐户连接到尚未为服务终结点配置子网的现有新虚拟网络。 这是通过使用 `--ignore-missing-vnet-service-endpoint` 参数完成的。 这样就可以在完成对虚拟网络的子网的配置之前，完成 Cosmos 帐户的配置而不会出现错误。 子网配置完成后，便可通过配置的子网访问 Cosmos 帐户。

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet
# that is not yet enabled for service endpoints.

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet but without configuring service endpoints (--service-endpoints Microsoft.AzureCosmosDB)
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values
az cosmosdb create -n $accountName -g $resourceGroupName

# Add the virtual network rule but ignore the missing service endpoint on the subnet
az cosmosdb network-rule add \
   -n $accountName \
   -g $resourceGroupName \
   --virtual-network $vnetName \
   --subnet svcEndpoint \
   --ignore-missing-vnet-service-endpoint true

read -p'Press any key to now configure the subnet for service endpoints'

az network vnet subnet update \
   -n $backEnd \
   -g $resourceGroupName \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB
```

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>从 IP 防火墙规则迁移到虚拟网络 ACL

要将 Azure Cosmos DB 帐户从使用 IP 防火墙规则迁移到使用虚拟网络服务终结点，请使用以下步骤。

为子网的服务终结点配置 Azure Cosmos DB 帐户后，来自该子网的请求将发送到 Azure Cosmos DB，其中包含虚拟网络和子网源信息，而不是源公共 IP 地址。 这些请求将不再与 Azure Cosmos DB 帐户上配置的 IP 筛选器匹配，因此需要以下步骤来避免停机。

在继续操作之前，请使用上面显示的步骤在虚拟网络和子网上启用 Azure Cosmos DB 服务终结点，这些步骤"为虚拟网络的现有子网启用服务终结点"。

1. 获取虚拟网络和子网信息：

   ```powershell
   $resourceGroupName = "myResourceGroup"
   $accountName = "mycosmosaccount"
   $vnetName = "myVnet"
   $subnetName = "mySubnet"

   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. 为 Azure Cosmos DB 帐户准备新的虚拟网络规则对象：

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. 更新 Azure Cosmos DB 帐户以启用从子网访问服务终结点：

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. 对从子网访问的所有 Azure Cosmos DB 帐户重复上述步骤。

1. 从 Azure Cosmos DB 帐户的防火墙规则中删除子网的 IP 防火墙规则。

## <a name="next-steps"></a>后续步骤

* 若要为 Azure Cosmos DB 配置防火墙，请参阅[防火墙支持](firewall-support.md)一文。
