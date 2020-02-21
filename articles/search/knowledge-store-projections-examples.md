---
title: ナレッジ ストアでのプロジェクションを定義する
titleSuffix: Azure Cognitive Search
description: Power BI または Azure ML で使用するために、ナレッジ ストアにエンリッチされたドキュメントを射影する方法について、一般的なパターンの例を示します。
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: f29f4b91b85c0027df4be2fd5f26ef8f9749fe33
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77163820"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments-to-the-knowledge-store"></a>ナレッジ ストアのプロジェクション:エンリッチメントを整形してナレッジ ストアにエクスポートする方法

> [!IMPORTANT] 
> ナレッジ ストアは現在、パブリック プレビューの段階です。 プレビュー段階の機能はサービス レベル アグリーメントなしで提供しています。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 プレビュー機能は [REST API バージョン 2019-05-06-Preview](search-api-preview.md) で提供しています。 現時点でポータルによるサポートは一部のみにとどまります。また、.NET SDK によるサポートはありません。

プロジェクションとは、ナレッジ ストア内のエンリッチされたドキュメントの物理表現です。 エンリッチされたドキュメントを効果的に使用するには、構造が必要になります。 この記事では、構造とリレーションシップの両方について取り上げ、プロジェクションのプロパティを構築する方法と、作成したプロジェクションの種類間でデータを関連付ける方法について説明します。 

プロジェクションを作成するには、Shaper スキルを使用してカスタム オブジェクトを作成するか、またはインライン整形の構文を使用して、データを整形する必要があります。 1 つのデータ シェイプには、射影する対象データがすべて含まれます。 このドキュメントでは、各オプションの例が示されており、作成するプロジェクションにいずれかのオプションを使用することを選択できます。


プロジェクションには、次の 3 つの種類があります。
+ テーブル
+ Objects
+ ファイル

テーブル プロジェクションは、Azure Table Storage に格納されます。 オブジェクト プロジェクションおよびファイル プロジェクションは、BLOB ストレージに書き込まれます。オブジェクト プロジェクションは JSON ファイルとして保存され、ドキュメントからのコンテンツと任意のスキル出力やエンリッチメントを含むことができます。 エンリッチメント パイプラインでは、画像などのバイナリを抽出することもできます。これらのバイナリは、ファイル プロジェクションとして射影されます。 バイナリ オブジェクトがオブジェクト プロジェクションとして射影された場合、関連付けられているメタデータのみが JSON BLOB として保存されます。 

データ シェイプとプロジェクションの共通部分を理解するために、基礎として次のスキルセットを使用して、各種の構成を確認します。 このスキルセットでは、未加工の画像とテキスト コンテンツが処理されます。 プロジェクションは、サポートされるシナリオに対して、ドキュメントのコンテンツとスキルの出力から定義されます。

または、[REST API サンプル](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json)をダウンロードして、このチュートリアル内のすべての呼び出しと共に使用することもできます。

> [!IMPORTANT] 
> プロジェクションを試すときは、確実にコスト管理できるように、[インデクサー キャッシュ プロパティを設定](search-howto-incremental-index.md)すると便利です。 インデクサー キャッシュが設定されていない場合、プロジェクションを編集すると、ドキュメント全体が再度エンリッチされます。 キャッシュが設定されていれば、そのプロジェクションだけが更新された場合に、以前にエンリッチされたドキュメントに対してスキルセットが実行されて Cognitive Services の料金が発生することはありません。


```json
{
    "name": "azureblob-skillset",
    "description": "Skillset created from the portal. skillsetName: azureblob-skillset; contentField: merged_content; enrichmentGranularity: document; knowledgeStoreStorageAccount: confdemo;",
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
        "description": "DemosCS",
        "key": "<COGNITIVE SERVICES KEY>"
    },
    "knowledgeStore": null
}
```

これで、`knowledgeStore` オブジェクトを追加して、必要に応じて各シナリオに対するプロジェクションを構成できます。 

