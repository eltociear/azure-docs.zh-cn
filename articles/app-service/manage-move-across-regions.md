---
title: 将应用移动到其他区域
description: 了解如何将应用服务资源从一个区域移动到另一个区域。
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 7e68f12ce062831ad361c88345188aca61922c4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925704"
---
# <a name="move-an-app-service-app-to-another-region"></a>将应用服务应用移动到其他区域

本文介绍如何将应用服务资源移动到其他 Azure 区域。 出于多种原因，您可以将资源移动到另一个区域。 例如，利用新的 Azure 区域，部署仅在特定区域可用的功能或服务，以满足内部策略和治理要求，或响应容量规划要求。

应用服务资源特定于区域，不能跨区域移动。 您必须在目标区域中创建现有应用服务资源的副本，将内容移动到新应用。 如果源应用使用自定义域，则可以在完成自定义域后[将其迁移到目标区域中的新应用](manage-custom-dns-migrate-domain.md)。

为了简化应用的复制，您可以将[单个应用服务应用克隆](app-service-web-app-cloning.md)到另一个区域中的应用服务计划中，但它确实有[限制](app-service-web-app-cloning.md#current-restrictions)，尤其是它不支持 Linux 应用。

## <a name="prerequisites"></a>先决条件

- 确保应用服务应用位于要从其移动的 Azure 区域中。
- 确保目标区域支持应用服务和任何相关服务，您希望移动其资源。
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>准备

标识当前使用的所有应用服务资源。 例如：

- 应用服务应用
- [应用服务计划](overview-hosting-plans.md)
- [部署插槽](deploy-staging-slots.md)
- [在 Azure 中购买的自定义域](manage-custom-dns-buy-domain.md)
- [SSL 证书数](configure-ssl-certificate.md)
- [Azure 虚拟网络集成](web-sites-integrate-with-vnet.md)
- [混合连接](app-service-hybrid-connections.md)。
- [托管标识](overview-managed-identity.md)
- [备份设置](manage-backup.md)

某些资源（如导入的证书或混合连接）包含与其他 Azure 服务的集成。 有关如何跨区域移动这些资源的信息，请参阅相应服务的文档。

## <a name="move"></a>移动

1. [创建源应用的备份](manage-backup.md)。
1. [在目标区域中创建新的应用服务计划中](app-service-plan-manage.md#create-an-app-service-plan)的应用。
2. [在目标应用中还原备份](web-sites-restore.md)
2. 如果使用自定义域，[则先先将其绑定到目标应用](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively)，`awverify.`并在[目标应用中启用域](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app)。
3. 将目标应用中的其他所有内容配置为与源应用相同，并验证您的配置。
4. 当您准备好自定义域指向目标应用时，[重新映射域名](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name)。

<!-- 1. Login to the [Azure portal](https://portal.azure.com) > **Resource Groups**.
2. Locate the Resource Group that contains the source App Service resources and click on it.
3. Select > **Settings** > **Export template**.
4. Choose **Deploy** in the **Export template** blade.
5. Click **TEMPLATE** > **Edit template** to open the template in the online editor.
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"identity": {` to find any managed identity definition. The following is an example if you have a user-assigned managed identity.
    ```json
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>": {
                "principalId": "00000000-0000-0000-0000-000000000000",
                "clientId": "00000000-0000-0000-0000-000000000000"
            }
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/sites/hostNameBindings` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/sites/hostNameBindings",
        "apiVersion": "2018-11-01",
        "name": "[concat(parameters('sites_webapp_name'), '/', parameters('sites_webapp_name'), '.azurewebsites.net')]",
        "location": "West Europe",
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('sites_webapp_name'))]"
        ],
        "properties": {
            "siteName": "<app-name>",
            "hostNameType": "Verified"
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/certificates` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/certificates",
        "apiVersion": "2018-11-01",
        "name": "[parameters('certificates_test2_cephaslin_com_name')]",
        "location": "West Europe",
        "properties": {
            "hostNames": [
                "[parameters('certificates_test2_cephaslin_com_name')]"
            ],
            "password": "[parameters('certificates_test2_cephaslin_com_password')]"
        }
    },
    ```
7. Delete the entire JSON block. Click **Save** in the online editor.
8. Click **BASICS** > **Create new** to create a new resource group. Type the group name and click **OK**.
9. In **BASICS** > **Location**, select the region you want.   -->

## <a name="clean-up-source-resources"></a>清理源资源

删除源应用和应用服务计划。 [非免费层中的应用服务计划会收取费用，即使其中没有应用运行。](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>后续步骤

[使用 PowerShell 克隆 Azure 应用服务应用](app-service-web-app-cloning.md)