---
title: 合作伙伴中心商业市场分析的常见问题和术语
description: 了解如何解决有关商业市场分析的常见问题。 包括用于分析术语的数据字典。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: d53ae01fb302382ca5bc2d31e729e153aea6c838
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286075"
---
# <a name="frequently-asked-questions-and-terminology-for-commercial-marketplace-analytics"></a>商业市场分析的常见问题和术语

本文解决了合作伙伴中心中有关分析消息的常见问题，并提供了分析术语字典。

## <a name="frequently-asked-questions"></a>常见问题

本节提供有关合作伙伴中心中 **"无分析可用"** 消息的常见问题解答。

**我无法在合作伙伴中心查看我的分析数据。当我访问这些页面时，我看到下面的消息。为什么会这样？**

![尚未提供任何数据](./media/analytics-faq-no-data.png)

为什么您可能会收到此消息：

- 您在市场上发布的优惠目前不存在任何收购。 这可能意味着您的产品/服务是实时市场，并在产品显示页面中从客户获取视图，但客户尚未采取行动购买和部署它们。
- 您的优惠的发布可能正在进行中，尚未提供。 只有现场优惠才能由客户购买。 要检查产品/服务的状态，请参阅["分析"仪表板](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)中的"概述"。 有关详细信息，请参阅[商业市场分析中的摘要仪表板](./summary-dashboard.md)。
- 您的优惠可能被列为 **"联系我**"，这是仅列表优惠，不能由市场客户购买。 尽管这些优惠生成潜在顾客并与您共享，但订单不会针对这些产品/服务创建，因为它们无法购买。 要检查产品/服务列表类型，请转到设置页面。

**我知道我有分析数据，但下面的消息显示：**

![没有给定日期范围的数据](./media/analytics-faq-data-range.png)

如果您收到此消息，则意味着您有分析数据，但没有所选日期范围的数据。 选择不同的日期范围或自定义日期范围以查看自 2010 年以来的任何数据。 有关详细信息，请参阅[商业市场分析中摘要仪表板](./summary-dashboard.md)的日期范围部分。

## <a name="dictionary-of-data-terms"></a>数据术语词典