## <a name="projecting-to-tables-for-scenarios-like-power-bi"></a>Power BI などのシナリオにおけるテーブルへの射影

> [!NOTE] 
> ナレッジ ストアは Azure ストレージ アカウントです。そのため、テーブル プロジェクションは Azure Storage テーブルであり、テーブル上のストレージの制限によって管理されます。詳細については、[テーブル ストレージの制限](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model)に関する記事を参照してください。 エンティティ サイズは 1 MB を超えることはできず、また、単一のプロパティは 64 KB を超えられないことを知っておくと便利です。 これらの制約によって、テーブルは、多数の小さなエンティティの格納に適したソリューションになっています。

Power BI では、テーブルから読み取りを行い、ナレッジ ストアのプロジェクションによって作成されたキーに基づくリレーションシップを検出できます。このため、テーブルは、エンリッチされたデータに対してダッシュボードを作成しようとする場合に、データを射影するための適切なオプションとなります。 ドキュメントからワード クラウドとして抽出されたキー フレーズを視覚化できるダッシュボードを作成しようとしている場合、Shaper スキルをスキルセットに追加して、そのドキュメントに固有の詳細やキー フレーズを含むカスタム シェイプを作成できます。 Shaper スキルをスキルセットに追加して、```document``` 上に ```pbiShape``` という新しいエンリッチメントを作成します。

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Shaper スキルを使用してカスタム シェイプを作成する

テーブル ストレージに射影できるカスタム シェイプを作成します。 カスタム シェイプがない場合、1 つのプロジェクションでは単一のノード (出力ごとに 1 つのプロジェクション) しか参照できません。 カスタム シェイプを作成することで、さまざまな要素を新しい論理的なまとまりに集約して、それを単一のテーブルとして射影したり、テーブルのコレクション全体にスライスおよび分散したりできるようになります。 この例では、カスタム シェイプによって、メタデータと識別されたエンティティおよびキー フレーズが結合されています。 オブジェクトは pbiShape と呼ばれ、`/document` 下で親となります。 

> [!IMPORTANT] 
> 射影できるようにするためには、エンリッチメントのソース パスは、事前に適切な形式の JSON オブジェクトにする必要があります。 たとえば、エンリッチメントが文字列などのプリミティブ型に対する親になる場合など、強化ツリーによって、適切な形式の JSON になっていないエンリッチメントを表すことができます。 `KeyPhrases` と `Entities` が `sourceContext` による有効な JSON オブジェクトにどのようにラップされているかに注意してください。このことは、`keyphrases` としては必須です。また、`entities` はプリミティブ型に対するエンリッチメントであり、射影できるようにするには、事前に有効な JSON に変換される必要があります。

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
                    "targetName": "pbiShape"
                }
            ]
        }
```
先ほど定義した Shaper スキルをスキルセット内のスキル一覧に追加します。 

テーブルに射影するために必要なすべてのデータを用意できたので、テーブル定義によって knowledgeStore オブジェクトを更新します。 

```json

