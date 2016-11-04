---
title: SQL Data Warehouse の設計に関する設計上の決定とコーディング技法 | Microsoft Docs
description: SQL Data Warehouse に関する開発コンセプト、設計上の決定、推奨事項、およびコーディング技法。
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: barbkess
editor: ''

ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 08/16/2016
ms.author: jrj;barbkess;sonyama

---
# SQL Data Warehouse の設計上の決定と コーディング技法
SQL Data Warehouse に関する主要な設計上の決定、推奨事項、およびコーディング技法をより深く理解するために、開発に関する次の記事に目を通してください。

## 主要な設計上の決定
次の記事には、SQL Data Warehouse を使用した分散データ ウェアハウスの開発を理解するのに必要ないくつかの主な概念と設計上の決定事項が概説されています。

* [接続][接続]
* [同時実行][同時実行]
* [トランザクション][トランザクション]
* [ユーザー定義スキーマ][ユーザー定義スキーマ]
* [テーブルのディストリビューション][テーブルのディストリビューション]
* [テーブルのインデックス][テーブルのインデックス]
* [テーブルのパーティション][テーブルのパーティション]
* [CTAS][CTAS]
* [統計][統計]

## 開発における推奨事項とコーディング技法
次の記事には、SQL Data Warehouse を開発するための具体的なコーディング技法、ヒント、および推奨事項が概説されています。

* [ストアド プロシージャ][ストアド プロシージャ]
* [ラベル][ラベル]
* [ビュー][ビュー]
* [一時テーブル][一時テーブル]
* [動的 SQL][動的 SQL]
* [ループ][ループ]
* [オプションでのグループ化][オプションでのグループ化]
* [変数の代入][変数の代入]

## 次のステップ
開発記事に目を通したら、SQL Data Warehouse でサポートされる構文の詳細について、「[Transact-SQL リファレンス][Transact-SQL リファレンス]」ページを参照してください。

<!--Image references-->

<!--Article references-->
[同時実行]: ./sql-data-warehouse-develop-concurrency.md
[接続]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[動的 SQL]: ./sql-data-warehouse-develop-dynamic-sql.md
[オプションでのグループ化]: ./sql-data-warehouse-develop-group-by-options.md
[ラベル]: ./sql-data-warehouse-develop-label.md
[ループ]: ./sql-data-warehouse-develop-loops.md
[統計]: ./sql-data-warehouse-tables-statistics.md
[ストアド プロシージャ]: ./sql-data-warehouse-develop-stored-procedures.md
[テーブルのディストリビューション]: ./sql-data-warehouse-tables-distribute.md
[テーブルのインデックス]: ./sql-data-warehouse-tables-index.md
[テーブルのパーティション]: ./sql-data-warehouse-tables-partition.md
[一時テーブル]: ./sql-data-warehouse-tables-temporary.md
[トランザクション]: ./sql-data-warehouse-develop-transactions.md
[ユーザー定義スキーマ]: ./sql-data-warehouse-develop-user-defined-schemas.md
[変数の代入]: ./sql-data-warehouse-develop-variable-assignment.md
[ビュー]: ./sql-data-warehouse-develop-views.md
[Transact-SQL リファレンス]: ./sql-data-warehouse-overview-reference.md

<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0817_2016-->