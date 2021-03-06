---
title: 云解决方案提供商计划注意事项
description: 对于 CSP 合作伙伴，Azure 委派资源管理通过启用细化权限来帮助提高安全性和控制力。
ms.date: 12/18/2019
ms.topic: conceptual
ms.openlocfilehash: 66ea74751f12a499a1e2d9e083497da31746e3c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456917"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure Lighthouse 和云解决方案提供商计划

如果你是 [CSP（云解决方案提供商）](https://docs.microsoft.com/partner-center/csp-overview)合作伙伴，则已经可以使用[代表客户管理 (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) 功能，通过 CSP 计划访问为客户创建的 Azure 订阅。 通过这一访问权限，可直接支持、配置和管理客户的订阅。

使用[Azure 灯塔](../overview.md)，可以使用 Azure 委派的资源管理以及 AOBO。 这通过为用户启用更细粒度的权限，有助于提高安全性并减少不必要的访问。 它还可以提高效率和可伸缩性，因为你的用户可以使用租户中的单个登录名来处理多个客户订阅。

> [!TIP]
> 为了帮助保护客户资源，请务必查看并遵循我们的[建议安全做法](recommended-security-practices.md)以及[合作伙伴安全要求](https://docs.microsoft.com/partner-center/partner-security-requirements)。

## <a name="administer-on-behalf-of-aobo"></a>代表客户管理 (AOBO)

使用 AOBO，租户中具有[管理员代理](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles)角色的任何用户都可对通过 CSP 计划创建的 Azure 订阅进行 AOBO 访问。 需要访问任意客户订阅的任何用户都必须是此组的成员。 AOBO 不允许灵活创建对不同客户有效的不同组，也不允许为组或用户启用不同角色。

![使用 AOBO 进行租户管理](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Azure 委派资源管理

使用 Azure 委派资源管理，可以将不同的组分配给不同的客户或角色，如下图所示。 通过 Azure 委派资源管理，用户将具有适当级别的访问权限，所以可以减少具有管理员代理角色（并因此具有完整的 AOBO 访问权限）的用户的数量。 这有助于通过限制对客户资源的不必要访问来提高安全性。 还可以更灵活地大规模管理多个客户。

按照[将订阅加入到 Azure 委派资源管理](../how-to/onboard-customer.md)中所述的步骤，加入通过 CSP 计划创建的订阅。 租户中具有管理员代理角色的任何用户都可以执行此加入过程。

![使用 AOBO 和 Azure 委派资源管理进行租户管理](../media/csp-2.jpg)

> [!NOTE]
> [Azure 门户中的 **"我的客户**"页面](../how-to/view-manage-customers.md)现在包括**一个云解决方案提供程序（预览）** 部分，该部分显示[已签署 Microsoft 客户协议 （MCA）](https://docs.microsoft.com/partner-center/confirm-customer-agreement)且在 Azure[计划下](https://docs.microsoft.com/partner-center/azure-plan-get-started)的 CSP 客户的计费信息和资源。 有关详细信息，请参阅 [Microsoft 合作伙伴协议计费帐户入门](../../billing/mpa-overview.md)。
>
> CSP 客户可能会出现在本节中，无论他们是否也已加入 Azure 委派的资源管理。 如果有，它们也会显示在 **"客户"** 部分中，如["查看"中所述，并管理客户和委派的资源](../how-to/view-manage-customers.md)。 同样，CSP 客户不必出现在 **"我的客户"** 的**云解决方案提供商（预览）** 部分中，以便将其注册用于 Azure 委派的资源管理。

## <a name="next-steps"></a>后续步骤

- 了解[跨租户管理体验](cross-tenant-management-experience.md)。
- 了解如何[加入 Azure 委派资源管理的订阅](../how-to/onboard-customer.md)。
- 详细了解[云解决方案提供商计划](https://docs.microsoft.com/partner-center/csp-overview)。
