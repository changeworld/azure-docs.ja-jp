---
title: プロジェクションを定義する
titleSuffix: Azure Cognitive Search
description: ナレッジ ストアにテーブル、オブジェクト、ファイル プロジェクションを作成して、インデクサーおよびスキルセットのエンリッチメント パイプラインからのエンリッチされたコンテンツを保存します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: aeb236b0b016cd86b492ce5bf0f64b14d1443fca
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737604"
---
# <a name="define-projections-in-a-knowledge-store"></a>ナレッジ ストアでのプロジェクションを定義する

[プロジェクション](knowledge-store-projection-overview.md)は、[ナレッジ ストア](knowledge-store-concept-intro.md)内のエンリッチされたドキュメントの物理的表現であり、Azure Storage 内のテーブル、オブジェクト、ファイルの形式で表されます。 エンリッチされたドキュメントを効果的に使用するには、構造が必要になります。 この記事では、構造とリレーションシップの両方について取り上げ、プロジェクションのプロパティを構築する方法と、作成したプロジェクションの種類間でデータを関連付ける方法について説明します。

プロジェクションは、エンリッチメントを定義して、[Shaper スキルまたはインライン シェイプ](knowledge-store-projection-shape.md)を使用してデータを整形した後に、[knowledgeStore 定義](knowledge-store-concept-intro.md) で指定されます。 そのためこの記事では、プロジェクションに適したシェイプを生成する Shaper スキルなど、エンリッチメントを定義するスキルセットの例を参照します。

## <a name="enable-caching"></a>キャッシュの有効化

プロジェクションを開発する場合は、コスト管理を確実に行うために、[インデクサー キャッシュ プロパティを設定します](search-howto-incremental-index.md)。 インデクサー キャッシュが設定されていない場合、プロジェクションを編集すると、ドキュメント全体が再度エンリッチされます。 キャッシュが設定されていて、そのプロジェクションだけが更新された場合、以前にエンリッチされたドキュメントに対してスキルセットが実行されて新しい Cognitive Services の料金が発生することはありません。

## <a name="example-enrichments"></a>エンリッチメントの例

データ シェイプとプロジェクションの共通部分を理解するために、エンリッチされたコンテンツの基礎として、次のスキルセットを参照します。 このスキルセットは、生の画像とテキストの両方を処理し、シェイプとプロジェクションで参照される出力を生成します。

```json
{
    "name": "azureblob-skillset",
    "description": "Skillset that enriches blob data found in "merged_content". The enrichment granularity is a document.",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "includeTypelessEntities": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "A Cognitive Services resource in the same region as Search.",
        "key": "<COGNITIVE SERVICES All-in-ONE KEY>"
    },
    "knowledgeStore": null
}
```

## <a name="example-shaper-skill"></a>Shaper スキルの例

Shaper スキルは、エンリッチされたコンテンツを作成するのではなく、操作するためのユーティリティです。 Shaper をスキルセットに追加すると、テーブル ストレージに射影できるカスタム シェイプを作成できます。 カスタム シェイプがない場合、プロジェクションは 1 つのノード (出力ごとに 1 つのプロジェクション) の参照に制限されますが、これはテーブルにとって適切ではありません。 カスタム シェイプを作成することで、さまざまな要素を新しい論理的なまとまりに集約して、それを単一のテーブルとして射影したり、テーブルのコレクション全体にスライスおよび分散したりできます。 

この例では、カスタム シェイプによって、blob メタデータと識別されたエンティティおよびキー フレーズが結合されています。 カスタム シェイプは `projectionShape` と呼ばれ、`/document` の下に作成されます。 

整形の目的は、すべてのエンリッチメント ノードが適切な形式の JSON で表されるようにすることです。これは、ナレッジ ストアへの射影に必要です。 これは特に、エンリッチメント ツリーに含まれるノードが適切な形式の JSON ではないときに当てはまります (たとえば、エンリッチメントが、文字列などのプリミティブ型の親である場合)。

