---
title: 分配变量
description: 有关开发解决方案时在 Azure SQL 数据仓库中分配 T-SQL 变量的技巧。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0adcd9bdf92b7ec649b7d91ca0e655fc006b3549
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351661"
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>在 Azure SQL 数据仓库中分配变量

有关开发解决方案时在 Azure SQL 数据仓库中分配 T-SQL 变量的技巧。

## <a name="setting-variables-with-declare"></a>使用 DECLARE 设置变量

SQL 数据仓库中的变量是使用 `DECLARE` 语句或 `SET` 语句设置的。 使用 DECLARE 初始化变量是在 SQL 数据仓库中设置变量值的最灵活方式之一。

```sql
DECLARE @v  int = 0
;
```

还可以使用 DECLARE 一次性设置多个变量。 不能使用 SELECT 或 UPDATE 执行以下操作：

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

不能在同一 DECLARE 语句中初始化和使用某个变量。 为了演示要点，**不**允许出现以下示例中的情况，因为同一 DECLARE 语句中初始化和使用了 @p1。 下面的示例会出错。

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>使用 SET 设置值

SET 是设置单个变量的常见方法。

以下语句是使用 SET 设置变量的有效方法：

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

一次只能使用 SET 设置一个变量。 但是可使用复合运算符。

## <a name="limitations"></a>限制

不能使用 UPDATE 来分配变量。

## <a name="next-steps"></a>后续步骤

有关更多开发技巧，请参阅[开发概述](sql-data-warehouse-overview-develop.md)。
