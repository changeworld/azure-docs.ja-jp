---
title: プロジェクションの例
titleSuffix: Azure Cognitive Search
description: ナレッジ ストアのコンテンツの構造や構成が反映された、豊富なスキルセットの出力を複雑なシェイプに射影する詳細な例について説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: a820101897ae20dbdeac029187d4ace356ae08af
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491110"
---
# <a name="detailed-example-of-shapes-and-projections-in-a-knowledge-store"></a>ナレッジ ストア内のシェイプとプロジェクションの詳細な例

この記事では、[高度な概念](knowledge-store-projection-overview.md)と[構文ベースの記事](knowledge-store-projections-examples.md)を補足する詳細な例として、[ナレッジ ストア](knowledge-store-concept-intro.md)の豊富なスキルセットの出力を完全に表現するために必要な整形とプロジェクションの手順を示す詳細な例を紹介します。

アプリケーションの要件で複数のスキルとプロジェクションが必要な場合、この例を使用すると、シェイプとプロジェクションがどのように交差するかについて理解を深めることができます。

## <a name="download-sample-definitions"></a>サンプル定義をダウンロードする

この例では、[Postman デスクトップ アプリケーション](https://www.postman.com/downloads/)と [Search REST API](/rest/api/searchservice/) を使用します。

GitHub にある [azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples) を複製またはダウンロードし、[**プロジェクション コレクション**](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/projections)をインポートして、この例を自分で試してみましょう。

## <a name="set-up-sample-data"></a>サンプル データを設定する

プロジェクト コレクションにはサンプル ドキュメントは特に含まれていませんが、[azure-search-sample-data リポジトリ](https://github.com/Azure-Samples/azure-search-sample-data)の [AI エンリッチメント デモ データ ファイル](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/ai-enrichment-mixed-media)にはテキストと画像が含まれており、この例で説明するプロジェクションと連携しています。

Azure Storage に BLOB コンテナーを作成し、14 項目すべてをアップロードします。

この Azure Storage 内に存在する間に、Postman コレクションで指定できるように接続文字列をコピーします。

## <a name="example-skillset"></a>サンプル スキルセット

シェイプとプロジェクションの依存関係を理解するには、エンリッチメントされたコンテンツを作成する次のスキルセットを確認してください。 このスキルセットは、生の画像とテキストの両方を処理し、シェイプとプロジェクションで参照される出力を生成します。

スキルの出力 (targetNames) に注目してください。 エンリッチされたドキュメント ツリーに書き込まれた出力は、(Shaper スキルを介して) プロジェクションおよびシェイプで参照されます。

```json
{
    "name": "projections-demo-ss",
    "description": "Skillset that enriches blob data found in "merged_content". The enrichment granularity is a document.",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
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

[Shaper スキル](cognitive-search-skill-shaper.md)は、エンリッチされたコンテンツを新たに作成するのではなく、エンリッチされた既存のコンテンツを操作するためのユーティリティです。 Shaper をスキルセットに追加すると、テーブルまたは BLOB ストレージに射影できるカスタム シェイプを作成できます。 カスタム シェイプがない場合、プロジェクションは 1 つのノード (出力ごとに 1 つのプロジェクション) の参照に制限されますが、これはテーブルにとって適切ではありません。 カスタム シェイプを作成することで、さまざまな要素を新しい論理的なまとまりに集約して、それを単一のテーブルとして射影したり、テーブルのコレクション全体にスライスおよび分散したりできます。 

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

### <a name="add-shapers-to-a-skillset"></a>スキルセットに Shapers を追加する

この記事の最初に紹介したスキルセットの例には Shaper スキルは含まれていませんでしたが、Shaper スキルはスキルセットに含まれており、多くの場合、スキルセットの最後に配置されています。

スキルセット内では、シェイパーのスキルは次のようになります。

```json
    "name": "projections-demo-ss",
    "skills": [
        {
            <Shaper skill goes here>
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

## <a name="projecting-to-tables"></a>テーブルへの射影

上記の例を参照すると、テーブル プロジェクションで参照できるエンリッチメントとデータ シェイプには既知の量があります。 以下のテーブル プロジェクションでは、`tableName`、`source`、`generatedKeyName` のプロパティを設定して、3 つのテーブルが定義されています。

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

### <a name="test-your-work"></a>作業をテストする

プロジェクション定義を確認するには、次の手順に従います。

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

## <a name="projecting-blob-documents"></a>BLOB ドキュメントのプロジェクション

オブジェクト プロジェクションは、任意のノードをソースにすることができる強化ツリーの JSON 表現です。 テーブル プロジェクションと比較すると、オブジェクト プロジェクションはより簡単に定義でき、ドキュメント全体のプロジェクションを行うときに使用されます。 オブジェクト プロジェクションは、1 つのコンテナー内では単一のプロジェクションに限定され、スライスすることはできません。

オブジェクト プロジェクションを定義するには、プロジェクション プロパティ内で `objects` 配列を使用します。

ソースは、プロジェクションのルートとなるエンリッチメント ツリーのノードのパスです。 これは必須ではありませんが、通常、ノード パスは Shaper スキルの出力です。 これは、ほとんどのスキルが単体では有効な JSON オブジェクトを出力しないため、何らかの形の整形が必要になるためです。 多くのケースでは、テーブル プロジェクションの作成と同じ Shaper スキルを使用してオブジェクト プロジェクションを生成できます。 または、ソースをノードに設定し、構造を提供するために[インライン整形](knowledge-store-projection-shape.md#inline-shape)を行うこともできます。

宛先は常に BLOB コンテナーです。

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

## <a name="projecting-an-image-file"></a>イメージ ファイルのプロジェクション

ファイル プロジェクションは、常にバイナリで正規化されたイメージです。正規化とは、スキルセットの実行に使用するための潜在的なサイズ変更やローテーションを指します。 オブジェクト プロジェクションと同様に、ファイル プロジェクションは Azure Storage の BLOB として作成され、イメージを格納します。

ファイル プロジェクションを定義するには、プロジェクション プロパティ内で `files` 配列を使用します。

ソースは常に `/document/normalized_images/*` です。 ファイル プロジェクションは、`normalized_images` コレクションに対してのみ機能します。 インデクサーもスキルセットも、元の正規化されていないイメージを通過しません。

宛先は常に BLOB コンテナーで、ドキュメント ID の base64 でエンコードされた値のフォルダー プレフィックスが付きます。 ファイル プロジェクションとオブジェクト プロジェクションのコンテナーを同じにすることはできないため、両者は別々のコンテナーに射影する必要があります。 

次の例では、エンリッチされたドキュメントのドキュメント ノードから抽出したすべての正規化されたイメージを、`myImages` というコンテナーにプロジェクションしています。

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

## <a name="next-steps"></a>次のステップ

この記事の例では、プロジェクションの作成方法に関する一般的なパターンを紹介しました。 その概念を十分に理解したら、特定のシナリオを視野に入れて、いつでもプロジェクションを作成することができます。

> [!div class="nextstepaction"]
> [インクリメンタル エンリッチメントのためのキャッシュの構成](search-howto-incremental-index.md)
