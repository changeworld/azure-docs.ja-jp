---
title: サーバーレス SQL プールのセルフヘルプ
description: このセクションには、サーバーレス SQL プールに関する問題のトラブルシューティングに役立つ情報が含まれています。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 848f5f13218fde513bf48575c2f9bb298521d3ad
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2021
ms.locfileid: "113214751"
---
# <a name="self-help-for-serverless-sql-pool"></a>サーバーレス SQL プールのセルフヘルプ

この記事には、Azure Synapse Analytics のサーバーレス SQL プールでよく発生する問題のトラブルシューティング方法に関する情報が含まれています。

## <a name="synapse-studio"></a>Synapse Studio

### <a name="serverless-sql-pool-is-grayed-out-in-synapse-studio"></a>Synapse Studio でサーバーレス SQL プールがグレー表示される

Synapse Studio がサーバーレス SQL プールへの接続を確立できない場合、サーバーレス SQL プールがグレー表示されるか、状態が "オフライン" と表示されます。 通常、この問題は次のいずれかの場合に発生します。

1) Azure Synapse バックエンドとの通信がネットワークによって妨げられている。 最も頻繁なケースは、ポート 1443 がブロックされているケースです。 サーバーレス SQL プールを動作させるには、このポートのブロックを解除します。 これ以外の問題が原因でサーバーレス SQL プールが動作しなくなることがあります。[詳細については、完全なトラブルシューティング ガイドを参照してください](../troubleshoot/troubleshoot-synapse-studio.md)。
2) サーバーレス SQL プールにログインするアクセス許可がない。 ユーザーがアクセスできるようにするには、Azure Synapse ワークスペース管理者がワークスペース管理者または SQL 管理者ロールにユーザーを追加する必要があります。 [詳細については、アクセス制御に関する完全なガイドを参照してください](../security/synapse-workspace-access-control-overview.md)。

### <a name="query-fails-with-error-websocket-connection-was-closed-unexpectedly"></a>クエリがエラーで失敗する: Websocket connection was closed unexpectedly. (Websocket 接続が予期せずに閉じられました。)

"Websocket connection was closed unexpectedly" (Websocket 接続が予期せずに閉じられました) というエラー メッセージでクエリが失敗する場合は、たとえばネットワークの問題のために Synapse Studio へのブラウザー接続が中断されたことを意味します。 

この問題を解決するには、このクエリを再実行します。 環境でこのメッセージが頻繁に発生する場合は、ネットワーク管理者にサポートを依頼し、ファイアウォールの設定を調べて、[こちらのトラブルシューティング ガイドで詳細を確認](../troubleshoot/troubleshoot-synapse-studio.md)します。 

問題が引き続き発生する場合は、Azure portal で[サポート チケット](../../azure-portal/supportability/how-to-create-azure-support-request.md)を作成し、Synapse Studio でさらに調査するのではなく、同じクエリを [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) または [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) で試してください。

## <a name="query-execution"></a>クエリ実行

### <a name="query-fails-because-file-cannot-be-opened"></a>ファイルを開くことができないため、クエリが失敗する

"File cannot be opened because it does not exist or it is used by another process" (ファイルが存在しないか、別のプロセスで使用されているため、開くことができません) というエラーでクエリが失敗したときに、ファイルが存在し、かつ別のプロセスで使用されていないことが確認されている場合は、サーバーレス SQL プールがファイルにアクセスできないことを意味します。 この問題が発生するのは、通常、Azure Active Directory ID にファイルへのアクセス権がないか、ファイアウォールによってファイルへのアクセスがブロックされているためです。 サーバーレス SQL プールでは、既定で、Azure Active Directory ID を使用してファイルへのアクセスを試みます。 この問題を解決するには、ファイルにアクセスするための適切な権限を持っている必要があります。 最も簡単な方法は、クエリの対象となるストレージ アカウントに対する "ストレージ BLOB データ共同作成者" ロールを自分に付与することです。 
- [詳細については、ストレージの Azure Active Directory アクセス制御に関する完全なガイドを参照してください](../../storage/common/storage-auth-aad-rbac-portal.md)。 
- [「Azure Synapse Analytics でサーバーレス SQL プールのストレージ アカウント アクセスを制御する」を参照してください](develop-storage-files-storage-access-control.md)。

#### <a name="alternative-to-storage-blob-data-contributor-role"></a>ストレージ BLOB データ共同作成者ロールに代わるもの

