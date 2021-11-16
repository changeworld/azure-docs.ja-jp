---
title: プロジェクションの概念
titleSuffix: Azure Cognitive Search
description: プロジェクションの概念とベスト プラクティスについて説明します。 Cognitive Search でナレッジ ストアを作成している場合、プロジェクションによって Azure Storage 内のオブジェクトの種類、数量、構成が決まります。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 1b698ae5b03a7d622e4fccb717789be8cb330057
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561292"
---
# <a name="knowledge-store-projections-in-azure-cognitive-search"></a>Azure Cognitive Search のナレッジ ストアでの "プロジェクション"

プロジェクションは、Cognitive Search AI エンリッチメント パイプラインからコンテンツを受け入れる [**ナレッジ ストア**](knowledge-store-concept-intro.md)内の物理的なテーブル、オブジェクト、ファイルです。 ナレッジ ストアを作成する場合は、作業の多くがプロジェクションの定義と整形になります。

この記事では、コーディングを開始する前にある程度の背景を理解できるように、プロジェクションの概念とワークフローについて説明します。

プロジェクションは、Cognitive Search スキルセットで定義されますが、最終的な成果は Azure Storage 内のテーブル、オブジェクト、画像ファイル プロジェクションになります。

:::image type="content" source="media/knowledge-store-concept-intro/kstore-in-storage-explorer.png" alt-text="Azure Storage 内で表されるプロジェクション" border="true":::

## <a name="types-of-projections-and-usage"></a>プロジェクションの種類と使用方法

ナレッジ ストアは、Azure Storage 内のテーブル、JSON オブジェクト、またはバイナリ画像ファイルの疎集合として物理的に表される論理構造です。

