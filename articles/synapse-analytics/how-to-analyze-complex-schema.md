---
title: 配列と入れ子構造を含むスキーマの分析
description: Apache Spark と SQL を使用して配列と入れ子構造を分析する方法
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 06/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: b02c3627cea5e441739c77d1882505c6b82489bc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84908027"
---
# <a name="analyze-complex-data-types-in-synapse"></a>Synapse で複合データ型を分析する

この記事は、**Azure Synapse Link for Azure Cosmos DB** の Parquet ファイルとコンテナーに関連しています。 ユーザーが Spark または SQL を使用して、配列や入れ子構造など、スキーマが複雑なデータの読み取りや変換を行う方法について説明しています。 次の例で使用されるドキュメントは 1 つですが、Spark または SQL を使うと膨大な数のドキュメントに簡単にスケーリングできます。 次のコードでは、PySpark (Python) を使用しています。

## <a name="use-case"></a>ユース ケース

最新のデータ型では、複雑なデータ型を扱うことが一般的であり、データ エンジニアにとっての課題となっています。 入れ子になったスキーマと配列の分析には、いくつの課題がかあります。
* SQL クエリの記述が複雑である
* 入れ子になった列の名前変更やデータ型のキャストが難しい
* オブジェクトの入れ子が深くなるとパフォーマンスの問題が発生する

データ エンジニアは、これらのデータ型を効率的に処理し、だれでも簡単にアクセスできるようにする方法を理解する必要があります。

次の例では、Synapse Spark を使用して、データ フレームを介してオブジェクトを読み取り、フラット構造に変換します。 このようなオブジェクトに直接クエリを実行し、その結果を通常のテーブルとして返すために Synapse SQL サーバーレスを使用します。

## <a name="what-are-arrays-and-nested-structures"></a>配列と入れ子構造とは

次のオブジェクトは、アプリの分析情報から取得されます。 このオブジェクト内には、入れ子構造がありますが、入れ子構造が含まれる配列もあります。

```json
{
    "id": "66532691-ab20-11ea-8b1d-936b3ec64e54",
    "context": {
        "data": {
            "eventTime": "2020-06-10T13:43:34.553Z",
            "samplingRate": "100.0",
            "isSynthetic": "false"
        },
        "session": {
            "isFirst": "false",
            "id": "38619c14-7a23-4687-8268-95862c5326b1"
        },
        "custom": {
            "dimensions": [
                {
                    "customerInfo": {
                        "ProfileType": "ExpertUser",
                        "RoomName": "",
                        "CustomerName": "diamond",
                        "UserName": "XXXX@yahoo.com"
                    }
                },
                {
                    "customerInfo": {
                        "ProfileType": "Novice",
                        "RoomName": "",
                        "CustomerName": "topaz",
                        "UserName": "XXXX@outlook.com"
                    }
                }
            ]
        }
    }
}
```

### <a name="schema-example-of-arrays-and-nested-structures"></a>配列と入れ子構造のスキーマ例
そのオブジェクトのデータ フレーム (**df**) のスキーマをコマンド **df.printschema** を使用して出力すると、次のように表示されます。

* 黄色は入れ子構造を表します
* 緑色は 2 つの要素を持つ配列を表します

