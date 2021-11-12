---
title: プロジェクションを定義する
titleSuffix: Azure Cognitive Search
description: 構文と例を確認して、ナレッジ ストアでテーブル、オブジェクト、およびファイルのプロジェクションを定義する方法について説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 0aa980d2268f6d055f3aa05a8e30d57712c022df
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563627"
---
# <a name="define-projections-in-a-knowledge-store"></a>ナレッジ ストアでのプロジェクションを定義する

[プロジェクション](knowledge-store-projection-overview.md)は、AI で強化されたコンテンツを Azure Storage にどのように保存するかを決定する、[ナレッジ ストアの定義](knowledge-store-concept-intro.md)のコンポーネントです。 プロジェクションによって、コンテンツを含むデータ構造の種類、数量、および構成が決まります。

この記事では、各プロジェクションの種類の構文について説明します。

+ [テーブル プロジェクション](#define-a-table-projection)
+ [オブジェクト プロジェクション](#define-an-object-projection)
+ [ファイル プロジェクション](#define-a-file-projection)

プロジェクションは、スキルセットの "knowledgeStore" プロパティで指定されることを思い出してください。

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
      {
        "tables": [ ],
        "objects": [ ],
        "files": [ ]
      }
    ]
```

始める前に背景情報をさらに確認したい場合は、[こちらのチェック リスト](knowledge-store-projection-overview.md#checklist-for-getting-started)でヒントやワークフローを確認してください。

> [!TIP]
> プロジェクションを開発するときは、[エンリッチメント キャッシュを有効](search-howto-incremental-index.md)にして、プロジェクション定義の編集時に既存のエンリッチメントを再利用できるようにします。  これはプレビュー機能であるため、インデクサー要求では必ずプレビュー REST API (api-version=2020-06-30-preview 以降) を使用するようにしてください。 キャッシュがないと、プロジェクションを少し編集するだけで、エンリッチメントされたコンテンツの再処理がフルで行われてしまいます。 エンリッチメントをキャッシュすれば、スキルセットの処理にかかる料金を発生させることなく、プロジェクションを反復処理することができます。

## <a name="requirements"></a>必要条件

すべてのプロジェクションには、ソースと宛先のプロパティがあります。 ソースは常に、スキルセットの実行中に作成されたエンリッチメント ツリーのコンテンツです。 宛先は、Azure Storage に作成されて読み込まれるオブジェクトの名前と種類です。

バイナリ イメージのみを受け入れるファイル プロジェクションの場合を除き、ソースは次である必要があります。

+ 有効な JSON
+ エンリッチメント ツリー内のノードへのパス (たとえば、`"source": "/document/objectprojection"`)

ノードは 1 つのフィールドに解決することもありますが、より一般的な表現は複雑なシェイプへの参照です。 複雑なシェイプは整形手法によって作成され、これは [Shaper スキル](cognitive-search-skill-shaper.md)または[インラインの整形定義](knowledge-store-projection-shape.md#inline-shape)のいずれかですが、通常は Shaper スキルを使用します。

ほとんどのスキルは単体では有効な JSON を出力しないため、Shaper スキルが好まれます。 多くの場合、Shaper スキルによって作成された同じデータ シェイプは、テーブルとオブジェクトの両方のプロジェクションで同じように使用できます。

ソース入力要件によっては、[データを整形する](knowledge-store-projection-shape.md)方法を知っておくと、特にテーブルを扱っている場合、プロジェクション定義の実用的な要件になります。

## <a name="define-a-table-projection"></a>テーブル プロジェクションの定義

テーブル プロジェクションは、データ探索が必要なシナリオ (データ フレームを使用する Power BI またはワークロードを使用した分析など) で推奨されます。 プロジェクション配列の tables セクションは、プロジェクションするテーブルのリストです。

テーブル プロジェクションを定義するには、プロジェクション プロパティ内で `tables` 配列を使用します。 テーブル プロジェクションには、次の 3 つの必須プロパティがあります。

| プロパティ | 説明 |
|----------|-------------|
| tableName | Azure Table Storage で作成された新しいテーブルの名前を決定します。  |
| generatedKeyName | 各行を一意に識別するキーの列名。 この値はシステムによって生成されます。 このプロパティを省略すると、名前付け規則としてテーブル名と "キー" を使用する列が自動的に作成されます。 |
| ソース | エンリッチメント ツリー内のノードへのパス。 このノードは、テーブルにどの列を作成するかを決定する複雑なシェイプへの参照である必要があります。|

テーブル プロジェクションにおいて、"source" は、テーブルの形状を定義する [Shaper スキル](cognitive-search-skill-shaper.md)の出力です。 テーブルには行と列があります。整形とは、行と列を指定するメカニズムのことです。 [Shaper スキルまたはインライン シェイプ](knowledge-store-projection-shape.md)を使用できます。 Shaper スキルは有効な JSON を生成しますが、有効な JSON であれば、ソースは任意のスキルからの出力とすることができます。

> [!NOTE]
> テーブル プロジェクションは、Azure Storage によって課せられた[ストレージ制限](/rest/api/storageservices/understanding-the-table-service-data-model)で制御されています。 エンティティ サイズは 1 MB を超えることはできず、また、単一のプロパティは 64 KB を超えられません。 これらの制約によって、テーブルは、多数の小さなエンティティの格納に適したソリューションになっています。

### <a name="single-table-example"></a>1 つのテーブルの例

テーブルのスキーマは、プロジェクション (テーブル名とキー)、およびテーブル (列) のシェイプを指定するソースによってその一部が指定されます。 この例では、定義の詳細に注目できるように、1 つのテーブルだけを示しています。

```json
"projections" : [
  {
    "tables": [
      { "tableName": "Hotels", "generatedKeyName": "HotelId", "source": "/document/tableprojection" }
    ]
  }
]
```

列は、"source" から派生しています。 HotelId、HotelName、Category、Description を含む以下のデータ シェイプは、これらの列がテーブルに作成されます。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#3",
    "description": null,
    "context": "/document",
    "inputs": [
    {
        "name": "HotelId",
        "source": "/document/HotelId"
    },
    {
        "name": "HotelName",
        "source": "/document/HotelName"
    },
    {
        "name": "Category",
        "source": "/document/Category"
    },
    {
        "name": "Description",
        "source": "/document/Description"
    },
    ],
    "outputs": [
    {
        "name": "output",
        "targetName": "tableprojection"
    }
    ]
}
```

### <a name="multiple-table-slicing-example"></a>複数のテーブル (スライス) の例

テーブル プロジェクションの一般的なパターンでは、複数の関連テーブルを使用します。ここでは、システムによって生成された partitionKey および rowKey の列が作成され、同じプロジェクション グループの下にあるすべてのテーブルのテーブル間リレーションシップをサポートします。 

複数のテーブルを作成することは、関連データの集計方法を制御する場合に便利です。 エンリッチメントされたコンテンツに無関係または独立したコンポーネントがある場合 (たとえば、ドキュメントから抽出されたキーワードが、同じドキュメントで認識されたエンティティとは無関係であるなど)、これらのフィールドを隣接するテーブルに分割することができます。

複数のテーブルに射影する場合、子ノードが同じグループ内の別のテーブルのソースでない限り、完全な形状が各テーブルに射影されます。 既存のプロジェクションの子であるソース パスを含むプロジェクションを追加すると、子ノードが親ノードからスライスされ、関連する新しいテーブルに射影されます。 この手法により、すべてのプロジェクションのソースとして使用できる 1 つのノードを Shaper スキルで定義できます。

複数のテーブルのパターンは、次の要素で構成されます。

+ 親またはメイン テーブルとして 1 つのテーブル
+ エンリッチされたコンテンツのスライスを含む追加のテーブル

たとえば、ある Shaper スキルが、ホテルの情報を含む "EnrichedShape" と、キー フレーズ、場所、組織などのエンリッチされたコンテンツを出力するとします。 メイン テーブルには、ホテル (ID、名前、説明、住所、カテゴリ) を説明するフィールドが含まれます。 キー フレーズには、キー フレーズの列があります。 エンティティには、エンティティ列があります。

```json
"projections" : [
  {
    "tables": [
    { "tableName": "MainTable", "generatedKeyName": "HotelId", "source": "/document/EnrichedShape" },
    { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
    { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
    ]
  }
]
```

### <a name="naming-relationships"></a>リレーションシップの名前付け

テーブル間で、またはプロジェクションの種類間であっても、データを関連付けるためには `generatedKeyName` および `referenceKeyName` プロパティが使用されます。 子テーブル内の各行には、親を参照し返すプロパティが含まれています。 子での列またはプロパティの名前は、親からの `referenceKeyName` です。 `referenceKeyName` が指定されていない場合、サービスでは、親からの `generatedKeyName` を既定値とします。 

Power BI では、これらの生成されたキーを利用して、テーブル内のリレーションシップを検出します。 子テーブル内の列に別の名前を付ける必要がある場合は、親テーブル上に `referenceKeyName` プロパティを設定します。 例として、tblDocument テーブル上で `generatedKeyName` を ID として設定し、`referenceKeyName` を DocumentID として設定します。 これにより、tblEntities および tblKeyPhrases テーブルに、ドキュメント ID を含む DocumentID という名前の列が生成されます。

## <a name="define-an-object-projection"></a>オブジェクト プロジェクションを定義する

オブジェクト プロジェクションは、任意のノードをソースにすることができる強化ツリーの JSON 表現です。 テーブル プロジェクションと比較すると、オブジェクト プロジェクションはより簡単に定義でき、ドキュメント全体のプロジェクションを行うときに使用されます。 オブジェクト プロジェクションは、1 つのコンテナー内では単一のプロジェクションに限定され、スライスすることはできません。

オブジェクト プロジェクションを定義するには、プロジェクション プロパティ内で `objects` 配列を使用します。 オブジェクト プロジェクションには、次の 3 つの必須プロパティがあります。

| プロパティ | 説明 |
|----------|-------------|
| storageContainer | Azure Storage で作成された新しいコンテナーの名前を決定します。  |
| generatedKeyName | 各行を一意に識別するキーの列名。 この値はシステムによって生成されます。 このプロパティを省略すると、名前付け規則としてテーブル名と "キー" を使用する列が自動的に作成されます。 |
| ソース | プロジェクションのルートとなるエンリッチメント ツリーのノードへのパス。 このノードは通常、BLOB の構造を決定する複雑なデータ シェイプへの参照です。|

次の例では、個々のホテル ドキュメントを、`hotels` というコンテナーに射影しています (BLOB ごとに 1 つのホテル ドキュメント)。

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
        "storageContainer": "hotels",
        "source": "/document/objectprojection",
        }
      ]
    },
    {
        "files": [ ]
    }
  ]
}
```

ソースは、"objectprojection" という名前の Shaper スキルの出力です。 各 BLOB は、各フィールドの入力を JSON で表現したものになります。

```json
    {
      "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
      "name": "#3",
      "description": null,
      "context": "/document",
      "inputs": [
        {
          "name": "HotelId",
          "source": "/document/HotelId"
        },
        {
          "name": "HotelName",
          "source": "/document/HotelName"
        },
        {
          "name": "Category",
          "source": "/document/Category"
        },
        {
          "name": "keyPhrases",
          "source": "/document/HotelId/keyphrases/*"
        },
      ],
      "outputs": [
        {
          "name": "output",
          "targetName": "objectprojection"
        }
      ]
    }
