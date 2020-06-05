---
title: SQL オンデマンド (プレビュー) を使用して JSON ファイルに対してクエリを実行する
description: このセクションでは、Azure Synapse Analytics の SQL オンデマンドを使用して JSON ファイルを読み取る方法について説明します。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 7a8c9083ecbadbf63cf0ac65dc1803b478e939fe
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873398"
---
# <a name="query-json-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics の SQL オンデマンド (プレビュー) を使用して JSON ファイルに対してクエリを実行する

この記事では、Azure Synapse Analytics の SQL オンデマンド (プレビュー) を使用してクエリを作成する方法について説明します。 このクエリの目的は、JSON ファイルを読み取ることです。 サポートされている形式の一覧については、[OPENROWSET](develop-openrowset.md) のページを参照してください。

## <a name="prerequisites"></a>前提条件

最初の手順として、クエリを実行する**データベースを作成**します。 次に、そのデータベースで[セットアップ スクリプト](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)を実行して、オブジェクトを初期化します。 このセットアップ スクリプトにより、データ ソース、データベース スコープの資格情報、これらのサンプルで使用される外部ファイル形式が作成されます。

## <a name="sample-json-files"></a>サンプルの JSON ファイル

以下のセクションには、JSON ファイルを読み取るためのサンプル スクリプトが含まれています。 ファイルは *json* コンテナーの *books* フォルダーに格納され、次の構造を持つ 1 つの book エントリを含みます。

```json
{
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

## <a name="read-json-files"></a>JSON ファイルの読み取り

JSON_VALUE と [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) を使用して JSON ファイルを処理するには、JSON ファイルを 1 つの列としてストレージから読み取る必要があります。 次のスクリプトでは、*book1.json* ファイルを 1 つの列として読み取ります。

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'json/books/book1.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r];
```

> [!NOTE]
> JSON ファイル全体を 1 つの行または列として読み取ります。 そのため、FIELDTERMINATOR、FIELDQUOTE、および ROWTERMINATOR は 0x0b に設定されています。

## <a name="query-json-files-using-json_value"></a>JSON_VALUE を使用して JSON ファイルに対してクエリを実行する

次のクエリは、[JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) を使用して、「*Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics*」という書籍からスカラー値 (タイトル、出版社) を取得する方法を示しています。

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title,
    JSON_VALUE(jsonContent, '$.publisher') as publisher,
    jsonContent
FROM
    OPENROWSET(
        BULK 'json/books/*.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-json_query"></a>JSON_QUERY を使用して JSON ファイルに対してクエリを実行する

次のクエリは、[JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) を使用して、「*Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics*」という書籍からオブジェクトや配列 (作成者) を取得する方法を示しています。

```sql
SELECT
    JSON_QUERY(jsonContent, '$.authors') AS authors,
    jsonContent
FROM
    OPENROWSET(
        BULK 'json/books/*.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent varchar(8000)
    ) AS [r]
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="query-json-files-using-openjson"></a>OPENJSON を使用して JSON ファイルに対してクエリを実行する

次のクエリでは [OPENJSON](/sql/t-sql/functions/openjson-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) を使用します。 「*Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics*」という書籍内のオブジェクトとプロパティを取得します。

```sql
SELECT
    j.*
FROM
    OPENROWSET(
        BULK 'json/books/*.json',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='CSV',
        FIELDTERMINATOR ='0x0b',
        FIELDQUOTE = '0x0b',
        ROWTERMINATOR = '0x0b'
    )
    WITH (
        jsonContent NVARCHAR(max) -- Use appropriate length. Make sure JSON fits. 
    ) AS [r]
CROSS APPLY OPENJSON(jsonContent) AS j
WHERE
    JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics';
```

## <a name="next-steps"></a>次のステップ

このシリーズの次の記事では、次の方法について説明します。

- [フォルダーと複数のファイルに対するクエリ](query-folders-multiple-csv-files.md)
- [ビューの作成および使用](create-use-views.md)
