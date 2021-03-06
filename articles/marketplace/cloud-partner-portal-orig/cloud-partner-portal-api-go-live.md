---
title: 去现场 |Azure 应用商店
description: 上线 API 启动产品/服务上线一览过程。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: bf7bebf6e72e373811879a311d70255c29988ed6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288574"
---
<a name="go-live"></a>上线
=======

此 API 启动将应用投入生产的过程。 这通常是一项长期操作。 此调用使用[发布](./cloud-partner-portal-api-publish-offer.md) API 操作返回的通知电子邮件列表。

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>URI 参数
--------------

|  **名称**      |   **说明**                                                           | **数据类型** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | 要检索的产品/服务的发布者标识符，例如 `contoso`       |  String       |
| offerId        | 要检索的产品/服务的产品/服务标识符                                   |  String       |
| api-version    | API 最新版本                                                   |  Date         |
|  |  |  |


<a name="header"></a>标头
------

|  **名称**       |     **价值**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| 授权   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>正文示例
------------

### <a name="response"></a>响应

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>响应标头

|  **名称**             |      **价值**                                                            |
|  --------             |      ----------                                                           |
| Operation-Location    |  要查询的 URL，用于确定操作的当前状态            |
|  |  |


### <a name="response-status-codes"></a>响应状态代码

| **代码** |  **说明**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` - 已成功接受请求。 响应包含用于跟踪操作状态的位置。 |
|  400     | `Bad/Malformed request` - 响应正文中的其他错误信息。 |
|  404     |  `Not found` - 指定的实体不存在。                                       |
|  |  |
