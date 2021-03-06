---
title: 使用 CLI 将 Linux VM 部署到专用主机
description: 使用 Azure CLI 将 VM 部署到专用主机。
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: ba40e610e31a1215ac90baf63a04b435b636d68a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127692"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>使用 Azure CLI 将 VM 部署到专用主机
 

本文将指导您如何创建 Azure[专用主机](dedicated-hosts.md)来托管虚拟机 （VM）。 

请确保已安装 Azure CLI 版本 2.0.70 或更高版本，并使用 登录到 Azure`az login`帐户。 


## <a name="limitations"></a>限制

- 专用主机当前不支持虚拟机缩放集。
- 专用主机可用的大小和硬件类型因地区而异。 请参阅主机[定价页面](https://aka.ms/ADHPricing)以了解更多信息。
 

## <a name="create-resource-group"></a>创建资源组 
Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 使用 az group create 创建资源组。 下面的示例*在美国东部*位置创建名为*myDHResourceGroup*的资源组。

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="create-a-host-group"></a>创建主机组 

**主机组**是表示专用主机集合的资源。 在区域和可用性区域中创建主机组，并将主机添加到其中。 在规划高可用性时，还有其他选项。 您可以将以下一个或两个选项用于专用主机： 
- 跨多个可用性区域的跨范围。 在这种情况下，您需要在每个要使用的区域中具有一个主机组。
- 跨映射到物理机架的多个容错域。 
 
在这两种情况下，都需要为主机组提供容错域计数。 如果不想跨越组中的容错域，请使用容错域计数 1。 

您还可以决定同时使用可用性区域和容错域。 

在此示例中，我们将使用 az [vm 主机组创建](/cli/azure/vm/host/group#az-vm-host-group-create)，使用可用性区域和容错域创建主机组。 

```bash
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

### <a name="other-examples"></a>其他示例

您还可以使用 az [vm 主机组创建](/cli/azure/vm/host/group#az-vm-host-group-create)在可用性区域 1（无容错域）中创建主机组。

```bash
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
以下使用[az vm 主机组创建](/cli/azure/vm/host/group#az-vm-host-group-create)，仅使用容错域创建主机组（用于不支持可用性区域的区域）。 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>创建主机 

现在，让我们在主机组中创建一个专用主机。 除了主机的名称外，还需要为主机提供 SKU。 主机 SKU 捕获支持的 VM 系列以及专用主机的硬件生成。  

有关主机 SKU 和定价的详细信息，请参阅[Azure 专用主机定价](https://aka.ms/ADHPricing)。

使用[az vm 主机创建](/cli/azure/vm/host#az-vm-host-create)创建主机。 如果为主机组设置容错域计数，系统将要求您为主机指定容错域。  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>创建虚拟机 
使用[az vm 创建](/cli/azure/vm#az-vm-create)在专用主机中创建虚拟机。 如果在创建主机组时指定了可用性区域，则创建虚拟机时需要使用相同的区域。

```bash
az vm create \
   -n myVM \
   --image debian \
   --generate-ssh-keys \
   --host-group myHostGroup \
   --host myHost \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```
 
> [!WARNING]
> 如果在没有足够的资源的主机上创建虚拟机，则虚拟机将以 FAILED 状态创建。 


## <a name="check-the-status-of-the-host"></a>检查主机的状态

您可以使用[az vm 主机获取实例视图](/cli/azure/vm/host#az-vm-host-get-instance-view)检查主机运行状况状态以及仍可部署到主机的虚拟机数。

```bash
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 输出与此类似：
 
```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```
 
## <a name="export-as-a-template"></a>作为模板导出 
如果要创建具有相同参数的其他开发环境或与其匹配的生产环境，则可以导出模板。 Resource Manager 使用定义了所有环境参数的 JSON 模板。 通过引用此 JSON 模板构建出整个环境。 可以手动构建 JSON 模板，也可以通过导出现有环境来替你创建 JSON 模板。 使用[az 组导出](/cli/azure/group#az-group-export)导出资源组。

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

此命令在当前工作目录中创建 `myDHResourceGroup.json` 文件。 从此模板创建环境时，系统会提示输入所有资源名称。 可以通过将 `--include-parameter-default-value` 参数添加到 `az group export` 命令在模板文件中填充这些名称。 请编辑 JSON 模板以指定资源名称，或创建 parameters.json 文件来指定资源名称。
 
要从模板创建环境，请使用[az 组部署创建](/cli/azure/group/deployment#az-group-deployment-create)。

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>清除 

即使未部署虚拟机，也会为专用主机向您收费。 您应该删除当前不使用的任何主机以节省成本。  

仅当不再有虚拟机使用主机时，才能删除主机。 使用[az vm 删除](/cli/azure/vm#az-vm-delete)VM 删除 VM。

```bash
az vm delete -n myVM -g myDHResourceGroup
```

删除 VM 后，可以使用[az vm 主机删除](/cli/azure/vm/host#az-vm-host-delete)主机。

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
删除所有主机后，可以使用[az vm 主机组删除主机组](/cli/azure/vm/host/group#az-vm-host-group-delete)。  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
您还可以在单个命令中删除整个资源组。 这将删除组中创建的所有资源，包括所有 VM、主机和主机组。
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>后续步骤

- 有关详细信息，请参阅[专用主机](dedicated-hosts.md)概述。

- 您还可以使用[Azure 门户](dedicated-hosts-portal.md)创建专用主机。

- [此处](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)找到的示例模板，它同时使用区域和容错域，以实现区域中的最大恢复能力。