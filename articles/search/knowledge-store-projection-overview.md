---
title: プロジェクションの概念
titleSuffix: Azure Cognitive Search
description: 全文検索以外のシナリオで使用するために、AI エンリッチメントのインデックス作成パイプラインからエンリッチされたデータをナレッジ ストアに保存して整形します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 5b28540f30c23abc4ba1d58f6984524984f2c001
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740227"
---
# <a name="knowledge-store-projections-in-azure-cognitive-search"></a>Azure Cognitive Search のナレッジ ストアでの "プロジェクション"

[ナレッジ ストア](knowledge-store-concept-intro.md)の要素であるプロジェクションは、ナレッジ マイニングの目的で物理ストレージに保存できる強化されたドキュメントのビューです。 プロジェクションを使用すると、Power BI などのツールで追加の作業なしでデータを読み取ることができるように、関係を維持しながら、ニーズに合った形状にデータを "射影" できます。

プロジェクションは表形式にすることができます。その場合、データ表現は Azure Table Storage の行と列、Azure Blob Storage に格納された JSON オブジェクト、または Blob Storage に格納されたバイナリ イメージになります。 データが強化されると、データの複数のプロジェクションを定義できます。 同じデータを個々のユース ケースに応じて異なる方法で整形したい場合には、プロジェクションを複数用意することが有用です。

ナレッジ ストアは 3 種類のプロジェクションをサポートしています。

+ **[テーブル]** : 行と列として最も適切に表現されているデータの場合、テーブル プロジェクションを使用すると、テーブル ストレージでスキーマ化された形状またはプロジェクションを定義できます。 有効な JSON オブジェクトのみをテーブルとして射影できます。 強化されたドキュメントには、名前付き JSON オブジェクトではないノードを含めることができるため、スキル内で [Shaper スキルを追加するかインライン整形を使用して](knowledge-store-projection-shape.md)、有効な JSON を作成します。 

+ **オブジェクト**: データとエンリッチメントの JSON 表現が必要な場合は、オブジェクト プロジェクションを使用して、出力を BLOB として保存します。 テーブル プロジェクションと同様に、有効な JSON オブジェクトのみオブジェクトとして射影できます。また、整形によりその作業を容易に行うことができます。

+ **ファイル**: ドキュメントから抽出した画像を保存する必要がある場合には、ファイル プロジェクションを使うと、正規化済みの画像を BLOB ストレージに保存できます。

コンテキスト内の定義されているプロジェクションを確認する方法については、[REST でのナレッジ ストアの作成](knowledge-store-create-rest.md)に関する手順を参照してください。

## <a name="basic-pattern"></a>基本パターン

プロジェクションは、スキルセット オブジェクトの `knowledgeStore` 定義の下にある複雑なコレクションの配列です。 テーブル、オブジェクト、ファイルの各セットは "*プロジェクト グループ*" です。ストレージ要件にさまざまなツールとシナリオのサポートが含まれている場合、複数のグループを設定できます。 1 つのグループ内に、複数のテーブル、オブジェクト、ファイルを含めることができます。 

