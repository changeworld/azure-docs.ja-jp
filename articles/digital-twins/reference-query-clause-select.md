---
title: Azure Digital Twins クエリ言語リファレンス - SELECT 句
titleSuffix: Azure Digital Twins
description: Azure Digital Twins クエリ言語 SELECT 句のリファレンス ドキュメント
author: baanders
ms.author: baanders
ms.date: 03/31/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: c911a3ea8607cb83cb3d96a44bfac424042df30a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232870"
---
# <a name="azure-digital-twins-query-language-reference-select-clause"></a>Azure Digital Twins クエリ言語リファレンス: SELECT 句

このドキュメントには、[Azure Digital Twins クエリ言語](concepts-query-language.md)の **SELECT 句** に関するリファレンス情報が含まれています。

SELECT 句は、クエリの最初の部分です。 これにより、クエリによって返される列の一覧を指定します。

この句は、すべてのクエリで必要になります。

## <a name="select-"></a>SELECT *

`*` 文字を select ステートメントで使用すると、デジタル ツイン ドキュメントはそのまま射影され、結果セットのプロパティには割り当てられません。

>[!NOTE]
> `SELECT *` は、クエリで `JOIN` を使用しない場合にのみ有効な構文です。 `JOIN` を使用したクエリの詳細については、「[Azure Digital Twins クエリ言語リファレンス: JOIN 句](reference-query-clause-join.md)」を参照してください。

### <a name="syntax"></a>構文

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectSyntax":::

### <a name="returns"></a>戻り値

クエリから返されるプロパティのセット。

### <a name="example"></a>例

次のクエリでは、インスタンス内のすべてのデジタル ツインが返されます。 

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectExample":::

## <a name="select-columns-with-projections"></a>プロジェクションを使用した SELECT 列

プロジェクションを SELECT 句で使用して、クエリによって返される列を選択できます。 ツインとリレーションシップの名前指定コレクション、またはツインとリレーションシップのプロパティを指定できます。

プロジェクションは、プリミティブ プロパティと複合プロパティの両方でサポートされるようになりました。

### <a name="syntax"></a>構文

コレクションを射影するには:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectCollectionSyntax":::

プロパティを射影するには:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectPropertySyntax":::

### <a name="returns"></a>戻り値

プロジェクションで指定されたツイン、プロパティ、またはリレーションシップのコレクション。

