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
   ms.date="03/23/2016"
   ms.author="mausher;jrj;barbkess;sonyama"/>

# SQL Data Warehouse の一時テーブル
特に、中間結果が一時的なものである変換中にデータを処理する場合に、一時テーブルが非常に役立ちます。SQL Data Warehouse では、一時テーブルはセッション レベルで存在します。一時テーブルはローカル一時テーブルとして定義されますが、SQL Server テーブルとは異なり、セッション内のどこからでもアクセスできます。

この記事では、セッション レベルの一時テーブルの原則を中心に、一時テーブルの基本的な利用方法について説明します。ここの情報を利用することで、コードをモジュール化できます。コードのモジュール機能は、保守管理とコードの再利用を簡単にするために重要です。

## 一時テーブルを作成する
一時テーブルは非常に簡単に作成できます。必要な作業は、下の例のように、テーブル名の先頭に # を付けることだけです。

```sql
CREATE TABLE #stats_ddl
(
	[schema_name]			NVARCHAR(128) NOT NULL
,	[table_name]            NVARCHAR(128) NOT NULL
,	[stats_name]            NVARCHAR(128) NOT NULL
,	[stats_is_filtered]     BIT           NOT NULL
,	[seq_nmbr]              BIGINT        NOT NULL
,	[two_part_name]         NVARCHAR(260) NOT NULL
,	[three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
,	HEAP
)
```

一時テーブルで `CTAS` を利用して作成することもできます。手法はまったく同じになります。

```sql
CREATE TABLE #stats_ddl
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
,	HEAP
)
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

>[AZURE.NOTE] `CTAS` は非常に強力なコマンドであり、トランザクション ログ領域を非常に効率的に利用するという長所があります。


## 一時テーブルを削除する

`CREATE TABLE` ステートメントを正常に機能させるには、セッションにテーブルが存在しないことが重要です。これは、下のパターンを利用し、簡単に既存チェックで処理できます。

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
	DROP TABLE #stats_ddl
END
```

> [AZURE.NOTE] コードの一貫性のために、テーブルと一時テーブルの両方にこのパターンを利用することが推奨されます。

また、コードで一時テーブルの利用を終えたら、`DROP TABLE` でそれを削除することはよい方法です。

```sql
DROP TABLE #stats_ddl
```

ストアド プロシージャの開発では、一般的に、オブジェクトが消去されるように、プロシージャの終わりに削除コマンドがバンドルされています。

## コードのモジュール化

一時テーブルはユーザー セッションのどこでも表示できるという点を生かして、アプリケーション コードをモジュール化できます。

実際の使用例を見てみましょう。

次のストアド プロシージャは、上記の例をまとめたものです。コードを利用し、データベース内のすべての列の統計を更新するために必要な DDL を生成できます。

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
	,@sample_pct     tinyint
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

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
	DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
)
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
GO
```

この段階では、いかなるアクションもテーブルに発生していません。このプロシージャは、統計を更新するために必要な DDL を生成し、そのコードを一時テーブルに保管しただけです。

ただし、ストアド プロシージャの終わりに `DROP TABLE` コマンドが含まれていないことにも注意してください。ただし、コードを堅牢かつ繰り返し可能にする目的で、ストアド プロシージャに既存チェックを追加しました。セッションに重複オブジェクトが存在しても、`CTAS` は失敗しません。

次は、実に興味深い部分に入ります。

SQL Data Warehouse では、一時テーブルを作成したプロシージャの外部でそのテーブルを使用できます。この点が SQL Server とは異なります。実際には、一時テーブルはセッション内の**どこでも**使用できます。

これにより、コードのモジュール性が高まり、コードを管理しやすくなります。次の例をご覧ください。

```sql
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

結果的に生成されるコードはさらにコンパクトになります。

場合によっては、この手法を使ってインライン関数や複数ステートメント関数を置き換えることもできます。

> [AZURE.NOTE] このソリューションを拡張することもできます。1 つのテーブルだけを更新する場合は、たとえば、#stats\_ddl テーブルをフィルター処理するだけで済みます。

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

<!---HONumber=AcomDC_0330_2016------>