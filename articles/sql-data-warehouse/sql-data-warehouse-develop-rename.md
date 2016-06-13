<properties
   pageTitle="SQL Data Warehouse | Microsoft Azure での名前変更"
   description="ソリューション開発のための Azure SQL Data Warehouse でのテーブルの名前変更に関するヒント。"
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
   ms.date="05/28/2016"
   ms.author="mausher;jrj;barbkess;sonyama"/>

# SDL Data Warehouse での名前変更
SQL Data Warehouse では、[RENAME][] ステートメントを使用してテーブルの名前を変更します。これは、`sp_rename` を使用する SQL Server とは異なります。現時点で、名前を変更できるのはユーザー テーブルのみです。データベースと外部テーブルの名前は変更できません。

## テーブルの名前変更

テーブルの名前を変更すると、テーブルに関連付けられたすべてのオブジェクトおよびプロパティが、新しいテーブル名を参照するように更新されます。たとえば、テーブルの定義、インデックス、制約、およびアクセス許可が更新されます。ビューは更新されません。

テーブルの名前を変更するための構文は次のとおりです。

```sql
RENAME OBJECT dbo.Customer TO NewCustomer;
```

## テーブル スキーマの変更

オブジェクトが属するスキーマを変更するには、ALTER SCHEMA を使用します。

```sql
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```

## テーブル名の変更には排他的なテーブル ロックが必要

使用中のテーブルの名前を変更することはできません。テーブルの名前の変更には、テーブル上での排他的なロックが必要です。テーブルを使用中である場合は、テーブルを使用しているセッションを終了することが必要な場合があります。セッションを終了するには、[KILL][] コマンドを使用します。たとえば、「`KILL 'SID1234'`」のように指定します。KILL を使用する場合は注意が必要です。セッションが終了すると、コミットされていないトランザクション作業すべてがロールバックされるためです。[セッションと要求][]の詳細については、接続に関する記事をご覧ください。トランザクション クエリの強制終了の影響と、ロールバックの結果の詳細については、「[SQL Data Warehouse のトランザクションの最適化][]」を参照してください。


## 次のステップ
T-SQL 参照の詳細については、[T-SQL 参照][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[development overview]: ./sql-data-warehouse-overview-develop.md
[セッションと要求]: ./sql-data-warehouse-develop-connections.md#sessions-and-requests
[T-SQL 参照]: ./sql-data-warehouse-reference-tsql-statements.md
[SQL Data Warehouse のトランザクションの最適化]: ./sql-data-warehouse-develop-best-practices-transactions.md

<!--MSDN references-->
[KILL]: https://msdn.microsoft.com/library/ms173730.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx

<!---HONumber=AcomDC_0601_2016-->