| Projection | ストレージ | 使用方法 |
|------------|---------|-------|
| [テーブル](knowledge-store-projections-examples.md#define-a-table-projection) | Azure Table Storage | 行と列で表すのが最も適しているデータ、またはデータの詳細な表現 (データ フレームなど) が必要な場合に使用されます。 テーブル プロジェクションを使用すると、[Shaper スキルまたはインライン シェイプ](knowledge-store-projection-shape.md)を使用して列と行を指定することにより、スキーマ化されたシェイプを定義できます。 使い慣れた正規化の原則に基づいて、コンテンツを複数のテーブルに整理できます。 同じグループ内のテーブルは自動的に関連付けられます。 |
| [オブジェクト](knowledge-store-projections-examples.md#define-an-object-projection) | Azure Blob Storage | 1 つの JSON ドキュメントでデータとエンリッチメントの完全な JSON 表現が必要な場合に使用されます。 テーブル プロジェクションと同様に、有効な JSON オブジェクトのみオブジェクトとして射影できます。また、整形によりその作業を容易に行うことができます。 |
| [[ファイル]](knowledge-store-projections-examples.md#define-a-file-projection) | Azure Blob Storage | 正規化されたバイナリ イメージ ファイルを保存する必要がある場合に使用されます。 |

## <a name="projection-definition"></a>プロジェクション定義

プロジェクションは、[スキルセット](/rest/api/searchservice/create-skillset)の "knowledgeStore" プロパティで指定されます。 プロジェクション定義は、インデクサーの呼び出し中に、強化されたコンテンツを含むオブジェクトを作成し、Azure Storage に読み込むために使用されます。 これらの概念に習熟していない場合は、入門として [AI エンリッチメント](cognitive-search-concept-intro.md)から始めてください。

次の例は、knowledgeStore でのプロジェクションの配置と、基本的な構造を示しています。 プロジェクション定義は、名前、種類、コンテンツ ソースで構成されます。

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
      {
        "tables": [
          { "tableName": "ks-museums-main", "generatedKeyName": "ID", "source": "/document/tableprojection" },
          { "tableName": "ks-museumEntities", "generatedKeyName": "ID","source": "/document/tableprojection/Entities/*" }
        ],
        "objects": [
          { "storageContainer": "ks-museums", "generatedKeyName": "ID", "source": "/document/objectprojection" }
        ],
        "files": [ ]
      }
    ]
```

## <a name="projection-groups"></a>プロジェクション グループ

プロジェクションは複雑なコレクションの配列です。つまり、各種類の複数のセットを指定できます。 1 つのプロジェクション グループのみを使用するのが一般的ですが、ストレージ要件にさまざまなツールやシナリオのサポートが含まれる場合は、複数のグループを使用できます。 たとえば、スキルセットの設計とデバッグに 1 つのグループを使用し、2 つ目のセットでオンライン アプリに使用される出力を収集し、3 つ目をデータ サイエンスのワークロードに使用します。

プロジェクションの下にあるすべてのグループにデータを読み込むには、同じスキルセット出力を使用します。 次の例は、2 つを示しています。

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

プロジェクション グループには、次のように相互排他性と関連性の重要な特性があります。 

| 原則 | 説明 |
|-----------|-------------|
| 相互排他性 | 各グループは他のグループから完全に分離されており、さまざまなデータ整形シナリオをサポートします。 たとえば、異なるテーブル構造と組み合わせをテストする場合、AB テスト用の異なるプロジェクション グループに各セットを配置します。 各グループは、同じソース (強化ツリー) からデータを取得しますが、すべてのピア プロジェクション グループのテーブル-オブジェクト-ファイルの組み合わせからは完全に分離されています。|
| 関連性 | プロジェクション グループ内では、テーブル、オブジェクト、ファイルのコンテンツが関連付けられます。 ナレッジ ストアでは、生成されたキーが共通の親ノードへの参照ポイントとして使用されます。 たとえば、画像とテキストが含まれるドキュメントがあるシナリオを考えてみましょう。 テキストをテーブルに投影し、画像をバイナリ ファイルに投影できます。テーブルとオブジェクトの両方には、ファイルの URL を含む列およびプロパティが含まれます。|

## <a name="projection-source"></a>プロジェクションの "ソース"

ソース パラメーターは、プロジェクション定義の 3 番目のコンポーネントです。 プロジェクションは AI エンリッチメント パイプラインからのデータを格納するため、プロジェクションのソースは常にスキルの出力です。 そのため、出力は、単一のフィールド (たとえば、翻訳されたテキストのフィールド) の場合もありますが、多くの場合、データ シェイプへの参照です。

データ シェイプはスキルセットから取得されます。 Cognitive Search で提供されるすべての組み込みスキルの中には、データ シェイプの作成に使用される [**Shaper スキル**](cognitive-search-skill-shaper.md)と呼ばれるユーティリティ スキルがあります。 ナレッジ ストアのプロジェクションをサポートするために、Shaper スキル (必要に応じた数) を含めることができます。

シェイプはテーブル プロジェクションで頻繁に使用されます。シェイプを使用することにより、テーブルに入れる行だけでなく、作成される列も指定できます (オブジェクト プロジェクションにシェイプを渡すこともできます)。

シェイプは複雑になる可能性があります。ここでは範囲外のため詳細に説明しませんが、基本的なシェイプを簡単に示した例を次に示します。 Shaper スキルの出力は、テーブル プロジェクションのソースとして指定されます。 テーブル プロジェクション内で、それ自体は、シェイプで指定される、"metadata-storage_path"、"reviews_text"、"reviews_title" などの列です。

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

## <a name="projection-lifecycle"></a>プロジェクションのライフサイクル

プロジェクションには、データ ソース内のソース データに関連付けられているライフサイクルがあります。 ソース データが更新され、インデックスが再作成されると、エンリッチメントの結果と共にプロジェクションが更新され、最終的にプロジェクションがデータ ソースのデータと一致するようになります。 ただし、同時にプロジェクションは Azure Storage に個別に格納されています。 インデクサーまたは検索サービス自体が削除された場合でも、それらが削除されることはありません。 

## <a name="consume-in-apps"></a>アプリ内での使用

インデクサーを実行した後、プロジェクションに接続し、そのデータを他のアプリやワークロードで使用します。

+ [ストレージ ブラウザー](knowledge-store-view-storage-explorer.md)を使用して、オブジェクトの作成とコンテンツを確認します。

+ [データ探索用の Power BI](knowledge-store-connect-power-bi.md) を使用します。 このツールは、データが Azure Table Storage 内にある場合に最適です。 Power BI 内では、データを操作して、クエリと分析が容易な新しいテーブルに格納できます。

+ データ サイエンス パイプラインの BLOB コンテナーで強化されたデータを使用します。 たとえば、[データを BLOB から Pandas DataFrame に読み込む](/azure/architecture/data-science-process/explore-data-blob)ことができます。

+ 最後に、ナレッジ ストアからデータをエクスポートする必要がある場合、Azure Data Factory には、データをエクスポートして任意のデータベースに格納するコネクタがあります。

## <a name="checklist-for-getting-started"></a>使用を開始するためのチェックリスト

プロジェクションはナレッジ ストア専用であり、検索インデックスの構造化には使用されないことを忘れないでください。

1. Azure Storage で、**アクセス キー** から接続文字列を取得し、アカウントが StorageV2 (汎用 V2) であることを確認します。

1. Azure Storage 内で、コンテナーとテーブルの既存のコンテンツを十分に理解し、プロジェクション名として競合しない名前を選択します。 ナレッジ ストアは、テーブルおよびコンテナーの疎集合です。 関連するオブジェクトを追跡するために、名前付け規則を採用することを検討してください。

1. Cognitive Search のインデクサーで [エンリッチメント キャッシュ (プレビュー) を有効](search-howto-incremental-index.md)にした後、[インデクサーを実行](search-howto-run-reset-indexers.md)して、スキルセットを実行し、キャッシュにデータを読み込みます。 これはプレビュー機能であるため、インデクサー要求で必ずプレビュー REST API (api-version=2020-06-30-preview 以降) を使用してください。 キャッシュにデータが読み込まれると、(スキル自体が変更されない限り) ナレッジ ストア内のプロジェクション定義を無料で変更できます。

1. コードでは、すべてのプロジェクションはスキルセットでのみ定義されます。 プロジェクションに適用されるインデクサー プロパティ (フィールド マッピング、出力フィールド マッピング) はありません。 スキルセット定義内では、knowledgeStore プロパティとスキル配列の 2 つの領域に重点を置きます。

   1. knowledgeStore 下の `projections` セクションで、テーブル、オブジェクト、ファイルの各プロジェクションを指定します。 オブジェクトの種類、オブジェクト名、数量 (定義するプロジェクションの数ごと) は、このセクションで決定されます。

   1. スキル配列から、各プロジェクションの `source` で参照されるスキル出力を決定します。 すべてのプロジェクションにはソースがあります。 ソースは、アップストリーム スキルの出力になる場合もありますが、多くの場合は、Shaper スキルの出力です。 プロジェクションの構成は、シェイプによって決定されます。 

1. 既存のスキルセットにプロジェクションを追加する場合、[スキルセットを更新](/rest/api/searchservice/update-skillset)し、[インデクサーを実行](/rest/api/searchservice/run-indexer)します。

1. Azure Storage で結果を確認します。 以降の実行では、Azure Storage 内のオブジェクトを削除するか、スキルセットでプロジェクト名を変更して、名前の競合を回避します。

## <a name="next-steps"></a>次のステップ

各プロジェクトの種類の構文と例を確認します。

> [!div class="nextstepaction"]
> [ナレッジ ストアでのプロジェクションを定義する](knowledge-store-projections-examples.md)