"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "pbiDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/pbiShape"
                },
                {
                    "tableName": "pbiKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/pbiShape/keyPhrases/*"
                },
                {
                    "tableName": "pbiEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/pbiShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

```storageConnectionString``` プロパティに有効な V2 汎用ストレージ アカウント接続文字列を設定します。 このシナリオでは、```tableName```、```source```、および ```generatedKeyName``` プロパティを設定して、プロジェクション オブジェクトに 3 つのテーブルを定義します。 これで、PUT 要求を発行することで、スキルセットを更新できるようになりました。

### <a name="slicing"></a>スライス 

射影する必要があるすべてのコンテンツが単一のシェイプ (エンリッチメント ノード) 内にある統合されたシェイプを使って作業を開始する場合は、スライス機能によって、単一のノードを複数のテーブルまたはオブジェクトにスライスできます。 ここでは、```pbiShape``` オブジェクトを複数のテーブルにスライスします。 スライス機能を利用すると、シェイプの一部である ```keyPhrases``` および ```Entities``` を別個のテーブルに抽出できます。 各ドキュメントには複数のエンティティと keyPhrases が関連付けられているため、これは便利です。 スライスの際には、親テーブル内の ```generatedKeyName``` を使用して子テーブル内に同じ名前の列を作成することで、親テーブルと子テーブルの間にリレーションシップが暗黙的に生成されます。 

### <a name="naming-relationships"></a>リレーションシップの名前付け
テーブル間で、またはプロジェクションの種類間であっても、データを関連付けるためには ```generatedKeyName``` および ```referenceKeyName``` プロパティが使用されます。 子テーブル/プロジェクション内の各行には、親を参照し返すプロパティが含まれています。 子での列またはプロパティの名前は、親からの ```referenceKeyName``` です。 ```referenceKeyName``` が指定されていない場合、サービスでは、親からの ```generatedKeyName``` を既定値とします。 PowerBI では、これらの生成されたキーを利用して、テーブル内のリレーションシップを検出します。 子テーブル内の列に別の名前を付ける必要がある場合は、親テーブル上に ```referenceKeyName``` プロパティを設定します。 例として、pbiDocument テーブル上で ```generatedKeyName``` を ID として設定し、```referenceKeyName``` を DocumentID として設定します。 これにより、pbiEntities テーブルと pbiKeyPhrases テーブルに、ドキュメント ID を含む DocumentID という名前の列が生成されます。

更新されたスキルセットを保存してインデクサーを実行すると、3 つのテーブルを含む有効なプロジェクションができています。 これらのテーブルを Power BI にインポートすると、Power BI ではリレーションシップが自動的に検出されます。

## <a name="projecting-to-objects"></a>オブジェクトへの射影

オブジェクト プロジェクションにはテーブル プロジェクションと同じ制限事項はなく、大きなドキュメントを射影する場合により適しています。 この例では、ドキュメント全体をオブジェクト プロジェクションに射影します。 オブジェクト プロジェクションは、1 つのコンテナー内では単一のプロジェクションに限定されています。
オブジェクト プロジェクションを定義するには、プロジェクション内で ```objects``` 配列を使用します。 Shaper スキルを使用して新しいシェイプを生成するか、またはオブジェクト プロジェクションのインライン整形を使用することができます。 テーブルの例ではシェイプの作成とスライスの手法を示しましたが、この例ではインライン整形の使用を例示します。 インライン整形は、プロジェクションへの入力の定義内で新しいシェイプを作成する機能です。 インライン整形では、類似のシェーパーによって生成されるものと同じ匿名オブジェクトが作成されます。 再利用する予定がないシェイプを定義する場合は、インライン整形が便利です。
プロジェクション プロパティは配列であり、この例では、新しいプロジェクション インスタンスを配列に追加しています。 インライン プロジェクションを使用する場合、インラインに定義されたプロジェクションによって knowledgeStore 定義を更新すれば、シェーパー スキルは必要ありません。

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
## <a name="file-projections"></a>ファイル プロジェクション

ファイル プロジェクションは、ソース ドキュメントから抽出された画像か、エンリッチメント プロセスから射影できるエンリッチメントの出力となる画像です。 オブジェクト プロジェクションと同様に、ファイル プロジェクションは BLOB として実装され、画像を含みます。 ファイル プロジェクションを生成するには、プロジェクション オブジェクト内で ```files``` 配列を使用します。 この例では、ドキュメントから抽出されたすべての画像を `samplefile` というコンテナーに射影します。

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [ ],
                "objects": [ ],
                "files": [
                    {
                        "storageContainer": "samplefile",
                        "source": "/document/normalized_images/*"
                    }
                ]
            }
        ]
    }