通常は 1 つのグループしか使用されませんが、次の例には 2 つ表示されており、複数のグループが存在する場合のパターンが示されています。

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [],
            "objects": [],
            "files": []
        }, 
        {
            "tables": [],
            "objects": [],
            "files": []
        }
    ]
}
```

### <a name="projection-groups"></a>プロジェクション グループ

テーブル-オジェクト-ファイルの組み合わせのセットを複数使用すると、さまざまなシナリオをサポートする際に役立ちます。 たとえば、1 つ目のセットをスキルセットの設計とデバッグに使用して、さらに詳細な検査のために出力をキャプチャし、2 つ目のセットでオンライン アプリに使用する出力を収集し、3 つ目をデータ サイエンス ワークロードに使用するなどです。

プロジェクション グループには、次のように相互排他性と関連性の重要な特性があります。 

| 原則 | 説明 |
|-----------|-------------|
| 相互排他性 | 1 つのグループに射影されるすべてのコンテンツは、他のプロジェクション グループに射影されるデータとは無関係です。 このような独立性が確保されていることによって、同じデータを異なる形状で持ち、各プロジェクション グループで繰り返すことができるようになっています。 各グループは、同じソース (強化ツリー) からデータを取得しますが、すべてのピア プロジェクション グループのテーブル-オブジェクト-ファイルの組み合わせからは完全に分離されています。|
| 関連性 | プロジェクションは、グループ内の関連性をサポートします。 グループ内では、テーブル内のコンテンツは、オブジェクトまたはファイル内のコンテンツに関連しています。 同じグループ内の各種類 (テーブル、オブジェクト、ファイル) 間で、強化ツリーの 1 つのノード (例: `/document/translated_text`) がさまざまなテーブルとオブジェクトにわたって射影されるときに、リレーションシップは保持されます。 テーブル内部では、リレーションシップは生成されたキーに基づいており、各子ノードは親ノードへの参照を保持します。 たとえば、画像とテキストが含まれるドキュメントがあるシナリオを考えてみましょう。 テーブルまたはオブジェクトにファイルの URL を表す列/プロパティがあれば、テキストをテーブルまたはオブジェクト、画像をファイルに、それぞれ射影できます。|

追加のプロジェクション グループを作成するかどうかは、多くの場合、データ表現の要件に基づいて決定されます。 たとえば、異なるデータ リレーションシップが必要なときに、これを行う場合があります。 セット内では、それらのリレーションシップが存在し、検出できることを前提として、データが関連付けられます。 追加のセットを作成する場合、各グループ内のドキュメントは決して関連付けられません。 複数のプロジェクション グループを使用する例として、オンライン システムで使用するために同じデータをプロジェクションし、それを特定の方式で表現する必要がある場合に、異なる方式で表現されるデータ サイエンス パイプラインで使用するために同じデータをさらにプロジェクションする、というものがあります。

<!-- ## Knowledge Store composition

The knowledge store consists of an annotation cache and projections. The *cache* is used by the service internally to cache the results from skills and track changes. A *projection* defines the schema and structure of the enrichments that match your intended use.

Within Azure Storage, projections can be articulated as tables, objects, or files.

+ As an object, the projection maps to Blob storage, where the projection is saved to a container, within which are the objects or hierarchical representations in JSON for scenarios like a data science pipeline.

+ As a table, the projection maps to Table storage. A tabular representation preserves relationships for scenarios like data analysis or export as data frames for machine learning. The enriched projections can then be easily imported into other data stores. 

You can create multiple projections in a knowledge store to accommodate various constituencies in your organization. A developer might need access to the full JSON representation of an enriched document, while data scientists or analysts might want granular or modular data structures shaped by your skillset.

For instance, if one of the goals of the enrichment process is to also create a dataset used to train a model, projecting the data into the object store would be one way to use the data in your data science pipelines. Alternatively, if you want to create a quick Power BI dashboard based on the enriched documents the tabular projection would work well. -->

## <a name="table-projections"></a>テーブル プロジェクション

テーブル プロジェクションは、データ探索が必要なシナリオ (Power BI を使用した分析など) で推奨されます。 テーブル定義は、射影するテーブルの一覧です。 各テーブルには 3 つのプロパティが必要です。

+ tableName: Azure Table Storage 内のテーブルの名前。

+ generatedKeyName:この行を一意に識別するキーの列名。

+ source:強化のソースとする強化ツリーのノード。 このノードは通常、テーブルの形状を定義する Shaper スキルの出力です。 テーブルには行と列があります。整形とは、行と列を指定するメカニズムのことです。 [Shaper スキルまたはインライン シェイプ](knowledge-store-projection-shape.md)を使用できます。 Shaper スキルは有効な JSON を生成しますが、有効な JSON であれば、任意のスキルからの出力とすることができます。 

この例で示すように、キー フレーズとエンティティは異なるテーブルにモデル化されており、各行の親 (MainTable) への参照が含まれます。 

```json
"knowledgeStore": {
  "storageConnectionString": "an Azure storage connection string",
  "projections" : [
    {
      "tables": [
        { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
        { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
        { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
      ]
    },
    {
      "objects": [ ]
    },
    {
      "files": [ ]
    }
  ]
}
```

"source" で指定された強化ノードは、複数のテーブルに射影するためにスライスできます。 "EnrichedShape" への参照は、Shaper スキルの出力です (表示されていません)。 スキルの入力によって、テーブルの構成とそれを埋める行が決まります。 生成されたキーと各テーブル内の共通フィールドは、テーブル リレーションシップの基礎となります。

## <a name="object-projections"></a>オブジェクト プロジェクション

オブジェクト プロジェクションは、任意のノードをソースにすることができる強化ツリーの JSON 表現です。 多くのケースでは、テーブル プロジェクションの作成と同じ Shaper スキルを使用してオブジェクト プロジェクションを生成できます。 

オブジェクト プロジェクションを生成するには、いくつかのオブジェクト固有の属性が必要です。

+ storageContainer:オブジェクトが保存される BLOB コンテナー

+ source:プロジェクションのルートとなる強化ツリーのノードのパス

```json
"knowledgeStore": {
  "storageConnectionString": "an Azure storage connection string",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "hotelreviews", 
          "source": "/document/hotel"
        }
      ]
    },
    {
        "files": [ ]
    }
  ]
}
```

## <a name="file-projections"></a>ファイル プロジェクション

ファイル プロジェクションは `normalized_images` コレクションに対してのみ機能します。ただし、それ以外については、ドキュメント ID の base64 でエンコードされた値のフォルダー プレフィックスを使用して BLOB コンテナーに保存されるという点で、オブジェクト プロジェクションと同様です。 

ファイル プロジェクションとオブジェクト プロジェクションのコンテナーを同じにすることはできないため、両者は別々のコンテナーに射影する必要があります。 ファイル プロジェクションには、オブジェクト プロジェクションと同じ以下のプロパティがあります。

+ storageContainer:オブジェクトが保存される BLOB コンテナー

+ source:プロジェクションのルートとなる強化ツリーのノードのパス

```json
"knowledgeStore": {
  "storageConnectionString": "an Azure storage connection string",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [ ]
    },
    {
        "files": [
              {
              "storageContainer": "ReviewImages",
              "source": "/document/normalized_images/*"
            }
        ]
    }
  ]
}
```

## <a name="projection-shaping"></a>プロジェクションの整形

プロジェクションのスキーマに一致するオブジェクトが強化ツリーにあると、プロジェクションの定義が簡単になります。これは、テーブルの場合もオブジェクトの場合も同様です。 計画された使用法に基づいてデータを整形または構造化する機能は通常、[Shaper スキル](cognitive-search-skill-shaper.md)をスキルセットに追加することで実行されます。 Shaper スキルから生成されたシェイプはプロジェクションの `source` として使用されますが、別のスキルへの入力として使用することもできます。

簡単に言えば、テーブル プロジェクションでは、Shaper スキルによってテーブル内の列またはフィールドが決定されます。 Shaper スキルへの入力は、強化ツリー内のノードで構成されます。 出力は、テーブル プロジェクションで指定する構造体です。 次の例では、"mytableprojection" という名前のテーブル プロジェクションが、Shaper スキルで指定された入力で構成されます。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
          "name": "reviews_text",
          "source": "/document/reviews_text"
        }, 
        {
          "name": "reviews_title",
          "source": "/document/reviews_title"
        },
        {
          "name": "reviews_username",
          "source": "/document/reviews_username"
        },
    ],
    "outputs": [
      {
        "name": "output",
        "targetName": "mytableprojection"
      }
    ]
}
```

