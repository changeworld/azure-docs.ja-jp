---
title: 非推奨のコグニティブ スキル
titleSuffix: Azure Cognitive Search
description: このページには、近い将来 Azure Cognitive Search スキルセットでサポートされなくなり、非推奨と見なされるコグニティブ スキルの一覧が含まれています。
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/16/2021
ms.openlocfilehash: 884d5cd8d2a1318162a09a39a7ef03bb8b8f612e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631744"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Azure Cognitive Search の非推奨のコグニティブ スキル

このドキュメントでは、非推奨と見なされるコグニティブ スキルについて説明します。 以下の内容について、次のガイドを使用してください。

* スキル名: 非推奨となるスキルの名前です。@odata.type 属性にマッピングされます。
* 利用可能な最終 API バージョン: 対応する非推奨スキルを含むスキルセットを作成/更新できる Azure Cognitive Search パブリック API の最終バージョンです。 これらのスキルを含むスキルセットがアタッチされたインデクサーは、将来の API バージョンでも、"サポート終了"の日まで引き続き実行されます。その日以降は失敗するようになります。
* サポート終了: この日を過ぎると、対応するスキルはサポートされていないと見なされ、動作しなくなります。 以前に作成したスキルセットは引き続き機能するはずですが、ユーザーには非推奨のスキルから移行することが推奨されています。
* 推奨事項: サポートされているスキルを使用するための移行パスです。 ユーザーは、引き続きサポートを受けるため、推奨事項に従うことをお勧めします。

