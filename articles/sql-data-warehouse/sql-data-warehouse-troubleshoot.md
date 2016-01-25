<properties
   pageTitle="トラブルシューティング |Microsoft Azure"
   description="SQL Data Warehouse のトラブルシューティングを行います。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="mausher;sonyama;barbkess"/>

# トラブルシューティング
次のトピックでは、Azure SQL Data Warehouse で発生するいくつかの一般的な問題を示します。

## 接続
Azure SQL Data Warehouse への接続は、次の 2 つの一般的な原因によって失敗する可能性があります。

- ファイアウォール ルールが設定されていない
- サポートされていないツール/プロトコルを使用している

### ファイアウォール ルール
Azure SQL Database は、既知の IP アドレスのみがデータベースにアクセスできるように、サーバーとデータベース レベルのファイアウォールによって保護されています。ファイアウォールは、既定でセキュリティ保護されています。つまり、接続する前に、IP アドレスにアクセスを許可する必要があります。

アクセスするためにファイアウォールを構成するには、[プロビジョニング](sql-data-warehouse-get-started-provision.md) ページの[クライアント IP 用にサーバーのファイアウォール アクセスを構成する](sql-data-warehouse-get-started-provision.md/#step-4-configure-server-firewall-access-for-your-client-ip)に関するセクションの手順に従ってください。

### サポートされていないツール/プロトコルを使用している
SQL Data Warehouse は、開発環境として [Visual Studio 2013/2015](sql-data-warehouse-get-started-connect.md) を、クライアント接続用に [SQL Server Native Client 10/11 (ODBC)](https://msdn.microsoft.com/library/ms131415.aspx) をサポートします。

詳細については、[接続](sql-data-warehouse-get-started-connect.md)に関するページを参照してください。

## クエリのパフォーマンス
SQL Data Warehouse では、統計を含むクエリの実行で SQL Server の一般的なコンストラクトを使用します。[統計](sql-data-warehouse-develop-statistics.md)は、データベースの列値の範囲と頻度に関する情報を含むオブジェクトです。クエリ エンジンは、これらの統計情報を使用してクエリの実行を最適化し、クエリのパフォーマンスを向上させます。次のクエリを使用して、統計情報が更新された最後の時刻を確認できます。

```
SELECT
	sm.[name]								    AS [schema_name],
	tb.[name]								    AS [table_name],
	co.[name]									AS [stats_column_name],
	st.[name]									AS [stats_name],
	STATS_DATE(st.[object_id],st.[stats_id])	AS [stats_last_updated_date]
FROM
	sys.objects				AS ob
	JOIN sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
	JOIN sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND	st.[object_id]		= sc.[object_id]
	JOIN sys.columns		AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
	JOIN sys.types           AS ty	ON	co.[user_type_id]	= ty.[user_type_id]
	JOIN sys.tables          AS tb	ON	co.[object_id]		= tb.[object_id]
	JOIN sys.schemas         AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE
	1=1 
	AND st.[user_created] = 1;
```

詳細については、[統計](sql-data-warehouse-develop-statistics.md)に関するページを参照してください。

## 主要なパフォーマンスの概念

その他の主要なパフォーマンスとスケールの概念を理解するために、次の記事を参照してください。

- [パフォーマンスとスケール][]
- [同時実行モデル][]
- [テーブルの設計][]
- [テーブルのハッシュ分散キーの選択][]
- [パフォーマンスを向上させる統計][]

## 次のステップ
SQL Data Warehouse ソリューションの作成に関するガイダンスについては、[開発の概要][]に関する記事を参照してください。

<!--Image references-->

<!--Article references-->

[パフォーマンスとスケール]: sql-data-warehouse-performance-scale.md
[同時実行モデル]: sql-data-warehouse-develop-concurrency.md
[テーブルの設計]: sql-data-warehouse-develop-table-design.md
[テーブルのハッシュ分散キーの選択]: sql-data-warehouse-develop-hash-distribution-key
[パフォーマンスを向上させる統計]: sql-data-warehouse-develop-statistics.md
[開発の概要]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other web references-->

<!---HONumber=AcomDC_0114_2016-->