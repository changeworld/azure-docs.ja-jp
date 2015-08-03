<properties
   pageTitle="SQL Data Warehouse への SQL コードの移行 | Microsoft Azure"
   description="ソリューション開発のための Azure SQL Data Warehouse への SQL コードの移行に関するヒント"
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
   ms.date="06/25/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SQL Data Warehouse への SQL コードの移行

コードが SQL Data Warehouse と必ず準拠するようにするには、多くの場合、コード ベースに変更を加える必要があります。一部の SQL Data Warehouse 機能では分散環境で直接機能するように設計されているため、大幅にパフォーマンスを向上できます。ただし、パフォーマンスと拡張性を維持するには、一部の機能が使用できなくなる場合もあります。

## Transact-SQL コードの変更点

Azure SQL Data Warehouse でサポートされていない主な機能を次に示します。

- 更新での ANSI の JOIN
- 削除での ANSI の JOIN
- MERGE ステートメント
- 複数データベースの JOIN
- [(PIVOT および UNPIVOT ステートメント][]
- [カーソル][]
- [SELECT..INTO][]
- INSERT..EXEC
- OUTPUT 句
- インライン ユーザー定義関数
- 複数ステートメント関数
- 再帰共通テーブル式 (CTE)
- CTE を介した更新
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

また、サポートされていないシステム関数もいくつかあります。データ ウェアハウジングで一般的に使用されている主なものを次に示します。

- NEWID()
- NEWSEQUENTIALID()
- @@NESTLEVEL()
- @@IDENTITY()
- @@ROWCOUNT()
- ROWCOUNT_BIG
- ERROR_LINE()

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
[(PIVOT および UNPIVOT ステートメント]: sql-data-warehouse-develop-pivot-unpivot.md
[カーソル]: sql-data-warehouse-develop-loops.md
[SELECT..INTO]: sql-data-warehouse-develop-ctas.md
[rollup / cube / grouping セット オプションによる句ごとのグループ化]: sql-data-warehouse-develop-group-by-options.md
[8 を超えるの入れ子のレベル]: sql-data-warehouse-develop-transactions.md
[ビューを使用した更新]: sql-data-warehouse-develop-views.md
[変数代入のための SELECT の使用]: sql-data-warehouse-develop-variable-assignment.md
[動的 SQL 文字列の MAX 以外のデータ型]: sql-data-warehouse-develop-dynamic-sql.md
[開発の概要]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO4-->