[![スキーマの原形](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

_rid、_ts、_etag は、ドキュメントが Azure Cosmos DB トランザクション ストアに取り込まれたときにシステムに追加されました。

上記のデータ フレームは、5 列と 1 行のみをカウントします。 変換後、選別されたデータ フレームは、13 列、2 行の表形式になります。

## <a name="flatten-nested-structures-and-explode-arrays-with-apache-spark"></a>Apache Spark を使って、入れ子構造をフラット化し、配列を分解する

Synapse Spark を使用すると、入れ子構造は列に、配列要素は複数の行に簡単に変換できます。 次の手順は、だれでもご自分の実装に使用できます。

[![Spark 変換の手順](./media/how-to-complex-schema/spark-transfo-steps.png)](./media/how-to-complex-schema/spark-transfo-steps.png#lightbox)

**手順 1.** :入れ子になったスキーマをフラット化する関数を定義します。 この関数は、変更せずに使用できます。 関数を使用して、Pyspark ノートブックにセルを作成します。

```python
from pyspark.sql.functions import col

def flatten_df(nested_df):
    stack = [((), nested_df)]
    columns = []

    while len(stack) > 0:
        parents, df = stack.pop()

        flat_cols = [
            col(".".join(parents + (c[0],))).alias("_".join(parents + (c[0],)))
            for c in df.dtypes
            if c[1][:6] != "struct"
        ]

        nested_cols = [
            c[0]
            for c in df.dtypes
            if c[1][:6] == "struct"
        ]

        columns.extend(flat_cols)

        for nested_col in nested_cols:
            projected_df = df.select(nested_col + ".*")
            stack.append((parents + (nested_col,), projected_df))

    return nested_df.select(columns)
```

**手順 2**: 関数を使用して、データ フレーム **df** の入れ子になったスキーマを、新しいデータ フレーム **df_flat** にフラット化します。

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

display 関数から、10 列、1 行が返るはずです。 配列とその入れ子になった要素はまだそのままです。

**手順 3**: データ フレーム **df_flat** の配列 **context_custom_dimensions** を新しいデータフレーム **df_flat_explode** に変換します。 次のコードで、どの列を選択するかも定義します。

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

display 関数から返る結果は、10 列、2 行です。 次の手順では、手順 1 で定義した関数を使用して、入れ子になったスキーマをフラット化します。

**手順 4**: 関数を使用して、データ フレーム **df_flat_explode** の入れ子になったスキーマを、新しいデータ フレーム **df_flat_explode_flat** にフラット化します。
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

display 関数から、13 列、2 行が返るはずです。

データ フレーム df_flat_explode_flat の関数 printSchema から、次の結果が返ります。

[![スキーマの最終形](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly-with-sql-serverless"></a>SQL サーバーレスを使用して配列と入れ子構造を直接読み取る

SQL サーバーレスを使って、このようなオブジェクトに対してクエリを実行したり、ビューやテーブルを作成したりできます。

まず、データがどのように格納されているかによって、ユーザーは次の分類法を使用する必要があります。 大文字部分はすべて、ユース ケースに固有です。

| BULK              | FORMAT |
| -------------------- | --- |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet ' |'Parquet' (ADLSg2)|
| N'endpoint=https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/;account= ACCOUNTNAME;database=DATABASENAME;collection=COLLECTIONNAME;region=REGIONTOQUERY, SECRET='YOURSECRET' |'CosmosDB' (Synapse Link)|



**SQL サーバーレス**により、Azure Synapse Link for Azure Cosmos DB および AAD パススルー用のリンクされたサービスがサポートされます。 この機能は現在、Synapse Link の限定的なプレビューの段階です。

以下を置き換えます。
* 'YOUR BULK ABOVE' は、接続先のデータ ソースの接続文字列にする
* 'YOUR TYPE ABOVE' は、ソースへの接続に使用する形式にする

```sql
select *
FROM
openrowset(
BULK 'YOUR BULK ABOVE',
            FORMAT='YOUR TYPE ABOVE'
)
with (id varchar(50),
        contextdataeventTime varchar(50) '$.context.data.eventTime',
        contextdatasamplingRate varchar(50) '$.context.data.samplingRate',
        contextdataisSynthetic varchar(50) '$.context.data.isSynthetic',
        contextsessionisFirst varchar(50) '$.context.session.isFirst',
        contextsessionid varchar(50) '$.context.session.id',
        contextcustomdimensions varchar(max) '$.context.custom.dimensions'
) as q 
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType',
            RoomName varchar(50) '$.customerInfo.RoomName',
            CustomerName varchar(50) '$.customerInfo.CustomerName',
            UserName varchar(50) '$.customerInfo.UserName'
    )
```

実行される操作には、次の 2 種類があります。
* 次のコード行では、入れ子になった要素を参照する contextdataeventTime という名前の列を定義します:Context.Data.eventTime
```sql
contextdataeventTime varchar(50) '$.context.data.eventTime'
```

この行で、入れ子の要素を参照する contextdataeventTime という名前の列を定義します:Context>Data>eventTime

* **クロス適用**は、配列の各要素に対して新しい行を作成するために使用され、最初の箇条書きと同様に、入れ子になった各オブジェクトを定義します。 
```sql
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
```

配列の要素が 5 つで、入れ子構造が 4 つの場合、SQL サーバーレスにより、5 行、4 列が返されます。

SQL サーバーレスを使用して、インプレースでクエリを実行し、配列を 2 行にマップして、すべての入れ子構造を列表示することができます。

## <a name="next-steps"></a>次のステップ

* [Spark を使って Azure Synapse Link for Azure Cosmos DB に対してクエリを実行する方法について学ぶ](./synapse-link/how-to-query-analytical-store-spark.md)
* [Parquet の入れ子にされた型に対してクエリを実行する](./sql/query-parquet-nested-types.md) 