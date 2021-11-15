---
title: ナレッジ ストアのデータのシェイプ
titleSuffix: Azure Cognitive Search
description: データ シェイプを作成し、それをプロジェクションに渡して、ナレッジ ストア内のデータ構造を定義します。
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: f0558d7e3cffd744ec4effa1d4f81f359e909e97
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131503173"
---
# <a name="shaping-data-for-projection-into-a-knowledge-store"></a>ナレッジ ストアへのプロジェクションのためのデータのシェイプ

Azure Cognitive Search の、「データのシェイプ」では、[ナレッジ ストア ワークフロー](knowledge-store-concept-intro.md)のステップを説明します。このステップでは、Azure Storage のテーブル、オブジェクト、およびファイルに投影するコンテンツのデータ表現を作成します。

スキルを実行すると、出力はノードの階層内のエンリッチメント ツリーに書き込まれます。エンリッチメント ツリー全体を表示して使用することもできますが、翻訳されたテキストに関連するノードや抽出されたエンティティを特定のテーブルに配置するなど、さまざまなシナリオでノードのサブセットを作成する、より細かい粒度が必要になる可能性の方が高くなります。

エンリッチメント ツリー自体には、ナレッジ ストアでのコンテンツの表現方法を通知するロジックは含まれません。 データ シェイプは、各テーブル、オブジェクト、およびファイル プロジェクションに含まれるデータのスキーマを提供することで、このギャップを埋めます。 データ シェイプは、エンリッチされたデータのカスタム定義またはビューと考えられます。 必要な数のシェイプを作成してから、それらをナレッジ ストア定義の[プロジェクション](knowledge-store-projection-overview.md)に割り当てることができます。 

## <a name="approaches-for-creating-shapes"></a>シェイプを作成するための方法

エンリッチされたコンテンツを、ナレッジ ストアにプロジェクトできるシェイプにするには、次の 2 つの方法があります。

+ [Shaper スキル](cognitive-search-skill-shaper.md)を使用し、明示的にプロジェクションに使用されるノードをエンリッチメント ツリーに作成します。 ほとんどのスキルでは、新しいコンテンツが作成されます。 これに対し、Shaper スキルは既存のノードで動作し、通常は複数のノードを 1 つの複合オブジェクトに統合します。 これは、複数のノードの出力をテーブル内の列として物理的に表現するテーブルに便利です。 

+ プロジェクション定義自体の中では、インライン シェイプを使用します。

Shaper スキルを使用するとシェイプを外部化でき、複数のプロジェクションや他のスキルでも使用できます。 また、エンリッチメント ツリーのすべての変更がスキル内に格納され、出力が再利用できるオブジェクトとなります。 一方でインライン整形では、必要な形状を作成できますが、匿名オブジェクトであり、定義されているプロジェクションでのみ使用できます。

これらの方法は、組み合わせて使用することも、個別に使用することもできます。 この記事では、テーブル プロジェクションの Shaper スキルと、キー フレーズ テーブル プロジェクションを使用したインライン シェイプの両方について説明します。

## <a name="use-a-shaper-skill"></a>Shaper スキルを使用する

通常、Shaper スキルはスキルセットの最後に配置され、プロジェクションに渡すデータのビューを作成します。 この例では、"reviews_text"、"reviews_title"、"AzureSearch_DocumentKey" の各ノードと、ページ レビューのセンチメント スコアとキー フレーズを含む "tableprojection" という名前のシェイプを作成します。 

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
                    "name": "Sentiment",
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

### <a name="sourcecontext-property"></a>SourceContext プロパティ

Shaper スキル内で、入力は `sourceContext` エレメントを持つことができます。 この同じプロパティを、プロジェクションのインライン シェイプでも使用できます。 

`sourceContext` は、エンリッチメント パイプラインで複数レベルの入れ子になったオブジェクトを構築するために使用されます。 入力がスキル コンテキストとは *別の* コンテキストにある場合は、*sourceContext* を使用します。 *sourceContext* には、特定の要素がソースとして扱われる入れ子の入力を定義する必要があります。 

