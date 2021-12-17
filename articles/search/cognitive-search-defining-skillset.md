---
title: スキルセットを作成する
titleSuffix: Azure Cognitive Search
description: データの抽出、自然言語処理、または画像分析のステップを定義して、データから構造化された情報を抽出して強化し、Azure Cognitive Search で使用できるようにします。
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/15/2021
ms.openlocfilehash: 4425a4b7c29bc0f4bc237c021610087c933631d8
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224035"
---
# <a name="create-a-skillset-in-azure-cognitive-search"></a>Azure Cognitive Search にスキルセットを作成する

![インデクサーのステージ](media/cognitive-search-defining-skillset/indexer-stages-skillset.png "インデクサーのステージ")

スキルセットは、データを抽出して、データを検索できるようにする操作を定義します。 ドキュメント解析後にスキルセットが実行されるのは、テキストとイメージのコンテンツがソース ドキュメントから抽出された後と、ソース ドキュメントのフィールドがインデックスまたはナレッジ ストアのコピー先フィールドに (必要に応じて) マップされた後です。

この記事では、スキルセットを作成する手順について説明します。 参考のために、この記事では、[作成スキルセット (REST API)](/rest/api/searchservice/create-skillset)を使用します。 

スキルセットの使用規則には、次のようなものがあります。

+ スキルセットはトップレベル リソースであり、これは一度作成して多数のインデクサーで参照できます。
+ 1 つのスキルセットには、スキルが少なくとも 1 つ必要です。
+ スキルセットは、同じ種類のスキル (たとえば、複数の Shaper スキル) を繰り返すことができます。

インデクサーはスキルセットの実行を促進することを思い出してください。つまり、スキルセットをテストする前に、[インデクサー](search-howto-create-indexers.md)、[データ ソース](search-data-sources-gallery.md)、[検索インデックス](search-what-is-an-index.md)を作成する必要があります。

> [!TIP]
> [エンリッチメント キャッシング](cognitive-search-incremental-indexing-conceptual.md)を有効にすることで、既に処理したコンテンツを再利用し、開発コストを削減することができます。

## <a name="skillset-definition"></a>スキルセットの定義

基本構造から開始します。 [REST API](/rest/api/searchservice/create-skillset) では、スキルセットは JSON で作成され、以下のセクションがあります。

```json
{
   "name":"skillset-template",
   "description":"A description makes the skillset self-documenting (comments aren't allowed in JSON itself)",
   "skills":[
      
   ],
   "cognitiveServices":{
      "@odata.type":"#Microsoft.Azure.Search.CognitiveServicesByKey",
      "description":"A Cognitive Services resource in the same region as Azure Cognitive Search",
      "key":"<Your-Cognitive-Services-Multiservice-Key>"
   },
   "knowledgeStore":{
      "storageConnectionString":"<Your-Azure-Storage-Connection-String>",
      "projections":[
         {
            "tables":[ ],
            "objects":[ ],
            "files":[ ]
         }
      ]
    },
    "encryptionKey":{ }
}
```

名前と説明の後には、スキルセットに以下の 4 つの主要なプロパティがあります。

+ `skills` アレイは、各スキルに必要な入力に基づいて検索サービスによって実行順序が決定される、順序付けされていない[スキルのコレクション](cognitive-search-predefined-skills.md)です。 スキルが独立している場合は、並列に実行されます。 スキルには、実用的なもの (テキストの分割など)、変革をもたらすもの (Cognitive Services の AI をベースにしたもの)、ユーザーが提供するカスタム スキルなどがあります。 次のセクションでは、スキル配列の例を示しています。

+ `cognitiveServices` は、Cognitive Services APIs を呼び出す[課金対象のスキル](cognitive-search-predefined-skills.md)に使用されます。 課金対象のスキルやカスタム エンティティの参照を使用していない場合は、このセクションを削除します。 使用している場合は[リソースをアタッチ](cognitive-search-attach-cognitive-services.md)します。

+ `knowledgeStore`、(省略可能) Azure Storage のテーブル、BLOB、およびファイルにスキルセットの出力を投影するための Azure Storage アカウントと設定を指定します。 不要な場合は、このセクションを削除します。それ以外の場合は、[ナレッジストアを指定します](knowledge-store-create-rest.md)。

+ `encryptionKey`、(省略可能) スキルセット定義で機微な内容を暗号化するために使用される Azure Key Vault および[ユーザーが管理するキー](search-security-manage-encryption-keys.md)を指定します。 ユーザーが管理する暗号化を使用していない場合は、このプロパティを削除します。

## <a name="add-a-skills-array"></a>スキルの配列を追加する

スキルセット定義内では、実行するスキルをスキル配列で指定します。 次の例では、関連付けられていない 2 つの[組み込みのスキル](cognitive-search-predefined-skills.md)を示すことによって、そのコンポジションについて説明します。 各スキルには、型、コンテキスト、入力、および出力があることに注意してください。 

