---
title: スキルセットの概念
titleSuffix: Azure Cognitive Search
description: スキルセットは、Azure Cognitive Search で AI エンリッチメント パイプラインを作成する場所です。 スキルセットの作成に関する重要な概念と詳細について説明します。
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 08bf1a57c4b7d4905693c3f11964feeb04c8beef
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131014863"
---
# <a name="skillset-concepts-in-azure-cognitive-search"></a>Azure Cognitive Search のスキルセットの概念

この記事は、スキルセットの概念と構成について理解を深める必要がある開発者を対象としています。ここでは、[AI エンリッチメント](cognitive-search-concept-intro.md)に関する高度な概念とワークフローの知識を前提としています。

スキルセットは、[インデクサー](search-indexer-overview.md)にアタッチされている Azure Cognitive Search の再利用可能なリソースで、 1 つ以上のスキルが含まれています。スキルは、外部のデータ ソースから取得したドキュメントに対して、組み込みの AI や外部カスタム処理を呼び出すアトミック操作です。

スキルによって行われるのは、スキルセット処理の開始から終了まで、エンリッチされたドキュメントに対する読み取りと書き込みです。 エンリッチされたドキュメントは、最初はデータ ソースから抽出された単なる生のコンテンツですが、スキルが実行されるたびに、構造と実質的な内容を取得していきます。 最終的には、エンリッチメントされたドキュメントのノードが、検索インデックス内の[フィールドにマップされる](cognitive-search-output-field-mapping.md)か、ナレッジ ストア内の[プロジェクションにマップされる](knowledge-store-projection-overview.md)ため、コンテンツの適切なルーティングが可能です。そこで他のアプリによってクエリが実行されたり、消費されたりします。

:::image type="content" source="media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg" alt-text="スキルセットを使用したパイプライン" border="false":::

## <a name="skillset-definition"></a>スキルセットの定義

スキルセットは、アトミック エンリッチメント操作 (テキストの翻訳、キー フレーズの抽出、イメージ ファイルからの光学式文字認識の実行など) を表す 1 つ以上の "*スキル*" の配列です。 スキルには、Microsoft の[組み込みスキル](cognitive-search-predefined-skills.md)と[カスタム スキル](cognitive-search-create-custom-skill-example.md)があり、カスタム スキルには、ユーザーが指定するモデルまたは処理ロジックが含まれています。 これにより、インデックスを付けるときに使用される、またはナレッジ ストアに射影されるエンリッチされたドキュメントが生成されます。

スキルには型、コンテキスト、入力、出力があり、多くの場合、連携しています。 連携する 2 つの[組み込みスキル](cognitive-search-predefined-skills.md)の例を次に示します。また、スキルセット定義の用語をいくつか紹介します。 

+ スキル #1 は、"reviews_text" ソース フィールドのコンテンツを入力として受け取り、そのコンテンツを出力として 5,000 文字の "pages" に分割する[テキスト分割スキル](cognitive-search-skill-textsplit.md)です。 大きなテキストをより小さなチャンクに分割すると、自然言語の処理中に得られる結果が向上する場合があります。

+ スキル #2 は、"pages" を入力として受け入れ、センチメント分析の結果が含まれる "Sentiment" という名前の新しいフィールドを作成する[センチメント検出スキル](cognitive-search-skill-sentiment.md)です。

```json
{
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "#1",
            "description": null,
            "context": "/document/reviews_text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "name": "#2",
            "description": null,
            "context": "/document/reviews_text/pages/*",
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text/pages/*",
                }
            ],
            "outputs": [
                {
                    "name": "score",
                    "targetName": "Sentiment"
                }
            ]
        }
. . . 
}
```

上記の例で注目すべき重要なポイントは、入力と出力が名前と値の組であり、あるスキルの出力と、ダウンストリーム スキルの入力を一致させることができること、そして、すべてのスキルに、強化ツリーのどこで処理が発生するかを判断するコンテキストが含まれることです。

入力と出力がどのように形成されるかの詳細については、[注釈の参照方法](cognitive-search-concept-annotations-syntax.md)に関するページをご覧ください。

## <a name="enrichment-tree"></a>強化ツリー

エンリッチされたドキュメントは、スキルセットの実行中に作成された一時的なツリー状のデータ構造で、スキルを通じて行われたすべての変更を収集し、アドレス指定可能なノードの階層で表します。 ノードには、外部データ ソースから逐語的に渡されたエンリッチされていないフィールドも含まれます。 エンリッチされたドキュメントが存在するのは、スキルセットの実行中ですが、ナレッジ ストアにキャッシュまたは永続化することができます。 