ストレージ BLOB データ共同作成者を許可する代わりに、ファイルのサブセットに対してさらに細かいアクセス許可を付与することもできます。 

* このコンテナー内の一部のデータにアクセスする必要があるすべてのユーザーには、ルート (コンテナー) までのすべての親フォルダーに対する実行アクセス許可も必要です。 [Azure Data Lake Storage Gen2 で ACL を設定する方法](../../storage/blobs/data-lake-storage-explorer-acl.md)の詳細を確認してください。 

> [!NOTE]
> コンテナー レベルの実行アクセス許可は、Azure Data Lake Gen2 内で設定する必要があります。
> フォルダーに対するアクセス許可は、Synapse 内で設定できます。 


この例で data2.csv のクエリを実行する場合は、次のアクセス許可が必要です。 
   - コンテナーに対する実行アクセス許可
   - folder1 に対する実行アクセス許可 
   - data2.csv に対する読み取りアクセス許可

![Data Lake でのアクセス許可の構造を示す図。](./media/resources-self-help-sql-on-demand/folder-structure-data-lake.png)

* アクセスしたいデータに対する完全なアクセス許可を持つ管理者ユーザーで Synapse にログインします。

* データ ペインで、ファイルを右クリックして [アクセスの管理] を選択します。

![アクセス管理の UI を示すスクリーンショット。](./media/resources-self-help-sql-on-demand/manage-access.png)

* 少なくとも "読み取り" アクセス許可を選択し、ユーザーの UPN またはオブジェクト ID を入力して (user@contoso.com など)、[追加] をクリックします

* このユーザーに読み取りアクセス許可を付与します。
![読み取りアクセス許可の付与 UI を示すスクリーンショット](./media/resources-self-help-sql-on-demand/grant-permission.png)

> [!NOTE]
> ゲスト ユーザーの場合は、Synapse で直接行うことはできないので、Azure Data Lake サービスを使用して直接行う必要があります。 

### <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>現在のリソース制約によりクエリを実行できないため、クエリが失敗する 

"This query can't be executed due to current resource constraints (現在のリソース制約のため、このクエリを実行できません)" というエラー メッセージでクエリが失敗する場合、リソースの制約により、サーバーレス SQL プールが現時点でクエリを実行できないことを意味します。 

- 適切なサイズのデータ型が使用されていることを確認してください。 また、文字列型の列の場合は既定で VARCHAR (8000) になるため、Parquet ファイルのスキーマを指定します。 