[Microsoft.Skills.Text.EntityRecognitionSkill](#microsoftskillstextentityrecognitionskill) を使用している場合、この記事は、そのスキルセットを [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md) (一般公開されていて、新機能が導入されたもの) を使用するようにアップグレードするうえで役立ちます。 

[Microsoft.Skills.Text.SentimentSkill](#microsoftskillstextsentimentskill) を使用している場合、この記事は、そのスキルセットを [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md) (一般公開されていて、新機能が導入されたもの) を使用するようにアップグレードするうえで役立ちます。 

[Microsoft.Skills.Text.NamedEntityRecognitionSkill](#microsoftskillstextnamedentityrecognitionskill) を使用している場合、この記事は、そのスキルセットを [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md) (一般公開されていて、新機能が導入されたもの) を使用するようにアップグレードするうえで役立ちます。

## <a name="microsoftskillstextentityrecognitionskill"></a>Microsoft.Skills.Text.EntityRecognitionSkill

### <a name="last-available-api-version"></a>利用可能な最終 API バージョン

2021-04-30-Preview

### <a name="end-of-support"></a>サポート終了

2024 年 8 月 31 日

### <a name="recommendations"></a>Recommendations 

代わりに [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md) を使用します。 EntityRecognitionSkill のほとんどの機能を提供しており、品質も向上しています。 さらに、複合出力フィールドに詳細な情報が格納されています。

[Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md) に移行するには、スキル定義で以下の 1 つ以上の変更を行う必要があります。 スキル定義は、[スキルセット更新 API](/rest/api/searchservice/update-skillset) を使用して更新することができます。

1. *(必須)*`@odata.type` を `"#Microsoft.Skills.Text.EntityRecognitionSkill"` から `"#Microsoft.Skills.Text.V3.EntityRecognitionSkill"` に変更します。

2. *(省略可能)* 新しいスキルでは既知の型を持つエンティティのみが返されるため、`includeTypelessEntities` パラメーターはサポートされなくなりました。以前のスキル定義でそれが参照されている場合は、削除されるようになっています。

3. *(省略可能)* `namedEntities` 出力を利用している場合、プロパティ名に軽微な変更がいくつか行われました。
    1. `value` の名前が `text` に変更されました
    2. `confidence` の名前が `confidenceScore` に変更されました

    まったく同じプロパティ名を生成する必要がある場合は、[ShaperSkill](cognitive-search-skill-shaper.md) を追加して、必要な名前を含めて出力を再形成する必要があります。 たとえば、この ShaperSkill により、プロパティの名前が古い値に変更されます。

    ```json
    {
        "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
        "name": "NamedEntitiesShaper",
        "description": "NamedEntitiesShaper",
        "context": "/document/namedEntitiesV3",
        "inputs": [
            {
                "name": "old_format",
                "sourceContext": "/document/namedEntitiesV3/*",
                "inputs": [
                    {
                        "name": "value",
                        "source": "/document/namedEntitiesV3/*/text"
                    },
                    {
                        "name": "offset",
                        "source": "/document/namedEntitiesV3/*/offset"
                    },
                    {
                        "name": "category",
                        "source": "/document/namedEntitiesV3/*/category"
                    },
                    {
                        "name": "confidence",
                        "source": "/document/namedEntitiesV3/*/confidenceScore"
                    }
                ]
            }
        ],
        "outputs": [
            {
                "name": "output",
                "targetName": "namedEntities"
            }
        ]
    }
    ```

4. *(省略可能)* `entities` 出力を利用してエンティティを既知のエンティティにリンクしている場合、この機能は新しいスキルである [Microsoft.Skills.Text.V3.EntityLinkingSkill](cognitive-search-skill-entity-linking-v3.md) になりました。 エンティティ リンク設定スキルをスキルセットに追加して、リンクされたエンティティを生成します。 また、`entities` 出力のプロパティ名にも、`EntityRecognitionSkill` と新しい `EntityLinkingSkill` の間で軽微な変更がいくつか行われています。
    1. `wikipediaId` の名前が `id` に変更されました
    2. `wikipediaLanguage` の名前が `language` に変更されました
    3. `wikipediaUrl` の名前が `url` に変更されました
    4. `type` および `subtype` プロパティは返されなくなりました。

    まったく同じプロパティ名を生成する必要がある場合は、[ShaperSkill](cognitive-search-skill-shaper.md) を追加して、必要な名前を含めて出力を再形成する必要があります。 たとえば、この ShaperSkill により、プロパティの名前が古い値に変更されます。

    ```json
    {
        "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
        "name": "LinkedEntitiesShaper",
        "description": "LinkedEntitiesShaper",
        "context": "/document/linkedEntitiesV3",
        "inputs": [
            {
                "name": "old_format",
                "sourceContext": "/document/linkedEntitiesV3/*",
                "inputs": [
                    {
                        "name": "name",
                        "source": "/document/linkedEntitiesV3/*/name"
                    },
                    {
                        "name": "wikipediaId",
                        "source": "/document/linkedEntitiesV3/*/id"
                    },
                    {
                        "name": "wikipediaLanguage",
                        "source": "/document/linkedEntitiesV3/*/language"
                    },
                    {
                        "name": "wikipediaUrl",
                        "source": "/document/linkedEntitiesV3/*/url"
                    },
                    {
                        "name": "bingId",
                        "source": "/document/linkedEntitiesV3/*/bingId"
                    },
                    {
                        "name": "matches",
                        "source": "/document/linkedEntitiesV3/*/matches"
                    }
                ]
            }
        ],
        "outputs": [
            {
                "name": "output",
                "targetName": "entities"
            }
        ]
    }
    ```

5. *(省略可能)*`categories` を明示的に指定しない場合、`EntityRecognitionSkill V3` は `EntityRecognitionSkill` でサポートされているカテゴリものだけでなく、別の種類のカテゴリも返すことがあります。 この動作が望ましくない場合は、明示的に `categories` パラメーターを `["Person", "Location", "Organization", "Quantity", "Datetime", "URL", "Email"]` に設定します。

    _移行定義サンプル_

    * 単純な移行

        _(変更前) EntityRecognition スキルの定義_

        ```json
        {   
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person" ],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                }
            ]
        }
        ```

        _(変更後) EntityRecognition スキル V3 の定義_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "categories": [ "Person" ],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                }
            ]
        }
        ```
    
    * 複雑な移行

        _(変更前) EntityRecognition スキルの定義_
    
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "includeTypelessEntities": true,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "namedEntities",
                    "targetName": "namedEntities"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        }
        ```
    
        _(変更後) EntityRecognition スキル V3 の定義_
    
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "namedEntities",
                    "targetName": "namedEntitiesV3"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "NamedEntitiesShaper",
            "description": "NamedEntitiesShaper",
            "context": "/document/namedEntitiesV3",
            "inputs": [
                {
                    "name": "old_format",
                    "sourceContext": "/document/namedEntitiesV3/*",
                    "inputs": [
                        {
                            "name": "value",
                            "source": "/document/namedEntitiesV3/*/text"
                        },
                        {
                            "name": "offset",
                            "source": "/document/namedEntitiesV3/*/offset"
                        },
                        {
                            "name": "category",
                            "source": "/document/namedEntitiesV3/*/category"
                        },
                        {
                            "name": "confidence",
                            "source": "/document/namedEntitiesV3/*/confidenceScore"
                        }
                    ]
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "namedEntities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityLinkingSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "entities",
                    "targetName": "linkedEntities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "LinkedEntitiesShaper",
            "description": "LinkedEntitiesShaper",
            "context": "/document/linkedEntitiesV3",
            "inputs": [
                {
                    "name": "old_format",
                    "sourceContext": "/document/linkedEntitiesV3/*",
                    "inputs": [
                        {
                            "name": "name",
                            "source": "/document/linkedEntitiesV3/*/name"
                        },
                        {
                            "name": "wikipediaId",
                            "source": "/document/linkedEntitiesV3/*/id"
                        },
                        {
                            "name": "wikipediaLanguage",
                            "source": "/document/linkedEntitiesV3/*/language"
                        },
                        {
                            "name": "wikipediaUrl",
                            "source": "/document/linkedEntitiesV3/*/url"
                        },
                        {
                            "name": "bingId",
                            "source": "/document/linkedEntitiesV3/*/bingId"
                        },
                        {
                            "name": "matches",
                            "source": "/document/linkedEntitiesV3/*/matches"
                        }
                    ]
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "entities"
                }
            ]
        }
        ```

## <a name="microsoftskillstextsentimentskill"></a>Microsoft.Skills.Text.SentimentSkill

### <a name="last-available-api-version"></a>利用可能な最終 API バージョン

2021-04-30-Preview

### <a name="end-of-support"></a>サポート終了

2024 年 8 月 31 日

### <a name="recommendations"></a>Recommendations 

代わりに [Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md) を使用します。 改善されたモデルが提供され、オピニオン マイニングまたはアスペクトベースのセンチメントを追加するオプションが含まれています。 スキルの複雑性が大幅に増しているため、出力も大きく異なっています。

[Microsoft.Skills.Text.V3.SentimentSkill](cognitive-search-skill-sentiment-v3.md) に移行するには、スキル定義で以下の 1 つ以上の変更を行う必要があります。 スキル定義は、[スキルセット更新 API](/rest/api/searchservice/update-skillset) を使用して更新することができます。

> [!NOTE]
> センチメント スキル V3 のスキル出力は、SentimentSkill に基づくインデックス定義に対応していません。 インデックス定義、スキルセット (後のスキル入力やナレッジ ストア プロジェクション)、インデクサー出力フィールド マッピングに変更を行って、センチメント スキルを新しいバージョンに置き換える必要があります。

1. *(必須)*`@odata.type` を `"#Microsoft.Skills.Text.SentimentSkill"` から `"#Microsoft.Skills.Text.V3.SentimentSkill"` に変更します。

2. *(必須)* センチメント スキル V3 では、テキスト全体で `positive`、`neutral`、`negative` のスコアが提供され、テキスト全体のそれぞれの文に同じスコアが提供されます。それに対し、以前の SentimentSkill では、テキスト全体に対して 0.0 (負) から 1.0 (正) までの範囲の単一の double しか提供されませんでした。 インデックス定義を更新して、1 つのスコアの代わりに 3 つの double 値を受け入れるようにし、ダウンストリーム スキル入力、ナレッジ ストア プロジェクション、出力フィールド マッピングがすべて名前付けの変更と整合することを確認する必要があります。

古い SentimentSkill を SentimentSkill V3 に完全に置き換え、ダウンストリーム スキル入力、ナレッジ ストア プロジェクション、インデクサー出力フィールド マッピング、インデックス定義を新しい出力形式と一致するように更新し、インデクサーをリセットして、すべてのドキュメントで一貫したセンチメント結果が得られるようにすることをお勧めします。

> [!NOTE]
> 最新バージョンのセンチメント スキルを使用するためのエンリッチメント パイプラインの更新で追加の支援が必要な場合、またはインデクサーをリセットすることがご自身に合ったオプションではない場合は、[新しいサポート要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)をオープンしていただくと、Microsoft はお客様と直接協力して作業できます。

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>利用可能な最終 API バージョン

2017-11-11-Preview

### <a name="end-of-support"></a>サポート終了

2024 年 8 月 31 日

### <a name="recommendations"></a>Recommendations 

代わりに [Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md) を使用します。 NamedEntityRecognitionSkill のほとんどの機能を提供しており、品質も向上しています。 さらに、複合出力フィールドに詳細な情報が格納されています。

[Microsoft.Skills.Text.V3.EntityRecognitionSkill](cognitive-search-skill-entity-recognition-v3.md) に移行するには、スキル定義で以下の 1 つ以上の変更を行う必要があります。 スキル定義は、[スキルセット更新 API](/rest/api/searchservice/update-skillset) を使用して更新することができます。

1. *(必須)*`@odata.type` を `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` から `"#Microsoft.Skills.Text.V3.EntityRecognitionSkill"` に変更します。

2. *(省略可能)*`entities` 出力を使用する場合は、`EntityRecognitionSkill V3` からの `namedEntities` 複合コレクション出力を使用します。 `namedEntities` 複合出力のプロパティ名に軽微な変更がいくつか行われています。
    1. `value` の名前が `text` に変更されました
    2. `confidence` の名前が `confidenceScore` に変更されました
    
    まったく同じプロパティ名を生成する必要がある場合は、[ShaperSkill](cognitive-search-skill-shaper.md) を追加して、必要な名前を含めて出力を再形成する必要があります。 たとえば、この ShaperSkill により、プロパティの名前が古い値に変更されます。

    ```json
    {
        "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
        "name": "NamedEntitiesShaper",
        "description": "NamedEntitiesShaper",
        "context": "/document/namedEntities",
        "inputs": [
            {
                "name": "old_format",
                "sourceContext": "/document/namedEntities/*",
                "inputs": [
                    {
                        "name": "value",
                        "source": "/document/namedEntities/*/text"
                    },
                    {
                        "name": "offset",
                        "source": "/document/namedEntities/*/offset"
                    },
                    {
                        "name": "category",
                        "source": "/document/namedEntities/*/category"
                    },
                    {
                        "name": "confidence",
                        "source": "/document/namedEntities/*/confidenceScore"
                    }
                ]
            }
        ],
        "outputs": [
            {
                "name": "output",
                "targetName": "entities"
            }
        ]
    }
    ```

3. *(省略可能)*`categories` を明示的に指定しない場合、`EntityRecognitionSkill V3` は `NamedEntityRecognitionSkill` でサポートされているカテゴリものだけでなく、別の種類のカテゴリも返すことがあります。 この動作が望ましくない場合は、明示的に `categories` パラメーターを `["Person", "Location", "Organization"]` に設定します。

    _移行定義サンプル_

    * 単純な移行

        _(変更前) NamedEntityRecognition スキルの定義_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person" ],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                }
            ]
        }
        ```

        _(変更後) EntityRecognition スキル V3 の定義_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "categories": [ "Person" ],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                }
            ]
        }
        ```
    
    * 少し複雑な移行

        _(変更前) NamedEntityRecognition スキルの定義_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "entities"
                }
            ]
        }
        ```

        _(変更後) EntityRecognition スキル V3 の定義_

        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.V3.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "namedEntities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "name": "NamedEntitiesShaper",
            "description": "NamedEntitiesShaper",
            "context": "/document/namedEntities",
            "inputs": [
                {
                    "name": "old_format",
                    "sourceContext": "/document/namedEntities/*",
                    "inputs": [
                        {
                            "name": "value",
                            "source": "/document/namedEntities/*/text"
                        },
                        {
                            "name": "offset",
                            "source": "/document/namedEntities/*/offset"
                        },
                        {
                            "name": "category",
                            "source": "/document/namedEntities/*/category"
                        },
                        {
                            "name": "confidence",
                            "source": "/document/namedEntities/*/confidenceScore"
                        }
                    ]
                }
            ],
            "outputs": [
                {
                    "name": "output",
                    "targetName": "entities"
                }
            ]
        }
        ```

## <a name="see-also"></a>関連項目

+ [組み込みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [エンティティ認識スキル (V3)](cognitive-search-skill-entity-recognition-v3.md)
+ [センチメント スキル (V3)](cognitive-search-skill-sentiment-v3.md)