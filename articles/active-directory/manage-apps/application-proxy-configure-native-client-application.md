---
title: 发布本机客户端应用 - Azure AD | Microsoft Docs
description: 介绍如何使本机客户端应用与 Azure AD 应用程序代理连接器通信，以提供本地应用的安全远程访问。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d2a2bb9dd543da7455a276075a829ef06032edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159278"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>如何启用本机客户端应用程序与代理应用程序交互

可以使用 Azure 活动目录 （Azure AD） 应用程序代理发布 Web 应用，但它也可用于发布使用 Azure AD 身份验证库 （ADAL） 配置的本机客户端应用程序。 本机客户端应用程序不同于 Web 应用，因为前者安装在设备上，而后者需通过浏览器进行访问。

为了支持本机客户端应用程序，应用程序代理接受在标头中发送的 Azure AD 颁发的令牌。 应用程序代理服务对用户执行身份验证。 此解决方案不使用应用程序令牌进行身份验证。

![最终用户、Azure AD 和已发布应用程序之间的关系](./media/application-proxy-configure-native-client-application/richclientflow.png)

要发布本机应用程序，请使用 Azure AD 身份验证库，该库负责身份验证并支持许多客户端环境。 应用程序代理适合[本机应用程序到 Web API 方案](../azuread-dev/native-app.md)。

本文引导读者完成使用应用程序代理和 Azure AD 身份验证库发布本机应用程序的四个步骤。

## <a name="step-1-publish-your-proxy-application"></a>第 1 步：发布代理应用程序

像发布任何其他应用程序一样发布代理应用程序，并分配可访问该应用程序的用户。 有关详细信息，请参阅[使用应用程序代理发布应用程序](application-proxy-add-on-premises-application.md)。

## <a name="step-2-register-your-native-application"></a>第 2 步：注册本机应用程序

现在需要在 Azure AD 中注册应用程序，如下所示：

1. 登录到 Azure[活动目录门户](https://aad.portal.azure.com/)。 将显示 Azure**活动目录管理中心**的**仪表板**。
1. 在边栏中，选择**Azure 活动目录**。 将显示**Azure 活动目录**概览页。
1. 在 Azure AD 概述边栏中，选择**应用注册**。 将显示所有应用注册的列表。
1. 选择“新注册”。**** 将显示 **"注册应用程序**"页。

   ![在 Azure 门户中创建新的应用注册](./media/application-proxy-configure-native-client-application/create.png)

1. 在 **"名称"** 标题中，为应用程序指定面向用户的显示名称。
1. 在 **"支持帐户类型"** 标题下，使用以下准则选择访问级别：

   - 要仅针对组织内部的帐户，请选择 **"仅在此组织目录中的帐户**"。
   - 要仅针对业务或教育客户，请选择**任何组织目录中的帐户**。
   - 要定位最广泛的 Microsoft 标识集，请选择**任何组织目录中的帐户和个人 Microsoft 帐户**。

1. 在**重定向 URI**标题中，选择**公共客户端（移动&桌面），** 然后键入应用程序的重定向 URI。
1. 选择并阅读**Microsoft 平台策略**，然后选择 **"注册**"。 创建并显示新应用程序注册的概览页。

有关创建新应用程序注册的详细信息，请参阅[将应用程序与 Azure 活动目录集成](../develop/quickstart-register-app.md)。

## <a name="step-3-grant-access-to-your-proxy-application"></a>第 3 步：授予对代理应用程序的访问权限

现在，您已经注册了本机应用程序，您可以授予它访问目录中的其他应用程序（本例中为访问代理应用程序） 要使本机应用程序能够公开给代理应用程序，请执行以下情况：

1. 在新应用程序注册页的侧边栏中，选择**API 权限**。 将显示新应用程序注册的**API 权限**页。
1. 选择“添加权限”****。 将显示 **"请求 API 权限**"页。
1. 在 **"选择 API"** 设置下，选择**组织使用的 API。** 将显示一个列表，其中包含目录中公开 API 的应用程序。
1. 在搜索框中键入或滚动以查找在步骤 1 中发布的代理应用程序[：发布代理应用程序](#step-1-publish-your-proxy-application)，然后选择代理应用程序。
1. 在"**应用程序需要哪些类型的权限"** 标题中，选择权限类型。 如果本机应用程序需要作为登录用户访问代理应用程序 API，请选择**委派权限**。
1. 在 **"选择权限"** 标题中，选择所需的权限，然后选择 **"添加权限**"。 本机应用程序的**API 权限**页现在显示您添加的代理应用程序和权限 API。

## <a name="step-4-edit-the-active-directory-authentication-library"></a>步骤 4：编辑 Active Directory 身份验证库

在 Active Directory 身份验证库 (ADAL) 的身份验证上下文中编辑本机应用程序代码，以包含以下文本：

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = await authContext.AcquireTokenAsync("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

示例代码中所需的信息可在 Azure AD 门户中找到，如下所示：

| 所需信息 | 如何在 Azure AD 门户中找到它 |
| --- | --- |
| \<租户 ID> | **Azure 活动目录** > **属性** > **目录 ID** |
| \<代理应用程序>的外部 Url | **企业应用程序** > *您的代理* > **应用程序应用程序代理** > **外部 Url** |
| \<本机应用的应用 id> | **企业应用程序** > *您的本机应用程序* > **属性** > **应用程序 ID** |
| \<重定向本机应用>的 URI | **Azure 活动目录** > **应用注册** > *本机应用程序* > **重定向 URI** |
| \<代理应用 API Url> | **Azure 活动目录** > **应用注册** > *本机应用程序* > **API 权限** > **API /权限名称** |

使用这些参数编辑 ADAL 后，即使本地客户端应用程序处于公司网络之外，也可以对本机客户端应用程序进行身份验证。

## <a name="next-steps"></a>后续步骤

有关本机应用程序流的详细信息，请参阅 Azure[活动目录中的本机应用](../azuread-dev/native-app.md)。

了解如何在[Azure 活动目录中设置应用程序的单一登录](what-is-single-sign-on.md#choosing-a-single-sign-on-method)。
