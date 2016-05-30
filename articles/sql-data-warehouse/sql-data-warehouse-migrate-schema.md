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
   ms.date="05/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# SQL Data Warehouse へのスキーマの移行#

次の概要を確認して、SQL Server と SQL Data Warehouse の相違を理解し、データベースの移行に役立ててください。

### テーブルの機能
SQL Data Warehouse では、次の機能は使用またはサポートされません。

- 主キー  
- 外部キー  
- CHECK 制約  
- UNIQUE 制約  
- 一意のインデックス  
- 計算列  
- スパース列  
- ユーザー定義型  
- インデックス付きビュー  
- ID  
- シーケンス  
- トリガー  
- シノニム  


### データ型の相違
SQL Data Warehouse では、次の一般的なビジネス データ型がサポートされています。

- bigint
- binary
- bit
- char
- date
- datetime
- datetime2
- datetimeoffset
- decimal
- float
- int
- money
- nchar
- nvarchar
- numeric
- real
- smalldatetime
- smallint
- smallmoney
- sysname
- time
- tinyint
- varbinary
- varchar
- uniqueidentifier

次のクエリを使用して、互換性のない型を含むデータ ウェアハウス内の列を特定できます。

```sql
SELECT  t.[name]
,       c.[name]
,       c.[system_type_id]
,       c.[user_type_id]
,       y.[is_user_defined]
,       y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN
                (   'geography'
                ,   'geometry'
                ,   'hierarchyid'
                ,   'image'
                ,   'ntext'
                ,   'sql_variant'
                ,   'text'
                ,   'timestamp'
                ,   'xml'
                )
AND  y.[is_user_defined] = 1
;

```

このクエリには、サポートされていないユーザー定義データ型も含まれています。

データベースにサポーサポートされていない型をデータベースで使用していても心配ありません。代わりに使用できる代替の型があります。

代替のデータ型は次のとおりです。

- **geometry**: varbinary 型を使用します。
- **geography**: varbinary 型を使用します。
- **hierarchyid**: この CLR 型はサポートされていません。
- **image**、**text**、**ntext**: rchar/nvarchar を使用します (より小さいほうが適切)。
- **sql\_variant**: 列を厳密に型指定された複数の列に分割します。
- **table**: 一時テーブルに変換します。
- **timestamp**: datetime2 と `CURRENT_TIMESTAMP` 関数を使用するようにコードを再作成します。current\_timestamp を既定の制約として指定することはできません。また、値は自動的には更新されません。timestamp で型指定された列から rowversion 値を移行する必要がある場合は、行バージョンの値 NOT NULL または NULL に binary(8) または varbinary(8) を使用します。
- **ユーザー定義型**: 可能な限り、ネイティブ型に変換します。
- **xml**: パフォーマンスを向上させるために、varchar(max) 以下を使用します。必要に応じて、列全体を分割します。

パフォーマンス向上のための代替のデータ型は次のとおりです。

- nvarchar(max): パフォーマンスを向上させるために、nvarchar(4000) 以下を使用します
- varchar(max): パフォーマンスを向上させるために、varchar(8000) 以下を使用します

部分的なサポート:

- 既定の制約では、リテラルと定数のみがサポートされます。`GETDATE()` や `CURRENT_TIMESTAMP` など、不明確な式または関数はサポートされていません。

> [AZURE.NOTE] テーブルの読み込みに Polybase を使用している場合は、可変長列の全長を含め、行の最大サイズが 32,767 バイトを超えないように、テーブルを定義します。この数値を超える可能性のある可変長データを含む行を定義し、BCP を含む行を読み込むことはできますが、Polybase を使用して、このデータを完全に読み込むことはまだできません。行のサイズが大きな Polybase のサポートはまもなく追加されます。また、クエリを実行する際のスループットをさらに向上させるために、可変長列のサイズを制限してください。

## 次のステップ
SQLDW にデータベース スキーマを正常に移行した後、次の記事のいずれかに進むことができます。

- [データの移行][]
- [コードの移行][]

開発に関するその他のヒントについては、[開発の概要][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[コードの移行]: sql-data-warehouse-migrate-code.md
[データの移行]: sql-data-warehouse-migrate-data.md
[開発の概要]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0518_2016-->