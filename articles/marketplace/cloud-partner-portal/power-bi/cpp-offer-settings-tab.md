---
title: 电源 BI 应用产品/服务提供设置 |Azure 应用商店
description: 为 Microsoft AppSource 市场配置 Power BI 应用产品/服务的产品/服务设置。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: f3a8d740d391edc09a290d3dba4307af7eec00b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286347"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Power BI 应用套餐设置选项卡

当您为服务应用打开 **"新产品/服务**"页面时，首先可以看到"**产品/服务设置"** 选项卡。您可以在此选项卡上提供主要标识符和产品/服务的名称。星号 （*） 表示必填字段。

![“产品/服务设置”选项卡](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>产品/服务设置字段 

在"**优惠设置"** 选项卡上，您需要在以下必填字段中输入信息。 必填字段用星号 (*) 表示。

|  字段        |  描述                                                               |
|---------------|----------------------------------------------------------------------------|
| **产品/服务 ID\***  | 产品/服务的唯一标识符（在发布者配置文件内）。 此标识符将显示在产品 URL、Azure 资源管理器模板和计费报表中。 最大长度为 50 个字符。 它只能包含小写字母数字字符和破折号 （-）。 它不能以破折号结束。 此标识符在产品/服务生效后无法更改。 如果 Contoso 发布报价 ID，`sample-SvcApp`则为产品/服务分配了`https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`AppSource URL。      |
| **发布者\*** | 你的组织在 [AppSource](https://appsource.microsoft.com) 中的唯一标识符。 你的所有产品/服务都应当与你的发布者 ID 相关联。 保存产品/服务后，无法更改此值。                         |
| **“属性”\***      | 优惠的显示名称。 此名称将显示在 AppSource 和云合作伙伴门户上。 最大长度为 50 个字符。 使用产品可识别的品牌名称。 不要在此处包含组织的名称，除非应用使用该名称进行销售。 如果您在其他网站和出版物上提供此优惠，则在所有出版物中使用同一名称。    <br/>如果在 Power BI 应用的预览期内发布产品/服务，请在`(Preview)`应用程序名称的末尾添加字符串，如下所示： `Sample Scv App (Preview)`。 |
|     |     |


## <a name="next-steps"></a>后续步骤

在下一个选项卡上，您将为产品/服务指定[技术信息](./cpp-technical-info-tab.md)。
