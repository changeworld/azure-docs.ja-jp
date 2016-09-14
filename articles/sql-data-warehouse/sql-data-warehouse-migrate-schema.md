<properties
   pageTitle="SQL Data Warehouse へのスキーマの移行 | Microsoft Azure"
   description="ソリューション開発のための Azure SQL Data Warehouse へのスキーマの移行に関するヒント。"
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
   ms.date="08/25/2016"
   ms.author="jrj;barbkess;sonyama"/>

# SQL Data Warehouse へのスキーマの移行#

次の概要を確認して、SQL Server と SQL Data Warehouse の相違点を理解し、データベースの移行に役立ててください。

## テーブルの移行

テーブルを移行する場合、SQL Data Warehouse テーブルのテーブル機能を理解する必要があります。[テーブルの概要][]に関する記事は、その開始点として最適です。この記事では、テーブルの統計、ディストリビューション、パーティション分割、およびインデックス作成など、テーブルを作成する場合の最も重要な考慮事項を紹介します。また、[サポートされていないテーブルの機能][]と回避策についても取り上げます。

SQL Data Warehouse では、次の一般的なビジネス データ型がサポートされています。サポートされているデータ型と[サポートされていないデータ型][]の一覧については、[データ型][]に関する記事を参照してください。[データ型][]に関する記事には、[サポートされていないデータ型][]を識別するためのクエリも含まれています。データ型を変換する場合は、必ず、「[データ型のベスト プラクティス][]」をご覧ください。

## 次のステップ
SQL Data Warehouse にデータベース スキーマを正常に移行したら、次の記事のいずれかに進みます。

- [データの移行][]
- [コードの移行][]

[ベスト プラクティス][]に関する記事で SQL Data Warehouse のベスト プラクティスの詳細を確認します。

<!--Image references-->

<!--Article references-->
[コードの移行]: ./sql-data-warehouse-migrate-code.md
[データの移行]: ./sql-data-warehouse-migrate-data.md
[ベスト プラクティス]: ./sql-data-warehouse-best-practices.md
[テーブルの概要]: ./sql-data-warehouse-tables-overview.md
[サポートされていないテーブルの機能]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[データ型]: ./sql-data-warehouse-tables-data-types.md
[サポートされていないデータ型]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[データ型のベスト プラクティス]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0831_2016-->