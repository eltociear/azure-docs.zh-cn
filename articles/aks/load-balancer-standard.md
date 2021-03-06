---
title: 在 Azure Kubernetes 服务 (AKS) 中使用标准 SKU 负载均衡器
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中使用标准 SKU 负载均衡器来公开服务。
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: 9c414572e1c3b2f046ae9a14139885e9927ab3bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252904"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中使用标准 SKU 负载均衡器

若要通过 Azure Kubernetes 服务 (AKS) 中 `LoadBalancer` 类型的 Kubernetes 服务提供对应用程序的访问权限，可以使用 Azure 负载均衡器。 在 AKS 中运行的负载均衡器可用作内部或外部负载均衡器。 内部负载均衡器使得仅 AKS 群集所在的同一虚拟网络中运行的应用程序能够访问 Kubernetes 服务。 外部负载均衡器接收入口的一个或多个公共 IP，并使得 Kubernetes 服务可以通过公共 IP 在外部进行访问。

Azure 负载均衡器以两种 SKU 提供：“基本”和“标准”****。 默认情况下，在创建 AKS 群集时使用*标准*SKU。 使用标准 SKU 负载均衡器可提供其他特性和功能，例如更大的后端池和可用性区域。** 在选择使用标准或基本负载均衡器之前，必须了解两者之间的差异。**** 创建 AKS 群集后，无法更改该群集的负载均衡器 SKU。 有关基本和标准 SKU 的详细信息，请参阅 [Azure 负载均衡器 SKU 的比较][azure-lb-comparison]。****

本文假设读者基本了解 Kubernetes 和 Azure 负载均衡器的概念。 有关详细信息，请参阅 [Azure Kubernetes 服务 (AKS) 的 Kubernetes 核心概念][kubernetes-concepts]和[什么是 Azure 负载均衡器？][azure-lb]。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装和使用 CLI，则本文要求您运行 Azure CLI 版本 2.0.81 或更高版本。 运行 `az --version` 即可查找版本。 如果需要安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="before-you-begin"></a>开始之前

本文假定您具有具有*标准*SKU Azure 负载均衡器的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

如果使用现有子网或资源组，则 AKS 群集服务主体还需要有权管理网络资源。 通常，将“网络参与者”** 角色分配给委派资源上的服务主体。 有关权限的详细信息，请参阅[委派 AKS 访问其他 Azure 资源][aks-sp]。

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>从基本 SKU 负载均衡器转移到标准 SKU

如果现有的群集包含基本 SKU 负载均衡器，则在进行迁移以使用包含标准 SKU 负载均衡器的群集时，需要注意一些重要的行为差异。

例如，假设只能在创建群集时定义群集的 `load-balancer-sku` 类型，则通过蓝/绿部署迁移群集是常用的做法。 但是，基本 SKU 负载均衡器使用基本 SKU IP 地址，而这些地址与标准 SKU 负载均衡器不兼容，因为这些负载均衡器需要标准 SKU IP 地址。******** 在迁移群集以升级负载均衡器 SKU 时，需要提供一个具有兼容 IP 地址 SKU 的新 IP 地址。

有关如何迁移群集的更多注意事项，请访问[有关迁移注意事项的文档](aks-migration.md)，以查看迁移时要考虑的重要主题列表。 以下限制也是在 AKS 中使用标准 SKU 负载均衡器时要注意的重要行为差异。

### <a name="limitations"></a>限制

创建和管理支持标准 SKU 负载均衡器的 AKS 群集时存在以下限制：**

* 至少需要指定一个公共 IP 或 IP 前缀来允许 AKS 群集的出口流量。 此外，需要使用公共 IP 或 IP 前缀来保持控制平面与代理节点之间的连接，以及保持与旧版 AKS 的兼容性。 可以使用以下选项指定标准 SKU 负载均衡器的公共 IP 或 IP 前缀：**
    * 提供自己的公共 IP。
    * 提供自己的公共 IP 前缀。
    * 指定最大为 100 的数字，以允许 AKS 群集在其所在的同一个资源组（名称通常以 *MC_* 开头）中创建多个标准 SKU 公共 IP。** AKS 会将公共 IP 分配到标准 SKU 负载均衡器。** 默认情况下，如果未指定公共 IP、公共 IP 前缀或 IP 数目，系统会在 AKS 群集所在的同一个资源组中自动创建一个公共 IP。 此外，必须允许公共地址，并避免创建任何会阻止创建 IP 的 Azure Policy。
* 对负载均衡器使用标准 SKU 时，必须使用 Kubernetes 1.13 或更高版本。****
* 只能在创建 AKS 群集时定义负载均衡器 SKU。 创建 AKS 群集后，无法更改负载均衡器 SKU。
* 在一个群集中只能使用一种类型的负载均衡器 SKU（基本或标准）。
* 标准 SKU 负载均衡器仅支持标准 SKU IP 地址。****

