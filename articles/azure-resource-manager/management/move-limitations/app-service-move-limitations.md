---
title: 移动 Azure 应用服务资源
description: 使用 Azure 资源管理器将应用服务资源移到新的资源组或订阅。
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: 338b0559a5de9468ff60024b88d0f676a9fc3e8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75479795"
---
# <a name="move-guidance-for-app-service-resources"></a>针对应用服务资源的移动指南

本文介绍移动应用服务资源的步骤。 将应用服务资源移到新订阅有特定要求。

## <a name="move-across-subscriptions"></a>跨订阅移动

在订阅之间移动 Web 应用时，应遵循以下指导：

- 目标资源组中不能有任何现有的应用服务资源。 应用服务资源包括：
    - Web 应用
    - 应用服务计划
    - 上传或导入的 SSL 证书
    - 应用服务环境
- 资源组中的所有应用服务资源必须一起移动。 请注意，应用服务环境不能移到新资源组，也不能移到新订阅。
- 只要将证书与资源组中的所有其他资源一起移动，就可以将绑定的证书移到 Web，而无需删除 SSL 绑定。
- 只能从最初创建应用服务资源的资源组中移动它们。 如果应用服务资源不再位于其原始资源组中，请将其移回其原始资源组。 然后，在订阅之间移动资源。

如果忘记了原始资源组，可以通过诊断来查找。 对于 Web 应用，请选择“诊断和解决问题”****。 然后，选择“配置和管理”。****

![选择诊断](./media/app-service-move-limitations/select-diagnostics.png)

选择“迁移选项”****。

![选择迁移选项](./media/app-service-move-limitations/select-migration.png)

选择通过建议的步骤来移动 Web 应用的选项。

![选择建议的步骤](./media/app-service-move-limitations/recommended-steps.png)

可以看到在移动资源之前需采取的建议操作。 该信息包含 Web 应用的原始资源组。

![建议](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>移动支持

若要确定可以移动哪些应用服务资源，请查看以下项的移动支持状态：

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [微软.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>后续步骤

有关移动资源的命令，请参阅[将资源移动至新资源组或订阅](../move-resource-group-and-subscription.md)。
