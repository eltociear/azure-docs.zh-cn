---
title: 市场‎商业交易功能和注意事项 | Azure
description: 本文介绍套餐类型的交易定价、计费、开具发票和付款注意事项。
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: dsindona
ms.openlocfilehash: de93a3c48e0b0aceb447e54e11190f487d1aa2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279821"
---
# <a name="commercial-marketplace-transaction-capabilities-and-considerations"></a>商业市场交易功能和注意事项

本文介绍商业市场的以下与商业相关的主题

* 市场发布选项
* 交易一般概述
* 交易计费模式
* 交易要求

## <a name="marketplace-publishing-options"></a>市场发布选项

以下发布选项可供商业市场发布商使用。

### <a name="list--trial-publishing-options"></a>列表和试用发布选项

发布商可以利用列表、试用版和 BYOL 发布选项进行促销和用户获取。 使用这些选项，Microsoft 不会直接参与发布者的软件许可证交易，并且没有关联的交易费用。 发布者负责支持软件许可证交易的各个方面，包括但不限于：订单、履行、计量、计费、开具发票、付款和费用收集。 借助列表和试用发布选项，发布者保留 100% 的收集自客户的发布者软件许可费。 

### <a name="transact-publishing-option"></a>交易发布选项

除了列表和试用发布选项外，发行商还可以使用交易发布选项。 这利用了 Microsoft 的全球可用商务功能，并允许 Microsoft 代表发布者托管云市场事务。

## <a name="transact-general-overview"></a>交易一般概述

使用交易发布选项时，Microsoft 允许销售第三方软件，并将某些产品/服务类型部署到客户的 Azure 订阅。 在选择计费模型和产品/服务类型时，发布者必须考虑基础结构费用的计费以及发布者自己的软件许可费用。

当前支持以下产品类型（虚拟机、Azure 应用程序和 SaaS 应用）的 Transact 发布选项。


