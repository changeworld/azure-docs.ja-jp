---
title: SQL オンデマンド (プレビュー) を使用して CSV ファイルに対してクエリを実行する
description: この記事では、SQL オンデマンド (プレビュー) を使用して、さまざまなファイル形式の単一の CSV ファイルに対してクエリを実行する方法について説明します。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 41c4a8940cc49a3859a2511f0de65d0019817078
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836551"
---
# <a name="query-csv-files"></a>CSV ファイルに対してクエリを実行する

この記事では、Azure Synapse Analytics の SQL オンデマンド (プレビュー) を使用して、単一の CSV ファイルに対してクエリを実行する方法について説明します。 CSV ファイルには、次のようなさまざまな形式があります。 

- ヘッダー行を含む、ヘッダー行を含まない
- コンマ区切りの値およびタブ区切りの値
- Windows スタイルおよび Unix スタイルの行の終わり
- 引用符で囲まれていない値、引用符で囲まれた値、エスケープ文字

上記のすべてのバリエーションについては、以下で説明します。

## <a name="prerequisites"></a>前提条件

最初の手順として、テーブルが作成される**データベースを作成**します。 次に、そのデータベースで[セットアップ スクリプト](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)を実行して、オブジェクトを初期化します。 このセットアップ スクリプトにより、データ ソース、データベース スコープの資格情報、これらのサンプルで使用される外部ファイル形式が作成されます。

## <a name="windows-style-new-line"></a>Windows スタイルの改行

次のクエリは、CSV ファイル (ヘッダー行がなく、Windows スタイルの改行およびコンマ区切りの列を含む) を読み取る方法を示しています。

ファイル プレビュー:

![ヘッダーがなく、Windows スタイルの改行を含む CSV ファイルの最初の 10 行。](./media/query-single-csv-file/population.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="unix-style-new-line"></a>Unix スタイルの改行

次のクエリは、CSV ファイル (ヘッダー行がなく、Unix スタイルの改行およびコンマ区切りの列を含む) を読み取る方法を示しています。 他の例と比較して、ファイルの場所が異なることに注意してください。

ファイル プレビュー:

![ヘッダー行がなく Unix スタイルの改行を含む CSV ファイルの最初の 10 行。](./media/query-single-csv-file/population-unix.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="header-row"></a>ヘッダー行

次のクエリは、CSV ファイル (ヘッダー行があり、Unix スタイルの改行およびコンマ区切りの列を含む) を読み取る方法を示しています。 他の例と比較して、ファイルの場所が異なることに注意してください。

ファイル プレビュー:

![ヘッダー行があり、Unix スタイルの改行を含む CSV ファイルの最初の 10 行。](./media/query-single-csv-file/population-unix-hdr.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

## <a name="custom-quote-character"></a>カスタム引用符文字

次のクエリは、ファイル (ヘッダー行があり、Unix スタイルの改行、コンマ区切りの列、および引用符で囲まれた値を含む) を読み取る方法を示しています。 他の例と比較して、ファイルの場所が異なることに注意してください。

ファイル プレビュー:

![ヘッダー行があり、Unix スタイルの改行と引用符で囲まれた値を含む CSV ファイルの最初の 10 行。](./media/query-single-csv-file/population-unix-hdr-quoted.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-quoted/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        FIELDQUOTE = '"'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017;
```

> [!NOTE]
> FIELDQUOTE の既定値は二重引用符であるため、このクエリでは、FIELDQUOTE パラメーターを省略した場合と同じ結果が返されます。

## <a name="escaping-characters"></a>文字のエスケープ

次のクエリは、ファイル (ヘッダー行があり、Unix スタイルの改行、コンマ区切りの列、および値内のフィールド区切り記号 (コンマ) に使用されるエスケープ文字を含む) を読み取る方法を示しています。 他の例と比較して、ファイルの場所が異なることに注意してください。

ファイル プレビュー:

![ヘッダー行があり、Unix スタイルの改行と、フィールド区切り記号に使用されるエスケープ文字を含む CSV ファイルの最初の 10 行。](./media/query-single-csv-file/population-unix-hdr-escape.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-escape/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2,
        ESCAPECHAR = '\\'
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Slov,enia';
```

> [!NOTE]
> ESCAPECHAR が指定されていない場合、このクエリは失敗します。"Slov,enia" 内のコンマが、国/地域名の一部ではなく、フィールド区切り記号として扱われることがその理由です。 "Slov,enia" は 2 つの列として扱われます。 そのため、特定の行には、他の行よりも列が 1 つ多く存在し、WITH 句で定義した列数より 1 つ多くなります。

## <a name="tab-delimited-files"></a>タブ区切りファイル

次のクエリは、ファイル (ヘッダー行があり、Unix スタイルの改行およびタブ区切りの列を含む) を読み取る方法を示しています。 他の例と比較して、ファイルの場所が異なることに注意してください。

ファイル プレビュー:

![ヘッダー行があり、Unix スタイルの改行およびタブ区切り記号を含む CSV ファイルの最初の 10 行。](./media/query-single-csv-file/population-unix-hdr-tsv.png)

```sql
SELECT *
FROM OPENROWSET(
        BULK 'csv/population-unix-hdr-tsv/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        FIELDTERMINATOR ='\t',
        ROWTERMINATOR = '0x0a',
        FIRSTROW = 2
    )
    WITH (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    ) AS [r]
WHERE
    country_name = 'Luxembourg'
    AND year = 2017
```

## <a name="returning-subset-of-columns"></a>列のサブセットを返す

ここまで、WITH を使用し、すべての列をリストして CSV ファイル スキーマを指定してきました。 クエリにおいて実際に必要な列だけを指定するには、必要な各列に対して序数を使用します。 また、関心のない列も省略します。

次のクエリでは、ファイル内の異なる国/地域名の数が返されます。これにより必要な列のみを指定できます。

> [!NOTE]
> 次のクエリ内の WITH 句を見てください。 *[country_name]* 列を定義した行の末尾に "2" (引用符はなし) があることがわかります。 これは、 *[country_name]* 列がファイル内の 2 番目の列であることを意味します。 このクエリでは、2 番目の列を除く、ファイル内のすべての列が無視されます。

```sql
SELECT
    COUNT(DISTINCT country_name) AS countries
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        FIELDTERMINATOR =',',
        ROWTERMINATOR = '\n'
    )
WITH (
    --[country_code] VARCHAR (5),
    [country_name] VARCHAR (100) 2
    --[year] smallint,
    --[population] bigint
) AS [r]
```

## <a name="next-steps"></a>次のステップ

次の記事では、次の方法について説明します。

- [Parquet ファイルに対するクエリ](query-parquet-files.md)
- [フォルダーと複数のファイルに対するクエリ](query-folders-multiple-csv-files.md)
