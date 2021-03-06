---
title: Azure 活动目录标识保护安全概述
description: 了解"安全概述"如何让您深入了解组织的安全状况。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9550684ad154f28a02ee347fd0a79c1ec286beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382187"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Azure Active Directory 标识保护 - 安全概览

Azure 门户中[的安全概述](https://aka.ms/IdentityProtectionRefresh)让您深入了解组织的安全状况。 它有助于发现潜在攻击，并了解策略有效性。

“安全概览”大致分为以下两部分：

- 趋势：位于左侧，呈现组织的风险时间线。
- 磁贴：位于右侧，突出显示组织中正在发生的关键问题，并建议如何快速采取措施。

![安全概述](./media/concept-identity-protection-security-overview/01.png)
  
## <a name="trends"></a>趋势

### <a name="new-risky-users-detected"></a>检测到新的有风险用户

此图表显示在选定时间段内检测到的新的有风险用户数。 可以按用户风险级别（低、中、高）筛选此图表视图。 将鼠标悬停在 UTC 日期增量之上可以查看当天检测到的有风险用户数。 单击此图表会转到“有风险用户”报告。 若要修正有风险用户，不妨更改这些用户的密码。

### <a name="new-risky-sign-ins-detected"></a>检测到新的有风险登录

此图表显示在选定时间段内检测到的有风险登录数。 可以按登录风险类型（实时或聚合）和登录风险级别（低、中、高）筛选此图表视图。 未保护的登录是指未接受 MFA 质询的成功实时风险登录。 （注意：由于脱机检测而存在风险的登录无法通过登录风险策略实时保护）。 将鼠标悬停在 UTC 日期增量之上可以查看当天检测到的有风险登录数。 单击此图表会转到“有风险登录”报告。

## <a name="tiles"></a>磁贴
 
### <a name="high-risk-users"></a>高风险用户

“高风险用户”磁贴显示标识遭入侵可能性高的用户的最新计数。 应最优先调查这些用户。 单击“高风险用户”磁贴会重定向到“有风险用户”报告的筛选后视图，其中只显示高风险级别用户。 使用此报告，可以了解详细信息，并通过重置密码来修正这些用户。

![安全概述](./media/concept-identity-protection-security-overview/02.png)

### <a name="medium-risk-users"></a>中等风险用户
“中等风险用户”磁贴显示标识遭入侵可能性中等的用户的最新计数。 单击“中等风险用户”磁贴会重定向到“有风险用户”报告的筛选后视图，其中只显示中等风险级别用户。 使用此报告，可以进一步调查，并修正这些用户。

### <a name="unprotected-risky-sign-ins"></a>未保护的有风险登录

"未受保护的风险登录"磁贴显示上周成功的实时风险登录计数，这些登录未被条件访问策略、身份保护风险策略或每个用户 MFA 阻止或 MFA 提出质疑。 这些是成功但没有接受 MFA 质询的潜在遭入侵登录。 为了今后保护此类登录，请应用登录风险策略。 单击“未保护的有风险登录”磁贴会重定向到“登录风险策略配置”边栏选项卡，在其中可以将登录风险策略配置为，必须对指定风险级别的登录进行 MFA。

### <a name="legacy-authentication"></a>旧式身份验证

“旧身份验证”磁贴显示上周组织中的旧身份验证数。 旧身份验证协议不支持 MFA 等新式安全方法。 为了防止使用旧身份验证，可以应用条件访问策略。 单击“旧身份验证”磁贴会重定向到“标识安全功能分数”。

### <a name="identity-secure-score"></a>标识安全评分

“标识安全功能分数”衡量并比较你的安全状态与行业模式。 如果您单击"身份安全分数（预览）"磁贴，它将重定向到"身份安全分数"边栏选项卡，您可以在其中了解有关改善安全状态的更多信息。

## <a name="next-steps"></a>后续步骤

- [什么是风险](concept-identity-protection-risks.md)

- [可用于规避风险的策略](concept-identity-protection-policies.md)