```

## <a name="define-a-file-projection"></a>ファイル プロジェクションを定義する

ファイル プロジェクションは、常にバイナリで正規化されたイメージです。正規化とは、スキルセットの実行に使用するための潜在的なサイズ変更やローテーションを指します。 オブジェクト プロジェクションと同様に、ファイル プロジェクションは Azure Storage で BLOB として作成され、(JSON ではなく) バイナリ データが含まれます。

ファイル プロジェクションを定義するには、プロジェクション プロパティ内で `files` 配列を使用します。 ファイル プロジェクションには、次の 3 つの必須プロパティがあります。

| プロパティ | 説明 |
|----------|-------------|
| storageContainer | Azure Storage で作成された新しいコンテナーの名前を決定します。  |
| generatedKeyName | 各行を一意に識別するキーの列名。 この値はシステムによって生成されます。 このプロパティを省略すると、名前付け規則としてテーブル名と "キー" を使用する列が自動的に作成されます。 |
| ソース | プロジェクションのルートとなるエンリッチメント ツリーのノードへのパス。 イメージ ファイルの場合、ソースは常に `/document/normalized_images/*` です。  ファイル プロジェクションは、`normalized_images` コレクションに対してのみ機能します。 インデクサーもスキルセットも、元の正規化されていないイメージを通過しません。|

宛先は常に BLOB コンテナーで、ドキュメント ID の base64 でエンコードされた値のフォルダー プレフィックスが付きます。 複数のイメージがある場合は、同じフォルダーにまとめて配置されます。 ファイル プロジェクションとオブジェクト プロジェクションのコンテナーを同じにすることはできないため、両者は別々のコンテナーに射影する必要があります。 

次の例では、エンリッチされたドキュメントのドキュメント ノードから抽出したすべての正規化されたイメージを、`myImages` というコンテナーにプロジェクションしています。

```json
"projections": [
    {
        "tables": [ ],
        "objects": [ ],
        "files": [
            {
                "storageContainer": "myImages",
                "source": "/document/normalized_images/*"
            }
        ]
    }
]
```

## <a name="test-projections"></a>プロジェクションのテスト

次の手順に従って、プロジェクションの処理を行うことができます。

1. ナレッジ ストアの `storageConnectionString` プロパティに有効な V2 汎用ストレージ アカウント接続文字列を設定します。  

1. プロジェクション定義をスキルセットのボディに入れた PUT 要求を発行して、[スキルセットを更新](/rest/api/searchservice/update-skillset)します。

1. [インデクサーを実行](/rest/api/searchservice/run-indexer)してスキルセットを実行します。 

1. [インデクサーの実行を監視](search-howto-monitor-indexers.md)して進行状況を確認し、エラーを検出します。

1. [ストレージ ブラウザーを使用](knowledge-store-view-storage-explorer.md)して、Azure Storage でオブジェクトが作成されたことを確認します。

1. テーブルを投影する場合は、テーブルの操作と可視化を行うために [Power BI にインポート](knowledge-store-connect-power-bi.md)します。 ほとんどの場合、Power BI によってテーブル間のリレーションシップが自動的に検出されます。

## <a name="common-issues"></a>一般的な問題

以下の手順のいずれかを省略すると、予期しない結果になることがあります。 出力が正しくない場合は、以下の条件を確認してください。

+ 文字列のエンリッチメントが有効な JSON に整形されていない。 たとえば、キー フレーズによって `merged_content` がエンリッチされるなど、文字列がエンリッチされた場合、エンリッチされたプロパティはエンリッチメント ツリー内で `merged_content` の子として表されます。 既定の表現は、適切な形式の JSON ではありません。 射影時には必ず、エンリッチメントを名前と値を含む有効な JSON オブジェクトに変換してください。 Shaper スキルを使用するか、インライン シェイプを定義すると、この問題を解決できます。

+ ソース パスの末尾にある `/*` を省略する。 プロジェクションのソースが `/document/projectionShape/keyPhrases` の場合、キー フレーズの配列は単一のオブジェクトまたは行として射影されます。 ソース パスを `/document/projectionShape/keyPhrases/*` に設定した場合は、キー フレーズごとに単一の行またはオブジェクトが生成されます。

+ パス構文エラー。 [パス セレクター](cognitive-search-concept-annotations-syntax.md)では大文字と小文字が区別されるため、セレクターに対して大文字と小文字を正しく使用していないと、入力が見つからないという警告が表示される場合があります。 

## <a name="next-steps"></a>次のステップ

次の手順では、豊富なスキルセットからの出力の整形とプロジェクションについて説明します。 スキルセットが複雑な場合は、次の記事でシェイプとプロジェクションの両方の例を示します。

> [!div class="nextstepaction"]
> [シェイプとプロジェクションの詳細な例](knowledge-store-projection-example-long.md)
