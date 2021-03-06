---
title: 使用 Azure 数据工厂从 IBM Informix 源复制数据
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 IBM Informix 源复制到支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: cb4b81f7c5e219c520078ecf34eba3f5e98da684
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75892577"
---
# <a name="copy-data-from-and-to-ibm-informix-data-stores-using-azure-data-factory"></a>使用 Azure 数据工厂从/向 IBM Informix 数据存储复制数据

本文概述了如何在 Azure 数据工厂中使用复制活动从 IBM Informix 数据存储复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 Informix 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [查找活动](control-flow-lookup-activity.md)

可将数据从 Informix 源复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

## <a name="prerequisites"></a>先决条件

若要使用此 Informix 连接器，需要：

- 设置自承载集成运行时。 有关详细信息[，请参阅自托管集成运行时](create-self-hosted-integration-runtime.md)文章。
- 在集成运行时计算机上安装数据存储的 Informix ODBC 驱动程序。 例如，可以使用驱动程序“IBM INFORMIX Informix 驱动程序（64 位）”。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 Informix 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Informix 链接服务支持以下属性：

| properties | 描述 | 必选 |
|:--- |:--- |:--- |
| type | 类型属性必须设置为 **：Informix** | 是 |
| connectionString | 不包括凭据部分的 ODBC 连接字符串。 可以指定连接字符串，也可以利用在 Integration Runtime 计算机上设置的系统 DSN（数据源名称）（仍需要相应地指定链接服务中的凭据部分）。 <br> 还可以将密码放在 Azure 密钥保管库中，并从连接字符串中拉取  `password`  配置。 有关详细信息，请参阅 [在 Azure 密钥保管库中](store-credentials-in-key-vault.md) 存储凭据。| 是 |
| authenticationType | 用于连接 Informix 数据存储的身份验证类型。<br/>允许的值是：Basic**** 和 Anonymous****。 | 是 |
| userName | 如果使用基本身份验证，请指定用户名。 | 否 |
| password | 指定为 userName 指定的用户帐户的密码。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 否 |
| credential | 连接字符串的访问凭据部分，采用特定于驱动程序的属性值格式指定。 将此字段标记为 SecureString。 | 否 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如[先决条件](#prerequisites)中所述，需要自承载集成运行时。 |是 |

**例子：**

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Informix",
        "typeProperties": {
            "connectionString": "<Informix connection string or DSN>",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的节和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 Informix 数据集支持的属性列表。

从 Informix 复制数据时，支持以下属性：

| properties | 描述 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的类型属性必须设置为 **：InformixTable** | 是 |
| tableName | Informix 中表的名称。 | 源为否（如果指定了活动源中的“query”）；<br/>接收器为是 |

**示例**

```json
{
    "name": "InformixDataset",
    "properties": {
        "type": "InformixTable",
        "linkedServiceName": {
            "referenceName": "<Informix linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Informix 源支持的属性列表。

### <a name="informix-as-source"></a>Informix 作为源

从 Informix 复制数据时，复制活动的 **source** 节支持以下属性：

| properties | 描述 | 必选 |
|:--- |:--- |:--- |
| type | 必须将复制活动源的类型属性设置为 **：InformixSource** | 是 |
| query | 使用自定义查询读取数据。 例如：`"SELECT * FROM MyTable"`。 | 否（如果指定了数据集中的“tableName”） |

**例子：**

```json
"activities":[
    {
        "name": "CopyFromInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Informix input dataset name>",
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
                "type": "InformixSource",
                "query": "SELECT * FROM MyTable"
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
