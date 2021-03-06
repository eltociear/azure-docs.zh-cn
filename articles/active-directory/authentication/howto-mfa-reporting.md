---
title: Azure MFA 的访问和使用报告 - Azure 活动目录
description: 介绍如何使用 Azure 多重身份验证功能 - 报告。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b47e6bac031a5f2cffc8734fee976fbf8dadc666
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129084"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Azure 多重身份验证中的报告

Azure 多重身份验证提供了几个可通过 Azure 门户访问的报告，供你和你的组织使用。 下表列出了可用的报告：

| 报表 | 位置 | 描述 |
|:--- |:--- |:--- |
| 阻止的用户历史记录 | Azure AD >安全> MFA >阻止/取消阻止用户 | 显示请求阻止或解除阻止用户的历史记录。 |
| 使用情况和欺诈警报 | Azure AD > 登录 | 提供有关总体使用情况、用户摘要和用户详细信息的信息；以及指定日期范围内提交的欺诈警报的历史记录。 |
| 本地组件的使用情况 | Azure AD >安全> MFA >活动报告 | 提供有关通过 NPS 扩展、ADFS 和 MFA 服务器实现的 MFA 的总体使用情况信息。 |
| 跳过的用户历史记录 | Azure AD >安全> MFA >一次性绕过 | 提供请求对用户跳过多重身份验证的历史记录。 |
| 服务器状态 | Azure AD >安全> MFA >服务器状态 | 显示与帐户关联的多重身份验证服务器的状态。 |

## <a name="view-mfa-reports"></a>查看 MFA 报告