エンリッチされたドキュメントは、最初は、"[*ドキュメント解析*](search-indexer-overview.md#document-cracking)" 中にデータ ソースから抽出されたコンテンツに過ぎません。ここでテキストやイメージがソースから抽出され、言語解析やイメージ解析に利用できるようになります。 

最初のコンテンツは "*ルート ノード*" (`document\content`) であり、通常は、ドキュメント全体であるか、ドキュメントのクラッキング中にデータ ソースから抽出された正規化されたイメージです。 強化ツリーでの表現方法は、データ ソースの種類によって異なります。 次の表は、サポートされているいくつかのデータ ソースについて、エンリッチメント パイプラインに入ったドキュメントの状態を示しています。

|データ ソース/解析モード|Default|JSON、JSON 行、CSV|
|---|---|---|
|Blob Storage|/document/content<br>/document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|Azure SQL|/document/{column1}<br>/document/{column2}<br>…|該当なし |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|該当なし|

スキルが実行されると、出力が新しいノードとして強化ツリーに追加されます。 これらのノードは、ダウンストリーム スキルの入力として使用でき、最終的にナレッジ ストアに射影されるか、インデックス フィールドにマップされます。 出力は、翻訳された文字列などのコンテンツを作成するスキルによって、エンリッチされたドキュメントに書き込まれます。 同様に、アップストリーム スキルの出力を使用するスキルによって、エンリッチされたドキュメントから読み取られ、必要な入力が取得されます。

:::image type="content" source="media/cognitive-search-working-with-skillsets/skillset-def-enrichment-tree.png" alt-text="強化ツリーからのスキルの読み取りと書き込み" border="false":::

強化ツリーは、ソースから抽出されたコンテンツとメタデータ、および[テキスト翻訳スキル](cognitive-search-skill-text-translation.md)の `translated_text`、[エンティティ認識スキル](cognitive-search-skill-entity-recognition-v3.md)の `locations`、[キー フレーズ抽出スキル](cognitive-search-skill-keyphrases.md)の `keyPhrases` といったスキルによって作成された新しいノードで構成されます。 ビジュアル エディターで[強化ツリーを視覚化して操作](cognitive-search-debug-session.md)できますが、ほとんどが内部構造です。 

強化は変更できません。作成されたノードを編集することはできません。 スキルセットや強化ツリーの複雑さが増しても、強化ツリー内のすべてのノードをインデックスやナレッジ ストアにする必要はありません。 エンリッチメント出力のサブセットのみを選択的に保持して、使用する情報のみを保持することができます。

スキルの入出力によって、強化ツリーの読み取りと書き込みが行われるため、スキルセット設計の一環として[出力フィールド マッピング](cognitive-search-output-field-mapping.md)を作成します。これにより、強化ツリーのコンテンツが、検索インデックス内のフィールドに移動されます。 同様に、ナレッジ ストアを作成する場合は、プロジェクションに割り当てられている[シェイプ](knowledge-store-projection-shape.md)に、出力をマップできます。

> [!NOTE]
> 強化ツリー形式により、エンリッチメント パイプラインでは、メタデータをプリミティブ データ型にもアタッチできます。 メタデータは有効な JSON オブジェクトになりませんが、ナレッジ ストア内のプロジェクション定義で有効な JSON 形式に射影できます。 詳細については、[Shaper スキル](cognitive-search-skill-shaper.md)に関するページをご覧ください。

## <a name="context"></a>コンテキスト

各スキルにはコンテキストがあります。コンテキストはドキュメント全体 (`/document`) であったり、ツリー内の下位のノード (`/document/countries/`) であったりします。 コンテキストによって次のことが決まります。

+ 1 つの値に対してスキルが実行される回数 (フィールドごと、ドキュメントごとに 1 回)。型コレクションのコンテキスト値の場合、`/*` を追加すると、コレクション内のインスタンスごとにスキルが 1 回呼び出されます。 

+ 出力の宣言。スキルの出力が追加される強化ツリー内の場所。 出力は、常にコンテキスト ノードの子としてツリーに追加されます。

+ 入力の形状。 複数レベルのコレクションの場合、コンテキストを親コレクションに設定すると、スキル入力の形状に影響します。 たとえば、国または地域のリストが含まれる強化ツリーがあり、それぞれが郵便番号のリストを含む州のリストでエンリッチメント処理されている場合、コンテキストをどのように設定するかによって、入力がどのように解釈されるかが決まります。

