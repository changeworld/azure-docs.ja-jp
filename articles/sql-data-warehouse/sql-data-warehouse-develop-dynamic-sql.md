<properties
   pageTitle="SQL Data Warehouse | Microsoft Azure での動的 SQL"
   description="ソリューション開発のための Azure SQL Data Warehouse での動的 SQL の使用に関するヒント。"
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
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# SQL Data Warehouse での動的 SQL
SQL Data Warehouse のアプリケーション コードを開発する際に、柔軟性、ジェネリック、モジュール型のソリューションを提供するための動的 SQL を使用しなければならない場合があります。しかし、SQL Data Warehouse は、この時点で blob データ型をサポートしていません。これにより、blob 型には varchar (max) と nvarchar (max) の両方の型が含まれるので、文字列のサイズが制限される場合があります。実行する必要がある動的 SQL コードに大量の文字列を構築する際に、使用しているアプリケーション コードにこれらの型を使用していることがあります。

このような状況では、代わりにコードをチャンクに分割して、EXEC ステートメントを使用する必要があります。

簡単な例は、以下のとおりです。

```
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

文字列がそれほど長くない場合は、通常どおり [sp_executesql][] を使用することができます。


## 次のステップ
開発のその他のヒントについては、[開発の概要][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[開発の概要]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp_executesql]: https://msdn.microsoft.com/ja-jp/library/ms188001.aspx

<!--Other Web references-->

<!---HONumber=July15_HO3-->