![[处理 Azure 市场中的企业交易]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

### <a name="billing-infrastructure-costs"></a>计费基础结构成本

**对于虚拟机和 Azure 应用程序**

对于虚拟机和 Azure 应用程序，Azure 基础结构使用费将计费到客户的 Azure 订阅。  基础设施使用费在客户发票上与软件提供商的许可费分开定价和列报。

**对于 SaaS 应用**

对于 SaaS 应用，发布者必须将 Azure 基础结构使用费和软件许可费视为单一费用项。  它表示为客户收取固定费用。 Azure 基础结构使用情况由合作伙伴直接管理并对其计费。  客户将无法看到实际的基础结构使用费。  发布者通常选择将 Azure 基础结构使用费捆绑到其软件许可证定价中。  不计量软件许可费，它也不基于消耗量。

## <a name="transact-billing-models"></a>交易计费模式

根据所使用的交易选项，发布者的软件许可费用可以显示如下：  

* 免费：不收取软件许可证费用。 

* 自带许可 (BYOL)：直接在发布者和客户之间管理所有适用的软件许可证费用。 Microsoft 仅收取 Azure 基础结构使用费。 （仅限虚拟机和 Azure 应用程序。）

* 即用即付：软件许可证费用将根据所用的 Azure 基础结构显示为每小时每个核心 (vCPU) 定价费率。 这仅适用于虚拟机和 Azure 应用程序。

* 订阅定价：软件许可费以每月或每年定期费用的形式提供，按统一费率或按席位计费。 这仅适用于 SaaS 应用和 Azure 应用程序 - 托管应用。

* 免费软件试用版：免费使用软件许可证 30 天或 90 天。

### <a name="free-and-bring-your-own-license-byol-pricing"></a>免费和自带许可 (BYOL) 定价

发布免费或自带许可的交易产品/服务时，Microsoft 在促成有关软件许可证费用的销售交易中不扮演任何角色。 如列表和试用发布选项一样，发布者保留 100% 的软件许可证费用。 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>即用即付和订阅（基于站点）定价

发布即用即付或订阅型交易产品/服务时，Microsoft 提供技术和服务用于处理软件许可证的购买、退货和退款。 在这种情况下，发布者出于上述目的授权 Microsoft 充当代理人。 发布者允许 Microsoft 促成软件许可交易，同时保留自身的卖家、提供商、分销商和许可方的称号。

Microsoft 使客户能够订购、许可和使用发布商软件，但须遵守 Microsoft 商业应用商店和发布商最终用户许可协议的条款和条件。 发布商必须在创建产品/服务时提供其最终用户许可协议或选择[标准合同](https://docs.microsoft.com/azure/marketplace/standard-contract)。


### <a name="free-software-trials"></a>免费软件试用

对于交易发布方案，发布者可使软件许可证免费提供 30 天或 90 天。 此折扣功能不包括使用合作伙伴解决方案引致的 Azure 基础结构使用费用。

### <a name="private-offers"></a>专用产品/服务

除了使用产品/服务类型和计费模型将产品/服务货币化外，发布商还可以处理私人产品/服务，并完成协商、特定于交易的定价或自定义配置。 专用产品/服务受全部 3 个交易发布选项的支持。

此选项允许比公开产品更高或更低的定价。 专用产品/服务可用于为产品/服务打折扣或为其添加奖励金。 可在产品/服务级别将其 Azure 订阅添加到允许列表，向一个或多个客户提供专用产品/服务。


### <a name="examples"></a>示例

**即用即付** 

* 如果启用“即用即付”选项，则会得到以下成本结构。

|你的许可证费用  | 每小时 $1.00  |
|---------|---------|
|Azure 使用费用（D1/1 核）    |   每小时 $0.14     |
|*由 Microsoft 向客户收费*    |  *每小时 $1.14*       |

* 在此场景中，Microsoft 按每小时 $1.14 收取所发布的 VM 映像的使用费用。

|Microsoft 收费  | 每小时 $1.14  |
|---------|---------|
|Microsoft 将许可证费用的 80% 支付给你|   每小时 $0.80     |
|Microsoft 保留许可证费用的 20%  |  每小时 $0.20       |
|Microsoft 保留 100% 的 Azure 使用费 | 每小时 $0.14 |

**自带许可证 （BYOL）**

* 如果启用“BYOL”选项，则会得到以下成本结构。

|你的许可证费用  | 许可证费用由你协商确定和收取  |
|---------|---------|
|Azure 使用费用（D1/1 核）    |   每小时 $0.14     |
|*由 Microsoft 向客户收费*    |  *每小时 $0.14*       |

* 在此场景中，Microsoft 按每小时 $0.14 收取所发布的 VM 映像的使用费用。

|Microsoft 收费  | 每小时 $0.14  |
|---------|---------|
|Microsoft 保留 Azure 使用费用    |   每小时 $0.14     |
|Microsoft 保留许可证费用的 0%   |  每小时 $0.00       |

**SaaS 应用程序订阅**

此选项必须配置为通过 Microsoft 销售，并且可以按固定费率定价，也可以按月或按年按每个用户定价。
*    如果为 SaaS 产品/服务启用"通过 Microsoft 销售"选项，则具有以下成本结构。

|你的许可证费用       | 100.00 美元/月  |
|--------------|---------|
|Azure 使用费用（D1/1 核）    | 直接向发布者而不是客户收费 |
|*由 Microsoft 向客户收费*    |  100.00 美元/月（注意：发布者必须将任何引致的或者转付的基础结构费用考虑到许可证费用中）**  |

* 在这种情况下，Microsoft 向你收取 100.00 美元的软件许可证费用，并向发布者支付 80.00 美元。
* 符合减少市场服务费条件的合作伙伴将在 2019 年 5 月至 2020 年 6 月期间降低 SaaS 优惠的交易费用。 在这种情况下，Microsoft 会为您的软件许可证支付 100.00 美元，然后向发布者支付 90.00 美元。

|Microsoft 收费  | 100.00 美元/月  |
|---------|---------|
|Microsoft 将许可证费用的 80% 支付给你 <br> \*Microsoft 为任何合格的 SaaS 应用支付 90% 的许可证成本   |   80.00 美元/月 <br> \*$90.00 每月    |
|Microsoft 保留许可证费用的 20% <br> \*对于任何合格的 SaaS 应用，Microsoft 保留 10% 的许可证成本。  |  20.00 美元/月 <br> \*$100.00     |

* **降低市场服务费：** 对于您在我们的商业市场上发布的某些 SaaS 产品，Microsoft 将降低其市场服务费从 20%（如 Microsoft 发布商协议中所述）降至 10%。  为了使您的产品符合资格，Microsoft 必须至少将其中一种产品指定为 IP 共同销售就绪或 IP 共同销售优先级。 要获得该月此降低的市场服务费，必须在上一个日历月结束前至少五 （5） 个工作日满足资格。 降低的市场服务费不适用于 VM、托管应用或通过我们的商业市场提供的任何其他产品。  此降低的市场服务费将提供给合格的产品/服务，Microsoft 将在 2019 年 5 月 1 日至 2020 年 6 月 30 日期间收取许可费。  之后，市场服务费将恢复到正常金额。

### <a name="customer-invoicing-payment-billing-and-collections"></a>客户开具发票、付款、计费和费用收集

**开具发票与付款**

发布商可以使用客户首选的开票方式提供订阅或 PAYGO 软件许可费用。

**企业协议** 

如果客户的首选开票方法是 Microsoft 企业协议，则软件许可费用将使用此开票方法作为明细成本计费，与任何特定于 Azure 的使用成本分开。

**信用卡和每月发票** 

客户还可以使用信用卡和每月发票进行付款。 在这种情况下，将像企业协议方案一样对软件许可证进行计费：采用逐项列出费用的形式并与任何特定于 Azure 的使用费分开。

例如，如果客户使用信用卡购买：

|描述    |    Date  |
|----------|----------|
|订购期   | 2018 年 8 月 15 日 - 2018 年 8 月 30 日 |
|期限结束（月）   | 2018 年 8 月 30 日 |
|计费日期 | 2018 年 9 月 1 日 |
|客户付款日期 | 2018 年 9 月 1 日 |
|代管期（仅限信用卡，30 天） | 九月 1， 2018 - 九月 30， 2018 |
|费用收集期开始 | 2018 年 9 月 1 日 |
|费用收集期结束（最大值，30 天） | 2018 年 9 月 30 日 |
|付款计算日期（每月第 15 天） | 2018 年 10 月 1 日 |
|付款日期 | 2018 年 10 月 15 日 |

如果客户使用企业协议购买：

| 描述 |    Date  |
|----------|----------|
|订购期 | 2018 年 8 月 15 日 - 2018 年 8 月 30 日 |
|期限结束（季度） | 2018 年 9 月 30 日 |
|计费日期 | 2018 年 10 月 15 日 |
|代管期（仅限信用卡，30 天） | 不适用 |
|费用收集期开始 | 2018 年 10 月 15 日 |
|费用收集期结束（最大值，90 天） | 2019 年 1 月 15 日 |
|客户付款日期 | 2018 年 12 月 30 日 |
|付款计算日期（每月第 15 天） | 2019 年 1 月 15 日 |
|付款日期 | 2019 年 2 月 15 日 |

**免费信用额度和货币承诺** 

某些客户选择通过企业协议中的货币承诺向 Azure 预付款，或者已向他们提供免费信用额度用于 Azure。 尽管可以这些信用额度可用于支付 Azure 使用情况，但它们不能用于支付发布者软件许可证费用。

**计费和费用收集** 

发布者软件许可证计费使用客户所选的发票开票方法进行显示，并遵循开票时间线。 将对没有企业协议的客户就市场软件许可证按月计费。 通过每季度显示的发票对具有企业协议的客户按月计费。

选择订阅或即用即付定价模型时，Microsoft 将充当发布者的代理，并且负责计费、付款和费用收集的所有方面。

### <a name="publisher-payout-and-reporting"></a>发布者付款和报表

* Microsoft 作为代理收集的所有软件许可费用将收取 20% 的手续费（另行指定的除外），且会在发布者付款时扣除。

* 客户通常使用企业协议或支持信用卡的即用即付协议进行购买。 协议类型确定计费、发票、费用收集和付款时间。

>[!NOTE] 
>交易发布选项的所有报告和见解均可通过合作伙伴中心的云合作伙伴门户或分析部分的"见解"部分获得。

#### <a name="billing-questions-and-support"></a>计费问题和支持

有关详细信息和法律策略，请参阅[发布者协议](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf)（请参见云合作伙伴门户）。

要获取有关计费问题的帮助，请联系[商业市场发布商支持](https://aka.ms/marketplacepublishersupport)。

## <a name="transact-requirements"></a>交易要求

本部分介绍不同产品/服务类型的交易要求。

### <a name="requirements-for-all-offer-types"></a>所有产品/服务类型的要求

- 无论产品/服务/服务的价格模式如何，交易发布选项都需要 Microsoft 帐户和财务信息。
- 强制性财务信息包括付款帐户和税务配置文件。

有关设置这些帐户的详细信息，请参阅[管理合作伙伴中心帐户](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account)。


### <a name="requirements-for-specific-offer-types"></a>特定产品/服务类型的要求

交易发布选项仅适用于以下市场产品/服务类型： 

**虚拟机** 

从免费、自带许可或即用即付定价模型中选择，并显示为在套餐级别定义的 SKU。 在客户的 Azure 帐单上，Microsoft 将发布者软件许可证费用与基础 Azure 基础结构费用分开显示。 Azure 基础结构费用取决于发布者软件使用情况。

**Azure 应用程序：解决方案模板或托管应用** 

必须通过虚拟机定价的总和预配一个或多个虚拟机和拉取。 对于单个计划的托管应用，可以选择固定费率每月订阅作为定价模型，而不是虚拟机定价。 在某些情况下，Azure 基础结构使用费与软件许可证费用分开传递给客户，但在同一计费报表上。 但是，如果为 ISV 基础结构费用配置托管应用产品，Azure 资源将计费给发布者，并且客户将收到固定费用，其中包括基础结构、软件许可证和管理服务的成本。

## <a name="next-steps"></a>后续步骤

* 查看发布选项中按套餐类型划分的资格要求，以便最终确定套餐的选择和配置。
* 查看按店面划分的发布模式，例如，解决方案如何映射到套餐类型和配置。
