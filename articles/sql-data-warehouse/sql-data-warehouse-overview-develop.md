<properties
   pageTitle="SQL Data Warehouse の設計に関する設計上の決定とコーディング技法 | Microsoft Azure"
   description="SQL Data Warehouse に関する開発コンセプト、設計上の決定、推奨事項、およびコーディング技法。"
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
   ms.date="09/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SQL Data Warehouse の設計上の決定と コーディング技法

SQL Data Warehouse に関する主要な設計上の決定、推奨事項、およびコーディング技法をより深く理解するために、開発に関する次の記事に目を通してください。

## 主要な設計上の決定
次の記事には、SQL Data Warehouse を使用した分散データ ウェアハウスの開発を理解するのに必要ないくつかの主な概念と設計上の決定事項が概説されています。

- [接続][]
- [同時実行][]
- [トランザクション][]
- [ユーザー定義スキーマ][]
- [テーブル設計][]
- [ハッシュ分散キー][]
- [テーブルのパーティション][]
- [CTAS][]
- [統計][]

## 開発における推奨事項とコーディング技法
次の記事には、SQL Data Warehouse を開発するための具体的なコーディング技法、ヒント、および推奨事項が概説されています。

- [ストアド プロシージャ][]
- [ラベル][]
- [ビュー][]
- [一時テーブル][]
- [動的 SQL][]
- [ループ][]
- [オブジェクトの名前変更][]
- [データのピボット][]
- [オプションでのグループ化][]
- [変数の代入][]

## 次のステップ
開発記事に目を通したら、SQL Data Warehouse でサポートされる構文の詳細について、「[Transact-SQL リファレンス][]」ページを参照してください。

<!--Image references-->

<!--Article references-->
[同時実行]: sql-data-warehouse-develop-concurrency.md
[接続]: sql-data-warehouse-develop-connections.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[動的 SQL]: sql-data-warehouse-develop-dynamic-sql.md
[オプションでのグループ化]: sql-data-warehouse-develop-group-by-options.md
[ハッシュ分散キー]: sql-data-warehouse-develop-hash-distribution-key.md
[ラベル]: sql-data-warehouse-develop-label.md
[ループ]: sql-data-warehouse-develop-loops.md
[データのピボット]: sql-data-warehouse-develop-pivot-unpivot.md
[オブジェクトの名前変更]: sql-data-warehouse-develop-rename.md
[統計]: sql-data-warehouse-develop-statistics.md
[ストアド プロシージャ]: sql-data-warehouse-develop-stored-procedures.md
[テーブル設計]: sql-data-warehouse-develop-table-design.md
[テーブルのパーティション]: sql-data-warehouse-develop-table-partitions.md
[一時テーブル]: sql-data-warehouse-develop-temporary-tables.md
[トランザクション]: sql-data-warehouse-develop-transactions.md
[ユーザー定義スキーマ]: sql-data-warehouse-develop-user-defined-schemas.md
[変数の代入]: sql-data-warehouse-develop-variable-assignment.md
[ビュー]: sql-data-warehouse-develop-views.md

[Transact-SQL リファレンス]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=Oct15_HO1-->