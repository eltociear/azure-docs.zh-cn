---
title: 创建&管理授权管理中的资源目录 - Azure AD
description: 了解如何在 Azure 活动目录授权管理中创建新的资源和访问包容器。
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffa6f2fbdcde41d81e268779c3a0586bd8310792
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437557"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>在 Azure AD 授权管理中创建和管理资源目录

## <a name="create-a-catalog"></a>创建目录

目录是资源和访问包的容器。 需要将相关的资源和访问包分组时，可以创建目录。 创建目录的任何人将成为第一个目录所有者。 目录所有者可以添加其他目录所有者。

**先决条件角色：** 全局管理员、用户管理员或目录创建者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。********

1. 在左侧菜单中，单击“目录”****。

    ![Azure 门户中的权利管理目录](./media/entitlement-management-catalog-create/catalogs.png)

1. 单击“新建目录”。****

1. 输入目录的唯一名称，并提供说明。

    用户将在访问包的详细信息中看到此信息。

1. 如果你希望在创建此目录中的访问包后，用户可立即请求这些访问包，请将“启用”设置为“是”。********

1. 如果希望允许选定外部目录中的用户请求此目录中的访问包，请将“为外部用户启用”设置为“是”。********

    ![“新建目录”窗格](./media/entitlement-management-shared/new-catalog.png)

1. 单击“创建”以创建该目录。****

### <a name="creating-a-catalog-programmatically"></a>以编程方式创建目录

您还可以使用 Microsoft 图形创建目录。  具有委派`EntitlementManagement.ReadWrite.All`权限的应用程序具有适当角色的用户可以调用 API[以创建访问包目录](https://docs.microsoft.com/graph/api/accesspackagecatalog-post?view=graph-rest-beta)。

## <a name="add-resources-to-a-catalog"></a>将资源添加到目录

若要在访问包中包含资源，这些资源必须存在于目录中。 可添加的资源类型包括组、应用程序和 SharePoint Online 站点。 这些组可以是云创建的 Office 365 组或云创建的 Azure AD 安全组。 应用程序可以是 Azure AD 企业应用程序，包括 SaaS 应用程序，以及你自己的已联合到 Azure AD 的应用程序。 站点可以是 SharePoint Online 站点或 SharePoint Online 站点集合。

**先决条件角色：** 请参阅[将资源添加到目录所需的角色](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。********

1. 在左侧菜单中单击“目录”，然后打开要将资源添加到的目录。****

1. 在左侧菜单中单击“资源”。****

1. 单击“添加资源”。****

1. 单击资源类型：**组和团队**、**应用程序**或**SharePoint 网站**。

    如果未看到所要添加的资源或无法添加资源，请确保具有所需的 Azure AD 目录角色和权利管理角色。 可能需要求助某个具有所需角色的人员将资源添加到目录。 有关详细信息，请参阅[将资源添加到目录所需的角色](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)。

1. 选择一个或多个要添加到目录中的所需类型的资源。

    ![将资源添加到目录](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. 完成后，单击“添加”。****

    现在，可将这些资源包含在目录中的访问包内。

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>以编程方式将资源添加到目录

您还可以使用 Microsoft 图形将资源添加到目录。  具有委派`EntitlementManagement.ReadWrite.All`权限的应用程序具有相应角色的用户或目录和资源所有者可以调用 API[来创建访问包资源请求](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta)。

## <a name="remove-resources-from-a-catalog"></a>从目录中删除资源

可以从目录中删除资源。 如果资源未在目录的任何访问包中使用时，才能从该目录中删除该资源。

**先决条件角色：** 请参阅[将资源添加到目录所需的角色](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。********

1. 在左侧菜单中单击“目录”，然后打开要从中删除资源的目录。****

1. 在左侧菜单中单击“资源”。****

1. 选择要删除的资源。

1. 单击“删除”（或单击省略号 (**...**)，然后单击“删除资源”）。********

## <a name="add-additional-catalog-owners"></a>添加其他目录所有者

创建目录的用户将成为第一个目录所有者。 要委派目录的管理，可以将用户添加到目录所有者角色。 这有助于共享目录管理职责。 

按照以下步骤将用户分配给目录所有者角色：

**先决条件角色：** 全局管理员、用户管理员或目录所有者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。********

1. 在左侧菜单中单击“目录”，然后打开要将管理员添加到的目录。****

1. 在左侧菜单中，单击“角色和管理员”。****

    ![编目角色和管理员](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. 单击"**添加所有者**"以选择这些角色的成员。

1. 单击“选择”以添加这些成员。****

## <a name="edit-a-catalog"></a>编辑目录

可以编辑目录的名称和说明。 用户将在访问包的详细信息中看到此信息。

**先决条件角色：** 全局管理员、用户管理员或目录所有者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。********

1. 在左侧菜单中单击“目录”，然后打开要编辑的目录。****

1. 在该目录的“概述”页上单击“编辑”。********

1. 编辑目录的名称、说明或启用的设置。

    ![编辑目录设置](./media/entitlement-management-shared/catalog-edit.png)

1. 单击“保存”  。

## <a name="delete-a-catalog"></a>删除目录

可以删除目录，但前提是它不包含任何访问包。

**先决条件角色：** 全局管理员、用户管理员或目录所有者

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。********

1. 在左侧菜单中单击“目录”，然后打开要删除的目录。****

1. 在该目录的“概述”中单击“删除”。********

1. 在出现的消息框中单击“是”****。

### <a name="deleting-a-catalog-programmatically"></a>以编程方式删除目录

您还可以使用 Microsoft 图形删除目录。  具有委派`EntitlementManagement.ReadWrite.All`权限的应用程序具有适当角色的用户可以调用 API[以删除访问包目录](https://docs.microsoft.com/graph/api/accesspackagecatalog-delete?view=graph-rest-beta)。

## <a name="next-steps"></a>后续步骤

- [将访问治理委派给访问包管理器](entitlement-management-delegate-managers.md)
