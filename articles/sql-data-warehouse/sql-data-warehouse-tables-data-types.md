<properties
   pageTitle="SQL Data Warehouse のテーブルのデータ型 | Microsoft Azure"
   description="Azure SQL Data Warehouse のテーブルのデータ型の概要です。"
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
   ms.date="06/29/2016"
   ms.author="jrj;barbkess;sonyama"/>

# SQL Data Warehouse のテーブルのデータ型

> [AZURE.SELECTOR]
- [概要][]
- [データ型][]
- [分散][]
- [Index][]
- [Partition][]
- [統計][]
- [一時][]

SQL Data Warehouse では、最もよく使用されるデータ型がサポートされています。SQL Data Warehouse でサポートされるデータ型の一覧を以下に示します。データ型のサポートの詳細については、[CREATE TABLE][] に関するページを参照してください。

|**サポートされているデータ型**|||
|---|---|---|
[bigint][]|[decimal][]|[smallint][]|
[binary][]|[float][]|[smallmoney][]|
[bit][]|[int][]|[sysname][]|
[char][]|[money][]|[time][]|
[date][]|[nchar][]|[tinyint][]|
[datetime][]|[nvarchar][]|[uniqueidentifier][]|
[datetime2][]|[real][]|[varbinary][]|
[datetimeoffset][]|[smalldatetime][]|[varchar][]|


## データ型のベスト プラクティス

 列の型を定義するときに、データをサポートする最小のデータ型を使用すると、クエリのパフォーマンスが向上します。これは、CHAR および VARCHAR 列の場合に特に重要です。列の最長の値が 25 文字の場合は、列を VARCHAR(25) として定義します。すべての文字列を既定の長さで定義しないようにします。さらに、VARCHAR で済む場合は、[NVARCHAR][] を使用せずに、列を VARCHAR として定義します。可能なときは、NVARCHAR(MAX) または VARCHAR(MAX) の代わりに、NVARCHAR(4000) または VARCHAR(8000) を使用します。

## Polybase の制限事項

テーブルの読み込みに Polybase を使用している場合は、可変長列の全長を含め、行の最大サイズが 32,767 バイトを超えないように、テーブルを定義します。この幅を超える可能性のある可変長データを含む行を定義し、BCP を含む行を読み込むことはできますが、Polybase を使用して、このデータを完全に読み込むことはできません。行のサイズが大きな Polybase のサポートはまもなく追加されます。

## サポートされていないデータ型

Azure SQL Database などの別の SQL プラットフォームからデータベースを移行する場合は、一部のデータ型が SQL Data Warehouse でサポートされていない可能性があります。以下に、サポートされていないデータ型とサポートされていないデータ型の代わりに使用できるデータ型をいくつか示します。

|データ型|対処法|
|---|---|
|[ジオメトリ][]|[varbinary][]|
|[地理][]|[varbinary][]|
|[hierarchyid][]|[nvarchar][](4000)|
|[image][ntext,text,image]|[varbinary][]|
|[text][ntext,text,image]|[varchar][]|
|[ntext][ntext,text,image]|[nvarchar][]|
|[sql\_variant][]|列を厳密に型指定された複数の列に分割します。|
|[table][]|一時テーブルに変換します。|
|[timestamp][]|[datetime2][] と `CURRENT_TIMESTAMP` 関数を使用するようにコードを再作成します。定数のみが既定値としてサポートされているため、current\_timestamp を既定の制約として定義できません。timestamp で型指定された列から行バージョンの値を移行する必要がある場合は、行バージョンの値 NOT NULL または NULL に [BINARY][](8) または [VARBINARY][BINARY](8) を使用します。|
|[xml][]|[varchar][]|
|[ユーザー定義型][]|可能な限り、ネイティブ型に変換します。|
|既定値|既定値では、リテラルと定数のみがサポートされます。`GETDATE()` や `CURRENT_TIMESTAMP` など、不明確な式または関数はサポートされていません。|

Azure SQL Data Warehouse でサポートされていない列を識別するために、現在の SQL データベースで以下の SQL を実行できます。

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```

## 次のステップ

詳細について、[テーブルの概要][Overview]、[テーブルの分散][Distribute]、[テーブルのインデックス作成][Index]、[テーブルのパーティション分割][Partition]、[テーブル統計の更新][Statistics]、[一時テーブル][Temporary]に関する各記事を参照します。ベスト プラクティスの詳細について、[SQL Data Warehouse のベスト プラクティス][]に関するページを参照します。

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[概要]: ./sql-data-warehouse-tables-overview.md
[データ型]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[分散]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[統計]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[一時]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse のベスト プラクティス]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
[create table]: https://msdn.microsoft.com/library/mt203953.aspx
[bigint]: https://msdn.microsoft.com/library/ms187745.aspx
[binary]: https://msdn.microsoft.com/library/ms188362.aspx
[bit]: https://msdn.microsoft.com/library/ms177603.aspx
[char]: https://msdn.microsoft.com/library/ms176089.aspx
[date]: https://msdn.microsoft.com/library/bb630352.aspx
[datetime]: https://msdn.microsoft.com/library/ms187819.aspx
[datetime2]: https://msdn.microsoft.com/library/bb677335.aspx
[datetimeoffset]: https://msdn.microsoft.com/library/bb630289.aspx
[decimal]: https://msdn.microsoft.com/library/ms187746.aspx
[float]: https://msdn.microsoft.com/library/ms173773.aspx
[ジオメトリ]: https://msdn.microsoft.com/library/cc280487.aspx
[地理]: https://msdn.microsoft.com/library/cc280766.aspx
[hierarchyid]: https://msdn.microsoft.com/library/bb677290.aspx
[int]: https://msdn.microsoft.com/library/ms187745.aspx
[money]: https://msdn.microsoft.com/library/ms179882.aspx
[nchar]: https://msdn.microsoft.com/library/ms186939.aspx
[nvarchar]: https://msdn.microsoft.com/library/ms186939.aspx
[ntext,text,image]: https://msdn.microsoft.com/library/ms187993.aspx
[real]: https://msdn.microsoft.com/library/ms173773.aspx
[smalldatetime]: https://msdn.microsoft.com/library/ms182418.aspx
[smallint]: https://msdn.microsoft.com/library/ms187745.aspx
[smallmoney]: https://msdn.microsoft.com/library/ms179882.aspx
[sql\_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysname]: https://msdn.microsoft.com/library/ms186939.aspx
[table]: https://msdn.microsoft.com/library/ms175010.aspx
[time]: https://msdn.microsoft.com/library/bb677243.aspx
[timestamp]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[uniqueidentifier]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[xml]: https://msdn.microsoft.com/library/ms187339.aspx
[ユーザー定義型]: https://msdn.microsoft.com/library/ms131694.aspx

<!---HONumber=AcomDC_0706_2016-->