Shaper スキルを使用すると、強化ツリーのさまざまなノードからオブジェクトを構成し、それらを新しいノード以下の子にすることができます。 また、これを使用すると、入れ子になったオブジェクトを含む複合型を定義することができます。 例については、[Shaper スキル](cognitive-search-skill-shaper.md)のドキュメントを参照してください。

## <a name="projection-slicing"></a>プロジェクションのスライス

テーブル プロジェクション グループ内で、強化ツリーの 1 つのノードを複数の関連テーブルにスライスし、各テーブルに 1 つのデータ カテゴリが含まれるようにすることができます。 これは、関連データを集計するかどうか、およびその方法をユーザーが制御できる分析に役立つことがあります。

複数の子テーブルを作成するには、親テーブルから始め、親のソースに基づいて作成されるテーブルを追加で作成します。 この例では、"KeyPhrases" と "Entities" は "/document/EnrichedShape" のスライスを取得します。

```json
"tables": [
  { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
  { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
  { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
]
```

複数のテーブルに射影する場合、子ノードが同じグループ内の別のテーブルのソースでない限り、完全な形状が各テーブルに射影されます。 既存のプロジェクションの子であるソース パスを含むプロジェクションを追加すると、子ノードが親ノードからスライスされ、関連する新しいテーブルまたはオブジェクトに射影されます。 この手法により、すべてのプロジェクションのソースとして使用できる 1 つのノードを Shaper スキルで定義できます。

## <a name="projection-lifecycle"></a>プロジェクションのライフサイクル

プロジェクションには、データ ソース内のソース データに関連付けられているライフサイクルがあります。 ソース データが更新され、インデックスが再作成されると、エンリッチメントの結果と共にプロジェクションが更新され、最終的にプロジェクションがデータ ソースのデータと一致するようになります。 ただし、同時にプロジェクションは Azure Storage に個別に格納されています。 インデクサーまたは検索サービス自体が削除された場合でも、それらが削除されることはありません。 

## <a name="using-projections"></a>プロジェクションの使用

インデクサーの実行後に、プロジェクションによって指定したコンテナーまたはテーブル内の射影されたデータを読み取ることができます。

分析に関しては、Power BI での探索は、Azure Table Storage をデータ ソースとして設定する場合と同じくらい簡単です。 内部のリレーションシップを利用すると、データを簡単に視覚化できます。

また、データ サイエンス パイプラインで強化されたデータを使用する必要がある場合は、[BLOB から Pandas DataFrame にデータを読み込む](/azure/architecture/data-science-process/explore-data-blob)こともできます。

最後に、ナレッジ ストアからデータをエクスポートする必要がある場合、Azure Data Factory には、データをエクスポートして任意のデータベースに格納するコネクタがあります。 

## <a name="next-steps"></a>次のステップ

次の手順として、サンプル データと手順を使用して最初のナレッジ ストアを作成します。

> [!div class="nextstepaction"]
> [REST でナレッジ ストアを作成する](knowledge-store-create-rest.md)

スライス、インラインの整形、リレーションシップなどの高度な概念についての詳細は、[ナレッジ ストアでのプロジェクションを定義する](knowledge-store-projections-examples.md)に関する記事を参照してください。
