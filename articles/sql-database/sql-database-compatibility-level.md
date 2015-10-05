<properties 
	pageTitle="SQL Database の互換性レベル | Microsoft Azure" 
	description="Azure SQL Database データベースに互換性レベルを設定する方法と、それによって影響を受ける機能について説明します。"
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/21/2015" 
	ms.author="genemi"/>


# SQL Database の互換性レベル


このトピックでは、Azure SQL Database で Transact-SQL ステートメントの **ALTER DATABASE** を使用して実行できる機能の選択について説明します。互換性レベルの概念は、あらゆるアップグレード リスクを減らすために設計されています。


互換性レベルによってアクティブ化/非アクティブ化が決定される機能のほとんどは、クエリ オプティマイザーが備える機能です。新しいクエリ オプティマイザー機能のアクティブ化は、次の条件に該当する場合に、互換性レベルに設定されます。


- コンテキストがアップグレードされたことにより、以前の機能のセマンティクスが変更された。
- クエリの最適化に伴い、一般的ではないが有効な SQL クエリのパフォーマンスが損なわれる可能性があると考えられる。


Azure SQL Database サーバーまたはデータベースに対するバージョン アップグレード後にクエリのパフォーマンスが低下する場合は、互換性レベルを下げることができます。設定を下げると、一部のオプティマイザー拡張機能セットを非アクティブ化でき、それにより、使用するクエリに悪影響を与えている機能も非アクティブ化できる可能性があります。


## 互換性レベルのしくみ


Microsoft SQL Server では、長年にわたって、設定可能な互換性レベルを提供してきました。Azure SQL Database でも、バージョン V12 から互換性レベルが採用されます。いくつかの新しい機能のアクティブ化は、各 SQL Database データベース内の互換性レベルの設定によって制御されます。使用できる設定は、次のとおりです。


- 110: 使用可能な最も下位の設定。そのため、ほとんどの新しい機能は除外されています。
- 120: SQL Database V11 と弱い関連性があります (`SELECT @@version;` では **11.**0.0.0 が返されます)。
 - V11 バージョンは、"SAWA V2" の SQL Database とも呼ばれていました。
 - この 120 は、Microsoft SQL Server 2014 では最も高い値です。
- 130: V12 と弱い関連性があります (`SELECT @@version;` では **12.**0.0.0 が返されます)。
 - この 130 は、Microsoft SQL Server 2016 では最も高い値です。


たとえば、120 に設定した場合、データベースは、レベル 120 でアクティブ化される機能のサブセットに*加えて*、110 などの低い設定でアクティブになるすべての機能にアクセスできます。120 に設定すると、データベースはレベル 130 の機能にはアクセスできません。


## 互換性レベルの読み取りまたは設定


### 互換性レベルの読み取り


```
SELECT d.name, d.compatibility_level
	FROM sys.databases AS d
	WHERE d.name = db_name();
```


### 互換性レベルの設定


```
ALTER DATABASE YourDatabaseName SET COMPATIBILITY_LEVEL = 130;
```


詳細については、次を参照してください。


