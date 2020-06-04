---
title: スキルセットの概念とワークフロー
titleSuffix: Azure Cognitive Search
description: スキルセットは、Azure Cognitive Search で AI エンリッチメント パイプラインを作成する場所です。 スキルセットの作成に関する重要な概念と詳細について説明します。
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e8e263d29bc71ac76c374eeda78e5250a0af2095
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744787"
---
# <a name="skillset-concepts-and-composition-in-azure-cognitive-search"></a>Azure Cognitive Search でのスキルセットの概念と構成

この記事の対象者は、エンリッチメント パイプラインの動作をより深く理解する必要がある開発者であり、AI エンリッチメント プロセスの概念を理解していることが前提です。 この概念を初めて知った場合は、次の方法で開始します。
+ [Azure Cognitive Search における AI エンリッチメント](cognitive-search-concept-intro.md)
+ [ナレッジ ストア (プレビュー)](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>スキルセットを指定する
スキルセットは Azure Cognitive Search の再利用可能なリソースであり、スキルセットではインデックス作成時にテキストや画像の内容を分析、変換、および強化するために使用されるコグニティブ スキルのコレクションが指定されています。 スキルセットを作成すると、データ インジェスト フェーズでテキストや画像の強化をアタッチし、生コンテンツから新しい情報や構造を抽出して作成することができます。

スキルセットには、次の 3 つのプロパティがあります。

+    ```skills``` は、各スキルに必要な入力に基づいてプラットフォームによって実行順序が決定される、順序付けされていないスキルのコレクションです
+    ```cognitiveServices``` は、呼び出されたコグニティブ スキルの請求に必要な cognitive services key です
+    ```knowledgeStore``` は、強化されたドキュメントが射影されるストレージ アカウントです



スキルセットは JSON で作成します。 [式言語](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional)を使用して、ループや[分岐](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional)が含まれる複雑なスキルセットを構築できます。 式言語では、いくつかの修正が加えられた [JSON ポインター](https://tools.ietf.org/html/rfc6901) パス表記を使用して、強化ツリー内のノードを識別します。 ```"/"``` ではツリー内の下位レベルが走査され、```"*"``` はコンテキストでの for-each 演算子として機能します。 これらの概念については、例を参照してください。 いくつかの概念と機能を説明するために、[ホテル レビュー サンプル](knowledge-store-connect-powerbi.md)のスキルセットを見ていきます。 データのインポート ワークフローを行った後でスキルセットを表示するには、REST API クライアントを使用して[スキルセットを取得する](https://docs.microsoft.com/rest/api/searchservice/get-skillset)必要があります。

### <a name="enrichment-tree"></a>強化ツリー

スキルセットによってドキュメントが徐々にエンリッチメント処理される様子を見るため、エンリッチメント前のドキュメントの内容から始めましょう。 ドキュメント解析の出力は、データ ソースと選択した特定の解析モードに依存します。 これは、検索インデックスにデータを追加するときに、[フィールド マッピング](search-indexer-field-mappings.md)によってコンテンツを取得できるドキュメントの状態でもあります。
![パイプラインにおけるナレッジ ストアの図](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "パイプラインにおけるナレッジ ストアの図")

強化パイプラインに入ったドキュメントは、コンテンツおよび関連付けられている強化のツリーとして表されます。 このツリーは、ドキュメント解析の出力としてインスタンス化されます。 強化ツリー形式により、強化パイプラインではメタデータをプリミティブ データ型にもアタッチできます。これは有効な JSON オブジェクトではありませんが、有効な JSON 形式に射影できます。 次の表では、強化パイプラインに入ったドキュメントの状態を示します。

|データ ソース/解析モード|Default|JSON、JSON 行、CSV|
|---|---|---|
|Blob Storage|/document/content<br>/document/normalized_images/*<br>...|/document/{key1}<br>/document/{key2}<br>...|
|SQL|/document/{column1}<br>/document/{column2}<br>...|該当なし |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>...|該当なし|

 実行されたスキルによって、強化ツリーに新しいノードが追加されます。 これらの新しいノードは、ダウンストリームのスキル、ナレッジ ストアへの射影、またはインデックス フィールドへのマッピングに対する入力として使用できます。 強化は変更できません。作成されたノードを編集することはできません。 スキルセットや強化ツリーの複雑さが増しても、強化ツリー内のすべてのノードをインデックスやナレッジ ストアにする必要はありません。 

強化のサブセットのみを選択して、インデックスまたはナレッジ ストアに保存することができます。
このドキュメントの残りの部分では、[ホテル レビューの例](https://docs.microsoft.com/azure/search/knowledge-store-connect-powerbi)を使用しますが、他のすべてのデータ ソースからのドキュメントの強化に同じ概念が適用されます。

### <a name="context"></a>Context
各スキルにはコンテキストが必要です。 コンテキストによって次のことが決まります。
+    選択されたノードに基づいて、スキルが実行される回数。 型コレクションのコンテキスト値の場合、末尾に ```/*``` を追加すると、コレクション内のインスタンスごとにスキルが 1 回呼び出されます。 
+    スキルの出力が追加される強化ツリー内の場所。 出力は、常にコンテキスト ノードの子としてツリーに追加されます。 
+    入力の形状。 複数レベルのコレクションの場合、コンテキストを親コレクションに設定すると、スキル入力の形状に影響します。 たとえば、国または地域のリストが含まれるエンリッチメント ツリーがあり、それぞれが郵便番号のリストを含む州のリストでエンリッチメント処理されている場合、次のようになります。

|Context|入力|入力の形状|スキルの呼び出し|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |国または地域のすべての郵便番号のリスト |国または地域ごとに 1 回 |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |州内の郵便番号のリスト | 国または地域と州の組み合わせごとに 1 回|

### <a name="sourcecontext"></a>SourceContext

`sourceContext` は、スキルの入力と[プロジェクション](knowledge-store-projection-overview.md)でのみ使用されます。 それは、複数レベルの入れ子になったオブジェクトを構築するために使用されます。 場合によっては、新しいオブジェクトを作成して、それをスキルまたはプロジェクトへの入力としてナレッジストアに渡す必要があります。 エンリッチメント ノードは、エンリッチメント ツリー内の有効な JSON オブジェクトではない可能性があり、ツリー内のノードを参照すると、作成時のノードの状態のみが返されるため、エンリッチメントをスキル入力またはプロジェクションとして使用するには、適切な形式の JSON オブジェクトを作成する必要があります。 `sourceContext` を使用すると、階層型の匿名型オブジェクトを構築でき、コンテキストを使用する場合にのみ、複数のスキルが必要になります。 `sourceContext` の使用については、次のセクションで説明します。 エンリッチメントを生成したスキル出力を調べて、それがプリミティブ型ではなく有効な JSON オブジェクトであるかを確認します。

### <a name="projections"></a>プロジェクション

プロジェクションとは、ナレッジ ストアに保存するノードを強化ツリーから選択するプロセスです。 プロジェクションとは、テーブルのプロジェクションまたはオブジェクトのプロジェクションとして出力できるドキュメント (コンテンツと強化) のカスタム形状です。 プロジェクションの使用について詳しくは、[プロジェクションの操作](knowledge-store-projection-overview.md)に関する記事をご覧ください。

![フィールド マッピング オプション](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "エンリッチメント パイプラインのフィールド マッピング オプション")

上の図は、強化パイプライン内の場所に基づいて使用するセレクターを示したものです。

## <a name="generate-enriched-data"></a>強化データを生成する 

それでは、ホテル レビューのスキルセットの手順を見てみましょう。[チュートリアル](knowledge-store-connect-powerbi.md)に従うことで、スキルセットを作成したり、スキルセットを[表示](https://github.com/Azure-Samples/azure-search-postman-samples/)したりすることができます。 以下のことを見ていきます。

* 各スキルの実行によって強化ツリーがどのように発展するか 
* スキルの実行回数の決定に対してコンテキストと入力がどのように働くか 
* コンテキストに基づく入力の形状。 

インデクサーに対して区切りテキスト解析モードを使用しているため、強化プロセスにおけるドキュメントでは、CSV ファイル内の 1 つの行が表されます。

### <a name="skill-1-split-skill"></a>スキル #1: 分割スキル 

![ドキュメント解析後の強化ツリー](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "ドキュメント解析後、スキル実行前の強化ツリー")

```"/document/reviews_text"``` のスキル コンテキストでは、このスキルは `reviews_text` に対して 1 回実行されます。 スキルの出力はリストであり、`reviews_text` が 5000 文字のセグメントに分割されています。 分割スキルからの出力は `pages` という名前で、強化ツリーに追加されます。 `targetName` 機能を使用することで、強化ツリーに追加される前に、スキル出力の名前を変更できます。

これで、強化ツリーのスキルのコンテキストの下に、新しいノードが配置されました。 このノードは、任意のスキル、プロジェクション、または出力フィールド マッピングで使用できます。


すべての強化のルート ノードは `"/document"` です。 BLOB インデクサーを使用すると、`"/document"` ノードに子ノード `"/document/normalized_images"` と `"/document/content"` ができます。 この例のように、CSV データを使用すると、列名は `"/document"` の下のノードにマップされます。 スキルによってノードに追加されたいずれかの強化にアクセスするには、強化の完全なパスが必要です。 たとえば、```pages``` ノードのテキストを別のスキルへの入力として使用する場合は、```"/document/reviews_text/pages/*"``` として指定する必要があります。
 
 ![スキル #1 の後の強化ツリー](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "スキル #1 実行後の強化ツリー")

### <a name="skill-2-language-detection"></a>スキル #2: 言語検出
 言語検出スキルは、スキルセットで定義されている 3 番目のスキルですが (スキル #3)、次に実行されるスキルです。 入力要求によってブロックされないため、前のスキルと並列に実行されます。 先行する分割スキルと同様に、言語検出スキルもドキュメントごとに 1 回呼び出されます。 強化ツリーには言語用の新しいノードが追加されています。
 ![スキル #2 の後の強化ツリー](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "スキル #2 実行後の強化ツリー")
 
 ### <a name="skill-3-key-phrases-skill"></a>スキル #3: キー フレーズ スキル 

コンテキストとして ```/document/reviews_text/pages/*``` が指定されたキー フレーズ スキルは、`pages` コレクションの各項目に対して 1 回ずつ呼び出されます。 スキルからの出力は、関連付けられている page 要素の下のノードになります。 

 スキルセットに含まれる残りのスキルを表示し、各スキルの実行によって強化のツリーがどのように成長し続けるかを視覚化できるようになるはずです。 マージ スキルや Shaper スキルなどの一部のスキルでも新しいノードが作成されますが、既存のノードのデータのみが使用され、新しい強化は作成されません。

![すべてのスキルの後の強化ツリー](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "すべてのスキルの後の強化ツリー")

上のツリーのコネクタの色は、強化が異なるスキルで作成されたことを示しています。ノードは個別に指定する必要があり、親ノードを選択したときに返されるオブジェクトの一部にはなりません。

## <a name="save-enrichments-in-a-knowledge-store"></a>ナレッジ ストアに強化を保存する 

スキルセットでは、強化されたドキュメントをテーブルまたはオブジェクトとして射影できるナレッジ ストアも定義されています。 強化されたデータをナレッジ ストアに保存するには、強化されたドキュメントの一連のプロジェクションを定義します。 ナレッジ ストアの詳細については、[ナレッジ ストアの概要](knowledge-store-concept-intro.md)に関するページを参照してください。

### <a name="slicing-projections"></a>プロジェクションのスライス

テーブル プロジェクション グループを定義するときに、強化ツリー内の 1 つのノードを複数の関連テーブルにスライスできます。 既存のテーブル プロジェクションの子であるソース パスを含むテーブルを追加した場合、結果の子ノードは既存のテーブル プロジェクションの子にはならず、代わりに新しい関連テーブルに射影されます。 このスライス技法を使用すると、すべてのテーブル プロジェクションのソースとして使用できる 1 つのノードを Shaper スキルで定義できます。 

### <a name="shaping-projections"></a>プロジェクションの整形

プロジェクションを定義するには 2 つの方法があります。 Shaper スキルを使用して、射影するすべての強化のルート ノードである新しいノードを作成することができます。 その後、プロジェクションでは、Shaper スキルの出力のみを参照します。 また、プロジェクション定義自体の中でプロジェクションをインライン整形することもできます。

Shaper による方法は、インライン整形より手間がかかりますが、強化ツリーのすべての変更がスキル内に含まれ、出力が再利用できるオブジェクトになることが保証されます。 インライン整形では、必要な形状を作成できますが、匿名オブジェクトであり、定義されているプロジェクションでのみ使用できます。 これらの方法は、組み合わせて使用することも、個別に使用することもできます。 ポータル ワークフローで自動的に作成されるスキルセットには、両方が含まれています。 テーブル プロジェクションに対しては Shaper スキルが使用されていますが、キー フレーズ テーブルの射影にはインライン整形も使用されます。

この例を拡張するには、インライン整形を削除し、Shaper スキルを使用してキー フレーズ用の新しいノードを作成することができます。 以下のセクションでは、3 つのテーブル `hotelReviewsDocument`、`hotelReviewsPages`、`hotelReviewsKeyPhrases` に射影される形状を作成する 2 つのオプションについて説明します。


#### <a name="shaper-skill-and-projection"></a>Shaper スキルとプロジェクション 

> [!Note]
> 簡潔にするため、この例ではドキュメント テーブルの一部の列が削除されています。
>
```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
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

上の `outputs` セクションで定義されている `tableprojection` ノードでは、スライス機能を使用して、`tableprojection` ノードの部分を別のテーブルに射影できます。

> [!Note]
> これは、ナレッジ ストア構成内のプロジェクションのスニペットにすぎません。
>
```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

#### <a name="inline-shaping-projections"></a>プロジェクションのインライン整形

インライン整形方式では、プロジェクションに必要なすべての形状は必要になった時点で作成されるため、Shaper スキルは必要ありません。 前の例と同じデータを射影するためのインライン プロジェクション オプションは次のようになります。

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
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
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
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
]
```
  
両方の方法から観察されることの 1 つは、`"sourceContext"` を使用して `"Keyphrases"` の値が射影される方法です。 文字列のコレクションが含まれる `"Keyphrases"` ノードは、それ自体がページ テキストの子になります。 ただし、プロジェクションには JSON オブジェクトが必要であり、ページはプリミティブ (文字列) であるため、`"sourceContext"` を使用して、キー フレーズを名前付きプロパティを持つオブジェクトにラップします。 この手法を使用すると、プリミティブであっても個別に射影できます。

## <a name="next-steps"></a>次のステップ

次のステップでは、コグニティブ スキルを使用して最初のスキルセットを作成します。

> [!div class="nextstepaction"]
> [最初のスキルセットを作成する](cognitive-search-defining-skillset.md)