上の例では、より効率的な分析のために、ページに分割されたテキストに対して感情分析とキー フレーズ抽出が実行されています。 スコアとフレーズをテーブルに射影するには、スコアとフレーズを提供する入れ子になった入力にコンテキストを設定する必要があります。

### <a name="projecting-a-shape-into-multiple-tables"></a>シェイプを複数のテーブルに射影する

上記の `outputs` セクションで定義された `tableprojection` ノードを使用し、`tableprojection` ノードの部分を個々の関連テーブルに分割できます。

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

## <a name="inline-shape-for-table-projections"></a>テーブル プロジェクションのインライン シェイプ

インライン シェイプは、プロジェクション定義自体の中で新しいシェイプを形成する機能です。 インライン シェイプには、次の特徴があります。

+ シェイプは、それを含むプロジェクションでのみ使用できます。
+ シェイプは、Shaper スキルによって生成されるものと同じにすることができます。

インライン シェイプは、`sourceContext` と `inputs` を使用して作成されます。

| プロパティ | 説明 |
|----------|-------------|
| sourceContext | プロジェクションのルートを設定します。  |
| inputs | 各入力はテーブル内の列です。 Name は列名です。 Source は、値を提供するエンリッチメント ノードです。 |

前の例と同じデータを射影するためのインライン プロジェクション オプションは次のようになります。

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
  
両方の方法から観察されることの 1 つは、"sourceContext" を使用して "Keyphrases" の値が射影される方法です。 文字列のコレクションが含まれる "Keyphrases" ノードは、それ自体がページ テキストの子になります。 ただし、プロジェクションには JSON オブジェクトが必要であり、ページはプリミティブ (文字列) であるため、"sourceContext" を使用して、キー フレーズが名前付きプロパティを持つオブジェクトにラップされます。 この手法を使用すると、プリミティブであっても個別に射影できます。

<a name="inline-shape"></a>

## <a name="inline-shape-for-object-projections"></a>オブジェクト プロジェクションのインライン シェイプ

Shaper スキルを使用して新しいシェイプを生成するか、またはオブジェクト プロジェクションのインライン整形を使用することができます。 テーブルの例ではシェイプの作成とスライスの手法を示しましたが、この例ではインライン整形の使用を例示します。 

インライン整形とは、プロジェクションへの入力の定義内で新しいシェイプを作成する機能です。 インライン整形では、Shaper スキルによって生成されるもの (この場合は `projectionShape`) と同じ匿名のオブジェクトが作成されます。 再利用する予定がないシェイプを定義する場合は、インライン整形が便利です。

projections プロパティは配列です。 この例では、新しいプロジェクション インスタンスを配列に追加しています。そこでは、インライン プロジェクションが knowledgeStore の定義に含まれています。 インライン プロジェクションを使用するときは、Shaper スキルを省略することができます。

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
            {
            "tables": [ ],
            "objects": [
                {
                    "storageContainer": "sampleobject",
                    "source": null,
                    "generatedKeyName": "myobject",
                    "sourceContext": "/document",
                    "inputs": [
                        {
                            "name": "metadata_storage_name",
                            "source": "/document/metadata_storage_name"
                        },
                        {
                            "name": "metadata_storage_path",
                            "source": "/document/metadata_storage_path"
                        },
                        {
                            "name": "content",
                            "source": "/document/content"
                        },
                        {
                            "name": "keyPhrases",
                            "source": "/document/merged_content/keyphrases/*"
                        },
                        {
                            "name": "entities",
                            "source": "/document/merged_content/entities/*/name"
                        },
                        {
                            "name": "ocrText",
                            "source": "/document/normalized_images/*/text"
                        },
                        {
                            "name": "ocrLayoutText",
                            "source": "/document/normalized_images/*/layoutText"
                        }
                    ]

                }
            ],
            "files": []
        }
    ]
}
```

## <a name="next-steps"></a>次のステップ

この記事では、プロジェクション シェイプの概念と原則について説明します。 次のステップとして、テーブル、オブジェクト、およびファイル プロジェクションのパターンでこれらがどのように適用されるのかについて説明します。

> [!div class="nextstepaction"]
> [ナレッジ ストアでのプロジェクションを定義する](knowledge-store-projections-examples.md)