- [ALTER TABLE 互換性レベル (Transact-SQL)](http://msdn.microsoft.com/library/bb510680.aspx)
- [sys.databases (Transact-SQL)](http://msdn.microsoft.com/library/ms178534.aspx)


## インメモリ テーブルと列ストア インデックス


レベル 120 とレベル 130 でアクティブになる互換性レベル機能の大半は、*インメモリ*のテーブルとインデックスに関連しています。そのため、重要なアイテムとして次のようなものがあります。


- メモリ最適化テーブル
- 列ストア インデックス


インメモリ アイテムが関係している場合、互換性レベル 130 には次のような利点があります。


- クエリ オプティマイザーがより多くのクエリを*バッチ* モードで処理できる機能がアクティブ化されます。
 - 対象の行数が非常に多い場合、バッチ モードは*行*モードよりも処理が高速になります。
- **SORT** 演算子が追加されます。
- ウィンドウ集計が追加されます。


インメモリ テーブルおよび列ストア インデックスの詳細については、以下を参照してください。


- [メモリ最適化テーブル](http://msdn.microsoft.com/library/dn133165.aspx)
- [CREATE CLUSTERED COLUMNSTORE INDEX (Transact-SQL)](http://msdn.microsoft.com/library/dn511016.aspx)
- [列ストア インデックスの説明](http://msdn.microsoft.com/library/gg492088.aspx)


## 130 以上のレベルを必要とする一般的な機能


基数推定機能 (CE) では、テーブル内の行の概数に関する統計情報を生成して格納します。クエリ オプティマイザーの多くの部分で、基数情報が使用されます。


CE で使用されるアルゴリズムに改良が加えられました。改良には変更が伴い、その変更によって、めったにな特殊な事例が発生する可能性があります。


| 130 が<br/>必要<br/>最小限のレベル | クエリ領域、<br/>全般 | <br/>クエリ プラン<br/>の改良の詳細 |
| :-- | :-- | :-- |
| 130 | 基数推定機能 (CE) | 基数推定機能 (CE) に改良を加え、以前のレベル 120 の CE から向上しました。<br/><br/>クエリ プランには、次のように表示されます: **CardinalityEstimationModelVersion="130"**<br/><br/>**トレース フラグ** [**9481**](http://www.sqlservergeeks.com/sql-server-2014-trace-flags-9481/) をオンにして、データベースがレベル 130 の場合でもレベル 120 の CE を使用できます。<br/><br/>**トレース フラグ** [**4199**](http://support.microsoft.com/kb/974006) をオフにして、データベースがレベル 130 のときにクエリ オプティマイザーに対する修正プログラムを無効にできます。このフラグは、レベル 130 のプレビューが完全に終了し、一般提供 (GA) としてリリースされた後に実装された修正プログラムにのみ適用されます。詳細については、以下を参照してください。<br/><br/>● [DBCC TRACEON](http://msdn.microsoft.com/library/ms187329.aspx) |
| 130 | インメモリ テーブルの並列クエリ プラン | クエリでは複数のスレッドを使用でき、インメモリ テーブルに対するクエリは並列で実行できます。このインメモリ テーブルとは、**MEMORY\_OPTIMIZED = YES** 句を使用して作成されたテーブルです。並列処理により、クエリの実行が高速になります。<br/><br/>この拡張機能は、標準の Transact-SQL とユーザー ストアド プロシージャでサポートされています。ただし、DLL にコンパイルされるネイティブ ストアド プロシージャではサポートされていません。 |


## 130 以上のレベルを必要とする列ストア インデックスの機能


互換性レベル 130 では、クエリ オプティマイザーの拡張機能によってクエリ プランが新しくなる場合があります。列ストア インデックスに関連するプランが変更される場合、その変更には通常、次のいずれかが伴います。


- 今後のサブ操作のためにデータをコピーする必要がある状況が減少します。
- 並べ替えなどの操作が*列*処理から***バッチ***処理に変更されます。


ほとんどの場合、プランの変更では、次の内容を実行して、クエリのパフォーマンスを向上します。


- 列ストア インデックスに対する並列挿入。
 - レベル 130 では、非クラスター化インデックスの並列スキャンは提供されません。
- **tempdb** データベースの使用率の増加


| 130 が<br/>必要<br/>最小限のレベル | クエリ領域、<br/>列ストア インデックス | <br/>クエリ プラン<br/>の改良の詳細 |
| :-- | :-- | :-- |
| 130 | 関数クエリ | バッチ モードへの切り替えにより、次のような場合のパフォーマンスが向上します。<br/><br/>• 並べ替えが関連している。<br/><br/>• *複数の*異なる関数を使用して集計する。<br/>(次の一覧の 2 つの箇条書き項目からそれぞれ 1 つずつ):<br/>&nbsp;&nbsp;&nbsp;▫ **COUNT** *または* **COUNT\_BIG**<br/>&nbsp;&nbsp;&nbsp;▫ **AVG** *または* **SUM**<br/>&nbsp;&nbsp;&nbsp;▫ **CHECKSUM\_AGG**<br/>&nbsp;&nbsp;&nbsp;▫ **STDEV** *または* **STDEVP**<br/><br/>• ウィンドウ集計関数<br/>(詳細は[こちらの MSDN 記事](http://msdn.microsoft.com/library/ms189461.aspx)および [Kathi Kellenberger によるこちらの記事](http://www.bidn.com/blogs/KathiKellenberger/sql-server/4397/what-is-a-window-aggregate-function)を参照):<br/>&nbsp;&nbsp;&nbsp;▫ **COUNT**、**COUNT\_BIG**、**SUM**、**AVG**、**MIN**、**MAX**、**CLR**<br/><br/>• ウィンドウ [ユーザー定義](http://msdn.microsoft.com/library/ms131057.aspx)集計:<br/>&nbsp;&nbsp;&nbsp;▫ [**CHECKSUM\_AGG**](http://msdn.microsoft.com/library/ms188920.aspx)、[**STDEV**](http://msdn.microsoft.com/library/ms190474.aspx)、[**STDEVP**](http://msdn.microsoft.com/library/ms176080.aspx)、[**VAR**](http://msdn.microsoft.com/library/ms186290.aspx)、[**VARP**](http://msdn.microsoft.com/library/ms188735.aspx)、[**GROUPING**](http://msdn.microsoft.com/library/ms178544.aspx)<br/><br/>• ウィンドウ集計分析関数:<br/>&nbsp;&nbsp;&nbsp;▫ [**LAG**](http://msdn.microsoft.com/library/hh231256.aspx)、[**LEAD**](http://msdn.microsoft.com/library/hh213125.aspx)、[**FIRST\_VALUE**](http://msdn.microsoft.com/library/hh213018.aspx)、[**LAST\_VALUE**](http://msdn.microsoft.com/library/hh231517.aspx)、[**PERCENTILE\_CONT**](http://msdn.microsoft.com/library/hh231473.aspx)、[**PERCENTILE\_DISC**](http://msdn.microsoft.com/library/hh231327.aspx)、[**CUME\_DIST**](http://msdn.microsoft.com/library/hh231078.aspx)、[**PERCENT\_RANK**](http://msdn.microsoft.com/library/hh213573.aspx) |
| 130 | シングル スレッドの直列クエリ プラン | 単一のスレッドで処理されるクエリをバッチ モードで実行できます。これにより、クエリの実行が高速になります。<br/><br/>クエリ プランをシングル スレッドとして設計することも、クエリを **MAXDOP 1** で実行することもできます。 |
| 130 | 並列挿入 | クエリ プランで複数の挿入を並列実行できます。<br/<br/>このトピックの後半にある[例](#ExampleQueryParallelCciByCompatLevel)は、この並列処理を示しています。 |
| 130 | anti-semi join | この演算子をバッチ モードで実行できるようになりました。 |


## 120 以上のレベルを必要とする一般的な機能


| 120 が<br/>必要<br/>最小限のレベル | クエリ領域、<br/>全般 | <br/>クエリ プラン<br/>の改良の詳細 |
| :-- | :-- | :-- |
| 120 | [メモリ最適化テーブルの変更](http://msdn.microsoft.com/library/dn269114.aspx) | **MEMORY\_OPTIMIZED = YES** が指定されているテーブルに対して Transact-SQL の **ALTER TABLE** を実行できます。<br/><br/>データベース アプリケーションは引き続き実行できますが、テーブルへのアクセスを伴う操作は **ALTER TABLE** が完了するまでブロックされます。 |
| 120 | [メモリ最適化オブジェクト用ストレージの作成と管理](http://msdn.microsoft.com/library/dn133174.aspx) | インメモリ OLTP エンジンは、SQL Server に統合されています。そのため、**メモリ最適化**テーブルと従来のディスク ベース テーブルの両方を同じデータベースに格納できます。 |
| 120 | [Transact-SQL によるインメモリ OLTP のサポート](http://msdn.microsoft.com/library/dn133180.aspx) | いくつかの Transact-SQL コマンドが強化され、インメモリ オンライン トランザクション処理 (OLTP) をサポートするようになりました。<br/><br/>一例として、[CREATE PROCEDURE](http://msdn.microsoft.com/library/ms187926.aspx) コマンドに新たに **NATIVE\_COMPILATION** キーワードが追加されました。 |
| 120 | 基数推定機能 (CE) | 基数推定機能 (CE) に改良を加え、以前のレベル 110 の CE から向上しました。<br/><br/>クエリ プランには、次のように表示されます: **CardinalityEstimationModelVersion="120"**<br/><br/>**トレース フラグ 4199** が互換性レベルの値を処理する方法の詳細については、[KB 974006](http://support.microsoft.com/kb/974006) を参照してください。|


## 120 以上のレベルを必要とする列ストア インデックスの機能


このセクションでは、120 以上の互換性レベルでアクティブ化される[列ストア インデックスの機能](http://msdn.microsoft.com/library/dn934994.aspx)について説明します。


| 120 が最小<br/>互換性<br/>レベル | 列ストア インデックス<br/>機能の名前 | 列ストア インデックス<br/>機能の説明 |
| :-- | :-- | :-- |
| 120 | スナップショット分離、<br/><br/>Read Committed スナップショット分離 (RCSI) レベル | クエリ プランに列ストア インデックスが含まれている場合、SI と RCSI を使用すると、部分的に完了したトランザクションのデータがクエリの結果に含まれなくなり、過剰なロックが不要になります。 |
| 120 | インデックスの最適化 | 行を削除する際、インデックスの明示的な再構築を伴わずに削除されます。<br/><br/>**ALTER INDEX ...REORGANIZE** を使用すると、削除対象の行が列ストア インデックスから削除され、その間、テーブルとインデックスは引き続きオンラインで動作します。 |
| 120 | AlwaysOn の [読み取り可能なセカンダリ レプリカ](http://msdn.microsoft.com/library/ff878253.aspx)からアクセス可能 | 分析クエリを AlwaysOn セカンダリ レプリカにオフロードすることで、運用分析のパフォーマンスを向上できます。 |
| 120 | プッシュ ダウンの集計、<br/>集計関数のテーブル スキャン フェーズ中 | クエリ プランの早い段階で中間計算を実行することでパフォーマンスを向上し、後のフェーズでコピーが必要となるデータ量を削減します。<br/><br/>**MIN**、**MAX**、**SUM**、**COUNT**、**AVG** に適用されます。<br/><br/>データ型が 8 バイト以下で、文字列でない場合にのみ適用されます。 |
| 120 | 文字列ベースの **WHERE** 句のプッシュ ダウン | 述語のプッシュ ダウンを最適化することで、文字列データ型 &#x5b;var&#x5d;char または n&#x5b;var&#x5d;char を比較するクエリを高速化できます。この最適化には、次の特徴があります。<br/><br/>• ビットマップ フィルターを使用する **LIKE** が含まれた一般的な比較演算子に適用されます。<br/><br/>• 文字列の述語が 1 つの場合にのみ機能します。<br/><br/>• 製品でサポートされているすべての照合順序に対応します。<br/><br/>ビットマップ フィルターの詳細については、[クエリ実行ビットマップ フィルターに関する概要](http://blogs.msdn.com/b/sqlqueryprocessing/archive/2006/10/27/query-execution-bitmaps.aspx)が記載されたブログ投稿を参照してください。 |



<a id="ExampleQueryParallelCciByCompatLevel" name="ExampleQueryParallelCciByCompatLevel"></a>


&nbsp;


## 互換性レベルによるクエリ プランの変更点の例


このセクションでは、Transact-SQL **INSERT...SELECT** ステートメントを取り上げ、互換性レベル 120 と 130 におけるクエリ プランの変更点について説明します。


#### ソース テーブル スキーマ


次のテーブルには、少なくとも 300,000 行が含まれています。高度なクエリ プランでは、データが少なすぎて並列処理のメリットが得られない場合、並列処理が実行されないことがあります。


```
CREATE TABLE [dbo].[ccitestoriginal](
	[FinanceKey] [int] NOT NULL,
	[DateKey] [int] NOT NULL,
	[OrganizationKey] [int] NOT NULL,
	[DepartmentGroupKey] [int] NOT NULL,
	[ScenarioKey] [int] NOT NULL,
	[AccountKey] [int] NOT NULL,
	[Amount] [float] NOT NULL,
	[Date] [datetime] NULL
) ON [PRIMARY];
GO

CREATE CLUSTERED COLUMNSTORE INDEX [cci_ccitestoriginal]
	ON [dbo].[ccitestoriginal]
	WITH (DROP_EXISTING = OFF) ON [PRIMARY];
GO
```


#### クエリ プランを生成するスクリプト


以下の Transact-SQL スクリプトに対して、次の手順を実行します。


1. **Ssms.exe** でデータベースに接続します。
2. Transact-SQL スクリプトを **Ssms.exe** クエリ ウィンドウに貼り付けます。
3. スクリプトを編集し、**ALTER DATABASE** ステートメントの値を **120** にします。
4. スクリプトの **INSERT INTO** ステートメントの*前まで*の部分のみを実行します。
5. メニュー オプションをアクティブ化します。**[クエリ]**、**[実際の実行プランを含める] (Ctrl + M)** を順に選択します。
6. **INSERT INTO** ステートメントのみを実行します。<br/><br/>
7. メニュー オプションを非アクティブ化します。**[クエリ]**、**[実際の実行プランを含める] (Ctrl + M)** を順に選択します。
8. 最後に、スクリプトの **INSERT INTO** ステートメント*以降*の部分のみを実行します。
9. **[結果]** タブの近くにある **[実行プラン]** タブの **120** のクエリ プランに注目してください。<br/>-- -- -- -- -- --
10. スクリプトを編集し、**ALTER DATABASE** の値を **130** にします。
11. 上記の手順で説明したとおりに、スクリプトをもう一度実行します。
12. **130** のクエリ プランが変わり、並列処理が含まれていることに注目してください。


&nbsp;


```
go
SET NOCOUNT ON;
go

ALTER DATABASE YourDatabaseName SET COMPATIBILITY_LEVEL =
	120
	--130
;
go

DROP TABLE ccitest_into_work;
go

SELECT *
	INTO ccitest_into_work  -- Create an empty copy of the table.
	FROM ccitestoriginal
	WHERE 1=2;
go

CREATE CLUSTERED COLUMNSTORE INDEX ccitest_into_work_cci
	ON ccitest_into_work;
go

SET NOCOUNT OFF;
go
	
	-- Run this INSERT statement alone!
	--
	-- First run all the preceding Transact-SQL statements.
	-- Then run this one INSERT statement alone.
	-- Then run all remaining Transact-SQL statements.

INSERT INTO ccitest_into_work WITH (TABLOCK)
	SELECT TOP 300000 *
		FROM ccitestoriginal;
go

SET NOCOUNT ON;
go

DROP TABLE ccitest_into_work;
go
```


#### 2 つのプランの表示



<br/>**120:** 以下は、互換性レベルが **120** の場合のクエリ プランです。


![Plan at level 120][1-Plan-at-level-120]


<br/>**130:** 以下は、互換性レベルが **130** の場合のクエリ プランです。


**130** のプランには*並列処理*が含まれているのに対し、**120** のプランには含まれていません。


このプランの表示は、**Ssms.exe**では幅がもっと広くなっています。ここでは、表示を見やすくするために、スクリーンショットを 2 つに分割しています。実際には、2 枚目が 1 枚目の横に続いています。


![Plan at level 130][2-Plan-at-level-130]


## データベースの既定の互換性レベル


バージョン V11 から V12 へのように Azure SQL Database サーバーをアップグレードしても、各データベースの互換性レベルは変更されません。アップグレード後に、**ALTER DATABASE** ステートメントを使用して、任意のデータベースの互換性レベルを上げることができます。


新規作成したデータベースはすべて、その SQL Database バージョンで許可されている最高の互換性レベルが設定されます。



<!-- Image references. -->

[1-Plan-at-level-120]: ./media/sql-database-compatibility-level/sql-db-compat-level-query-plan-b-120.png

[2-Plan-at-level-130]: ./media/sql-database-compatibility-level/sql-db-compat-level-query-plan-b12-130.png

<!---HONumber=Sept15_HO4-->