---
title: 自定义策略中的技术配置文件概述
titleSuffix: Azure AD B2C
description: 了解如何在 Azure Active Directory B2C 的自定义策略中使用技术配置文件。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5d89d123fe757b0502f4c6b3a0d33c185b25224b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057314"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>关于 Azure Active Directory B2C 自定义策略中的技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

技术配置文件提供了一个框架，该框架具有内置机制，可使用 Azure Active Directory B2C (Azure AD B2C) 中的自定义策略与不同类型的参与方进行通信。 技术配置文件用于与 Azure AD B2C 租户通信、创建用户或读取用户配置文件。 技术配置文件可以自断言以实现与用户的交互。 例如，收集用户的凭据进行登录，然后呈现注册页面或密码重置页面。

## <a name="type-of-technical-profiles"></a>技术配置文件的类型

技术配置文件支持以下类型的方案：

- [应用程序见解](application-insights-technical-profile.md)- 将事件数据发送到[应用程序见解](../azure-monitor/app/app-insights-overview.md)。
- [Azure Active Directory](active-directory-technical-profile.md) - 为 Azure Active Directory B2C 用户管理提供支持。
- [Azure 多重身份验证](multi-factor-auth-technical-profile.md)- 支持使用 Azure 多重身份验证 （MFA） 验证电话号码。 
- [声明转换](claims-transformation-technical-profile.md) - 调用输出声明转换，针对一组输出声明来操作声明值、验证声明或设置默认值。
- [JWT 令牌颁发者](jwt-issuer-technical-profile.md) - 发出返回给信赖方应用程序的 JWT 令牌。
- [OAuth1](oauth1-technical-profile.md) - 与任何 OAuth 1.0 协议标识提供者联合。
- [OAuth2](oauth2-technical-profile.md) - 与任何 OAuth 2.0 协议标识提供者联合。
- [一次性密码](one-time-password-technical-profile.md)- 支持管理一次性密码的生成和验证。
- [OpenID Connect](openid-connect-technical-profile.md) - 与任何 OpenID Connect 协议标识提供者联合。
- [电话因素](phone-factor-technical-profile.md)- 支持注册和验证电话号码。
- [RESTful 提供程序](restful-technical-profile.md)- 调用 REST API 服务，例如验证用户输入、丰富用户数据或与业务线应用程序集成。
- [SAML2](saml-technical-profile.md) - 与任何 SAML 协议标识提供者联合。
- [SAML 令牌颁发者](saml-issuer-technical-profile.md)- 发出返回依赖方应用程序的 SAML 令牌。
- [自断言](self-asserted-technical-profile.md) - 与用户交互。 例如，收集用户的凭据进行登录、呈现注册页或密码重置。
- [会话管理](custom-policy-reference-sso.md) - 处理不同类型的会话。

## <a name="technical-profile-flow"></a>技术配置文件流

所有类型的技术配置文件都具有相同的概念。 你发送输入声明、运行声明转换以及与已配置的参与方（例如标识提供者、REST API 或 Azure AD 目录服务）进行通信。 该过程完成后，技术配置文件返回输出声明，并可能运行输出声明转换。 下图显示了如何处理技术配置文件中引用的转换和映射。 无论技术配置文件与哪个参与方交互，在执行任意声明转换后，技术配置文件的输出声明都会立即存储在声明包中。

![说明技术配置文件流的示意图](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. **单一登录 （SSO） 会话管理**- 使用[SSO 会话管理](custom-policy-reference-sso.md)还原技术配置文件的会话状态。
1. **输入声明转换**- 从索赔包中选取每个输入[声明转换](claimstransformations.md)的输入声明。  输入声明转换的输出声明可以是后续输入声明转换的输入声明。
1. **输入索赔**- 索赔从索赔包中选取，用于技术配置文件。 例如，[自断言技术配置文件](self-asserted-technical-profile.md)使用输入声明来预填充用户提供的输出声明。 REST API 技术配置文件使用输入声明将输入参数发送到 REST API 终结点。 Azure Active Directory 使用输入声明作为读取、更新或删除帐户的唯一标识符。
1. 技术配置文件执行**** - 技术配置文件与已配置的参与方交换声明。 例如：
    - 将用户重定向到标识提供者以完成登录。 成功登录后，用户返回并继续执行技术配置文件。
    - 在将参数作为 InputClaims 发送并将信息作为 OutputClaims 返回时调用 REST API。
    - 创建或更新用户帐户。
    - 发送并验证 MFA 文本信息。
1. **验证技术配置文件**-[自断言技术配置文件](self-asserted-technical-profile.md)可以调用[验证技术配置文件](validation-technical-profile.md)。 验证技术配置文件可验证用户分析的数据并返回错误消息或正常信息，包含或不包含输出声明。 例如，在 Azure AD B2C 创建新帐户之前，它会检查用户是否已存在于目录服务中。 你可以调用 REST API 技术配置文件来添加自己的业务逻辑。<p>验证技术配置文件的输出声明的范围仅限于调用验证技术配置文件的技术配置文件。 和同一技术配置文件下的其他验证技术配置文件。 如果要在下一个业务流程步骤中使用输出声明，则需要将输出声明添加到调用验证技术配置文件的技术配置文件中。
1. **输出声明**- 索赔返回索赔袋。 可以在下一个业务流程步骤或输出声明转换中使用这些声明。
1. **输出声明转换**- 从声明包中选取每个输出[声明转换](claimstransformations.md)的输入声明。 先前步骤中的技术配置文件的输出声明可以是输出声明转换的输入声明。 执行后，输出声明将被放回到声明包中。 输出声明转换的输出声明也可以是后续输出声明转换的输入声明。
1. **单一登录 （SSO） 会话管理**- 使用[SSO 会话管理](custom-policy-reference-sso.md)将技术配置文件的数据保存到会话中。


## <a name="technical-profile-inclusion"></a>技术配置文件包含

技术配置文件可以包括另一个技术配置文件来更改设置或添加新功能。  该`IncludeTechnicalProfile`元素是对从中派生技术配置文件的基本技术配置文件的引用。 级别数没有限制。

例如，**** AAD-UserReadUsingAlternativeSecurityId-NoError 技术配置文件包括 AAD-UserReadUsingAlternativeSecurityId****。 此技术配置文件将`RaiseErrorIfClaimsPrincipalDoesNotExist`元数据项设置为`true`，如果目录中不存在社交帐户，则引发错误。 **AAD-UserRead使用替代安全Id-NoError**重写此行为，并禁用该错误消息。

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**** AAD-UserReadUsingAlternativeSecurityId 包括 `AAD-Common` 技术配置文件。

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

**AAD-UserRead 使用替代安全Id-NoError**和**AAD-UserReadUsing 替代安全Id**不指定所需的**协议**元素，因为它在**AAD 通用**技术配置文件中指定。

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```