|Context|入力|入力の形状|スキルの呼び出し|
|-------|-----|--------------|----------------|
|`/document/countries/*` |`/document/countries/*/states/*/zipcodes/*` |国または地域のすべての郵便番号のリスト |国または地域ごとに 1 回 |
|`/document/countries/*/states/*` |`/document/countries/*/states/*/zipcodes/*` |州内の郵便番号のリスト | 国または地域と州の組み合わせごとに 1 回|

## <a name="enrichment-example"></a>エンリッチメントの例

この例では、[ホテル レビュー スキルセット](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json)を参照ポイントとして使用し、スキルの実行によって[強化ツリー](cognitive-search-working-with-skillsets.md#enrichment-tree)がどのように発展するかを概念図を使って説明します。

また、この例では以下もわかります。

+ スキルの実行回数の決定に対して、スキルのコンテキストと入力がどのように働くか
+ コンテキストに基づく入力の形状

この例では、CSV ファイルのソース フィールドに、ホテルに関する顧客レビュー ("reviews_text") と評価 ("reviews_rating") が含まれています。 インデクサーによって、BLOB ストレージからメタデータ フィールドが追加され、スキルによって、翻訳されたテキスト、センチメント スコア、キーフレーズ検出が追加されます。

ホテル レビューの例で、エンリッチメント プロセス内の "ドキュメント" は 1 つのホテル レビューを表します。

> [!TIP]
> このデータの検索インデックスやナレッジ ストアを作成するには、[Azure portal](knowledge-store-create-portal.md)、[Postman、または REST API](knowledge-store-create-rest.md) を使用します。 [デバッグ セッション](cognitive-search-debug-session.md)を使用して、スキルセットの構成、依存関係、強化ツリーへの影響の分析情報を確認することもできます。 この記事のイメージは、デバッグ セッションからプルされます。

概念的には、最初の強化ツリーは次のようになります。

![ドキュメント解析後の強化ツリー](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "ドキュメント解析後、スキル実行前の強化ツリー")

すべての強化のルート ノードは `"/document"` です。 BLOB インデクサーを使用すると、`"/document"` ノードに子ノード `"/document/normalized_images"` と `"/document/content"` ができます。 この例のように、CSV データを使用すると、列名は `"/document"` の下のノードにマップされます。

### <a name="skill-1-split-skill"></a>スキル #1: 分割スキル

ソース コンテンツが大量のテキストで構成されている場合は、言語、センチメント、キーフレーズ検出の精度を高めるために、より小さいコンポーネントに分割すると便利です。 使用できるグレインは、ページと文の 2 つです。 ページは約 5,000 文字で構成されます。

通常、テキスト分割スキルは、スキルセットにおいて最初に選ばれます。

```json
"@odata.type": "#Microsoft.Skills.Text.SplitSkill",
"name": "#1",
"description": null,
"context": "/document/reviews_text",
"defaultLanguageCode": "en",
"textSplitMode": "pages",
"maximumPageLength": 5000,
"inputs": [
{
    "name": "text",
    "source": "/document/reviews_text"
}
],
"outputs": [
{
    "name": "textItems",
    "targetName": "pages"
}
```

`"/document/reviews_text"` のスキル コンテキストでは、この分割スキルは `reviews_text` に対して 1 回実行されます。 スキルの出力はリストであり、`reviews_text` が 5000 文字のセグメントに分割されています。 分割スキルからの出力は `pages` という名前で、これは強化ツリーに追加されます。 `targetName` 機能を使用することで、強化ツリーに追加される前に、スキル出力の名前を変更できます。

これで、強化ツリーのスキルのコンテキストの下に、新しいノードが配置されました。 このノードは、任意のスキル、プロジェクション、または出力フィールド マッピングで使用できます。 
 
![スキル #1 の後の強化ツリー](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "スキル #1 実行後の強化ツリー")

スキルによってノードに追加されたいずれかの強化にアクセスするには、強化の完全なパスが必要です。 たとえば、```pages``` ノードのテキストを別のスキルへの入力として使用する場合は、```"/document/reviews_text/pages/*"``` として指定する必要があります。 パスの詳細については、[注釈の参照](cognitive-search-concept-annotations-syntax.md)に関するページをご覧ください。

### <a name="skill-2-language-detection"></a>スキル #2: 言語検出

ホテル レビュー ドキュメントには、複数の言語で表される顧客フィードバックが含まれています。 言語検出スキルによって、どの言語が使用されているか判断されます。 結果は、キー フレーズ抽出とセンチメント検出に渡され (非表示)、センチメントと語句を検出するときに言語が考慮されます。

言語検出スキルは、スキルセットで定義されている 3 番目のスキルですが (スキル #3)、次に実行されるスキルです。 入力要求によってブロックされないため、前のスキルと並列に実行されます。 先行する分割スキルと同様に、言語検出スキルもドキュメントごとに 1 回呼び出されます。 強化ツリーには言語用の新しいノードが追加されています。

 ![スキル #2 の後の強化ツリー](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "スキル #2 実行後の強化ツリー")

### <a name="skills-3-and-4-sentiment-analysis-and-key-phrase-detection"></a>スキル #3 と #4 (感情分析とキー フレーズ検出)

お客様からのフィードバックには、ポジティブなものからネガティブなものまで、さまざまなエクスペリエンスが反映されています。 フィードバックは、感情分析スキルによって分析され、負数から正数の範囲内で一連のスコアが割り当てられます。センチメントがはっきりしない場合は、中立が割り当てられます。 感情分析と同時に、結果的に重要であると思われる単語と短いフレーズが、キー フレーズ検出によって特定され、抽出されます。

コンテキストとして `/document/reviews_text/pages/*` が指定された感情分析スキルとキー フレーズ スキルは両方とも、`pages` コレクション内の項目ごとに 1 回ずつ呼び出されます。 スキルからの出力は、関連付けられている page 要素の下のノードになります。 

スキルセットに含まれる残りのスキルを表示し、各スキルの実行によって強化のツリーがどのように成長し続けるかを視覚化できるようになるはずです。 マージ スキルや Shaper スキルなどの一部のスキルでも新しいノードが作成されますが、既存のノードのデータのみが使用され、新しい強化は作成されません。

![すべてのスキルの後の強化ツリー](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "すべてのスキルの後の強化ツリー")

上のツリーのコネクタの色は、強化が異なるスキルで作成されたことを示しています。ノードは個別に指定する必要があり、親ノードを選択したときに返されるオブジェクトの一部にはなりません。

### <a name="skill-5-shaper-skill"></a>スキル #5: Shaper スキル

出力に[ナレッジ ストア](knowledge-store-concept-intro.md)が含まれる場合は、最後のステップとして [Shaper スキル](cognitive-search-skill-shaper.md)を追加します。 Shaper スキルによって、強化ツリー内のノードからデータ シェイプが作成されます。 たとえば、複数のノードを 1 つのシェイプに統合することができます。 その後、このシェイプをテーブルとして射影し (ノードはテーブル内の列になります)、名前によってシェイプをテーブル プロジェクションに渡します。

整形が 1 つのスキルにまとめられているため、Shaper スキルは簡単に操作できます。 また、個々のプロジェクション内でインライン整形を選ぶこともできます。 Shaper スキルによって強化ツリーの加減が行われることはありません。したがって、これは視覚化されません。 代わりに、Shaper スキルは、既にある強化ツリーを再現する手段と考えることができます。 概念的には、これはデータベース内のテーブルからビューを作成するのと似ています。

```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "name": "#5",
  "description": null,
  "context": "/document",
  "inputs": [
    {
      "name": "name",
      "source": "/document/name"
    },
    {
      "name": "reviews_date",
      "source": "/document/reviews_date"
    },
    {
      "name": "reviews_rating",
      "source": "/document/reviews_rating"
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
      "name": "AzureSearch_DocumentKey",
      "source": "/document/AzureSearch_DocumentKey"
    },
    {
      "name": "pages",
      "sourceContext": "/document/reviews_text/pages/*",
      "inputs": [
        {
          "name": "Sentiment",
          "source": "/document/reviews_text/pages/*/Sentiment"
        },
        {
          "name": "LanguageCode",
          "source": "/document/Language"
        },
        {
          "name": "Page",
          "source": "/document/reviews_text/pages/*"
        },
        {
          "name": "keyphrase",
          "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
          "inputs": [
            {
              "name": "Keyphrases",
              "source": "/document/reviews_text/pages/*/Keyphrases/*"
            }
          ]
        }
      ]
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "tableprojection"
    }
  ]
}
```

## <a name="next-steps"></a>次のステップ

概要と例を参考にしながら、[組み込みスキル](cognitive-search-predefined-skills.md)を使用して、最初のスキルセットを作成してみてください。

> [!div class="nextstepaction"]
> [最初のスキルセットを作成する](cognitive-search-defining-skillset.md)
