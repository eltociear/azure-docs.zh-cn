---
title: 按选项分组
description: 在 Synapse SQL 池中按选项实现组的技巧。
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
ms.openlocfilehash: 28ac075d043f7605b6dfdac6879063fbe9308123
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619048"
---
# <a name="group-by-options-in-synapse-sql-pool"></a>按 Synapse SQL 池中的选项分组

在本文中，您将在 SQL 池中找到按选项实现组的提示。

## <a name="what-does-group-by-do"></a>GROUP BY 的作用是什么？

[GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql) T-SQL 子句用于将数据聚合成摘要行集。 GROUP BY 具有 SQL 池不支持的一些选项。 这些选项具有解决方法，如下所示：

* 带 ROLLUP 的 GROUP BY
* GROUPING SETS
* 带 CUBE 的 GROUP BY

## <a name="rollup-and-grouping-sets-options"></a>Rollup 和 grouping sets 选项

此处最简单的选项是使用 UNION ALL 执行汇总，而不是依赖显式语法。 结果完全相同。

下面的示例使用了具有 ROLLUP 选项的 GROUP BY 语句：
```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

通过使用 ROLLUP，前面的示例请求以下聚合：

* 国家/地区和区域
* 国家/地区
* 总计

若要替换 ROLLUP 并返回相同的结果，可以使用 UNION ALL 并显式指定所需的聚合：

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

若要替换 GROUPING SETS，示例原则也适用。 只需要为希望查看的聚合级别创建 UNION ALL 部分。

## <a name="cube-options"></a>Cube 选项
可以使用"全联盟"方法创建具有 CUBE 的组。 问题在于，代码可能很快就会变得庞大且失控。 要缓解此问题，可以使用此更高级的方法。

使用前面的示例，第一步是定义"多维数据集"，该"多维数据集"定义我们想要创建的所有聚合级别。 

请注意两个派生表的 CROSS JOIN，因为这为我们生成所有级别。 代码的其余部分用于格式化：

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

下图显示了 CTAS 的结果：

![按多维数据集分组](./media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

第二步是指定目标表用于存储临时结果：

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

第三步是是循环访问执行聚合的列 cube。 查询将针对临时表中的每一行运行一次#Cube。 结果存储在#Results临时表中：

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

最后，您可以通过从#Results临时表中读取来返回结果：

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

通过将代码分解为多个部分并生成循环构造，代码变得更加可管理和可维护。

## <a name="next-steps"></a>后续步骤
有关更多开发技巧，请参阅[开发概述](sql-data-warehouse-overview-develop.md)。