プロジェクションに含まれるプロパティが特定のデータ行に存在しない場合、このプロパティは結果セットにも同様に存在しません。 この動作の例については、「[プロパティを射影する例: データ行のプロパティが存在しない場合](#project-property-example-property-not-present-for-a-data-row)」を参照してください。

### <a name="examples"></a>例

#### <a name="example-scenario"></a>シナリオ例

次の例では、次のデータ要素を含むツイン グラフについて考えてみましょう。
* FactoryA と呼ばれるファクトリ ツイン
    - `FactoryA` の値を持つ `name` というプロパティが含まれています
* Contoso と呼ばれるコンシューマー ツイン
    - `Contoso` の値を持つ `name` というプロパティが含まれています
* `FactoryA-consumerRelationship-Contoso` と呼ばれる、FactoryA から Contoso への consumerRelationship リレーションシップ
    - `Jeff` の値を持つ `managedBy` というプロパティが含まれています

次の図にこのシナリオを示します。

:::row:::
    :::column:::
        :::image type="content" source="media/reference-query-clause-select/projections-graph.png" alt-text="上記のサンプル グラフを示す図。":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="project-collection-example"></a>コレクションを射影する例

このグラフからコレクションを射影するクエリの例を下に示します。 次のクエリでは、ツイン コレクション全体に `T` の名前を付け、返すコレクションとして `T` を射影することで、インスタンス内のすべてのデジタル ツインが返されます。 

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectCollectionExample":::

このクエリから返される JSON ペイロードを次に示します。

```json
{
  "value": [
    {
      "result": [
        {
          "T": {
            "$dtId": "FactoryA",
            "$etag": "W/\"d22267a0-fd4f-4f6b-916d-4946a30453c9\"",
            "$metadata": {
              "$model": "dtmi:contosocom:DigitalTwins:Factory;1",
              "name": {
                "lastUpdateTime": "2021-04-19T17:15:54.4977151Z"
              }
            },
            "name": "FactoryA"
          }
        },
        {
          "T": {
            "$dtId": "Contoso",
            "$etag": "W/\"a96dc85e-56ae-4061-866b-058a149e03d8\"",
            "$metadata": {
              "$model": "dtmi:com:contoso:Consumer;1",
              "name": {
                "lastUpdateTime": "2021-04-19T17:16:30.2154166Z"
              }
            },
            "name": "Contoso"
          }
        }
      ]
    }
  ],
  "continuationToken": "null"
}
```

#### <a name="project-with-join-example"></a>JOIN を使用して射影する例

プロジェクションは通常、`JOIN` で指定されたコレクションを返すために使用されます。 次のクエリでは、プロジェクションを使用してコンシューマー、ファクトリ、リレーションシップのデータが返されます。 この例で使用されている `JOIN` 構文の詳細については、「[Azure Digital Twins クエリ言語リファレンス: JOIN 句](reference-query-clause-join.md)」を参照してください。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectJoinExample":::

このクエリから返される JSON ペイロードを次に示します。

```json
{
  "value": [
    {
      "result": [
        {
          "Consumer": {
            "$dtId": "Contoso",
            "$etag": "W/\"a96dc85e-56ae-4061-866b-058a149e03d8\"",
            "$metadata": {
              "$model": "dtmi:com:contoso:Consumer;1",
              "name": {
                "lastUpdateTime": "2021-04-19T17:16:30.2154166Z"
              }
            },
            "name": "Contoso"
          },
          "Factory": {
            "$dtId": "FactoryA",
            "$etag": "W/\"d22267a0-fd4f-4f6b-916d-4946a30453c9\"",
            "$metadata": {
              "$model": "dtmi:contosocom:DigitalTwins:Factory;1",
              "name": {
                "lastUpdateTime": "2021-04-19T17:15:54.4977151Z"
              }
            },
            "name": "FactoryA"
          },
          "Relationship": {
            "$etag": "W/\"f01e07c1-19e4-4bbe-a12d-f5761e86d3e8\"",
            "$relationshipId": "FactoryA-consumerRelationship-Contoso",
            "$relationshipName": "consumerRelationship",
            "$sourceId": "FactoryA",
            "$targetId": "Contoso",
            "managedBy": "Jeff"
          }
        }
      ]
    }
  ],
  "continuationToken": "null"
}
```

#### <a name="project-property-example"></a>プロパティを射影する例

プロパティを射影する例を次に示します。 次のクエリでは、プロジェクションを使用して、コンシューマー ツインの `name` プロパティとリレーションシップの `managedBy` プロパティが返されます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectPropertyExample":::

このクエリから返される JSON ペイロードを次に示します。

```json
{
  "value": [
    {
      "result": [
        {
          "managedBy": "Jeff",
          "name": "Contoso"
        }
      ]
    }
  ],
  "continuationToken": "null"
}
```

#### <a name="project-property-example-property-not-present-for-a-data-row"></a>プロパティを射影する例: データ行のプロパティが存在しない場合

プロジェクションに含まれるプロパティが特定のデータ行に存在しない場合、このプロパティは結果セットにも同様に存在しません。

この例では、人物を表すツインのセットを考えましょう。 ツインの中には年齢が関連付けられているものがありますが、そうでないものもあります。

`name` と `age` の各プロパティを射影するクエリを次に示します。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectProjectPropertyNotPresentExample":::

結果は次のようになります。結果の中で、一部のツインに `age` プロパティがありませんが、そのツインにはこのプロパティは存在しません。

```json
{
  "value": [
    {
      "result": [
        {
          "name": "John",
          "age": 27
        },
        {
          "name": "Keanu"
        }
      ]
    }
  ],
  "continuationToken": "null"
}
```

## <a name="select-count"></a>SELECT COUNT

このメソッドを使用すると、結果セット内の項目の数をカウントし、その数を返すことができます。

### <a name="syntax"></a>構文

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectCountSyntax":::

### <a name="arguments"></a>引数

[なし] :

### <a name="returns"></a>戻り値

`int` 値。

### <a name="example"></a>例

次のクエリは、インスタンス内のすべてのデジタル ツインの数を返します。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectCountExample":::

次のクエリは、インスタンス内のすべてのリレーションシップの数を返します。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectCountRelationshipsExample":::

## <a name="select-top"></a>SELECT TOP

このメソッドを使用すると、クエリの要件を満たす一部の上位項目だけを返すことができます。

### <a name="syntax"></a>構文

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectTopSyntax":::

### <a name="arguments"></a>引数

選択する上位項目の数を指定する `int` 値。

### <a name="returns"></a>戻り値

ツインのコレクション。

### <a name="example"></a>例

次のクエリでは、インスタンス内の上位 5 個のデジタル ツインのみが返されます。

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="SelectTopExample":::