## <a name="use-the-standard-sku-load-balancer"></a>使用标准 SKU 负载均衡器**

如果创建 AKS 群集，默认情况下，在该群集中运行服务时将使用标准 SKU 负载均衡器。** 例如，[使用 Azure CLI 的快速入门][aks-quickstart-cli]部署的是使用标准 SKU 负载均衡器的示例应用程序。**

> [!IMPORTANT]
> 通过自定义用户定义的路由 （UDR）， 可以避免公共 IP 地址。 将 AKS 群集的出站类型指定为 UDR 可以跳过 AKS 创建的 Azure 负载均衡器的 IP 预配和后端池设置。 请参阅[将群集`outboundType`设置为"用户定义路由"。](egress-outboundtype.md)

## <a name="configure-the-load-balancer-to-be-internal"></a>将负载均衡器配置为内部负载均衡器

还可将负载均衡器配置为内部负载均衡器且不公开公共 IP。 若要将负载均衡器配置为内部负载均衡器，请添加 `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` 作为 *LoadBalancer* 服务的注释。 可在[此处][internal-lb-yaml]查看示例 YAML 清单，以及有关内部负载均衡器的更多详细信息。

## <a name="scale-the-number-of-managed-public-ips"></a>调整托管公共 IP 的数量

结合默认创建的托管出站公共 IP 使用标准 SKU 负载均衡器时，可以使用 *load-balancer-managed-ip-count* 参数来调整托管出站公共 IP 的数量。**

要更新现有群集，请运行以下命令。 还可以在创建群集时设置此参数，以指定多个托管出站公共 IP。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

以上示例将 *myResourceGroup* 中 *myAKSCluster* 群集的托管出站公共 IP 数量设置为 *2*。 

还可以在创建群集时，通过追加 `--load-balancer-managed-outbound-ip-count` 参数并将其设置为所需的值，使用 *load-balancer-managed-ip-count* 参数来设置托管出站公共 IP 的初始数量。 托管出站公共 IP 的默认数量为 1。

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>提供自己的出口公共 IP 或前缀

使用标准 SKU 负载均衡器时，AKS 群集将自动在为它创建的同一个资源组中创建公共 IP，并将该公共 IP 分配给标准 SKU 负载均衡器。**** 或者，可以在创建群集时分配自己的公共 IP，或更新现有群集的负载均衡器属性。

通过引入多个 IP 地址或前缀，可以在单个负载均衡器对象后面定义 IP 地址时定义多个后备服务。 特定节点的出口终结点将依赖于与这些节点关联的服务。

> [!IMPORTANT]
> 必须将出口的标准 SKU 公共 IP 与负载均衡器的标准 SKU 配合使用。**** 可以使用 [az network public-ip show][az-network-public-ip-show] 命令验证公共 IP 的 SKU：
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

使用 [az network public-ip show][az-network-public-ip-show] 命令列出公共 IP 的 ID。

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

以上命令显示 *myResourceGroup* 资源组中 *myPublicIP* 公共 IP 的 ID。

结合 *load-balancer-outbound-ips* 参数使用 *az aks update* 命令，以更新群集中的公共 IP。

以下示例结合前一命令返回的 ID 使用 *load-balancer-outbound-ips* 参数。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

你也可以将出口的公共 IP 前缀与标准 SKU 负载均衡器配合使用。** 以下示例使用 [az network public-ip prefix show][az-network-public-ip-prefix-show] 命令列出公共 IP 前缀的 ID：

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

以上命令显示 *myResourceGroup* 资源组中 *myPublicIPPrefix* 公共 IP 前缀的 ID。

以下示例将 *load-balancer-outbound-ip-prefixes* 参数与前一命令返回的 ID 配合使用。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> 公共 IP 和 IP 前缀必须位于同一区域，并且与 AKS 群集位于同一订阅中。 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>在创建群集时定义自己的公共 IP 或前缀

在创建群集时，你可能想要使用自己的出口 IP 地址或 IP 前缀，以支持出口终结点允许列表等方案。 将上面所示的相同参数追加到群集创建步骤，可在群集生命周期的起始部分定义自己的公共 IP 和 IP 前缀。

结合 *load-balancer-outbound-ips* 参数使用 *az aks create* 命令可在启动时使用你的公共 IP 创建新的群集。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

结合 *load-balancer-outbound-ip-prefixes* 参数使用 *az aks create* 命令可在启动时使用你的公共 IP 前缀创建新的群集。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="configure-outbound-ports-and-idle-timeout"></a>配置出站端口和空闲超时

