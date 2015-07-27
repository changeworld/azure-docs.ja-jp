<properties
   pageTitle="SQL Data Warehouse の一時テーブル | Microsoft Azure"
   description="ソリューション開発のための、Azure SQL Data Warehouse での一時テーブルの使用に関するヒント。"
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
   ms.date="06/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SQL Data Warehouse の一時テーブル
SQL Data Warehouse では、一時テーブルはセッション レベルで存在します。一時テーブルはローカル一時テーブルとして定義されます。SQL Server テーブルとは異なり、セッション内のどこからでもアクセスできます。

特に、中間結果が一時的なものである変換中にデータを処理する場合に、一時テーブルが非常に役立ちます。

この記事では、一時テーブルを利用してコードをモジュール化するための具体的な方法について説明します。

## コードのモジュール化

一時テーブルはユーザー セッションのどこでも表示できるという点を生かして、アプリケーション コードをモジュール化できます。

実際の使用例を見てみましょう。

次のストアド プロシージャは、データベース内のすべての列の統計を更新するために必要な DDL を生成します。

```
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
,   @sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION = HASH([seq_nmbr])
        ,   LOCATION     = USER_DB
        )
WITH t1
AS
(
SELECT
		sm.[name]				                                                AS [schema_name]
,		tb.[name]				                                                AS [table_name]
,		st.[name]				                                                AS [stats_name]
,		st.[has_filter]			                                                AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,								 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,		QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM	sys.objects			AS ob
JOIN	sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
JOIN	sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND st.[object_id]		= sc.[object_id]
JOIN	sys.columns			AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
JOIN	sys.tables			AS tb	ON	co.[object_id]		= tb.[object_id]
JOIN	sys.schemas			AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE	1=1
AND		st.[user_created]   = 1
GROUP BY
		sm.[name]
,		tb.[name]
,		st.[name]
,		st.[filter_definition]
,		st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
```

実際には、このデータを使用して何も実行されていないことに注意してください。このプロシージャは DDL を生成し、一時テーブルに保存しただけです。

ただし、SQL Data Warehouse では、一時テーブルを作成したプロシージャの外部でそのテーブルを使用できます。この点が SQL Server とは異なります。実際には、一時テーブルはセッション内の**どこでも**使用できます。

これにより、コードのモジュール性が高まり、コードを管理しやすくなります。

```
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

場合によっては、この手法を使ってインライン関数や複数ステートメント関数を置き換えることもできます。

> [AZURE.NOTE]このソリューションを拡張することもできます。1 つのテーブルだけを更新する場合は、たとえば、#stats_ddl テーブルをフィルター処理するだけで済みます。

## 一時テーブルの制限事項
SQL Data Warehouse では、一時テーブルを実装するときに制限事項がいくつかあります。

主な制限事項は次のとおりです。

- グローバル一時テーブルはサポートされていません。
- 一時テーブルでビューを作成することはできません。


## 次のステップ
開発のその他のヒントについては、[開発の概要][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[開発の概要]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO3-->