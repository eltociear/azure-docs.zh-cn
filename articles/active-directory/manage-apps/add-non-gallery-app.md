---
title: 添加非库应用程序 - 微软身份平台 |微软文档
description: 将非库应用程序添加到 Azure AD 租户。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd5a5f100dbe09c3b82f58183a118ee3bf455f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063605"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>将未列出的（非库）应用程序添加到 Azure AD 组织

除了[Azure AD 应用程序库中](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)的选项之外，您还可以选择添加**非库应用程序**。 可以添加组织中已存在的任何应用程序，或者从尚未加入 Azure AD 库的供应商处添加任何第三方应用程序。 根据您的[许可协议](https://azure.microsoft.com/pricing/details/active-directory/)，以下功能可用：

- 支持[安全断言标记语言 （SAML） 2.0](https://wikipedia.org/wiki/SAML_2.0)标识提供程序（SP 启动或 IdP 启动）的任何应用程序的自助服务集成
- 通过自助方式集成包含 HTML 登录页并使用[基于密码的 SSO](what-is-single-sign-on.md#password-based-sso) 的任何 Web 应用程序
- 使用[跨域身份管理系统 （SCIM） 协议进行用户预配](../app-provisioning/use-scim-to-provision-users-and-groups.md)的应用程序的自助服务连接
- 可在 [Office 365 应用启动器](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/)或 [Azure AD 访问面板](what-is-single-sign-on.md#linked-sign-on)中添加任何应用程序的链接

本文介绍如何在不编写代码的情况下将非库应用程序添加到 Azure 门户中的**企业应用程序**。 相反，如果您正在寻找有关如何将自定义应用与 Azure AD 集成的开发人员指南，请参阅[Azure AD 的身份验证方案](../develop/authentication-scenarios.md)。 开发使用[OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md)等现代协议对用户进行身份验证的应用时，可以使用 Azure 门户中的[应用注册](../develop/quickstart-register-app.md)体验将其注册到 Microsoft 标识平台。

## <a name="add-a-non-gallery-application"></a>添加非库应用程序

1. 使用 Microsoft 标识平台管理员帐户登录到[Azure 活动目录门户](https://aad.portal.azure.com/)。

2. 选择**企业应用程序** > **新应用程序**。

3. （可选但推荐）在 **"浏览 Azure AD 库**"搜索框中，输入应用程序的显示名称。 

4. 选择 **"创建自己的应用程序**"。 将显示"**创建您自己的应用程序**"页。

   ![添加应用程序](media/add-non-gallery-app/create-your-own-application.png)

5. 开始键入新应用程序的显示名称。 如果有任何具有类似名称的库应用程序，它们将显示在搜索结果列表中。

   > [!NOTE]
   > 我们建议尽可能使用应用程序的库版本。 如果要添加的应用程序将显示在搜索结果中，请选择该应用程序并跳过此过程的其余部分。

6. 在"**您希望如何处理您的应用程序**"下？ 选择 **"集成任何其他您在库中找不到的应用程序**"。 此选项通常用于 SAML 和 WS-Fed 应用程序。

   > [!NOTE]
   > 其他两个选项用于以下方案：
   >* **配置应用程序代理以安全远程访问本地应用程序**将打开 Azure AD 应用程序代理和连接器的配置页。
   >* **注册正在使用与 Azure AD 集成的应用程序**将打开**应用注册**页。 此选项通常用于 OpenID 连接应用程序。

7. 选择 **“创建”**。 此时会打开应用程序的“概述”**** 页。

## <a name="configure-user-sign-in-properties"></a>配置用户登录属性

1. 选择“属性”，以打开属性页进行编辑。****

    ![“编辑属性”窗格](media/add-non-gallery-app/edit-properties.png)

2. 设置以下选项，以确定已分配或未分配给应用程序的用户如何登录应用程序，以及用户是否可以在访问面板中看到该应用程序。

    - **启用用户登录**可确定分配给应用程序的用户可以登录。
    - **所需的用户分配**确定未分配给应用程序的用户可以登录。
    - “对用户可见”决定了分配给应用的用户能否在访问面板和 O365 启动器中看到它。****

      **已分配**用户的行为：

       | 应用程序属性设置 | | | 已分配用户的体验 | |
       |---|---|---|---|---|
       | 启用以供用户登录? | 需要进行用户分配? | 对用户可见? | 已分配用户能否登录? | 已分配用户能否看到应用程序?* |
       | 是 | 是 | 是 | 是 | 是  |
       | 是 | 是 | 否  | 是 | 否   |
       | 是 | 否  | 是 | 是 | 是  |
       | 是 | 否  | 否  | 是 | 否   |
       | 否  | 是 | 是 | 否  | 否   |
       | 否  | 是 | 否  | 否  | 否   |
       | 否  | 否  | 是 | 否  | 否   |
       | 否  | 否  | 否  | 否  | 否   |

      **未分配**用户的行为：

       | 应用程序属性设置 | | | 未分配用户的体验 | |
       |---|---|---|---|---|
       | 启用以供用户登录? | 需要进行用户分配? | 对用户可见? | 未分配用户能否登录? | 未分配用户能否看到应用程序?* |
       | 是 | 是 | 是 | 否  | 否   |
       | 是 | 是 | 否  | 否  | 否   |
       | 是 | 否  | 是 | 是 | 否   |
       | 是 | 否  | 否  | 是 | 否   |
       | 否  | 是 | 是 | 否  | 否   |
       | 否  | 是 | 否  | 否  | 否   |
       | 否  | 否  | 是 | 否  | 否   |
       | 否  | 否  | 否  | 否  | 否   |

     *用户能否在访问面板和 Office 365 应用启动器中看到应用程序?

3. 若要使用自定义徽标，请创建 215 x 215 像素的徽标，并将其保存为 PNG 格式。 然后浏览到你的徽标并将其上传。

    ![更改徽标](media/add-non-gallery-app/change-logo.png)

4. 完成后，选择“保存”****。

## <a name="next-steps"></a>后续步骤

现在你已将应用程序添加到 Azure AD 组织，请[选择要使用的单一登录方法](what-is-single-sign-on.md#choosing-a-single-sign-on-method)，并参考下面的相应文章：

- [配置基于 SAML 的单一登录](configure-single-sign-on-non-gallery-applications.md)
- [配置密码单一登录](configure-password-single-sign-on-non-gallery-applications.md)
- [配置链接登录](configure-linked-sign-on.md)
