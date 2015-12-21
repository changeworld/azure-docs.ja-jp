<properties
   pageTitle="SQL Data Warehouse への SQL コードの移行 | Microsoft Azure"
   description="ソリューション開発のための Azure SQL Data Warehouse への SQL コードの移行に関するヒント"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="12/09/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SQL Data Warehouse への SQL コードの移行

コードが SQL Data Warehouse と必ず準拠するようにするには、多くの場合、コード ベースに変更を加える必要があります。一部の SQL Data Warehouse 機能は分散環境で直接機能するように設計されているため、大幅にパフォーマンスを向上できます。ただし、パフォーマンスと拡張性を維持するには、一部の機能が使用できなくなる場合もあります。

## Transact-SQL コードの変更点

Azure SQL Data Warehouse でサポートされていない主な機能を次の表に示します。リンクをクリックすると、サポートされていない機能に対する解決策が表示されます。

- [更新での ANSI の JOIN][]
- [削除での ANSI の JOIN][]
- [MERGE ステートメント][]
- 複数データベースの JOIN
- [カーソル][]
- [SELECT..INTO][]
- [INSERT..EXEC][]
- OUTPUT 句
- インライン ユーザー定義関数
- 複数ステートメント関数
- [共通テーブル式](#Common-table-expressions)
- [再帰共通テーブル式 (CTE)](#Recursive-common-table-expressions-(CTE)
- CLR 関数およびプロシージャ
- $partition 関数
- テーブル変数
- テーブル値パラメーター
- 分散トランザクション
- コミット/ロールバック処理
- トランザクションの保存
- 実行コンテキスト (EXECUTE AS)
- [rollup / cube / grouping セット オプションによる句ごとのグループ化][]
- [8 を超えるの入れ子のレベル][]
- [ビューを使用した更新][]
- [変数代入のための SELECT の使用][]
- [動的 SQL 文字列の MAX 以外のデータ型][]

幸運なことに、これらの制限の大部分は回避できます。上記の関連する開発記事に説明が記載されています。

### 共通テーブル式
SQL Data Warehouse 内の共通テーブル式 (CTE) の現在の実装には、次の機能と制限事項があります。

**CTE 機能** CTE は SELECT ステートメントで指定できます。CTE は CREATE VIEW ステートメントで指定できます。CTE は CREATE TABLE AS SELECT (CTAS) ステートメントで指定できます。CTE は CREATE REMOTE TABLE AS SELECT (CRTAS) ステートメントで指定できます。CTE は CREATE EXTERNAL TABLE AS SELECT (CETAS) ステートメントで指定できます。リモート テーブルは CTE から参照できます。外部テーブルは CTE から参照できます。複数の CTE クエリ定義を CTE に定義できます。

**CTE の制限事項** CTE の後ろには単一の SELECT ステートメントを続ける必要があります。INSERT、UPDATE、DELETE、および MERGE のステートメントはサポートされていません。それ自体への参照を含む共通テーブル式 (再帰共通テーブル式) はサポートされていません (下のセクションを参照)。複数の WITH 句を CTE に指定することはできません。たとえば、CTE\_query\_definition にサブクエリが含まれている場合、そのサブクエリには別の CTE を定義する入れ子になった WITH 句を含めることはできません。ORDER BY 句は、TOP 句が指定されている場合を除き、CTE\_query\_definition で使用することはできません。バッチの一部であるステートメントで CTE を使用すると、それより前のステートメントでは後ろにセミコロンが必要です。CTE が sp\_prepare で準備されているステートメントで使用されると、PDW の他の SELECT ステートメントと同じように動作します。ただし、CTE が sp\_prepare で準備される CETAS の一部として使用される場合、バインドを sp\_prepare に対して実装する方法によって、動作が SQL Server および他の PDW ステートメントとは異なる場合があります。CTE を参照する SELECT が CTE に存在しない間違った列を使用している場合、sp\_prepare はエラーを検出せずに渡されますが、代わりに sp\_execute でエラーがスローされます。

### 再帰共通テーブル式 (CTE)

これは複雑な移行シナリオで、CTE を分解したり、順番に処理したりする場合に最適です。通常、再帰的な中間クエリの反復処理時に、ループを使用したり、一時テーブルに値を取り込んだりできます。一時テーブルに値が取り込まれたら、単一の結果セットとしてデータを戻すことができます。[group by 句と rollup / cube / grouping sets オプション][]に関する記事でも `GROUP BY WITH CUBE` の解決に同様のアプローチを採用しています。

### システム関数

また、サポートされていないシステム関数もいくつかあります。データ ウェアハウジングで一般的に使用されている主なものを次に示します。

- NEWID()
- NEWSEQUENTIALID()
- @@NESTLEVEL()
- @@IDENTITY()
- @@ROWCOUNT()
- ROWCOUNT\_BIG
- ERROR\_LINE()

これらの問題の大部分も回避できます。

たとえば、次のコードは、@@ROWCOUNT 情報を取得するための代替ソリューションです。

```
SELECT  SUM(row_count) AS row_count 
FROM    sys.dm_pdw_sql_requests 
WHERE   row_count <> -1 
AND     request_id IN 
                    (   SELECT TOP 1    request_id 
                        FROM            sys.dm_pdw_exec_requests 
                        WHERE           session_id = SESSION_ID() 
                        ORDER BY end_time DESC
                    )
;
``` 

## 次のステップ
コード開発について詳しくは、[開発の概要][]に関するページを参照してください。

<!--Image references-->

<!--Article references-->
[更新での ANSI の JOIN]: sql-data-warehouse-develop-ctas.md
[削除での ANSI の JOIN]: sql-data-warehouse-develop-ctas.md
[MERGE ステートメント]: sql-data-warehouse-develop-ctas.md
[INSERT..EXEC]: sql-data-warehouse-develop-temporary-tables.md

[カーソル]: sql-data-warehouse-develop-loops.md
[SELECT..INTO]: sql-data-warehouse-develop-ctas.md
[group by 句と rollup / cube / grouping sets オプション]: sql-data-warehouse-develop-group-by-options.md
[rollup / cube / grouping セット オプションによる句ごとのグループ化]: sql-data-warehouse-develop-group-by-options.md
[8 を超えるの入れ子のレベル]: sql-data-warehouse-develop-transactions.md
[ビューを使用した更新]: sql-data-warehouse-develop-views.md
[変数代入のための SELECT の使用]: sql-data-warehouse-develop-variable-assignment.md
[動的 SQL 文字列の MAX 以外のデータ型]: sql-data-warehouse-develop-dynamic-sql.md
[開発の概要]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_1210_2015-->