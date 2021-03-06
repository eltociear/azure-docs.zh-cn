---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 03/02/2018
ms.openlocfilehash: 1aabc1b33722c571c6519b6620fb62211a44c2e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789767"
---
1. 在 [Azure 门户](https://portal.azure.com)中，创建一个空白逻辑应用。 

2. 在逻辑应用设计器中，输入“github”作为筛选器。 

3. 选择 GitHub 连接器和要使用的触发器。

   ![选择 GitHub 连接器和一个触发器](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > 所有逻辑应用工作流都必须以触发器开头。 仅当逻辑工作流已以触发器开头时，才可以选择操作。 

4. 如果以前未创建连接，请选择“登录”**** 以便在出现提示时提供 GitHub 凭据。  

   ![使用 GitHub 凭据登录](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   逻辑应用使用这些凭据授权连接和访问 GitHub 帐户的数据。 

5. 提供 GitHub 用户名和密码，然后确认授权。

   ![提供凭据并确认授权](./media/connectors-create-api-github/github-connector-authorize.png)   

   现已在 Azure 门户中创建连接，并且该连接可供使用。

6. 继续定义逻辑应用工作流。

   ![将更多操作添加到逻辑应用工作流](./media/connectors-create-api-github/github-connector-logic-app.png)