| 属性名称 | 报表 | 定义|
|---|---|---|
| Azure 许可证类型 | 客户， 订单 | 购买 Azure 的客户使用的许可协议类型。 也称为通道 |
| Azure 许可证类型：云解决方案提供商 | 客户， 订单 | 最终客户通过其云解决方案提供商（充当经销商）购买 Azure 和市场套餐。|
| Azure 许可证类型：企业 | 客户， 订单 | 最终客户通过直接与 Microsoft 签署的企业协议购买 Azure 和市场套餐。|
| Azure 许可证类型：通过经销商签署的企业协议  | 客户， 订单 | 最终客户通过经销商采购 Azure 和您的应用商店产品/服务，经销商为与 Microsoft 的企业协议提供便利。|  |
| Azure 许可证类型：即用即付| 客户， 订单 | 最终客户通过直接与 Microsoft 签署的"随用付费"协议采购 Azure 和应用商店产品/服务。||
| 云实例名称| 订单| 部署 VM 的 Microsoft 云。||
| 云实例名称：Azure 全局| 订单| 公共全球微软云。|| |
| 云实例名称：Azure 政府 | 订单| 针对政府特定的微软云为以下政府之一：中国，德国或美利坚合众国。| |
| 客户所在城市| 客户| 客户提供的城市名称。 城市可能不同于客户 Azure 订阅中的城市。||
| 客户通信语言  | 客户| 客户的首选通信语言。||
| 客户公司名称 | 客户， 订单 | 客户提供的公司名称。 名称可能不同于客户 Azure 订阅中的城市。|  |
| 客户所在国家/地区 | 客户， 订单 | 客户提供的国家/地区名称。 国家/地区可能不同于客户 Azure 订阅中的国家/地区。|  |
| 客户电子邮件| 客户| 最终客户提供的电子邮件地址。 电子邮件可能不同于客户的 Azure 订阅中的电子邮件地址。||
| 客户名字| 客户| 客户提供的名称。 名称可能不同于客户的 Azure 订阅中提供的名称。| |
| 客户 ID | 客户， 订单 | 分配给客户的唯一标识符。 客户可能具有零个或多个 Azure 应用商店订阅。|  |
| 客户邮政编码  | 客户| 客户提供的邮政编码。 代码可能不同于客户的 Azure 订阅中提供的邮政编码。| |
| 客户所在的州/省| 客户| 客户提供的状态（地址）。 状态可能不同于客户 Azure 订阅中提供的状态。| |
| 购买日期| 客户| 客户购买您发布的任何产品/服务的第一个日期。| |
| 丢失日期| 客户| 客户取消以前购买的所有优惠中的最后一个日期。||
| 是新客户  | 订单| 该值将识别首次（或不）获取一个或多个产品/服务的新客户。 如果在同一日历月内为"获取日期"，则值为"是"。 如果客户在报告日历月之前购买了您的任何优惠，则价值为"否"。 |
| 是预览 SKU| 订单| 该值将告知您是否将 SKU 标记为"预览"。 如果 SKU 已相应地标记，并且只有您授权的 Azure 订阅可以部署和使用此映像，则值将为"是"。 如果 SKU 未标识为"预览"，则值将为"否"。  |
| 促销联系人是否选择加入| 客户| 该值将让您知道，如果客户主动选择从发布商进行促销联系。 目前我们不会向客户提供该选项，因此，在整个板块中都指示了“否”。 部署此功能后，我们会相应地做出更新。|
| 市场许可证类型| 订单| 市场套餐的计费方式。||
| 市场许可证类型：通过 Azure 计费| 订单| Microsoft 是此市场套餐的代理，并代你向客户计费。 （PAYG 信用卡或企业发票）||
| 市场许可证类型：自带许可 | 订单| VM 需要客户提供的许可证密钥才能部署。 Microsoft 不向客户收取通过市场以这种方式列出其优惠的费用。||
| 市场许可证类型：免费| 订单| 该优惠配置为对所有用户免费。 Microsoft 不向客户收取此优惠的使用费用。||
| 市场许可证类型：Microsoft 充当经销商  | 订单| Microsoft 是此市场套餐的经销商。|  |
| 应用商店订阅 ID | 客户， 订单 | 与客户用于购买应用商店产品/服务所用的 Azure 订阅关联的唯一标识符。 ID 以前是 Azure 订阅 GUID。||
| 产品名称  | 订单| 应用商店产品的名称。|| |
| 产品/服务类型  | 订单| 微软应用商店产品的类型。|||
| 套餐类型：托管的应用程序  | 以 | 在需要以下条件时，请使用 Azure 应用：托管应用产品/服务类型：使用 VM 或基于 IaaS 的整个解决方案为客户部署基于订阅的解决方案。 您或您的客户要求解决方案由合作伙伴管理。 |
| 产品/服务类型：Azure 应用程序| 以 | 当解决方案需要除简单 VM 之外的其他部署和配置自动化时，请使用 Azure 应用程序解决方案解决方案产品类型。||
| 优惠类型：咨询服务| 订单| Azure 市场中的咨询服务有助于将客户与服务联系起来，以支持和扩展他们对 Azure 的使用。| |
| 优惠类型：容器 | 订单| 如果解决方案是预配为基于 Kubernetes 的 Azure 容器服务的 Docker 容器映像，请使用“容器”产品/服务类型。||
| 优惠类型： 动态 365 商务中心| 订单| 当您的解决方案与 Dynamics 365 集成财务和运营时，使用此产品/服务类型| |
| 产品类型：动态 365，用于客户互动 | 订单| 当您的解决方案与 Dynamics 365 集成以进行客户互动时，请使用此产品/服务类型。||
| 产品供应类型：物联网边缘模块 | 订单| Azure IoT Edge 模块是 IoT Edge 管理的最小计算单元，可以包含 Microsoft 服务（如 Azure 流分析）、第三方服务或您自己的特定于解决方案的代码。 |
| 产品/服务类型：电源 BI 应用程序 | 订单| 部署与 Power BI 集成的应用程序时，请使用 Power BI 应用程序产品类型。|  |
| 产品/服务类型：SaaS 应用程序| 订单| 使用 SaaS 应用程序产品/服务类型可让客户以订阅的形式购买基于 SaaS 的技术解决方案。||
| 产品/服务类型：虚拟机 | 订单| 将虚拟设备部署到与客户关联的订阅时，请使用“虚拟机”产品/服务类型。||
| 优惠类型：可视化工作室市场扩展  | 订单| 以前对 Azure DevOps 扩展开发人员可用的产品/服务类型。 今后，Azure DevOps 扩展开发人员可以直接将其扩展销售给客户。 扩展产品/服务可以配置为付费或包括试用版。 |
| 订单取消日期| 订单| 取消市场订单的日期。||
| 订单 ID| 订单| 市场服务的客户订单的唯一标识符。 基于虚拟机使用情况的优惠不与订单相关联。| |
| 订单购买日期| 订单| 创建市场订单的日期。|||
| 订单状态| 订单| 上次刷新数据时市场订单的状态。|     |
| 订单状态：活动  | 订单| 客户已购买订单，尚未取消订单。|         |
| 订单状态：已取消 | 订单| 客户以前购买了订单，随后取消了订单。||
| 提供商电子邮件| 客户| 参与 Microsoft 与最终客户之间关系的提供商的电子邮件地址。 如果客户是通过经销商的企业，这将是经销商。 如果涉及云解决方案提供商 （CSP），这将是 CSP。|
| Provider Name| 客户| Microsoft 和最终客户之间的关系中涉及的提供商的名称。 如果客户是通过经销商的企业，这将是经销商。 如果涉及云解决方案提供商 （CSP），这将是 CSP。|
| SKU| 订单| 发布期间定义的 SKU 名称。 一个套餐可能有许多 SKU，但一个 SKU 只能与一个套餐相关联。||
| 试用结束日期| 订单| 此订单的试用期将要结束或已结束的日期。||

## <a name="next-steps"></a>后续步骤

- 有关合作伙伴中心商业市场中可用的分析报告的概述，请参阅[合作伙伴中心商业市场的分析](./analytics.md)。
- 有关汇总产品/服务市场活动的汇总数据的图表、趋势和值，请参阅[商业市场分析中的摘要仪表板](./summary-dashboard.md)。
- 有关您的订单以图形和可下载格式的信息，请参阅[商业市场分析中的订单仪表板](./orders-dashboard.md)。
- 有关虚拟机 （VM） 提供使用情况和按流量计费指标，请参阅[商业应用商店分析中的使用情况仪表板](./usage-dashboard.md)。
- 有关客户的详细信息（包括增长趋势），请参阅[商业市场分析中的客户仪表板](./customer-dashboard.md)。
- 有关过去 30 天的下载请求列表，请参阅[商业应用商店分析中的下载仪表板](./downloads-dashboard.md)。
- 要查看 Azure 应用商店和 AppSource 上产品/服务的客户反馈的合并视图，请参阅[商业应用商店分析中的评级和审核仪表板](./ratings-reviews.md)。
