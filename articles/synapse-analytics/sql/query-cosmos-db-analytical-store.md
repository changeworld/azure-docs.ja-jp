---
title: Azure Synapse Link (プレビュー) で SQL オンデマンドを使用して Azure Cosmos DB のデータのクエリを実行する
description: この記事では、Azure Synapse Link (プレビュー) で SQL オンデマンドを使用して、Azure Cosmos DB のクエリを実行する方法について説明します。
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 09/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: c64a42c66a3b1c1810c17347e18979d599b36b6f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90931073"
---
# <a name="query-azure-cosmos-db-data-using-sql-on-demand-in-azure-synapse-link-preview"></a>Azure Synapse Link (プレビュー) で SQL オンデマンドを使用して Azure Cosmos DB のデータのクエリを実行する

SQL サーバーレス (以前の SQL オンデマンド) を使用すると、トランザクション ワークロードのパフォーマンスに影響を与えることなく、[Azure Synapse Link](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) で有効になっている Azure Cosmos DB コンテナー内のデータを、ほぼリアルタイムで分析できます。 T-SQL インターフェイスを使用して[分析ストア](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)および統合された接続からさまざまな BI やアドホック クエリ ツールへのデータのクエリを実行するために、使い慣れた T-SQL 構文が用意されています。

> [!NOTE]
> SQL オンデマンドを使用した Azure Cosmos DB 分析ストアのクエリのサポートは現在、限定的なプレビュー段階にあります。 

Azure Cosmos DB のクエリを実行する場合、[SQL の関数や演算子](overview-features.md)の大部分を含め、完全な [SELECT](/sql/t-sql/queries/select-transact-sql.md?view=sql-server-ver15&preserve-view=true) のセキュリティが、[OPENROWSET](develop-openrowset.md) 関数によってサポートされます。 また、Azure Blob Storage または Azure Data Lake Storage のデータと共に Azure Cosmos DB からデータを読み取るクエリの結果を、[create external table as select](develop-tables-cetas.md#cetas-in-sql-on-demand) を使用して格納することもできます。 現在は、[CETAS](develop-tables-cetas.md#cetas-in-sql-on-demand) を使用して、SQL オンデマンドのクエリの結果を Azure Cosmos DB に格納することはできません。

この記事では、SQL オンデマンドを使用して、Synapse Link が有効になっている Azure Cosmos DB コンテナーのデータを照会するクエリを作成する方法について説明します。 その後は、Azure Cosmos DB コンテナーに対する SQL オンデマンド ビューの構築と、Power BI モデルへのそれらの接続について、[こちら](./tutorial-data-analyst.md)のチュートリアルでさらに詳しく学習できます。 

## <a name="overview"></a>概要

Azure Cosmos DB 分析ストア内のデータのクエリと分析をサポートするため、SQL オンデマンドでは次の `OPENROWSET` 構文を使用します。

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ]
```

Azure Cosmos DB の接続文字列では、Azure Cosmos DB のアカウント名、データベース名、データベース アカウント マスター キー、および `OPENROWSET` 関数に対するオプションのリージョン名を指定します。 接続文字列は次のような形式です。
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

`OPENROWSET` の構文では、引用符を使用しないで Azure Cosmos DB のコンテナー名を指定します。 コンテナー名に特殊文字 (ダッシュ "-" など) が含まれている場合、`OPENROWSET` 構文では名前を `[]` (角かっこ) で囲む必要があります。

> [!NOTE]
> SQL オンデマンドでは、Azure Cosmos DB トランザクション ストアのクエリはサポートされていません。

## <a name="sample-data-set"></a>サンプル データ セット

この記事の例は、[European Centre for Disease Prevention and Control (ECDC) COVID-19 Cases](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) および [COVID-19 Open Research Dataset (CORD-19), doi:10.5281/zenodo.3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) のデータに基づいています。 

これらのページでライセンスとデータの構造を確認でき、[ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) と [Cord19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) のデータ セットのサンプル データをダウンロードすることができます。

SQL オンデマンドで Cosmos DB のデータのクエリを実行する方法を示すこの記事を理解するには、次のリソースを作成してください。
* [Synapse Link が有効にされている](../../cosmos-db/configure-synapse-link.md) Azure Cosmos DB データベース アカウント
* `covid` という名前の Azure Cosmos DB データベース
* 上記のサンプル データ セットが読み込まれた、`EcdcCases` および `Cord19` という名前の 2 つの Azure Cosmos DB コンテナー。

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>自動スキーマ推論を使用して Azure Cosmos DB のデータを探索する

Azure Cosmos DB のデータを探索する最も簡単な方法は、自動スキーマ推論機能を利用することです。 `OPENROWSET` ステートメントから `WITH` 句を省略することにより、Azure Cosmos DB コンテナーの分析ストアのスキーマを自動検出 (推論) するように、SQL オンデマンドに指示できます。

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases) as documents
```
上の例では、Azure Cosmos DB キー (上記の例ではダミー) を使用して認証された Azure Cosmos DB アカウント `MyCosmosDbAccount` の `covid` データベースに接続するように、SQL オンデマンドに命令しています。 その後、`West US 2` リージョンのコンテナー `EcdcCases` の分析ストアにアクセスします。 特定のプロパティのプロジェクションはないため、`OPENROWSET` 関数からは Azure Cosmos DB の項目のすべてのプロパティが返されます。