```

## <a name="projecting-to-multiple-types"></a>複数の種類への射影

シナリオが複雑になると、プロジェクションの種類間でコンテンツを射影する必要が生じる場合があります。 たとえば、キー フレーズやエンティティなどのデータをテーブルに射影する必要がある場合は、テキストとレイアウト テキストの OCR 結果をオブジェクトとして保存して、画像をファイルとして射影します。 スキルセットへのこの更新では、次のことが行われます。

1. ドキュメントごとに、1 行を含む 1 つのテーブルが作成されます。
2. ドキュメント テーブルに関連付けられた 1 つのテーブルが作成され、各キー フレーズがこのテーブル内の 1 行として識別されます。
3. ドキュメント テーブルに関連付けられた 1 つのテーブルが作成され、各エンティティがこのテーブル内の 1 行として識別されます。
4. 画像ごとにレイアウト テキストを含むオブジェクト プロジェクションが作成されます。
5. ファイル プロジェクションが作成され、抽出された各画像が射影されます。
6. ドキュメント テーブル、レイアウト テキストを含むオブジェクト プロジェクション、およびファイル プロジェクションへの参照を含む相互参照テーブルが作成されます。

最初に、整形されたオブジェクトを作成する新しいシェーパー スキルをスキル配列に追加します。 

```json
{
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "ShaperForCross",
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

オブジェクト プロジェクションでは、プロジェクションごとにコンテナー名が必要になります。オブジェクト プロジェクションまたはファイル プロジェクションでは、コンテナーを共有することはできません。 

### <a name="relationships"></a>リレーションシップ

この例では、プロジェクションの別の機能も示しています。同じプロジェクション オブジェクト内に複数の種類のプロジェクションを定義することで、異なる種類 (テーブル、オブジェクト、ファイル) のプロジェクション内またはプロジェクション間のリレーションシップが表されています。これにより、1 つのドキュメントに対する 1 つのテーブル行から始めて、オブジェクト プロジェクションにあるそのドキュメント内の画像の OCR テキストすべてを検出することが可能になります。 データを関連付けたくない場合は、異なるプロジェクション オブジェクト内にプロジェクションを定義します。たとえば、次のスニペットでは、テーブル間は関連付けられていますが、テーブルと OCR テキスト プロジェクション間にリレーションシップはありません。 異なるニーズに対応するために、異なるシェイプ内に同じデータを射影したい場合は、プロジェクション グループが便利です。 たとえば、1 つは Power BI ダッシュボードに対するプロジェクション グループ、もう 1 つはデータを使用して AI モデルにスキルのトレーニングを行うプロジェクション グループとします。
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
                        "source": "/document/pbiShape"
                    },
                    {
                        "tableName": "unrelatedKeyPhrases",
                        "generatedKeyName": "KeyPhraseid",
                        "source": "/document/pbiShape/keyPhrases"
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

上記の例では、プロジェクションの使用方法に関する一般的なパターンを示しました。これで、特定のシナリオにおいてプロジェクションを構築する際の概念も適切に理解できました。

## <a name="common-issues"></a>一般的な問題

プロジェクションを定義するときには、予期しない結果が生じる可能性がある一般的な問題がいくつかあります。

1. 文字列のエンリッチメントが整形されない。 たとえば、キー フレーズによって ```merged_content``` がエンリッチされるなど、文字列がエンリッチされた場合、エンリッチされたプロパティはエンリッチメント ツリー内で merged_content の子として表されます。 しかし、射影時には、名前と値を含む有効な JSON オブジェクトにこれを変換する必要があります。
2. ソース パスの末尾にある ```/*``` が省略される。 たとえば、プロジェクションのソースが ```/document/pbiShape/keyPhrases``` の場合、キー フレーズの配列は単一のオブジェクト/行として射影されます。 ソース パスを ```/document/pbiShape/keyPhrases/*``` に設定すると、キー フレーズごとに単一の行またはオブジェクトが生成されます。
3. パス セレクターでは大文字と小文字が区別され、セレクターに対して大文字と小文字を正しく使用していないと、入力が見つからないという警告が表示される場合があります。

