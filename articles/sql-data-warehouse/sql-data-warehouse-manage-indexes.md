<properties
   pageTitle="インデックスの管理 | Microsoft Azure"
   description="インデックスの管理に役立つガイダンス"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/18/2016"
   ms.author="jrj;barbkess;sonyama"/>

# インデックスの管理
この記事では、インデックスを管理する際の重要な手法について説明します。

## インデックスの再構築の最適化
2 つの方法でインデックスの再構築を最適化できます。

1. テーブルのパーティションを作成し、パーティション レベルでインデックスを再構築する
2. [CTAS][] を新しいテーブルのデータのパーティションの再作成と、新しテーブルのパーティション スイッチに使用する

## パーティション分割されたインデックスの再構築

以下は 1 つのパーティションを再構築する方法の例です。

```
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

ALTER INDEX..REBUILD は、小規模のデータ ボリュームに、特に列ストア インデックスに対して使用すると効果的です。オープン、クローズド、圧縮の行グループがすべて再構築に含まれます。ただし、パーティションが非常に大きければ、`CTAS` は効果的な操作となります。インデックスの完全再構築の例は下のようになります。

```
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

この構文に関する詳細については、[ALTER INDEX][] に関する記事を参照してください。

## CTAS を使用してパーティションを再構築する

以下は CTAS を使用してパーティションを再構築する方法の例です。

```
-- Step 01. Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 02. Create a SWITCH out table
 
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 03. Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 04. Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;

```

## 次のステップ
パーティションを作成し、サイズを調整する方法については、[テーブルのパーティション分割][]に関する記事を参照してください。この記事には、パーティションの境界の特定に役立つサンプルも含まれています。

管理に関するその他のヒントについては、[管理][]概要を参照してください。

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[テーブルのパーティション分割]: sql-data-warehouse-develop-table-partitions.md
[管理]: sql-data-warehouse-manage-monitor.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/ja-JP/library/ms188388.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0323_2016-->