- 対象のクエリが CSV ファイルをターゲットとしている場合は、[統計を作成する](develop-tables-statistics.md#statistics-in-serverless-sql-pool)ことを検討してください。 

- クエリを最適化するには、[サーバーレス SQL プールのパフォーマンスのベスト プラクティス](./best-practices-serverless-sql-pool.md)に関するページを参照してください。  

### <a name="query-fails-with-error-while-handling-an-external-file"></a>外部ファイルの処理中に、クエリがエラーで失敗する 

"外部ファイルの処理でエラーが発生しました: 最大エラー数に達しました" というエラー メッセージでクエリが失敗する場合は、指定した列の型と読み込む必要があるデータが一致していないことを意味します。 エラーおよび検索する行および列に関する詳細な情報を取得するには、パーサーのバージョンを "2.0" から "1.0" に変更します。 

#### <a name="example"></a>例
このクエリ 1 でファイル "names.csv" のクエリを実行した場合、Synapse SQL サーバーレスからこのようなエラーが返されます。 

names.csv
```csv
Id,first name, 
1,Adam
2,Bob
3,Charles
4,David
5,Eva
```

クエリ 1: 
```sql
SELECT
    TOP 100 *
FROM
    OPENROWSET(
        BULK '[FILE-PATH OF CSV FILE]',
        FORMAT = 'CSV',
        PARSER_VERSION='2.0',
       FIELDTERMINATOR =';',
       FIRSTROW = 2
    ) 
    WITH (
    [ID] SMALLINT, 
    [Text] VARCHAR (1) COLLATE Latin1_General_BIN2 
)

    AS [result]
```
原因:

```Error handling external file: ‘Max error count reached’. File/External table name: [filepath].```

パーサーのバージョンをバージョン 2.0 からバージョン 1.0 に変更するとすぐに、エラー メッセージが問題の特定に役立つようになります。 新しいエラー メッセージは、代わりに次のようになります。 

```Bulk load data conversion error (truncation) for row 1, column 2 (Text) in data file [filepath]```

"切り捨て" は、列の型が小さすぎてデータが収まらないことを意味します。 この "names.csv" ファイルに含まれる最長の名は 7 文字です。 したがって、少なくとも VARCHAR(7) のデータ型を使用する必要があります。 エラーは、次のコード行で発生します。 

```sql 
    [Text] VARCHAR (1) COLLATE Latin1_General_BIN2
```
このようにクエリを変更すると、エラーが解決されます。デバッグ後に、パフォーマンスを最大にするため、パーサーのバージョンを 2.0 に変更します。 どのようなときにどちらのパーサー バージョンを使用するのかについて詳しくは、[こちら](develop-openrowset.md)を参照してください。 

```sql 
SELECT
    TOP 100 *
FROM
    OPENROWSET(
        BULK '[FILE-PATH OF CSV FILE]',
        FORMAT = 'CSV',
        PARSER_VERSION='2.0',
        FIELDTERMINATOR =';',
        FIRSTROW = 2
    ) 
    WITH (
    [ID] SMALLINT, 
    [Text] VARCHAR (7) COLLATE Latin1_General_BIN2 
)

    AS [result]
```

### <a name="query-fails-with-conversion-error"></a>変換エラーでクエリが失敗する
"データ ファイル <ファイル パス> の行 n、列 m <列名> の一括読み込みデータ変換エラー (型の不一致または指定されたコードページでは無効な文字)" というエラー メッセージでクエリが失敗する場合は、データ型が行番号 n および列 m の実際のデータと一致しなかったことを意味します。 

たとえば、データ内は整数のみと予想したのに、行 n に文字列があった場合、このエラー メッセージが表示されます。 この問題を解決するには、ファイルと選択したデータ型を調べます。 また、行区切り記号とフィールド ターミネータの設定が正しいかどうかも確認します。 次の例では、列の型として VARCHAR を使用して検査を行う方法を示します。 フィールド ターミネータ、行区切り記号、引用符のエスケープの詳細については、[こちら](query-single-csv-file.md)を参照してください。 

#### <a name="example"></a>例 
このクエリ 1 でファイル "names.csv" のクエリを実行した場合、Synapse SQL サーバーレスからこのようなエラーが返されます。 

names.csv
```csv
Id, first name, 
1,Adam
2,Bob
3,Charles
4,David
five,Eva
```

クエリ 1: 
```sql 
SELECT
    TOP 100 *
FROM
    OPENROWSET(
        BULK '[FILE-PATH OF CSV FILE]',
        FORMAT = 'CSV',
        PARSER_VERSION='1.0',
       FIELDTERMINATOR =',',
       FIRSTROW = 2
    ) 
    WITH (
    [ID] SMALLINT, 
    [Firstname] VARCHAR (25) COLLATE Latin1_General_BIN2 
)

    AS [result]
```

このエラーの原因: ```Bulk load data conversion error (type mismatch or invalid character for the specified codepage) for row 6, column 1 (ID) in data file [filepath]```

この問題に対処するには、データを参照し、情報に基づいて決定する必要があります。 この問題の原因になっているデータを確認するには、最初にデータ型を変更する必要があります。 列 "ID" をデータ型 "SMALLINT" でクエリする代わりに、VARCHAR (100) を使用してこの問題を分析します。 この少し変更したクエリ 2 を使用すると、データを処理して名前の一覧を表示できるようになります。 

クエリ 2: 
```sql
SELECT
    TOP 100 *
FROM
    OPENROWSET(
        BULK '[FILE-PATH OF CSV FILE]',
        FORMAT = 'CSV',
        PARSER_VERSION='1.0',
       FIELDTERMINATOR =',',
       FIRSTROW = 2
    ) 
    WITH (
    [ID] VARCHAR(100), 
    [Firstname] VARCHAR (25) COLLATE Latin1_General_BIN2 
)

    AS [result]
```

names.csv
```csv
Id, first name, 
1,Adam
2,Bob
3,Charles
4,David
five,Eva
```

データの 5 行目の ID に予期しない値が含まれているようです。 このような状況では、データのビジネス所有者と、このようなデータの破損を回避する方法について同意することが重要です。 アプリケーション レベルで防止ができず、ID のすべてのデータ型を処理する必要がある場合は、ここでは適切なサイズの VARCHAR しか使用できない可能性があります。

> [!Tip]
> VARCHAR() をできるだけ短くしてみます。 パフォーマンスが低下する可能性があるため、可能な場合は VARCHAR(MAX) を避けます。 

### <a name="the-result-table-does-not-look-like-expected-result-columns-are-empty-or-unexpected-loaded"></a>結果のテーブルが予期したようにならない。 結果列が空であるか、予期せず読み込まれる。 

クエリは失敗しないが、結果のテーブルが想定どおりに読み込まれない場合は、行区切り記号またはフィールド ターミネータの選択が誤っている可能性があります。 この問題を解決するには、別のデータ検索方法に設定を変更する必要があります。 結果テーブルが表示されたら、次の例のようにこのクエリのデバッグを簡単に行うことができます。 

#### <a name="example"></a>例
このクエリ 1 でファイル "names.csv" のクエリを実行した場合、Synapse SQL サーバーレスから正しくない結果テーブルが返されます。 

names.csv
```csv
Id,first name, 
1,Adam
2,Bob
3,Charles
4,David
5,Eva
```

```sql
SELECT
    TOP 100 *
FROM
    OPENROWSET(
        BULK '[FILE-PATH OF CSV FILE]',
        FORMAT = 'CSV',
        PARSER_VERSION='1.0',
       FIELDTERMINATOR =';',
       FIRSTROW = 2
    ) 
    WITH (
    [ID] VARCHAR(100), 
    [Firstname] VARCHAR (25) COLLATE Latin1_General_BIN2 
)

    AS [result]
```

この結果テーブルが発生します

| id            |   firstname   | 
| ------------- |-------------  | 
| 1,Adam        | NULL | 
| 2,Bob         | NULL | 
| 3,Charles     | NULL | 
| 4,David       | NULL | 
| 5,Eva         | NULL | 

列 "firstname" に値がないようです。 代わりに、すべての値が列 "ID" に含まれます。 それらの値は、コンマで区切られています。 この問題は、フィールド ターミネータとしてセミコロン記号の代わりにコンマを選択する必要があるコード行によって発生しました。

```sql
FIELDTERMINATOR =';',
```

この 1 文字を変更すると、問題が解決します。

```sql
FIELDTERMINATOR =',',
```

クエリ 2 によって作成される結果テーブルは、想定したようになります。 

クエリ 2:
```sql
SELECT
    TOP 100 *
FROM
    OPENROWSET(
        BULK '[FILE-PATH OF CSV FILE]',
        FORMAT = 'CSV',
        PARSER_VERSION='1.0',
       FIELDTERMINATOR =',',
       FIRSTROW = 2
    ) 
    WITH (
    [ID] VARCHAR(100), 
    [Firstname] VARCHAR (25) COLLATE Latin1_General_BIN2 
)

    AS [result]
``` 

は、次の値は返します。

| id            |   firstname   | 
| ------------- |-------------  | 
| 1        | Adam | 
| 2         | Bob | 
| 3     | Charles | 
| 4       | David | 
| 5         | Eva | 


### <a name="query-fails-with-error-column-column-name-of-type-type-name-is--not-compatible-with-external-data-type-external-data-type-name"></a>次のエラーでクエリが失敗する: 型 <型名> の列 <列名> は外部データ型 <外部データ型名> と互換性がありません 

"型 <型名> の列 <列名> は外部データ型 <外部データ型名> と互換性がありません" というエラー メッセージでクエリが失敗する場合は、PARQUET データ型を間違った SQL データ型にマップしようとした可能性があります。 たとえば、parquet ファイルに浮動小数点数 (12.89 など) の価格の列があり、それを INT にマップしようとした場合、このエラー メッセージが表示されます。 

これを解決するには、ファイルと選択したデータ型を調べます。 こちらの[マッピング テーブル](develop-openrowset.md#type-mapping-for-parquet)は、SQL データ型の選択に役立ちます。 ベスト プラクティスのヒント: それ以外の場合は VARCHAR データ型に解決される列のマッピングのみを指定します。 可能な場合は VARCHAR を回避すると、クエリのパフォーマンスが向上します。 

#### <a name="example"></a>例
このクエリ 1 でファイル "taxi-data.parquet" のクエリを実行した場合、Synapse SQL サーバーレスからこのようなエラーが返されます。

taxi-data.parquet:

|PassengerCount |SumTripDistance|AvgTripDistance |
|---------------|---------------|----------------|
| 1 | 2635668.66000064 | 6.72731710678951 |
| 2 | 172174.330000005 | 2.97915543404919 |
| 3 | 296384.390000011 | 2.8991352022851  |
| 4 | 12544348.58999806| 6.30581582240281 |
| 5 | 13091570.2799993 | 111.065989028627 |

クエリ 1: 
```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK '<filepath>taxi-data.parquet',
        FORMAT='PARQUET'
    )  WITh
        (
        PassengerCount INT, 
        SumTripDistance INT, 
        AVGTripDistance FLOAT
        )

    AS [result]
```
このエラーの原因: 

```Column 'SumTripDistance' of type 'INT' is not compatible with external data type 'Parquet physical type: DOUBLE', please try with 'FLOAT'. File/External table name: '<filepath>taxi-data.parquet'.```

このエラー メッセージは、データ型に互換性がないことを示し、INT ではなく FLOAT を使用することが既に提案されています。 したがって、エラーは次のコード行で発生します。 

```sql
SumTripDistance INT, 
```

この少し変更したクエリ 2 を使用すると、データを処理して 3 つの列をすべて表示できるようになります。 

クエリ 2: 
```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK '<filepath>taxi-data.parquet',
        FORMAT='PARQUET'
    )  WITh
        (
        PassengerCount INT, 
        SumTripDistance FLOAT, 
        AVGTripDistance FLOAT
        )

    AS [result]
```

## <a name="configuration"></a>構成

### <a name="query-fails-with-please-create-a-master-key-in-the-database-or-open-the-master-key-in-the-session-before-performing-this-operation"></a>クエリが次のエラーで失敗する: この操作を実行するには、マスター キーをデータベースに作成するか、またはセッション内のマスター キーを開いてください。

"この操作を実行するには、マスター キーをデータベースに作成するか、またはセッション内のマスター キーを開いてください" というエラー メッセージでクエリが失敗する場合は、ユーザー データベースが現時点でマスター キーにアクセスできないことを意味します。 

ほとんどの場合、新しいユーザー データベースだけを作成し、マスター キーをまだ作成していません。 

これを解決するには、次のクエリを使用してマスター キーを作成します。

```sql
CREATE MASTER KEY [ ENCRYPTION BY PASSWORD ='password' ];
```

> [!NOTE]
> ここで、"password" は別のシークレットに置き換えます。 

### <a name="create-statement-is-not-supported-in-master-database"></a>CREATE ステートメントがマスター データベースでサポートされていない

次のエラー メッセージが表示され、クエリが失敗します。

> "クエリを実行できませんでした。 エラー:CREATE EXTERNAL TABLE/DATA SOURCE/DATABASE SCOPED CREDENTIAL/FILE FORMAT はマスター データベースでサポートされていません" 

つまり、サーバーレス SQL プールのマスター データベースでは次を作成できません。
  - 外部テーブル
  - 外部データ ソース
  - データベース スコープ資格情報
  - 外部ファイル形式

解決方法:

  1. ユーザー データベースの作成:

```sql
CREATE DATABASE <DATABASE_NAME>
```

  2. マスター データベースについて前に失敗した <DATABASE_NAME> のコンテキストで CREATE ステートメントを実行します。 
  
  外部ファイル形式の作成例:
    
```sql
USE <DATABASE_NAME>
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] 
WITH ( FORMAT_TYPE = PARQUET)
```

### <a name="operation-is-not-allowed-for-a-replicated-database"></a>操作はレプリケートされたデータベースでは許可されていません。
   
データベースで SQL オブジェクトやユーザーを作成したり、アクセス許可を変更したりしようとしている場合、"操作 CREATE USER はレプリケートされたデータベースでは許可されていません" のようなエラーが発生する可能性があります。 このエラーは、[Spark プールと共有されている](../metadata/database.md)データベースでオブジェクトを作成しようとすると返されます。 Apache Spark プールからレプリケートされたデータベースは読み取り専用です。 レプリケートされたデータベースに T-SQL を使用して新しいオブジェクトを作成することはできません。

別のデータベースを作成し、3 部構成の名前とデータベース間クエリを使用して、同期された[テーブル](../metadata/table.md)を参照します。

## <a name="cosmos-db"></a>Cosmos DB

### <a name="some-rows-are-not-returned"></a>一部の行が返されない

- トランザクション ストアと分析ストアの間には同期遅延があります。 Cosmos DB のトランザクション ストアに入力したドキュメントは、分析ストアに表示されるまでに 2 から 3 分かかる場合があります。
- ドキュメントが[スキーマ制約](../../cosmos-db/analytical-store-introduction.md#schema-constraints)に違反している可能性があります。 

### <a name="query-returns-null-values"></a>クエリから `NULL` 値が返される

Synapse SQL では、次の場合、トランザクション ストアにある値の代わりに `NULL` が返されます。
- トランザクション ストアと分析ストアの間には同期遅延があります。 Cosmos DB のトランザクション ストアに入力した値は、分析ストアに表示されるまでに 2 から 3 分かかる場合があります。
- `WITH` 句の列名またはパス式が間違っている可能性があります。 `WITH` 句の列名 (または列の型の後のパス式) は Cosmos DB コレクションのプロパティ名と一致する必要があります。 比較では、大文字と小文字が区別されます (たとえば、`productCode` と `ProductCode` は異なるプロパティです)。 列名が Cosmos DB のプロパティ名と完全に一致することを確認します。
- 1,000 を超えるプロパティや 127 を超える入れ子レベルなど、一部の[スキーマ制約](../../cosmos-db/analytical-store-introduction.md#schema-constraints)に違反している場合、プロパティが分析ストレージに移動されない可能性があります。
- 適切に定義された[スキーマ表現](../../cosmos-db/analytical-store-introduction.md#schema-representation)を使用している場合、トランザクション ストア内の値の型が正しくない可能性があります。 適切に定義されたスキーマにより、ドキュメントをサンプリングすることで、各プロパティの型がロックされます。 トランザクション ストアに追加された、型が一致しない値は、間違った値として扱われ、分析ストアに移行されません。 
- 完全に忠実な[スキーマ表現](../../cosmos-db/analytical-store-introduction.md#schema-representation)を使用している場合は、プロパティ名の後に型のサフィックスを追加していることを確認します (例: `$.price.int64`)。 参照先のパスに値が表示されない場合は、異なる型のパス (例: `$.price.float64`) の下に格納されている可能性があります。 [完全に忠実なスキーマで Cosmos DB コレクションのクエリを実行する方法](query-cosmos-db-analytical-store.md#query-items-with-full-fidelity-schema)に関する記事を参照してください。

### <a name="column-is-not-compatible-with-external-data-type"></a>列に外部データ型との互換性がない

`WITH` 句で指定されている値が分析ストレージ内の基になる Cosmos DB の型と一致せず、暗黙的に変換できません。 スキーマで `VARCHAR` 型を使用してください。

### <a name="performance-issues"></a>パフォーマンスの問題

予期しないパフォーマンスの問題が発生している場合は、次のようなベスト プラクティスを適用していることを確認してください。
- クライアント アプリケーション、サーバーレス プール、および Cosmos DB 分析ストレージを[同じリージョン](best-practices-serverless-sql-pool.md#colocate-your-cosmosdb-analytical-storage-and-serverless-sql-pool)に配置したことを確認する。
- [最適なデータ型](best-practices-serverless-sql-pool.md#use-appropriate-data-types)で `WITH` 句を使用していることを確認する。
- 文字列述語を使ってデータをフィルター処理するときに、[Latin1_General_100_BIN2_UTF8 照合順序](best-practices-serverless-sql-pool.md#use-proper-collation-to-utilize-predicate-pushdown-for-character-columns)を使用していることを確認する。
- キャッシュされる可能性があるクエリを繰り返している場合は、[クエリ結果を Azure Data Lake Storage に格納するために CETAS](best-practices-serverless-sql-pool.md#use-cetas-to-enhance-query-performance-and-joins) を使用してみる。

## <a name="delta-lake"></a>Delta Lake

Delta Lake のサポートは、現在、サーバーレス SQL プールでのパブリック プレビュー中です。 プレビュー中に発生する可能性がある既知の問題があります。
- [OPENROWSET](./develop-openrowset.md) 関数または外部テーブルの場所で、ルート Delta Lake フォルダーを参照していることを確認します。
  - ルート フォルダーには、`_delta_log` という名前のサブフォルダーが必要です。 `_delta_log` フォルダーがない場合、クエリは失敗します。 そのフォルダーがない場合は、Apache Spark プールを使用して [Delta Lake に変換する](../spark/apache-spark-delta-lake-overview.md?pivots=programming-language-python#convert-parquet-to-delta)必要があるプレーンな Parquet ファイルを参照しています。
  - パーティション スキーマを記述するためにワイルドカードを指定しないでください。 Delta Lake パーティションは、Delta Lake クエリによって自動的に識別されます。 
- Apache Spark プールで作成された Delta Lake テーブルは、サーバーレス SQL プールでは同期されません。 T-SQL 言語を使用して、Apache Spark プールの Delta Lake テーブルのクエリを実行することはできません。
- 外部テーブルでは、パーティション分割はサポートされていません。 パーティションの除去を利用するには、Delta Lake フォルダーの[パーティション分割されたビュー](create-use-views.md#delta-lake-partitioned-views)を使用します。 以下の既知の問題と回避策を参照してください。
- サーバーレス SQL プールでは、タイム トラベル クエリはサポートされていません。 [Azure フィードバック サイト](https://feedback.azure.com/forums/307516-azure-synapse-analytics/suggestions/43656111-add-time-travel-feature-in-delta-lake)でこの機能に投票することができます
- サーバーレス SQL プールでは、Delta Lake ファイルの更新はサポートされていません。 サーバーレス SQL プールを使用して、最新バージョンの Delta Lake のクエリを実行できます。 [Delta Lake の更新](../spark/apache-spark-delta-lake-overview.md?pivots=programming-language-python#update-table-data)または[履歴データの読み取り](../spark/apache-spark-delta-lake-overview.md?pivots=programming-language-python#read-older-versions-of-data-using-time-travel)には、Azure Synapse Analytics で Apache Spark プールを使用します。
- Delta Lake のサポートは、専用 SQL プールでは使用できません。 Delta Lake ファイルのクエリにはサーバーレス プールを使用していることを確認してください。

[Azure Synapse フィードバック サイト](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=171048)でアイデアや機能強化を提案できます。

### <a name="content-of-directory-on-path-cannot-be-listed"></a>パス上のディレクトリの内容を一覧表示できない

サーバーレス SQL プールで Delta Lake トランザクション ログ フォルダーを読み取ることができない場合、次のエラーが返されます。

```
Msg 13807, Level 16, State 1, Line 6
Content of directory on path 'https://.....core.windows.net/.../_delta_log/*.json' cannot be listed.
```

`_delta_log` フォルダーが存在することを確認します (Delta Lake 形式に変換されていないプレーンな Parquet ファイルのクエリを実行している可能性があります)。

`_delta_log` フォルダーが存在する場合は、基になる Delta Lake フォルダーに対する読み取りおよび一覧表示の両方のアクセス許可を持っていることを確認します。
FORMAT='CSV' を使用して直接 \*.json ファイルを読み取ってみます (URI を BULK パラメーターに含める)。

```sql
select top 10 * 
from openrowset(BULK 'https://.....core.windows.net/.../_delta_log/*.json', 
FORMAT='csv', FIELDQUOTE = '0x0b', FIELDTERMINATOR ='0x0b', ROWTERMINATOR = '0x0b') with (line varchar(max)) as logs
```

このクエリが失敗した場合、呼び出し元には、基になるストレージ ファイルを読み取るためのアクセス許可がありません。 

最も簡単な方法は、クエリの対象となるストレージ アカウントに対する "ストレージ BLOB データ共同作成者" ロールを自分に付与することです。 
- [詳細については、ストレージの Azure Active Directory アクセス制御に関する完全なガイドを参照してください](../../storage/common/storage-auth-aad-rbac-portal.md)。 
- [「Azure Synapse Analytics でサーバーレス SQL プールのストレージ アカウント アクセスを制御する」を参照してください](develop-storage-files-storage-access-control.md)。

### <a name="partitioning-column-returns-null-values"></a>パーティション分割列から NULL 値が返される

パーティション分割された Delta Lake フォルダーを読み取る `OPENROWSET` 関数でビューを使用している場合は、パーティション分割列の実際の列値の代わりに `NULL` 値を取得することがあります。 既知の問題により、`WITH` 句を持つ `OPENROWSET` 関数ではパーティション分割列を読み取ることができません。 Delta Lake の[パーティション分割されたビュー](create-use-views.md#delta-lake-partitioned-views)には、`WITH` 句を含む `OPENROWSET` 関数があってはなりません。 スキーマを明示的に指定しない `OPENROWSET` 関数を使用する必要があります。

**回避策:** ビューで使用される `OPENROWSET` 関数から `WITH` 句を削除します。

### <a name="query-failed-because-of-a-topology-change-or-compute-container-failure"></a>トポロジの変更またはコンピューティング コンテナーの障害が原因でクエリが失敗する

データベースの照合順序が `Latin1_General_100_BIN2_UTF8` ではない場合、パーティション分割されたデータ セットに対する Delta Lake クエリが、このエラー メッセージで失敗する可能性があります。 `Latin1_General_100_BIN2_UTF8` の照合順序でデータベースを作成し、既定の照合順序を持つ master データベースや他のデータベースではなく、そのデータベースに対してクエリを実行します。

```sql
CREATE DATABASE mydb 
    COLLATE Latin1_General_100_BIN2_UTF8;
```

master データベースを介して実行されるクエリは、この問題の影響を受けます。

**回避策:** `Latin1_General_100_BIN2_UTF8` データベース照合順序を持つカスタム データベースに対してクエリを実行します。

### <a name="column-of-type-varchar-is-not-compatible-with-external-data-type-parquet-column-is-of-nested-type"></a>"VARCHAR" 型の列は、外部データ型 "Parquet 列が入れ子にされた型" と互換性がありません

WITH 句を指定せずに (自動スキーマ推論を使用)、入れ子になった型の列が含まれる Delta Lake ファイルを読み取ろうとしています。 スキーマの自動推論は、Delta Lake の入れ子になった列では機能しません。

**回避策**: `WITH` 句を使用し、入れ子になった列に `VARCHAR` 型を明示的に割り当てます。

### <a name="cannot-find-value-of-partitioning-column-in-file"></a>ファイルのパーティション分割列の値が見つからない 

Delta Lake データ セットのパーティション分割列に、`NULL` 値がある可能性があります。 これらのパーティションは `HIVE_DEFAULT_PARTITION` フォルダーに格納されます。 現在、これはサーバーレス SQL プールではサポートされていません。 この場合、次のようなエラーが表示されます。

```
Resolving Delta logs on path 'https://....core.windows.net/.../' failed with error:
Cannot find value of partitioning column '<column name>' in file 
'https://......core.windows.net/...../<column name>=__HIVE_DEFAULT_PARTITION__/part-00042-2c0d5c0e-8e89-4ab8-b514-207dcfd6fe13.c000.snappy.parquet'.
```

**回避策:** Apache Spark プールを使用して Delta Lake データ セットを更新し、パーティション分割列の `"null"` ではなく、何らかの値 (空の文字列または `null`) を使用してみてください。

## <a name="constraints"></a>制約

ワークロードに影響する可能性があるいくつかの一般的なシステム制約があります。

| プロパティ | 制限事項 |
|---|---|
| サブスクリプションあたりの Synapse ワークスペースの最大数 | 20 |
| サーバーレス プールあたりのデータベースの最大数 | 20 (Apache Spark プールから同期されたデータベースを含まない) |
| Apache Spark プールから同期されたデータベースの最大数 | 制限なし |
| データベースあたりのデータベース オブジェクトの最大数 | データベース内のすべてのオブジェクトの合計数は 2,147,483,647 を超えることはできません ([SQL Server データベース エンジンでの制限事項](/sql/sql-server/maximum-capacity-specifications-for-sql-server#objects)に関するページを参照) |
| 識別子の最大長 (文字数) | 128 ([SQL Server データベース エンジンでの制限事項](/sql/sql-server/maximum-capacity-specifications-for-sql-server#objects)に関するページを参照)|
| 最大クエリ期間 | 30 分 |
| 結果セットの最大サイズ | 80 GB (現在実行中のすべての同時クエリ間で共有) |
| 最大コンカレンシー | 制限はなく、クエリの複雑さとスキャンされたデータの量によって異なります。 1 つのサーバーレス SQL プールで、軽量クエリを実行している 1,000 個のアクティブ セッションを同時に処理できますが、クエリがより複雑な場合、または大量のデータをスキャンする場合は数が減少します。 |

## <a name="next-steps"></a>次のステップ

サーバーレス SQL プールの使用方法について詳しくは、次の記事を参照してください。

- [単一の CSV ファイルに対するクエリを実行する](query-single-csv-file.md)

- [フォルダーと複数の CSV ファイルに対してクエリを実行する](query-folders-multiple-csv-files.md)

- [特定のファイルに対してクエリを実行する](query-specific-files.md)

- [Parquet ファイルに対してクエリを実行する](query-parquet-files.md)

- [Parquet の入れ子にされた型に対してクエリを実行する](query-parquet-nested-types.md)

- [JSON ファイルに対するクエリを実行する](query-json-files.md)

- [ビューの作成と使用](create-use-views.md)

- [外部テーブルの作成と使用](create-use-external-tables.md)

- [クエリ結果をストレージに格納する](create-external-table-as-select.md)
