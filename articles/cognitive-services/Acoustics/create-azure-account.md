---
title: 项目声学 Azure 批处理帐户设置
titlesuffix: Azure Cognitive Services
description: 此方法介绍设置 Azure 批处理帐户以用于项目声学统一和虚幻引擎集成。
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 8f0f726d9d23f20698d3510ad674331ad74fb703
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "68855079"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>项目声学 Azure 批处理帐户设置
此方法介绍设置 Azure 批处理帐户以用于项目声学统一和虚幻引擎集成。

## <a name="get-an-azure-subscription"></a>获取 Azure 订阅
设置 Batch 和存储帐户前需要 [Azure 订阅](https://azure.microsoft.com/free/)。 如果是首次注册，Azure 将提供部分限时可用的资源和 $200 信用额度。

## <a name="create-azure-batch-and-storage-accounts"></a>创建 Azure Batch 和存储帐户
接下来，请按照[以下说明](https://docs.microsoft.com/azure/batch/batch-account-create-portal)设置 Azure Batch 和关联的 Azure 存储帐户。

为 Batch 和存储帐户选择默认选项：
  
  ![显示默认设置的 Azure 批处理新帐户选项的屏幕截图](media/new-batch-account-create.png)

  ![显示默认设置的 Azure 存储新帐户选项的屏幕截图](media/batch-storage-account-create.png)

Azure 需要花费几分钟时间来部署帐户。 在门户右上角查看完成通知。
  
  ![Azure 帐户已部署通知的屏幕截图](media/batch-accounts-deploy-notification.png)

帐户现在应显示在仪表板上。
  
  ![显示批处理和存储帐户的 Azure 门户仪表板的屏幕截图](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>使用 Azure 凭据设置音响效果制作 UI
单击仪表板上的 Batch 帐户链接，然后单击 Batch 帐户页上的“密钥”链接以访问凭据****。
  
  ![突出显示链接到"密钥"页的 Azure 批处理帐户的屏幕截图](media/batch-access-keys.png)

  ![具有访问密钥的 Azure 批处理帐户密钥页的屏幕截图](media/batch-keys-info.png)

单击页面上的“存储帐户”链接，以访问 Azure 存储帐户凭据****。
  
  ![包含访问密钥的 Azure 存储帐户密钥页面的屏幕截图](media/storage-keys-info.png)

在[Unity 烘焙插件](unity-baking.md)或[虚幻烘焙插件](unreal-baking.md)中输入这些凭据。

## <a name="node-types-and-region-support"></a>节点类型和区域支持
项目声学需要 Fsv2 和 H 系列计算优化的 Azure VM 节点，这些节点可能并非在所有 Azure 区域中都受支持。 请检查[此表](https://azure.microsoft.com/global-infrastructure/services)，确保为 Batch 帐户选择正确的位置。
![按区域显示 Azure 虚拟机的屏幕截图](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>升级配额
创建帐户时，将预配 Azure Batch 帐户，并且其计算核心限制为 20 个。 我们可能希望提高此限制以加快烘焙时间，因为您可以在多个节点上并行化声学工作负载，最多到场景中的探测点数。 可以通过单击 Azure Batch 门户页上的“配额”链接并单击“请求增加配额”******** 来请求增加配额：

![Azure 配额页面的屏幕截图](media/azure-quotas.png)

## <a name="next-steps"></a>后续步骤
* 将项目声学插件集成到[您的 Unity](unity-integration.md)或[虚幻](unreal-integration.md)项目中