> [!WARNING]
> 以下部分适用于大规模网络的高级方案或用于解决默认配置的 SNAT 耗尽问题。 在将*分配的出站端口*或*IdleTimeoutInM*从默认值更改之前，您必须准确列出 VM 和 IP 地址的可用配额，以便维护健康的群集。
> 
> 更改*分配的出站端口*和*IdleTimeoutInM 值*可能会显著更改负载均衡器的出站规则的行为。 在更新这些值之前，请查看 Azure 中的[负载均衡器出站规则][azure-lb-outbound-rules-overview]、[负载均衡器出站规则][azure-lb-outbound-rules]和[出站连接][azure-lb-outbound-connections]，以充分了解更改的影响。

出站分配的端口及其空闲超时用于[SNAT][azure-lb-outbound-connections]。 默认情况下，*标准*SKU 负载均衡器[根据后端池大小对出站端口数使用自动分配][azure-lb-outbound-preallocatedports]，每个端口的空闲超时为 30 分钟。 要查看这些值，请使用[az 网络 lb 出站规则列表][az-network-lb-outbound-rule-list]来显示负载均衡器的出站规则：

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

上述命令将列出负载均衡器的出站规则，例如：

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

示例输出显示*分配的出站端口*和*空闲时间分钟中的*默认值。 *"已分配出站端口*"的值为 0，使用基于后端池大小自动分配出站端口数来设置出站端口数。 例如，如果群集具有 50 个或更少的节点，则为每个节点分配 1024 个端口。

如果您希望基于上述默认配置面临 SNAT 耗尽，请考虑更改*已分配的出站端口*或*空闲时间分钟设置*。 每个附加的 IP 地址启用 64，000 个附加端口进行分配，但是 Azure 标准负载均衡器不会在添加更多 IP 地址时自动增加每个节点的端口。 您可以通过设置*负载平衡器出站端口*和*负载平衡器空闲超时*参数来更改这些值。 例如：

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> 在自定义*分配的出站端口*之前，必须[计算所需的配额][calculate-required-quota]，以避免连接或缩放问题。 为*分配的出站端口*指定的值也必须为 8 的倍数。

创建群集时，还可以使用*负载均衡器-出站端口*和*负载均衡器空闲超时*参数，但还必须指定*负载均衡器管理-出站 ip 计数*、*负载均衡器-出站-ips*或*负载均衡器-出站-ip 前缀*。  例如：

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

当从默认值更改*负载平衡器出站端口*和*负载均衡器空闲超时*参数时，它会影响负载均衡器配置文件的行为，该配置文件会影响整个群集。

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>自定义分配的出站端口所需的配额
您必须有足够的出站 IP 容量，具体取决于节点 VM 的数量和所需的分配的出站端口数。 要验证您有足够的出站 IP 容量，请使用以下公式： 
 
*出站 IP* \* \> 64，000*节点VM* \* *需要分配的出站端口*。
 
例如，如果您有 3*个节点VM*和 50，000*个所需的分配出站端口*，则需要至少 3 个*出站 IP*。 建议您合并超出所需范围的其他出站 IP 容量。 此外，在计算出站 IP 容量时，必须考虑群集自动缩放器以及节点池升级的可能性。 对于群集自动缩放器，请查看当前节点计数和最大节点计数，并使用较高的值。 要进行升级，可以为每个允许升级的节点池考虑一个额外的节点 VM。
 
将*IdleTimeoutIn分钟*设置为与默认值 30 分钟不同的值时，请考虑工作负荷需要出站连接多长时间。 还要考虑 AKS 外部使用*的标准*SKU 负载均衡器的默认超时值为 4 分钟。 能够更准确地反映特定 AKS 工作负载*的 IdleTimeoutIn分钟*值可帮助减少因不再使用连接而导致的 SNAT 耗尽。

## <a name="restrict-access-to-specific-ip-ranges"></a>仅限特定的 IP 范围进行访问

默认情况下，与负载均衡器的虚拟网络关联的网络安全组 (NSG) 包含一个允许所有入站外部流量的规则。 可以更新此规则，以便仅允许来自特定 IP 范围的入站流量。 以下清单使用 *loadBalancerSourceRanges* 来指定允许其发送入站外部流量的新 IP 范围：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

以上示例将更新规则，以便仅允许来自 *MY_EXTERNAL_IP_RANGE* 范围的入站外部流量。 [Kubernetes 文档][kubernetes-cloud-provider-firewall]中提供了有关使用此方法限制负载均衡器服务访问权限的详细信息。

## <a name="next-steps"></a>后续步骤

在 [Kubernetes 服务文档][kubernetes-services]中详细了解 Kubernetes 服务。

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-cloud-provider-firewall]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-cloud-provider-firewall/#restrict-access-for-loadbalancer-service
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/concepts-limitations.md#skus
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-rules-overview.md#snatports
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#snat
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-rules-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[calculate-required-quota]: #required-quota-for-customizing-allocatedoutboundports
