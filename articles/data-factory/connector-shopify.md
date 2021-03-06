---
title: 从商店复制数据（预览）
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 Shopify 复制到支持的接收器数据存储。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: ec9c8e29adaa03620cc60d189ff4a830e107e782
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931052"
---
# <a name="copy-data-from-shopify-using-azure-data-factory-preview"></a>使用 Azure 数据工厂从 Shopify 复制数据（预览）

本文概述了如何使用 Azure 数据工厂中的复制活动从 Shopify 复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

> [!IMPORTANT]
> 此连接器目前提供预览版。 欢迎试用并提供反馈。 若要在解决方案中使用预览版连接器的依赖项，请联系 [Azure 客户支持](https://azure.microsoft.com/support/)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Shopify 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [查找活动](control-flow-lookup-activity.md)

可以将数据从 Shopify 复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

Azure 数据工厂提供内置的驱动程序用于启用连接，因此无需使用此连接器手动安装任何驱动程序。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Shopify 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Shopify 链接服务支持以下属性：

| properties | 描述 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**Shopify** | 是 |
| host | Shopify 服务器的终结点。 （即，mystore.myshopify.com）  | 是 |
| accessToken | 可用于访问 Shopify 的数据的 API 访问令牌。 如果处于脱机模式，该令牌不会过期。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| useEncryptedEndpoints | 指定是否使用 HTTPS 加密数据源终结点。 默认值为 true。  | 否 |
| useHostVerification | 指定通过 SSL 连接时是否需要服务器证书中的主机名匹配服务器的主机名。 默认值为 true。  | 否 |
| usePeerVerification | 指定通过 SSL 连接时是否要验证服务器的标识。 默认值为 true。  | 否 |

**例子：**

```json
{
    "name": "ShopifyLinkedService",
    "properties": {
        "type": "Shopify",
        "typeProperties": {
            "host" : "mystore.myshopify.com",
            "accessToken": {
                 "type": "SecureString",
                 "value": "<accessToken>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的节和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Shopify 数据集支持的属性列表。

要从 Shopify 复制数据，请将数据集的 type 属性设置为“ShopifyObject”****。 支持以下属性：

| properties | 描述 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的类型属性必须设置为：**商店对象** | 是 |
| tableName | 表的名称。 | 否（如果指定了活动源中的“query”） |

**示例**

```json
{
    "name": "ShopifyDataset",
    "properties": {
        "type": "ShopifyObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Shopify linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Shopify 数据源支持的属性列表。

### <a name="shopify-as-source"></a>Shopify 作为源

要从 Shopify 复制数据，请将复制活动中的源类型设置为“ShopifySource”****。 复制活动**源**部分支持以下属性：

| properties | 描述 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：ShopifySource**** | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如：`"SELECT * FROM "Products" WHERE Product_Id = '123'"`。 | 否（如果指定了数据集中的“tableName”） |

**例子：**

```json
"activities":[
    {
        "name": "CopyFromShopify",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Shopify input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ShopifySource",
                "query": "SELECT * FROM \"Products\" WHERE Product_Id = '123'"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Lookup 活动属性

若要了解有关属性的详细信息，请查看 [Lookup 活动](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动作为源和接收器支持的数据存储的列表，请参阅[受支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