```json
"skills":[
  {
    "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
    "context": "/document",
    "categories": [ "Organization" ],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "organizations",
        "targetName": "orgs"
      }
    ]
  },
  {
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "score",
        "targetName": "mySentiment"
      }
    ]
  }
]
```

> [!NOTE]
> [条件付きスキル](cognitive-search-skill-conditional.md)を使用して式を作成することにより、ループや分岐が含まれる複雑なスキルセットを構築できます。 構文では、いくつかの修正が加えられた [JSON ポインター](https://tools.ietf.org/html/rfc6901) パス表記を使用して、強化ツリー内のノードを識別します。 `"/"` ではツリー内の下位レベルが走査され、`"*"` はコンテキストでの for-each 演算子として機能します。 この記事の多くの例は、この構文を示しています。 

### <a name="how-built-in-skills-are-structured"></a>組み込みのスキルがどのように構造化されているか

各スキルは、入力値とそれに必要なパラメーターに関して一意です。 [各スキルのドキュメント](cognitive-search-predefined-skills.md)には、与えられたスキルのすべてのパラメーターとプロパティが記述されています。 違いはありますが、ほとんどのスキルは共通のセットを共有し、同様のパターンになっています。 いくつかのポイントを説明するために、[エンティティ認識スキル](cognitive-search-skill-entity-recognition-v3.md)を例に挙げます。

```json
{
  "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
  "context": "/document",
  "categories": [ "Organization" ],
  "defaultLanguageCode": "en",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content"
    }
  ],
  "outputs": [
    {
      "name": "organizations",
      "targetName": "orgs"
    }
  ]
}
```

共通パラメーターには、"odata. type"、"inputs"、"outputs" などがあります。 その他のパラメーター ("categories" および "defaultLanguageCode") は、エンティティ認識に固有のパラメーターの例です。 

+ **"odata.type"** は、各スキルを一意に識別します。 型については、[スキル リファレンス ドキュメント](cognitive-search-predefined-skills.md)を参照してください。

+ **"context"** は強化ツリーのノードであり、操作が行われるレベルを表します。 すべてのスキルにこのプロパティがあります。 "context" フィールドが明示的に設定されていない場合、既定のコンテキストは `"/document"` になります。 この例では、コンテキストはドキュメント全体であり、つまり、認識スキルがドキュメントごとに 1 回呼び出されることになります。

  また、コンテキストは、エンリッチメント ツリーで出力が生成される場所も決定します。 この例では、スキルは `"organizations"` というプロパティを返し、`orgs` としてキャプチャされ、`"/document"` の子ノードとして追加されています。 ダウンストリーム スキルでは、この新しく作成されたエンリッチメント ノードへのパスは `"/document/orgs"` です。 特定のドキュメントでは、`"/document/orgs"` の値は、テキストから抽出された組織の配列になります (例: `["Microsoft", "LinkedIn"]`)。 パスの構文の詳細については、「[スキルセット内の注釈の参照](cognitive-search-concept-annotations-syntax.md)」を参照してください。

+ **"入力"** は、受信データの送信元とその使用方法を指定します。 エンティティ認識の場合、入力の 1 つは `"text"` であり、エンティティを分析するコンテンツです。 コンテンツは、エンリッチメント ツリー内の `"/document/content"` ノードから供給されます。 エンリッチメント ツリーでは、`"/document"` がルート ノードです。 Azure Blob インデクサーを使用して取得されたドキュメントの場合、各ドキュメントの `content` フィールドは、インデクサーによって作成される標準フィールドです。 

+ **"outputs"** はスキルの出力を表します。 各スキルは、スキルセット内で名前によって参照される特定の種類の出力を生成するように設計されています。 エンティティ認識の場合、それがサポートされている出力の 1 つに `"organizations"` があります。 各スキルのドキュメントでは、生成できる出力について説明しています。

出力は、処理中にのみ存在します。 この出力をダウンストリーム スキルの入力に連結するには、`"/document/orgs"` としてこの出力を参照します。 検索インデックスのフィールドに出力を送るには、インデクサーで[出力フィールドマッピングを作成](cognitive-search-output-field-mapping.md)します。 出力をナレッジ ストアに送るには、[プロジェクションを作成します](knowledge-store-projection-overview.md)。

あるスキルからの出力が、別のスキルからの出力と競合する可能性があります。 同じ出力を返すスキルが複数ある場合は、エンリッチメント ノード パスでの名前のあいまいさを解消するために `"targetName"` を使用します。

状況によっては、配列の各要素を別々に参照する必要があります。 たとえば、`"/document/orgs"` の *各要素* を別のスキルに別々に渡す必要があるとします。 これを行うには、パスにアスタリスクを追加します。`"/document/orgs/*"` 

2 番目の、感情分析のスキルは、最初のエンリッチャーと同じパターンに従います。 入力として `"/document/content"` を受け取り、コンテンツのインスタンスごとにセンチメント スコアを返します。 "context" フィールドを明示的に設定していないため、出力 (mySentiment) は、`"/document"` の子になります。

```json
{
  "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content"
    }
  ],
  "outputs": [
    {
      "name": "score",
      "targetName": "mySentiment"
    }
  ]
}
```

## <a name="adding-a-custom-skill"></a>カスタム スキルの追加

[カスタム スキル](cognitive-search-custom-skill-web-api.md)の例を次に示します。 URI は Azure 関数を指します。これにより、指定したモデルまたは変換が呼び出されます。 詳細については、[カスタム インターフェイスの定義](cognitive-search-custom-skill-interface.md)に関するページを参照してください。

カスタム スキルは、パイプラインの外部にあるコードを実行していますが、スキル配列では、もう 1 つのスキルに過ぎません。 組み込みのスキルと同様に、型、コンテキスト、入力、および出力があります。 また、組み込みのスキルと同様に、エンリッチメント ツリーの読み取りと書き込みも行います。 "context" フィールドが、アスタリスク付きで `"/document/orgs/*"` に設定されていることに注目してください。これは、エンリッチメント ステップが`"/document/orgs"` の下にある組織 "*ごと*" に呼び出されることを意味します。

出力 (ここでは会社の説明) が、特定された組織ごとに生成されます。 ダウンストリームのステップ (キー フレーズの抽出など) で説明を参照するときは、パス `"/document/orgs/*/companyDescription"` を使用して実行します。 

```json
{
  "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
  "description": "This skill calls an Azure function, which in turn calls custom code",
  "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/InvokeCode?code=foo",
  "httpHeaders": {
      "Ocp-Apim-Subscription-Key": "foobar"
  },
  "context": "/document/orgs/*",
  "inputs": [
    {
      "name": "query",
      "source": "/document/orgs/*"
    }
  ],
  "outputs": [
    {
      "name": "description",
      "targetName": "companyDescription"
    }
  ]
}
```

## <a name="send-output-to-an-index"></a>インデックスへの出力を送信する

各スキルが実行されると、その出力がドキュメントのエンリッチメント ツリーのノードとして追加されます。 強化されたドキュメントは、パイプライン内に一時的なデータ構造として存在します。 永続的なデータ構造を作成し、スキルが実際にどのように生成されるかを完全に把握するには、検索インデックスまたは[ナレッジ ストア](knowledge-store-concept-intro.md)に出力を送信する必要があります。

スキルセット評価の初期段階では、最小限の労力で暫定的な結果を確認することをお勧めします。 セットアップが簡単であるため、検索インデックスをお勧めします。 スキルの出力ごとに、[インデクサーの出力フィールド マッピング](cognitive-search-output-field-mapping.md)と、インデックスのフィールドを定義します。

:::image type="content" source="media/cognitive-search-defining-skillset/skillset-indexer-index-combo.png" alt-text="Person エンティティがスキル出力、インデクサー フィールド マッピング、およびインデックス フィールドでどのように定義されているかを示すオブジェクト図。":::

インデクサーを実行した後、[検索エクスプローラー](search-explorer.md)を使用してインデックスからドキュメントを返し、各フィールドの内容を確認して、スキルセットが検出または作成されたかどうかを確認できます。

次の例は、テキストのチャンクに含まれる人物、場所、組織、およびその他のエンティティを検出したエンティティ認識スキルの結果を示しています。 検索エクスプローラーで結果を表示すると、スキルによってソリューションに価値が追加されるかどうかを判断するのに役立ちます。

:::image type="content" source="media/cognitive-search-defining-skillset/doc-in-search-explorer.png" alt-text="検索エクスプローラーでのドキュメントのスクリーンショット。":::

## <a name="tips-for-a-first-skillset"></a>最初のスキルセットのヒント

+ Blob Storage またはサポートされている別のインデクサー データ ソース内のコンテンツの代表的なサンプルをアセンブルし、**データのインポート** ウィザードを実行すると、スキルセット、インデックス、インデクサー、およびデータ ソース オブジェクトを作成できます。 

  このウィザードでは、インデックスでのフィールドの定義、インデクサーでの出力フィールド マッピングの定義、使用している場合はナレッジ ストアでのプロジェクションなど、初めは難しいいくつかの手順を自動化しています。 OCR や画像解析などの一部のスキルでは、ドキュメントの解析中で分離された画像とテキストの内容を結合するユーティリティ スキルがウィザードによって追加されます。

+ または、変換結果を表示するためにクエリを実行できる、スキルセットからインデックスまでのスキルを評価するために必要なすべてのオブジェクト定義の例をすべて提供するスキル Postman コレクションをインポートすることもできます。

## <a name="next-steps"></a>次のステップ

コンテキストと入力ソースのフィールドは、エンリッチメント ツリー内のノードへのパスです。 次の手順として、エンリッチメント ツリー内のノードへのパスを設定するための構文の詳細について説明します。

> [!div class="nextstepaction"]
> [スキルセットで注釈を参照する](cognitive-search-concept-annotations-syntax.md)
