---
title: Team Data Science Process 生命周期的客户验收阶段
description: 你的数据科学项目客户验收阶段的目标、任务和可交付结果
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7224a7bb26ef491915df9fcb9b6b84ff171a9fc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720514"
---
# <a name="customer-acceptance-stage-of-the-team-data-science-process-lifecycle"></a>Team Data Science Process 生命周期的客户验收阶段

本文概述与 Team Data Science Process (TDSP) 的客户验收阶段相关的目标、任务和可交付结果。 此过程提供可用于构建数据科学项目的建议生命周期。 该生命周期概述了项目通常执行的主要阶段（通常以迭代方式进行）：

   1. **业务理解**
   2. **数据采集和理解**
   3. **建模**
   4. **部署**
   5. **客户验收**

此处直观地展示了 TDSP 生命周期： 

![TDSP 生命周期](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>目标
**完成项目交付结果**：确认管道、模型及其在生产环境中的部署满足客户的目标。

## <a name="how-to-do-it"></a>如何执行
在此阶段中解决了两个主要任务：

   * **系统验证**：确认部署的模型和管道满足客户的需求。
   * **项目提交**：将项目提交到要在生产中运行系统的实体。

客户应验证系统是否满足自己的业务需求，且能否准确回答问题，以便将系统部署到生产环境中，供客户端应用程序使用。 最终确定所有文档，并进行评审。 已将项目提交到负责操作的实体。 例如，此实体可能是 IT、客户数据科学团队或负责在生产中运行系统的客户代理。 

## <a name="artifacts"></a>项目
在此最后阶段中生成的主要项目是“客户项目退出报表”****。 此技术报表包含对于了解如何操作系统有帮助的所有项目细节。 TDSP 提供[退出报表](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md)模板。 可按原样使用该模板，也可以根据特定客户需求对其进行自定义。 


## <a name="next-steps"></a>后续步骤

以下是 TDSP 生命周期中每个步骤的链接：

   1. [业务理解](lifecycle-business-understanding.md)
   2. [数据采集和理解](lifecycle-data.md)
   3. [建模](lifecycle-modeling.md)
   4. [部署](lifecycle-deployment.md)
   5. [客户验收](lifecycle-acceptance.md)

我们提供完整的演练，演示特定方案过程中的所有步骤。 [示例演练](walkthroughs.md)一文提供了包含链接和缩略图描述的方案列表。 该演练演示如何将云、本地工具以及服务结合到一个工作流或管道中，以创建智能应用程序。 

有关如何在使用 Azure 机器学习工作室的 TDSP 中执行步骤的示例，请参阅[通过 Azure 机器学习使用 TDSP](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)。
