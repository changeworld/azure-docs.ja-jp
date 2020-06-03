---
title: Synapse SQL 内で SQL オンデマンド (プレビュー) を使用してストレージ ファイルに対してクエリを実行する
description: Synapse SQL 内で SQL オンデマンド (プレビュー) リソースを使用してストレージ ファイルに対してクエリを実行する方法について説明します。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: c2e18919b287713f59ba8785006c952134994be0
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84258370"
---
# <a name="query-storage-files-using-sql-on-demand-preview-resources-within-synapse-sql"></a>Synapse SQL 内で SQL オンデマンド (プレビュー) リソースを使用してストレージ ファイルに対してクエリを実行する

SQL オンデマンド (プレビュー) を使用すると、データ レイク内のデータに対してクエリを実行できます。 これには、半構造化と非構造化のデータのクエリに対応する T-SQL クエリ領域が用意されています。

クエリでは、次の T-SQL の側面がサポートされています。

- 大部分の SQL 関数、演算子などを含む、完全な [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 領域。
- CREATE EXTERNAL TABLE AS SELECT ([CETAS](develop-tables-cetas.md)) は、[外部テーブル](develop-tables-external-tables.md)を作成し、次に並行して、Transact-SQL SELECT ステートメントの結果を Azure Storage にエクスポートします。

現在サポートされているものとされていないものの詳細については、[SQL オンデマンドの概要](on-demand-workspace-overview.md)に関する記事をご覧ください。

Azure AD ユーザーがクエリを実行すると、既定では、Azure AD パススルー認証プロトコルを使用してストレージ アカウントにアクセスします。 そのため、ユーザーは偽装され、ストレージ レベルでアクセス許可がチェックされます。 ニーズに合わせて、[ストレージ アクセスを制御](develop-storage-files-storage-access-control.md)できます。

## <a name="extensions"></a>拡張機能

Azure Storage ファイルに格納されているデータに対するインプレース クエリのスムーズな実行をサポートするために、SQL オンデマンドでは、次の追加機能を持つ [OPENROWSET](develop-openrowset.md) 関数が使用されます。

- [複数のファイルまたはフォルダーに対してクエリを実行する](#query-multiple-files-or-folders)
- [PARQUET ファイル形式](#parquet-file-format)
- [区切りテキストを操作するための追加オプション (フィールド ターミネータ、行ターミネータ、エスケープ文字)](#additional-options-for-working-with-delimited-text)
- [選択した列のサブセットの読み取り](#read-a-chosen-subset-of-columns)
- [スキーマ推論](#schema-inference)
- [filename 関数](#filename-function)
- [filepath 関数](#filepath-function)
- [複合型と入れ子または繰り返しのデータ構造を操作する](#work-with-complex-types-and-nested-or-repeated-data-structures)

### <a name="query-multiple-files-or-folders"></a>複数のファイルまたはフォルダーに対してクエリを実行する

1 つのフォルダーまたは一連のフォルダー内にある一連のファイルを 1 つのエンティティまたは行セットとして扱いながら、それらに対して T-SQL クエリを実行するには、フォルダーのパス、または一連のファイルやフォルダーのパターン (ワイルドカードを使用) を指定します。

次の規則が適用されます。

- パターンは、ディレクトリ パスの一部またはファイル名に使用できます。
- 同じディレクトリ ステップまたはファイル名に複数のパターンを使用できます。
- 複数のワイルドカードがある場合は、一致するすべてのパス内のファイルが、結果のファイル セットに含められます。

```
N'https://myaccount.blob.core.windows.net/myroot/*/mysubfolder/*.csv'
```

使用例については、[フォルダーと複数のファイルに対するクエリの実行](query-folders-multiple-csv-files.md)に関する記事を参照してください。

### <a name="parquet-file-format"></a>PARQUET ファイル形式

Parquet ソース データに対してクエリを実行するには、FORMAT = 'PARQUET' を使用します。

```syntaxsql
OPENROWSET
(
    { BULK 'data_file' ,
    { FORMATFILE = 'format_file_path' [ <bulk_options>] } }
)
AS table_alias(column_alias,...n)
<bulk_options> ::=
...
[ , FORMAT = {'CSV' | 'PARQUET'} ]
```

使用例については、[Parquet ファイルに対するクエリの実行](query-parquet-files.md)に関する記事を参照してください。

### <a name="additional-options-for-working-with-delimited-text"></a>区切りテキストを操作するための追加オプション

CSV (区切りテキスト) ファイルを操作するために、次の追加パラメーターが導入されています。

```syntaxsql
<bulk_options> ::=
...
[ , FIELDTERMINATOR = 'char' ]
[ , ROWTERMINATOR = 'char' ]
[ , ESCAPE_CHAR = 'char' ]
...
```

- ESCAPE_CHAR = 'char' は、ファイル内でそれ自体とすべての区切り記号の値をエスケープするために使用するファイル内の文字を指定します。 エスケープ文字の後にそれ自体以外の値またはいずれかの区切り記号の値が続く場合は、その値を読み取るときにエスケープ文字が削除されます。
ESCAPE_CHAR パラメーターは、FIELDQUOTE が有効かどうかに関係なく適用されます。 引用文字をエスケープするために使用されることはありません。 引用文字は、Excel CSV の動作に合わせて二重引用符でエスケープされます。
- FIELDTERMINATOR ='field_terminator' は、使用するフィールド ターミネータを指定します。 既定のフィールド ターミネータはコンマ (" **,** ") です。
- ROWTERMINATOR ='row_terminator' は、使用する行ターミネータを指定します。 既定の行ターミネータは、改行文字  **\r\n** です。

### <a name="read-a-chosen-subset-of-columns"></a>選択した列のサブセットを読み取る

読み取る列を指定するには、OPENROWSET ステートメント内にオプションの WITH 句を指定できます。

- CSV データ ファイルがある場合、すべての列を読み取るには、列名とそのデータ型を指定します。 列のサブセットが必要な場合は、序数を使用して、元のデータ ファイルから序数で列を選択します。 列は、序数の指定によってバインドされます。
- Parquet データ ファイルがある場合は、元のデータ ファイル内の列名と一致する列名を指定します。 列は名前によってバインドされます。

```syntaxsql
OPENROWSET
...
| BULK 'data_file',
{ FORMATFILE = 'format_file_path' [ <bulk_options>] } }
) AS table_alias(column_alias,...n) | WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })
```

サンプルについては、[すべての列を指定せずに CSV ファイルを読み取る](query-single-csv-file.md#returning-subset-of-columns)ことに関する記事を参照してください。

### <a name="schema-inference"></a>スキーマ推論

OPENROWSET ステートメントから WITH 句を省略することで、基になるファイルからスキーマを自動検出 (推論) するようにサービスに指示できます。

> [!NOTE]
> これは現在、PARQUET ファイル形式でのみ機能します。

```sql
OPENROWSET(
BULK N'path_to_file(s)', FORMAT='PARQUET');
```

最適なパフォーマンスが得られる[適切な推定データ型](best-practices-sql-on-demand.md#check-inferred-data-types)が使用されていることを確認してください。 

### <a name="filename-function"></a>filename 関数

この関数は、行の生成元のファイル名を返します。 

特定のファイルに対してクエリを実行するには、[特定のファイルに対するクエリの実行](query-specific-files.md#filename)に関する記事の「Filename」セクションをお読みください。

戻り値のデータ型は nvarchar(1024) です。 最適なパフォーマンスを確保するために、filename 関数の結果は必ず適切なデータ型にキャストしてください。 文字データ型を使用する場合は、適切な長さが使用されていることを確認します。

### <a name="filepath-function"></a>filepath 関数

この関数は、完全パスまたはパスの一部を返します。

- パラメーターを指定せずに呼び出された場合、行の生成元である完全なファイル パスを返します。
- パラメーターを指定して呼び出されると、パラメーターで指定された位置にあるワイルドカードと一致するパスの一部を返します。 たとえば、パラメーター値 1 は、最初のワイルドカードと一致するパスの一部を返します。

追加情報については、[特定のファイルに対するクエリの実行](query-specific-files.md#filepath)に関する記事の「Filepath」セクションをお読みください。

戻り値のデータ型は nvarchar(1024) です。 最適なパフォーマンスを確保するために、filepath 関数の結果は必ず適切なデータ型にキャストしてください。 文字データ型を使用する場合は、適切な長さが使用されていることを確認します。

### <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>複合型と入れ子または繰り返しのデータ構造を操作する

入れ子または繰り返しのデータ型 ([Parquet](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types) ファイルなど) に格納されているデータを操作する際のスムーズな実行を可能にするために、SQL オンデマンドで次の拡張機能が追加されています。

#### <a name="project-nested-or-repeated-data"></a>入れ子にされたデータまたは繰り返しのデータを射影する

データを射影するには、入れ子にされたデータ型の列を含む Parquet ファイルに対して SELECT ステートメントを実行します。 出力時、入れ子にされた値は JSON にシリアル化され、varchar(8000) SQL データ型として返されます。

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

詳細については、[Parquet の入れ子にされた型に対するクエリの実行](query-parquet-nested-types.md#project-nested-or-repeated-data)に関する記事の「入れ子または繰り返しのデータを射影する」セクションを参照してください。

#### <a name="access-elements-from-nested-columns"></a>入れ子にされた列から要素にアクセスする

入れ子にされた列 (構造体など) から入れ子にされた要素にアクセスするには、"ドット表記" を使用して、フィールド名をパスに連結します。 OPENROWSET 関数の WITH 句に column_name としてパスを指定します。

構文フラグメントの例を次に示します。

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

既定で、OPENROWSET 関数は、ソース フィールドの名前とパスを WITH 句に指定された列名と一致させます。 同じソース Parquet ファイル内異なる入れ子レベルに含まれている要素には、WITH 句を使用してアクセスできます。

**戻り値**

- 関数は、入れ子にされた型グループに含まれていないすべての Parquet の型について、指定された要素から、および指定されたパスで、int、decimal、varchar などのスカラー値を返します。
- パスが、入れ子にされた型の要素を指している場合、関数は、指定されたパスの先頭の要素から始まる JSON フラグメントを返します。 JSON フラグメントの型は varchar(8000) です。
- 指定された column_name でプロパティが見つからない場合、関数はエラーを返します。
- 指定された column_path でプロパティが見つからない場合、[パス モード](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#PATHMODE)に応じて、関数は、strict モードのときはエラーを、lax モードのときには null を返します。

クエリのサンプルについては、[Parquet の入れ子にされた型に対するクエリの実行](query-parquet-nested-types.md#access-elements-from-nested-columns)に関する記事の「入れ子にされた列から要素にアクセスする」セクションを参照してください。

#### <a name="access-elements-from-repeated-columns"></a>繰り返される列から要素にアクセスする

配列やマップの要素など、繰り返される列から要素にアクセスするには、射影する必要があるすべてのスカラー要素に対して [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 関数を使用し、次を指定します。

- 最初のパラメーターとして、入れ子にされた、または繰り返される列
- 2 番目のパラメーターとして、アクセスする要素またはプロパティを指定する [JSON パス](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

繰り返される列から非スカラー要素にアクセスするには、射影する必要があるすべての非スカラー要素に対して [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 関数を使用し、次を指定します。

- 最初のパラメーターとして、入れ子にされた、または繰り返される列
- 2 番目のパラメーターとして、アクセスする要素またはプロパティを指定する [JSON パス](/sql/relational-databases/json/json-path-expressions-sql-server?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

次の構文フラグメントをご覧ください。

```syntaxsql
    SELECT
       { JSON_VALUE (column_name, path_to_sub_element), }
       { JSON_QUERY (column_name [ , path_to_sub_element ]), )
    FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

繰り返される列から要素にアクセスするためのクエリのサンプルについては、[Parquet の入れ子にされた型に対するクエリの実行](query-parquet-nested-types.md#access-elements-from-repeated-columns)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

さまざまなファイルの種類に対してクエリを実行する方法、およびビューの作成と使用の詳細については、次の記事を参照してください。

- [単一の CSV ファイルに対してクエリを実行する](query-single-csv-file.md)
- [Parquet ファイルに対してクエリを実行する](query-parquet-files.md)
- [JSON ファイルに対してクエリを実行する](query-json-files.md)
- [Parquet の入れ子にされた型に対してクエリを実行する](query-parquet-nested-types.md)
- [フォルダーと複数の CSV ファイルに対してクエリを実行する](query-folders-multiple-csv-files.md)
- [クエリでファイルのメタデータを使用する](query-specific-files.md)
- [ビューの作成および使用](create-use-views.md)
