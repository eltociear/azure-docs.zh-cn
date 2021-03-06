---
title: 使用门户创建接近放置组
description: 了解如何使用 Azure 门户创建邻近放置组。
services: virtual-machines
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 8512d9b701242dc686d49bbe56e8a2059f14ee3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73180246"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>使用门户创建接近放置组

要尽可能接近 VM，实现尽可能低的延迟，应将它们部署在[接近放置组中](co-location.md#proximity-placement-groups)。

邻近放置组是一种逻辑分组，用于确保 Azure 计算资源在物理上彼此靠近。 邻近放置组用于要求低延迟的工作负荷。


## <a name="create-the-proximity-placement-group"></a>创建邻近放置组

1. 在搜索中键入**接近放置组**。
1. 在搜索结果中的 **"服务**"下，选择 **"邻近"放置组**。
1. 在 **"邻近放置组"** 页中，选择 **"添加**"。
1. 在“基本信息”**** 选项卡中的“项目详细信息”**** 下，确保选择了正确的订阅。
1. 在 **"资源"组中**，选择 **"新建**"以创建新组，或从下拉列表中选择现有资源组。
1. 在 **"区域"** 中选择要创建邻近放置组的位置。
1. 在 **"邻近放置"组名称**中键入名称，然后选择 **"审阅 + 创建**"。
1. 验证通过后，选择 **"创建**"以创建邻近放置组。

    ![创建邻近放置组的屏幕截图](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>创建 VM

1. 在门户中创建 VM 时，转到 **"高级"** 选项卡。 
1. 在 **"邻近放置组**"选择中，选择正确的放置组。 

    ![在门户中创建新 VM 时的临近放置组部分的屏幕截图](./media/ppg/vm-ppg.png)

1. 完成所有其他必需选择后，选择 **"审阅 + 创建**"。
1. 通过验证后，选择 **"创建"** 以在放置组中部署 VM。




## <a name="next-steps"></a>后续步骤

您还可以使用[Azure PowerShell](proximity-placement-groups.md)创建邻近放置组。