同じ Azure Cosmos DB データベース内の他のコンテナーのデータを探索する必要がある場合は、同じ接続文字列を使用し、3 番目のパラメーターとして必要なコンテナーを参照することができます。

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>スキーマを明示的に指定する

`OPENROWSET` の自動スキーマ推論機能では、簡単で使いやすいクエリが提供されますが、ビジネス シナリオでは、Azure Cosmos DB データから関連するプロパティのみを読み取るために、スキーマを明示的に指定することが必要になる場合があります。

`OPENROWSET` を使用すると、コンテナー内のデータから読み取るプロパティを明示的に指定したり、データ型を指定したりすることができます。 次のような構造の [ECDC COVID データ セット](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/)からデータを Azure Cosmos DB にインポートしたとします。

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

Azure Cosmos DB を使用すると、入れ子になったオブジェクトまたは配列として構成することで、より複雑なデータ モデルを表すことができます。 Azure Cosmos DB に対する Synapse Link の自動同期機能により、何もしなくても分析ストアのスキーマ表現が管理されます。これには、SQL オンデマンドからの高度なクエリを可能にする、入れ子になったデータ型の処理が含まれます。

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

詳細については、[Synapse Link での複雑なデータ型](../how-to-analyze-complex-schema.md)の分析および [SQL オンデマンドでの入れ子構造](query-parquet-nested-types.md)の分析に関するページを参照してください。

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

場合によっては、最上位の項目 (メタデータ) のプロパティを、配列 (authors) のすべての要素と "結合" することが必要になります。 SQL オンデマンドを使用すると、入れ子になった配列に対して `OPENJSON` 関数を適用することで、入れ子構造をフラット化できます。

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

最初に、Azure Cosmos DB のトランザクション ストアはスキーマに依存しませんが、分析ストアは分析クエリのパフォーマンスを最適化するためにスキーマ化されていることに、注意することが重要です。 Synapse Link の自動同期機能により、Azure Cosmos DB では何もしなくても分析ストアのスキーマ表現が管理され、これには入れ子になったデータ型の処理が含まれます。 SQL オンデマンドでは分析ストアのクエリが実行されるため、Azure Cosmos DB の入力データ型を SQL データ型にマップする方法を理解することが重要です。

SQL (Core) API の Azure Cosmos DB アカウントでは、数値、文字列、ブール値、null、入れ子になったオブジェクトまたは配列の JSON プロパティの型がサポートされています。 `OPENROWSET` で `WITH` 句を使用する場合は、これらの JSON 型に一致する SQL 型を選択する必要があります。 Azure Cosmos DB のさまざまなプロパティの型に対して使用する必要がある SQL 列の型については、以下を参照してください。

| Azure Cosmos DB のプロパティの型 | SQL 列の型 |
| --- | --- |
| Boolean | bit |
| Integer | bigint |
| Decimal | float |
| String | varchar (UTF8 データベース照合順序) |
| 日付と時刻 (ISO 形式の文字列) | varchar(30) |
| 日付と時刻 (Unix タイムスタンプ) | bigint |
| [Null] | `any SQL type` 
| 入れ子になったオブジェクトまたは配列 | varchar(max) (UTF8 データベース照合順序)、JSON テキストとしてシリアル化 |

Mongo DB API の種類の Azure Cosmos DB アカウントに対してクエリを実行する場合は、分析ストア内の完全に忠実なスキーマ表現と、使用される拡張プロパティ名の詳細を、[こちら](../../cosmos-db/analytical-store-introduction.md#analytical-schema)で確認してください。

## <a name="next-steps"></a>次のステップ

詳細については、次の記事を参照してください。

- [SQL オンデマンドでビューを作成および使用する方法](create-use-views.md) 
- [Azure Cosmos DB に対する SQL オンデマンド ビューの作成と、DirectQuery による Power BI モデルへのそれらの接続に関するチュートリアル](./tutorial-data-analyst.md)
