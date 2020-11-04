---
title: Azure Synapse Link (プレビュー) でサーバーレス SQL プールを使用して Azure Cosmos DB データのクエリを実行する
description: この記事では、Azure Synapse Link (プレビュー) で SQL オンデマンドを使用して、Azure Cosmos DB のクエリを実行する方法について説明します。
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 09/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 2b1af6fa5b0ccb95476c4ae169481e4aaa15f4f9
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92737834"
---
# <a name="query-azure-cosmos-db-data-with-serverless-sql-pool-in-azure-synapse-link-preview"></a>Azure Synapse Link (プレビュー) でサーバーレス SQL プールを使用して Azure Cosmos DB データのクエリを実行する

Synapse サーバーレス SQL プールを使用すると、トランザクション ワークロードのパフォーマンスに影響を与えることなく、[Azure Synapse Link](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) で有効になっている Azure Cosmos DB コンテナー内のデータをほぼリアルタイムで分析できます。 T-SQL インターフェイスを使用して[分析ストア](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)および統合された接続からさまざまな BI やアドホック クエリ ツールへのデータのクエリを実行するために、使い慣れた T-SQL 構文が用意されています。

Azure Cosmos DB のクエリを実行する場合、[SQL の関数や演算子](overview-features.md)の大部分を含め、完全な [SELECT](/sql/t-sql/queries/select-transact-sql?view=sql-server-ver15) のセキュリティが、[OPENROWSET](develop-openrowset.md) 関数によってサポートされます。 また、Azure Blob Storage または Azure Data Lake Storage のデータと共に Azure Cosmos DB からデータを読み取るクエリの結果を、[create external table as select](develop-tables-cetas.md#cetas-in-sql-on-demand) を使用して格納することもできます。 現在は、[CETAS](develop-tables-cetas.md#cetas-in-sql-on-demand) を使用して、サーバーレス SQL プールのクエリの結果を Azure Cosmos DB に格納することはできません。

この記事では、Synapse Link が有効になっている Azure Cosmos DB コンテナーのデータのクエリを実行する、サーバーレス SQL プールを使用するクエリの作成方法について説明します。 その後は、[この](./tutorial-data-analyst.md)チュートリアルで、Azure Cosmos DB コンテナーに対するサーバーレス SQL プールのビューを構築し、それらを Power BI モデルに接続することの詳細を学習できます。 

> [!IMPORTANT]
> このチュートリアルでは、[Azure Cosmos DB の適切に定義されたスキーマ](../../cosmos-db/analytical-store-introduction.md#schema-representation)を持つコンテナーを使用します。 [Azure Cosmos DB の完全な忠実性スキーマ](#full-fidelity-schema)で利用できるサーバーレス SQL プールがもたらすクエリ エクスペリエンスは、プレビューのフィードバックに基づいて変更される一時的な動作です。 クエリ エクスペリエンスは変更され適切に定義されたスキーマに合わせて調整されている可能性があるため、完全に忠実なスキーマを持つコンテナーからデータを読み取る `WITH` 句のない `OPENROWSET` 関数の結果セット スキーマに依存しないでください。 [Azure Synapse Analytics のフィードバック フォーラム](https://feedback.azure.com/forums/307516-azure-synapse-analytics)にフィードバックを投稿いただくか、[Synapse Link の製品チーム](mailto:cosmosdbsynapselink@microsoft.com)宛にフィードバックをお寄せください。

## <a name="overview"></a>概要

Azure Cosmos DB 分析ストア内のデータのクエリと分析をサポートするため、サーバーレス SQL プールでは次の `OPENROWSET` 構文を使用します。

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

Azure Cosmos DB の接続文字列では、Azure Cosmos DB のアカウント名、データベース名、データベース アカウント マスター キー、および `OPENROWSET` 関数に対するオプションのリージョン名を指定します。 

> [!IMPORTANT]
> `OPENROWSET` の後には必ず別名を使用してください。 `OPENROWSET` 関数の後に別名を指定しなかった場合、Synapse サーバーレス SQL エンドポイントに接続の問題が発生する[既知の問題](#known-issues)があります。

接続文字列は次のような形式です。
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

`OPENROWSET` の構文では、引用符を使用しないで Azure Cosmos DB のコンテナー名を指定します。 コンテナー名に特殊文字 (ダッシュ "-" など) が含まれている場合、`OPENROWSET` 構文では名前を `[]` (角かっこ) で囲む必要があります。

> [!NOTE]
> サーバーレス SQL プールでは、Azure Cosmos DB トランザクション ストアのクエリはサポートされていません。

## <a name="sample-data-set"></a>サンプル データ セット

この記事の例は、[European Centre for Disease Prevention and Control (ECDC) COVID-19 Cases](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) および [COVID-19 Open Research Dataset (CORD-19), doi:10.5281/zenodo.3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) のデータに基づいています。 

これらのページでライセンスとデータの構造を確認でき、[ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) と [Cord19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) のデータ セットのサンプル データをダウンロードすることができます。

サーバーレス SQL プールを使用して Cosmos DB データのクエリを実行する方法を示すこの記事の内容をたどるには、以下のリソースを作成してください。
* [Synapse Link が有効にされている](../../cosmos-db/configure-synapse-link.md) Azure Cosmos DB データベース アカウント
* `covid` という名前の Azure Cosmos DB データベース
* 上記のサンプル データ セットが読み込まれた、`EcdcCases` および `Cord19` という名前の 2 つの Azure Cosmos DB コンテナー。

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>自動スキーマ推論を使用して Azure Cosmos DB のデータを探索する

Azure Cosmos DB のデータを探索する最も簡単な方法は、自動スキーマ推論機能を利用することです。 `OPENROWSET` ステートメントから `WITH` 句を省略することにより、Azure Cosmos DB コンテナーの分析ストアのスキーマを自動検出 (推論) するように、サーバーレス SQL プールに指示できます。

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases) as documents
```
上の例では、Azure Cosmos DB キー (上記の例ではダミー) を使用して認証された Azure Cosmos DB アカウント `MyCosmosDbAccount` の `covid` データベースに接続するように、サーバーレス SQL プールに指示しています。 その後、`West US 2` リージョンのコンテナー `EcdcCases` の分析ストアにアクセスします。 特定のプロパティのプロジェクションはないため、`OPENROWSET` 関数からは、Azure Cosmos DB の項目のすべてのプロパティが返されます。 

Cosmos DB コンテナー内の項目に `date_rep`、`cases`、および `geo_id` プロパティがあるという前提の下、このクエリの結果を次の表に示します。

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

同じ Azure Cosmos DB データベース内の他のコンテナーのデータを探索する必要がある場合は、同じ接続文字列を使用し、3 番目のパラメーターとして必要なコンテナーを参照することができます。

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>スキーマを明示的に指定する

`OPENROWSET` の自動スキーマ推論機能では、簡単で使いやすいクエリが提供されますが、ビジネス シナリオでは、Azure Cosmos DB データの関連する読み取り専用プロパティに対するスキーマの明示的指定が必要な場合があります。

`OPENROWSET` を使用すると、コンテナー内のデータから読み取るプロパティを明示的に指定したり、データ型を指定したりすることができます。 次のような構造の [ECDC COVID データ セット](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/)から、一部のデータを Azure Cosmos DB にインポートしたとします。

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Azure Cosmos DB のこのようなフラットな JSON ドキュメントは、Synapse SQL では行と列のセットとして表すことができます。 `OPENROWSET` 関数を使用すると、読み取り対象のプロパティのサブセットと列の厳密な型を、`WITH` 句で指定できます。

```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

このクエリの結果は次のようになります。

| date_rep | cases | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Azure Cosmos DB の値に使用する必要がある SQL 型の詳細については、記事の最後にある [SQL 型のマッピングの規則](#azure-cosmos-db-to-sql-type-mappings)を参照してください。

## <a name="querying-nested-objects-and-arrays"></a>入れ子になったオブジェクトと配列のクエリ

Azure Cosmos DB を使用すると、入れ子になったオブジェクトまたは配列として構成することで、より複雑なデータ モデルを表すことができます。 Azure Cosmos DB に対する Synapse Link の自動同期機能により、何もしなくても分析ストアでのスキーマ表現が管理されます。これには、サーバーレス SQL プールからの高度なクエリを可能にする、入れ子になったデータ型の処理が含まれます。

たとえば、[CORD-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) データ セットには、次の構造に従った JSON ドキュメントが含まれます。

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

Azure Cosmos DB での入れ子になったオブジェクトと配列は、`OPENROWSET` 関数で読み取られるときは、クエリ結果内の JSON 文字列として表されます。 これらの複合型から SQL 列として値を読み取る 1 つのオプションは、SQL JSON 関数を使用することです。

```sql
SELECT
    title = JSON_VALUE(metadata, '$.title'),
    authors = JSON_QUERY(metadata, '$.authors'),
    first_author_name = JSON_VALUE(metadata, '$.authors[0].first')
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( metadata varchar(MAX) ) AS docs;
```

このクエリの結果は次のようになります。

| title | 作成者 | first_autor_name |
| --- | --- | --- |
| Supplementary Information An eco-epidemi… |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien |  

別のオプションとして、`WITH` 句を使用するときに、オブジェクト内の入れ子になった値へのパスを指定することもできます。

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( title varchar(1000) '$.metadata.title',
           authors varchar(max) '$.metadata.authors'
    ) AS docs;
```

詳細については、[Synapse Link での複雑なデータ型](../how-to-analyze-complex-schema.md)の分析に関するページと、[サーバーレス SQL プールでの入れ子構造](query-parquet-nested-types.md)の分析に関するページを参照してください。

> [!IMPORTANT]
> `Mélade` ではなく `MÃƒÂ©lade` のような予期しない文字がテキストで表示される場合は、データベースの照合順序が [UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) の照合順序に設定されていません。 
> `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` のような SQL ステートメントを使用して、いずれかの UTF8 照合順序に[データベースの照合順序を変更します](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation)。

## <a name="flattening-nested-arrays"></a>入れ子になった配列のフラット化

Azure Cosmos DB のデータには、[Cord19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) データ セットの authors 配列のように、入れ子になったサブ配列が存在する場合があります。

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

場合によっては、最上位の項目 (メタデータ) のプロパティを、配列 (authors) のすべての要素と "結合" することが必要になります。 サーバーレス SQL プールを使用すると、入れ子になった配列に対して `OPENJSON` 関数を適用することで、入れ子構造をフラット化できます。

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

このクエリの結果は次のようになります。

| title | 作成者 | first | last | affiliation |
| --- | --- | --- | --- | --- |
| Supplementary Information An eco-epidemi… |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Supplementary Information An eco-epidemi… | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 4# |`{"laboratory":"","institution":"U…` | 
| Supplementary Information An eco-epidemi… |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Supplementary Information An eco-epidemi… |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> `Mélade` ではなく `MÃƒÂ©lade` のような予期しない文字がテキストで表示される場合は、データベースの照合順序が [UTF8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) の照合順序に設定されていません。 
> `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` のような SQL ステートメントを使用して、いずれかの UTF8 照合順序に[データベースの照合順序を変更します](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation)。

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Azure Cosmos DB から SQL 型へのマッピング

最初に、Azure Cosmos DB のトランザクション ストアはスキーマに依存しませんが、分析ストアは分析クエリのパフォーマンスを最適化するためにスキーマ化されていることに、注意することが重要です。 Synapse Link の自動同期機能により、Azure Cosmos DB では何もしなくても分析ストアでのスキーマ表現が管理されます。これには、入れ子になったデータ型の処理が含まれます。 サーバーレス SQL プールでは分析ストアのクエリが実行されるため、Azure Cosmos DB の入力データ型を SQL データ型にマップする方法を理解することが重要です。

SQL (Core) API の Azure Cosmos DB アカウントでは、数値、文字列、ブール値、null、入れ子になったオブジェクトまたは配列の JSON プロパティの型がサポートされています。 `OPENROWSET` で `WITH` 句を使用する場合は、これらの JSON 型に一致する SQL 型を選択する必要があります。 Azure Cosmos DB のさまざまなプロパティの型に対して使用する必要がある SQL 列の型については、以下を参照してください。

| Azure Cosmos DB のプロパティの型 | SQL 列の型 |
| --- | --- |
| Boolean | bit |
| Integer | bigint |
| Decimal | float |
| String | varchar (UTF8 データベース照合順序) |
| 日付と時刻 (ISO 形式の文字列) | varchar(30) |
| 日付と時刻 (UNIX タイムスタンプ) | bigint |
| [Null] | `any SQL type` 
| 入れ子になったオブジェクトまたは配列 | varchar(max) (UTF8 データベース照合順序)、JSON テキストとしてシリアル化 |

## <a name="full-fidelity-schema"></a>完全に忠実なスキーマ

Azure Cosmos DB の完全に忠実なスキーマを使用すると、コンテナー内のすべてのプロパティについて、値と最も一致する型の両方を記録できます。
完全に忠実なスキーマを持つコンテナーで `OPENROWSET` 関数を使用すると、各セルに型と実際の値の両方が提供されます。 次のクエリでは、完全に忠実なスキーマでコンテナーから項目を読み取ると仮定します。

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) as rows
```

このクエリの結果は、JSON テキストとしてフォーマットされた型と値を返します。 

| date_rep | cases | geo_id |
| --- | --- | --- |
| {"date":"2020-08-13"} | {"int32":"254"} | {"string":"RS"} |
| {"date":"2020-08-12"} | {"int32":"235"}| {"string":"RS"} |
| {"date":"2020-08-11"} | {"int32":"316"} | {"string":"RS"} |
| {"date":"2020-08-10"} | {"int32":"281"} | {"string":"RS"} |
| {"date":"2020-08-09"} | {"int32":"295"} | {"string":"RS"} |
| {"string":"2020/08/08"} | {"int32":"312"} | {"string":"RS"} |
| {"date":"2020-08-07"} | {"float64":"339.0"} | {"string":"RS"} |

すべての値について、Cosmos DB コンテナー項目で識別された型を確認できます。 `date_rep` プロパティの値のほとんどに `date` 値が含まれていますが、一部の値が Cosmos DB に誤って文字列として格納されています。 完全に忠実なスキーマによって、正しく型指定された `date` 値と正しくフォーマットされていない `string` 値の両方が返されます。
ケースの数は `int32` 値として格納される情報ですが、10 進数として入力される値が 1 つあります。 この値の型は `float64` です。 `int32` の最大値を超える値がある場合は `int64` 型として格納されます。 この例のすべての `geo_id` 値は `string` 型として格納されます。

> [!IMPORTANT]
> `WITH` 句が指定されていない `OPENROWSET` 関数によって、予期される型を持つ値と、入力された型が正しくない値の両方が公開されます。 この関数は、レポート用ではなく、データの探索用に設計されています。 明示的な [WITH 句](#querying-items-with-full-fidelity-schema)を使用してレポートを作成し、この関数から返された JSON 値を解析してレポートを作成することは避けてください。
> 完全に忠実な分析ストアで補正を適用するためには、Azure Cosmos DB コンテナー内の型が正しくない値をクリーンアップする必要があります。 

Mongo DB API の種類の Azure Cosmos DB アカウントに対してクエリを実行する場合は、分析ストア内の完全に忠実なスキーマ表現と、使用される拡張プロパティ名の詳細を、[こちら](../../cosmos-db/analytical-store-introduction.md#analytical-schema)で確認してください。

### <a name="querying-items-with-full-fidelity-schema"></a>完全に忠実なスキーマを使用して項目に対してクエリを実行する

完全に忠実なスキーマにクエリを実行する際は、`WITH` 句で SQL 型と、想定される Cosmos DB プロパティ型を明示的に指定する必要があります。 結果セットの形式がフィードバックに基づいてプレビューで変更されている可能性があるため、レポートに `WITH` 句を指定せずに `OPENROWSET` を使用しないでください。

次の例では、`string` が `geo_id` プロパティの正しい型であり、`int32` が `cases` プロパティの正しい型であると仮定します。

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

他の型を持つ `geo_id` および `cases` の値は `NULL` 値として返されます。 このクエリは、式 (`cases.int32`) で指定された型の `cases` のみを参照します。

Cosmos DB コンテナーでクリーンアップできない他の型 (`cases.int64`、`cases.float64`) を持つ値がある場合は、`WITH` 句で明示的に参照し、その結果を結合する必要があります。 次のクエリでは、`cases` 列に格納されている `int32`、`int64`、および `float64` の両方を集約します。

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

この例では、ケースの数は `int32`、`int64`、または `float64` 値として格納され、国あたりのケース数を計算するためにすべての値を抽出する必要があります。 

## <a name="known-issues"></a>既知の問題

- 別名を `OPENROWSET` 関数 (`OPENROWSET (...) AS function_alias` など) の後に指定する **必要があります** 。 別名を省略すると、接続の問題が発生して、Synapse サーバーレスの SQL エンドポイントが一時的に使用できなくなる可能性があります。 この問題は、2020 年 11 月に解決される予定です。
- [Azure Cosmos DB の完全な忠実性スキーマ](#full-fidelity-schema)で利用できるサーバーレス SQL プールがもたらすクエリ エクスペリエンスは、プレビューのフィードバックに基づいて変更される一時的な動作です。 クエリ エクスペリエンスは顧客フィードバックに基づいて適切に定義されたスキーマに合わせて調整されている可能性があるため、パブリック プレビュー期間中は、`WITH` 句のない `OPENROWSET` 関数のスキーマには依存しないでください。 フィードバックを提供するには、[Synapse Link の製品チーム](mailto:cosmosdbsynapselink@microsoft.com)宛にご連絡ください。

次の表に、考えられるエラーとトラブルシューティングの操作を示します。

| エラー | 根本原因 |
| --- | --- |
| 構文エラー:<br/> - 'Openrowset' 付近に不適切な構文があります。<br/> - `...` は、認識されている BULK OPENROWSET プロバイダー オプションではありません。<br/> - `...` 付近に不適切な構文があります。 | 考えられる根本原因<br/> - 最初のパラメーターとして 'CosmosDB' を使用していません<br/> - 3 番目のパラメーターで識別子の代わりに文字列リテラルを使用しています<br/> - 3 番目のパラメーター (コンテナー名) が指定されていません |
| CosmosDB 接続文字列でエラーが発生しました。 | - アカウント、データベース、キーが指定されていません <br/> - 接続文字列に認識されないオプションがいくつかあります<br/> - 接続文字列の末尾にセミコロン `;` が配置されています |
| CosmosDB パスを解決できませんでした。エラー: 'アカウント名が正しくありません' または 'データベース名が正しくありません' | 指定されたアカウント名、データベース名、またはコンテナーが見つからないか、指定されたコレクションで分析ストレージが有効になっていません|
| CosmosDB パスを解決できませんでした。エラー: 'シークレット値が正しくありません' または 'シークレットが null または空です' | アカウント キーが無効か、見つかりません。 |
| 型 `type name` の列 `column name` は外部データ型 `type name` と互換性がありません。 | `WITH` 句に指定された列の型が Cosmos DB コンテナーの型と一致しません。 セクション「[Azure Cosmos DB から SQL 型へのマッピング](#azure-cosmos-db-to-sql-type-mappings)」で説明されているように列の型を変更するか、または `VARCHAR` 型を使用してください。 |
| すべてのセルで、この列には `NULL` 値が含まれます。 | `WITH` 句の列名またはパス式が間違っている可能性があります。 `WITH` 句の列名 (または列の型の後のパス式) は Cosmos DB コレクションの一部のプロパティ名と一致する必要があります。 比較では、 **大文字と小文字の区別** があります (たとえば、`productCode` と `ProductCode` は異なるプロパティです)。 |

[Azure Synapse のフィードバック ページ](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862)で、提案や問題を知らせることができます。

## <a name="next-steps"></a>次のステップ

詳細については、次の記事を参照してください。

- [Azure Synapse Link で Power BI とサーバーレス Synapse SQL プールを使用する](../../cosmos-db/synapse-link-power-bi.md)
- [SQL オンデマンドでビューを作成および使用する方法](create-use-views.md) 
- [Azure Cosmos DB に対する SQL オンデマンド ビューの作成と、DirectQuery による Power BI モデルへのそれらの接続に関するチュートリアル](./tutorial-data-analyst.md)