最後の 2 つのノード (`KeyPhrases` および `Entities`) に注目してください。 これらは、`sourceContext` を使用して、有効な JSON オブジェクトにラップされています。 これが必要なのは、`keyphrases` と `entities` がプリミティブ型に対するエンリッチメントであり、射影できるようにするには、事前に有効な JSON に変換しておく必要があるためです。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_content_type",
            "source": "/document/metadata_storage_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_content_type",
            "source": "/document/metadata_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "projectionShape"
        }
    ]
}
```

前出の Shaper スキルをスキルセットに追加します。 

```json
    "name": "azureblob-skillset",
    "description": "A friendly description of the skillset goes here.",
    "skills": [
        {
            Shaper skill goes here
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

## <a name="projecting-to-tables"></a>テーブルへの射影

Azure Storage 内のテーブルへの射影は、Power BI などのツールを使用してレポートと分析を行う場合に便利です。このツールは、テーブルから読み取りを行い、プロジェクション中に生成されたキーに基づいてリレーションシップを検出することができます。 ダッシュボードを作成する場合、関連するテーブルを使用することにより、その作業が簡単になります。

テーブルのスキーマは、プロジェクション (テーブル名とキー)、およびテーブル (列) のシェイプを指定するソースによってその一部が指定されます。

> [!NOTE] 
> テーブル プロジェクションは Azure Storage テーブルであり、Azure Storage によって課せられた容量の制限で制御されています。 詳細については、[Table Storage の制限](/rest/api/storageservices/understanding-the-table-service-data-model)に関するページを参照してください。 エンティティ サイズは 1 MB を超えることはできず、また、単一のプロパティは 64 KB を超えられないことを知っておくと便利です。 これらの制約によって、テーブルは、多数の小さなエンティティの格納に適したソリューションになっています。

上記の例を参照すると、テーブル プロジェクションで参照できるエンリッチメントとデータ シェイプには既知の量があります。 以下のテーブル プロジェクションでは、`tableName`、`source`、`generatedKeyName` のプロパティを設定して、3 つのテーブルが定義されています。

| プロパティ | 説明 |
|----------|-------------|
| tableName | (必須) Azure Table Storage で作成された新しいテーブルの名前を決定します。 テーブルは、partitionKey および rowKey 列を使用して作成されます。 |
| ソース | エンリッチメント ツリー内のノードへのパス。 テーブル プロジェクションは複雑なので (複数のノードに複数の列が設定されている)、パスはノードを含むデータ シェイプに解決される必要があります。 Shaper スキルの出力は、このプロパティの最も一般的な値ですが、プロジェクション内でインライン整形を使用してシェイプを作成することもできます。 |
| generatedKeyName | すべての行は、システムで生成された値によって一意に識別されます。 このプロパティは、それらの値を含む列の名前を決定します。 このプロパティを省略すると、名前付け規則としてテーブル名と "キー" を使用する列が自動的に作成されます。 |

これらの 3 つのテーブルはすべて、生成されたキーと共有親 `/document/projectionShape` によって関連付けされます。

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "tblDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/projectionShape"
                },
                {
                    "tableName": "tblKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/projectionShape/keyPhrases/*"
                },
                {
                    "tableName": "tblEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/projectionShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

次の手順に従って作業を進めることができます。

1. ナレッジ ストアの `storageConnectionString` プロパティに有効な V2 汎用ストレージ アカウント接続文字列を設定します。  

1. PUT 要求を発行してスキルセットを更新します。

1. スキルセットの更新後、インデクサーを実行します。 

これで、3 つのテーブルを含む有効なプロジェクションができました。 [これらのテーブルを Power BI にインポート](knowledge-store-connect-power-bi.md)すると、Power BI ではリレーションシップが自動的に検出されます。

次の例に進む前に、データのスライスと関連付けのメカニズムを理解するために、テーブル プロジェクションの特徴を振り返ってみましょう。

### <a name="slicing-a-table-into-multiple-child-tables"></a>テーブルを複数の子テーブルにスライスする

スライスは、統合されたシェイプ全体を細かな構成要素に分割する手法です。 その結果、互いに独立しながら関連するテーブルが作成されます。これらのテーブルは個別に編集することができます。

この例では、`projectionShape`が統合されたシェイプ (エンリッチメント ノード) です。 プロジェクションの定義では、`projectionShape` が追加のテーブルにスライスされます。これによって、シェイプの構成要素である `keyPhrases` と `Entities` を取り出すことができます。 これは Power BI に適した形と言えます。それぞれのドキュメントには複数のエンティティとキーフレーズが関連付けられており、分類されたデータとしてエンティティとキーフレーズを見ることができれば、得られる分析情報も多くなるからです。

スライスの際には、親テーブル内の `generatedKeyName` を使用して子テーブル内に同じ名前の列を作成することで、親テーブルと子テーブルの間のリレーションシップが暗黙的に生成されます。 

### <a name="naming-relationships"></a>リレーションシップの名前付け

テーブル間で、またはプロジェクションの種類間であっても、データを関連付けるためには `generatedKeyName` および `referenceKeyName` プロパティが使用されます。 子テーブル内の各行には、親を参照し返すプロパティが含まれています。 子での列またはプロパティの名前は、親からの `referenceKeyName` です。 `referenceKeyName` が指定されていない場合、サービスでは、親からの `generatedKeyName` を既定値とします。 

Power BI では、これらの生成されたキーを利用して、テーブル内のリレーションシップを検出します。 子テーブル内の列に別の名前を付ける必要がある場合は、親テーブル上に `referenceKeyName` プロパティを設定します。 例として、tblDocument テーブル上で `generatedKeyName` を ID として設定し、`referenceKeyName` を DocumentID として設定します。 これにより、tblEntities および tblKeyPhrases テーブルに、ドキュメント ID を含む DocumentID という名前の列が生成されます。

## <a name="projecting-to-objects"></a>オブジェクトへの射影

オブジェクト プロジェクションはより簡単に定義でき、ドキュメント全体を射影するときに使用されます。 オブジェクト プロジェクションは、1 つのコンテナー内では単一のプロジェクションに限定され、スライスすることはできません。

オブジェクト プロジェクションを定義するには、`projections` 内で `objects` 配列を使用します。 Shaper スキルを使用して新しいシェイプを生成するか、またはオブジェクト プロジェクションのインライン整形を使用することができます。 テーブルの例ではシェイプの作成とスライスの手法を示しましたが、この例ではインライン整形の使用を例示します。 

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

## <a name="projecting-to-file"></a>ファイルへの射影

ファイル プロジェクションは常に、ソース ドキュメントから抽出された画像か、エンリッチメント プロセスから射影できるエンリッチメントの出力となる画像です。 オブジェクト プロジェクションと同様に、ファイル プロジェクションは Azure Storage の BLOB として実装され、画像を格納します。 

ファイル プロジェクションを生成するには、プロジェクション オブジェクト内で `files` 配列を使用します。 この例では、ドキュメントから抽出されたすべての画像を `myImages` というコンテナーに射影します。

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
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
}
```

## <a name="projecting-to-multiple-types"></a>複数の種類への射影

シナリオが複雑になると、プロジェクションの種類間でコンテンツを射影する必要が生じる場合があります。 たとえば、キー フレーズとエンティティをテーブルに射影し、テキストとレイアウト テキストの OCR 結果をオブジェクトとして保存して、画像をファイルとして射影します。 

プロジェクションの種類が複数ある場合の手順は次のとおりです。

1. ドキュメントごとに、1 行を含む 1 つのテーブルが作成されます。
1. ドキュメント テーブルに関連付けられた 1 つのテーブルが作成され、各キー フレーズがこのテーブル内の 1 行として識別されます。
1. ドキュメント テーブルに関連付けられた 1 つのテーブルが作成され、各エンティティがこのテーブル内の 1 行として識別されます。
1. 画像ごとにレイアウト テキストを含むオブジェクト プロジェクションが作成されます。
1. ファイル プロジェクションが作成され、抽出された各画像が射影されます。
1. ドキュメント テーブル、レイアウト テキストを含むオブジェクト プロジェクション、およびファイル プロジェクションへの参照を含む相互参照テーブルが作成されます。

### <a name="shape-data-for-cross-projection"></a>クロスプロジェクション用のデータの整形

これらのプロジェクションに必要なシェイプを取得するには、まず、`crossProjection` という整形されたオブジェクトを作成する新しい Shaper スキルを追加します。 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForCrossProjection",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        },
        {
            "name": "images",
            "source": null,
            "sourceContext": "/document/normalized_images/*",
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                },
                {
                    "name": "layoutText",
                    "source": "/document/normalized_images/*/layoutText"
                },
                {
                    "name": "ocrText",
                    "source": "/document/normalized_images/*/text"
                }
                ]
        }
 
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "crossProjection"
        }
    ]
}
```

### <a name="define-table-object-and-file-projections"></a>テーブル、オブジェクト、およびファイル プロジェクションの定義

統合された crossProjection オブジェクトから、オブジェクトを複数のテーブルにスライスし、OCR 出力を BLOB としてキャプチャした後、その画像を (Blob Storage 内にも) ファイルとして保存することができます。

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
            {
            "tables": [
                {
                    "tableName": "crossDocument",
                    "generatedKeyName": "Id",
                    "source": "/document/crossProjection"
                },
                {
                    "tableName": "crossEntities",
                    "generatedKeyName": "EntityId",
                    "source": "/document/crossProjection/entities/*"
                },
                {
                    "tableName": "crossKeyPhrases",
                    "generatedKeyName": "KeyPhraseId",
                    "source": "/document/crossProjection/keyPhrases/*"
                },
                {
                    "tableName": "crossReference",
                    "generatedKeyName": "CrossId",
                    "source": "/document/crossProjection/images/*"
                }
                    
            ],
            "objects": [
                {
                    "storageContainer": "crossobject",
                    "generatedKeyName": "crosslayout",
                    "source": null,
                    "sourceContext": "/document/crossProjection/images/*/layoutText",
                    "inputs": [
                        {
                            "name": "OcrLayoutText",
                            "source": "/document/crossProjection/images/*/layoutText"
                        }
                    ]
                }
            ],
            "files": [
                {
                    "storageContainer": "crossimages",
                    "generatedKeyName": "crossimages",
                    "source": "/document/crossProjection/images/*/image"
                }
            ]
        }
    ]
}
```

オブジェクト プロジェクションでは、プロジェクションごとにコンテナー名が必要になります。 オブジェクト プロジェクションとファイル プロジェクションは、コンテナーを共有できません。 

### <a name="relationships-among-table-object-and-file-projections"></a>テーブル、オブジェクト、およびファイル プロジェクションの間のリレーションシップ

この例では、プロジェクションの別の機能についても取り上げます。 同じプロジェクション オブジェクト内に複数の種類のプロジェクションを定義することにより、さまざまな種類 (テーブル、オブジェクト、ファイル) の間のリレーションシップが表現されます。 これにより、ドキュメントのテーブル行から開始して、オブジェクト プロジェクションのそのドキュメント内の画像のすべての OCR テキストを検索できます。 

データを関連付けたくない場合は、別のプロジェクション グループ内でプロジェクションを定義します。 たとえば、次のスニペットでは、テーブルどうしは関連付けられますが、テーブルとオブジェクト (OCR テキスト) プロジェクションの間にリレーションシップはありません。 

異なるニーズに対応するために、異なるシェイプ内に同じデータを射影したい場合は、プロジェクション グループが便利です。 たとえば、1 つは Power BI ダッシュボードのためのプロジェクション グループ、もう 1 つはカスタム スキルにラップされた機械学習モデルのトレーニング用データをキャプチャするためのプロジェクション グループとします。

異なる種類のプロジェクションを構築する場合、ファイル プロジェクションとオブジェクト プロジェクションが最初に生成されてから、テーブルに対してパスが追加されます。

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "unrelatedDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/projectionShape"
                },
                {
                    "tableName": "unrelatedKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/projectionShape/keyPhrases"
                }
            ],
            "objects": [
                
            ],
            "files": []
        }, 
        {
            "tables": [],
            "objects": [
                {
                    "storageContainer": "unrelatedocrtext",
                    "source": null,
                    "sourceContext": "/document/normalized_images/*/text",
                    "inputs": [
                        {
                            "name": "ocrText",
                            "source": "/document/normalized_images/*/text"
                        }
                    ]
                },
                {
                    "storageContainer": "unrelatedocrlayout",
                    "source": null,
                    "sourceContext": "/document/normalized_images/*/layoutText",
                    "inputs": [
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

## <a name="common-issues"></a>一般的な問題

プロジェクションを定義するときには、予期しない結果が生じる可能性がある一般的な問題がいくつかあります。 ナレッジ ストアの出力が想定と異なる場合は、これらの問題をチェックしてください。

+ 文字列のエンリッチメントが有効な JSON に整形されていない。 たとえば、キー フレーズによって `merged_content` がエンリッチされるなど、文字列がエンリッチされた場合、エンリッチされたプロパティはエンリッチメント ツリー内で `merged_content` の子として表されます。 既定の表現は、適切な形式の JSON ではありません。 そのため射影時には必ず、名前と値を含む有効な JSON オブジェクトにエンリッチメントを変換してください。

+ ソース パスの末尾にある ```/*``` が省略される。 プロジェクションのソースが `/document/projectionShape/keyPhrases` の場合、キー フレーズの配列は単一のオブジェクトまたは行として射影されます。 ソース パスを `/document/projectionShape/keyPhrases/*` に設定した場合は、キー フレーズごとに単一の行またはオブジェクトが生成されます。

+ パス構文エラー。 パス セレクターでは大文字と小文字が区別されるため、セレクターに対して大文字と小文字を正しく使用していないと、入力が見つからないという警告が表示される場合があります。

## <a name="next-steps"></a>次のステップ

この記事の例では、プロジェクションの作成方法に関する一般的なパターンを紹介しました。 その概念を十分に理解したら、特定のシナリオを視野に入れて、いつでもプロジェクションを作成することができます。

新しい機能を調べる際には、次の手順としてインクリメンタル エンリッチメントを検討します。 インクリメンタル エンリッチメントはキャッシュに基づいており、スキルセットに対する変更の影響を受けないエンリッチメントを再利用することができます。 OCR や画像分析を含んだパイプラインでは、特に有益な手段となります。

> [!div class="nextstepaction"]
> [インクリメンタル エンリッチメントのためのキャッシュの構成](search-howto-incremental-index.md)