1. 登录到 Azure[门户](https://portal.azure.com)。
2. 在左侧，选择**Azure 活动目录** > **安全** > **MFA**。
3. 选择要查看的报告。

   ![Azure 门户中的 MFA 服务器服务器状态报告](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Azure AD 登录报告

通过 [Azure 门户](https://portal.azure.com)中的**登录活动报告**，可以获取确定环境运行状况所需的信息。

登录报告可以提供托管应用程序的使用和用户登录活动方面的信息，其中包括多重身份验证 (MFA) 使用方面的信息。 可以通过 MFA 数据了解 MFA 在组织中的使用情况。 可以通过它来了解以下情况：

- 登录时是否进行了 MFA？
- 用户如何完成 MFA？
- 用户无法完成 MFA 的原因是什么？
- 对多少用户进行了 MFA？
- 无法完成 MFA 质询的用户有多少？
- 最终用户遇到的常见 MFA 问题有哪些？

此数据可通过 Azure[门户](https://portal.azure.com)和报告[API](../reports-monitoring/concept-reporting-api.md)获得。

![Azure 门户中的 Azure AD 登录报告](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>登录报告结构

可以通过 MFA 登录活动报告了解以下信息：

**需要 MFA：** 登录时是否需要 MFA。 由于每个用户的 MFA、条件访问或其他原因，可能需要 MFA。 可能的值为“是”**** 或“否”****。

**MFA 结果：** 有关 MFA 是通过还是未通过的详细信息：

- 如果 MFA 通过，则此列会提供 MFA 如何通过的详细信息。
   - Azure 多重身份验证
      - 已在云中完成
      - 已到期，因为在租户上配置了相关策略
      - 系统提示注册
      - 已通过（根据令牌中的声明）
      - 已通过（根据外部提供程序提供的声明）
      - 已通过（采用强身份验证）
      - 已跳过，因为执行的流为 Windows 中转站登录流
      - 已跳过，因为应用密码原因
      - 已跳过，因为位置原因
      - 已跳过，因为设备已注册
      - 已跳过，因为系统已记住该设备
      - 已成功完成
   - 已重定向到多重身份验证的外部提供程序

- 如果 MFA 未通过，则此列会提供未通过的原因。
   - Azure 多重身份验证未通过；
      - 身份验证正在进行中
      - 重复进行身份验证尝试
      - 输入错误代码次数过多
      - 身份验证无效
      - 移动应用验证码无效
      - 配置错误
      - 电话呼叫转到语音邮件
      - 电话号码格式无效
      - 服务错误
      - 无法接通用户的电话
      - 无法向设备发送移动应用通知
      - 无法发送移动应用通知
      - 用户已拒绝身份验证
      - 用户未响应移动应用通知
      - 用户没有注册任何验证方法
      - 用户输入了不正确的代码
      - 用户输入了不正确的 PIN
      - 用户挂断了电话，没有成功进行身份验证
      - 用户被阻止
      - 用户从未输入验证代码
      - 找不到用户
      - 验证代码已使用过一次

**MFA 身份验证方法：** 用户用来完成 MFA 的身份验证方法。 可能的值包括：

- 短信
- 移动应用通知
- 电话呼叫（身份验证电话）
- 移动应用验证码
- 电话呼叫（办公电话）
- 电话呼叫（其他身份验证电话）

**MFA 身份验证详细信息：** 清理版的电话号码，例如：+X XXXXXXXX64。

**条件访问**查找有关影响登录尝试的条件访问策略的信息，包括：

- 策略名称
- 授权控制
- 会话控制
- 结果

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>PowerShell 报告为 MFA 注册的用户

首先，请确保您安装了[MSOnline V1 PowerShell 模块](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0)。

使用后面的 PowerShell 标识已注册 MFA 的用户。 此命令集排除已禁用的用户，因为这些帐户无法针对 Azure AD 进行身份验证。

```Get-MsolUser -All | Where-Object {$.StrongAuthenticationMethods -ne $null -and $.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName```

使用后面的 PowerShell 标识未注册 MFA 的用户。 此命令集排除已禁用的用户，因为这些帐户无法针对 Azure AD 进行身份验证。

```Get-MsolUser -All | Where-Object {$.StrongAuthenticationMethods.Count -eq 0 -and $.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName```

标识已注册的用户和输出方法。 

```PowerShell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="possible-results-in-activity-reports"></a>活动报告中的可能结果

下表可用于使用多重身份验证活动报告的下载版本对多重身份验证进行故障排除。 它们不会直接显示在 Azure 门户中。

| 呼叫结果 | 描述 | 广泛描述 |
| --- | --- | --- |
| SUCCESS_WITH_PIN | 已输入 PIN | 用户已输入 PIN。  如果身份验证成功，则他们输入的 PIN 是正确的。 如果拒绝身份验证，则输入了不正确的 PIN 或用户设置为标准模式。 |
| SUCCESS_NO_PIN | 仅 = 已输入 | 如果用户被设置为“PIN”模式并且身份验证被拒绝，这表示用户没有输入自己的 PIN，而只输入了 #。   如果用户被设置为“标准”模式并且身份验证成功，这表示用户只输入了 #，而这在“标准”模式下是正确的做法。 |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | 输入后未按 # | 由于未输入 #，因此用户未发送任何 DTMF 数字。  除非已输入 # 表明输入完成，否则不会发送已输入的其他数字。 |
|SUCCESS_NO_PIN_BUT_TIMEOUT | 未输入电话号码 - 已超时 | 呼叫已被接听，但是无人应答。  这通常表明呼叫由语音信箱接听。 |
| SUCCESS_PIN_EXPIRED | PIN 已过期且未更改 | 用户的 PIN 已过期，并且系统已提示用户更改 PIN，但他们未成功完成 PIN 的更改。 |
| SUCCESS_USED_CACHE | 已使用缓存 | 身份验证成功，无需进行多重身份验证调用，因为之前对同一用户名的成功身份验证是在配置的缓存时间范围内发生的。 |
| SUCCESS_BYPASSED_AUTH | 已免身份验证 | 已使用为用户发起的免验证一次成功进行身份验证。  有关免验证的详细信息，请参阅“免验证用户的历史记录”报告。 |
| SUCCESS_USED_IP_BASED_CACHE | 已使用基于 IP 的缓存 | 身份验证成功，无需进行多重身份验证调用，因为之前在配置的缓存时间范围内成功进行了对相同用户名、身份验证类型、应用程序名称和 IP 的身份验证。 |
| SUCCESS_USED_APP_BASED_CACHE | 已使用基于应用程序的缓存 | 身份验证成功，无需进行多重身份验证调用，因为之前在配置的缓存时间范围内成功验证了相同的用户名、身份验证类型和应用程序名称。 |
| SUCCESS_INVALID_INPUT | 手机输入无效 | 通过手机发送的响应无效。 这可能是来自传真机或调制解调器，或者用户可能已输入 * 作为其 PIN 的一部分。 |
| SUCCESS_USER_BLOCKED | 用户被阻止 | 用户的电话号码被阻止。 在身份验证调用期间，用户可以启动阻止的号码，也可以由使用 Azure 门户的管理员启动。 <br> 注：被阻止的号码也是欺诈警报的副产品。 |
| SUCCESS_SMS_AUTHENTICATED | 已通过文本消息进行身份验证 | 对于双向文本消息，用户正确地使用了一次性密码 (OTP) 或 OTP + PIN 进行回复。 |
| SUCCESS_SMS_SENT | 已发送文本消息 | 对于文本消息，已成功发送包含一次性密码 (OTP) 的文本消息。 用户将在应用程序中输入 OTP 或 OTP + PIN 以完成身份验证。 |
| SUCCESS_PHONE_APP_AUTHENTICATED | 已通过移动应用程序进行身份验证 | 用户已成功通过移动应用程序进行身份验证。 |
| SUCCESS_OATH_CODE_PENDING | OATH 代码待处理 | 系统已提示用户输入其 OATH 代码，但是该用户未做出响应。 |
| SUCCESS_OATH_CODE_VERIFIED | 已验证 OATH 代码 | 在出现提示后，用户输入了有效的 OATH 代码。 |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | 已验证回退 OATH 代码 | 用户已被拒绝使用其主要多重身份验证方法进行身份验证，于是提供了一个有效的 OATH 代码以进行回退。 |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | 已回答回退安全问题 | 用户已被拒绝使用其主要多重身份验证方法进行身份验证，于是正确回答了自己的安全问题以进行回退。 |
| FAILED_PHONE_BUSY | 身份验证已在进行中 | 多重身份验证已在处理对此用户的身份验证。 这通常是由 RADIUS 客户端引起的，这些客户端在同一登录期间发送了多个身份验证请求。 |
| CONFIG_ISSUE | 无法接通电话 | 已尝试呼叫，但无法发出呼叫或未应答。 这包括忙信号、快速忙信号（断开连接）、三音（不再使用的数字）、振铃时超时等。 |
| FAILED_INVALID_PHONENUMBER | 电话号码格式无效 | 电话号码格式无效。 电话号码必须是数字，并且国家代码 #1（美国&加拿大）必须为 10 位数字。 |
| FAILED_USER_HUNGUP_ON_US | 用户已挂断电话 | 用户接听了电话，但是没有按任何按钮就挂断了电话。 |
| FAILED_INVALID_EXTENSION | 分机号无效 | 该分机号包含无效字符。 只允许数字、逗号、*和 *。  还可以使用 @ 前缀。 |
| FAILED_FRAUD_CODE_ENTERED | 已输入欺诈行为代码 | 用户在通话期间选择举报欺诈行为，导致身份验证被拒绝且电话号码被阻止。| 
| FAILED_SERVER_ERROR | 无法拨打电话 | 多重身份验证服务无法发出呼叫。 |
| FAILED_SMS_NOT_SENT | 无法发送文本消息 | 无法发送文本消息。  身份验证被拒绝。 |
| FAILED_SMS_OTP_INCORRECT | 文本消息 OTP 不正确 | 用户从收到的短信中输入了不正确的一次性密码 （OTP）。  身份验证被拒绝。 |
| FAILED_SMS_OTP_PIN_INCORRECT | 文本消息 OTP + PIN 不正确 | 用户输入的一次性密码 （OTP） 和/或不正确的用户 PIN。  身份验证被拒绝。 |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | 超过最大短信 OTP 尝试次数 | 用户已超过一次性密码 （OTP） 尝试的最大次数。 |
| FAILED_PHONE_APP_DENIED | 已拒绝移动应用程序 | 用户通过按“拒绝”按钮，拒绝了在移动应用程序中进行身份验证。 |
| FAILED_PHONE_APP_INVALID_PIN | 移动应用程序 PIN 无效 | 当在移动应用程序中进行身份验证时，用户所输入的 PIN 无效。 |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | 移动应用程序 PIN 未更改 | 用户未在移动应用程序中成功完成所需的 PIN 更改。 |
| FAILED_FRAUD_REPORTED | 已举报欺诈行为 | 用户已报告移动应用程序中的欺诈行为。 |
| FAILED_PHONE_APP_NO_RESPONSE | 移动应用程序没有响应 | 用户未响应移动应用身份验证请求。 |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | 已在所有设备上阻止移动应用程序 | 此用户的移动应用程序设备不再对通知做出响应并且已被阻止。 |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | 移动应用程序通知失败 | 尝试在用户的设备上向移动应用程序发送通知时失败。 |
| FAILED_PHONE_APP_INVALID_RESULT | 移动应用程序结果无效 | 移动应用程序返回的结果无效。 |
| FAILED_OATH_CODE_INCORRECT | OATH 代码不正确 | 用户输入的 OATH 代码不正确。   身份验证被拒绝。 |
| FAILED_OATH_CODE_PIN_INCORRECT | 誓言代码 = PIN 不正确 | 用户输入的 OATH 代码和/或不正确的用户 PIN。   身份验证被拒绝。 |
| FAILED_OATH_CODE_DUPLICATE | OATH 代码重复 | 用户输入了一个以前用过的 OATH 代码。   身份验证被拒绝。 |
| FAILED_OATH_CODE_OLD | OATH 代码过期 | 用户输入的 OATH 代码早于以前使用过的 OATH 代码。   身份验证被拒绝。 |
| FAILED_OATH_TOKEN_TIMEOUT | OATH 代码结果超时 | 用户输入 OATH 代码的时间太长，多重身份验证尝试已超时。 |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | 安全问题结果超时 | 用户花了很长时间才输入对安全问题的回答，多重身份验证尝试已超时。 |
| FAILED_AUTH_RESULT_TIMEOUT | Auth 结果超时 | 用户完成多重身份验证尝试的时间太长。 |
| FAILED_AUTHENTICATION_THROTTLED | 身份验证已限制 | 多因素身份验证尝试被服务限制。 |

## <a name="next-steps"></a>后续步骤

* [SSPR 和 MFA 使用情况和见解报告](howto-authentication-methods-usage-insights.md)
* [面向用户](../user-help/multi-factor-authentication-end-user.md)
* [部署位置](concept-mfa-whichversion.md)
