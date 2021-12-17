---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: ed3824491e043ea99637604e76d83612d1ad6764
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520702"
---
## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)

## <a name="create-a-new-azure-resource-and-azure-blob-storage-account"></a>新しい Azure リソースと Azure Blob Storage アカウントを作成する

カスタム NER を使う前に、Azure 言語リソースを作成する必要があります。このリソースにより、プロジェクトを作ってモデルのトレーニングを始めるために必要な資格情報が提供されます。 また、モデルの構築に使うデータセットをアップロードできる Azure ストレージ アカウントも必要です。

> [!IMPORTANT]
> すぐに始めるには、以下で示す手順を使用して新しい Azure 言語リソースを作成することをお勧めします。これにより、リソースの作成とストレージ アカウントの構成を同時に行うことができ、後で行うより簡単です。
>
> 既存のリソースを使いたい場合は、それとストレージ アカウントの構成を個別に行う必要があります。 詳しくは、[プロジェクトの作成に関する記事](../../how-to/create-project.md#using-a-pre-existing-azure-resource)をご覧ください。

1. [Azure portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) に移動し、新しい Azure 言語リソースを作成します。 追加機能の選択を求めるメッセージが表示される場合は、 **[Custom text classification & custom NER]\(カスタム テキスト分類とカスタム NER\)** を選びます。 リソースを作るときは、次のパラメーターを指定します。

    |Azure リソースの要件  |必須の値  |
    |---------|---------|
    |Location | "米国西部 2" または "西ヨーロッパ"         |
    |Pricing tier     | Standard (**S**) 価格レベル        |

2. **[Custom Named Entity Recognition (NER) & Custom Classification (Preview)]\(カスタム固有表現認識 (NER) とカスタム分類 (プレビュー)\)** セクションで、既存のストレージ アカウントを選ぶか、 **[新しいストレージ アカウントの作成]** を選びます。 これらの値はこのクイックスタート用であり、必ずしもご自分の運用環境で使用する[ストレージ アカウントの値](/azure/storage/common/storage-account-overview)ではないことに注意してください。

    |ストレージ アカウントの値  |推奨値  |
    |---------|---------|
    | 名前 | 任意の名前 |
    | パフォーマンス | Standard |
    | アカウントの種類| ストレージ (汎用 v1) |
    | レプリケーション | ローカル冗長ストレージ (LRS)
    |Location | 最適な待機時間にするため、最も近い任意の場所。        |


## <a name="upload-sample-data-to-blob-container"></a>サンプル データを BLOB コンテナーにアップロードする

Azure ストレージ アカウントを作成し、それを言語リソースにリンクしたら、このクイックスタート用のサンプル ファイルをアップロードする必要があります。 後でこれらのファイルを使ってモデルをトレーニングします。

1. GitHub からこのクイックスタート用の[サンプル データをダウンロード](https://go.microsoft.com/fwlink/?linkid=2175226)します。

2. [Azure portal](https://ms.portal.azure.com) でご自分の Azure ストレージ アカウントに移動します。 ご自分のアカウントに移動し、サンプル データをそこにアップロードします。

提供されているサンプル データセットには、20 件のローン契約が含まれ、各契約は貸し手と借り手の 2 つの当事者です。 提供されているサンプル タグ ファイルを使って、両当事者の関連情報、契約日、ローン金額、利率を抽出できます。

### <a name="get-your-resource-keys-endpoint"></a>リソース キー エンドポイントを取得する

* [Azure portal](https://ms.portal.azure.com/#home) でリソースの概要ページに移動します

* 画面の左側のメニューで **[キーとエンドポイント]** を選びます。 API 要求にエンドポイントを使い、`Ocp-Apim-Subscription-Key` ヘッダーのキーが必要です。
:::image type="content" source="../../../media/azure-portal-resource-credentials.png" alt-text="Azure リソースのキーとエンドポイントの画面を示すスクリーンショット" lightbox="../../../media/azure-portal-resource-credentials.png":::

## <a name="create-a-custom-ner-project"></a>カスタム NER プロジェクトを作成する

リソースとストレージ コンテナーを構成したら、新しいカスタム NER プロジェクトを作ります。 プロジェクトは、データに基づいてカスタム AI モデルを構築するための作業領域です。 プロジェクトにアクセスできるのは、自分と、使われている Azure リソースへの共同作成者アクセス権を持つ他のユーザーのみです。

> [!NOTE]
> プロジェクト名は、すべての操作で大文字と小文字が区別されます。

プロジェクトを作成してタグ ファイルをインポートするには、次の URL、ヘッダー、JSON 本文を使って **POST** 要求を作成します。

次の URL を使ってプロジェクトを作成し、タグ ファイルをインポートします。 次のプレースホルダーの値を実際の値に置き換えてください。 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{projectName}/:import. 
```

|プレースホルダー  |値  | 例 |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | API 要求を認証するためのエンドポイント。   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

### <a name="parameters"></a>パラメーター

次のパラメーターを要求と一緒に渡します。 

|キー|説明|値|
|--|--|--|
|`api-version`| 使用されている API バージョン。| `2021-11-01-preview` |

パラメーターを渡す場合は、要求 URL の末尾に `?api-version=2021-11-01-preview` を追加します。

### <a name="headers"></a>ヘッダー

要求を認証するには、次のヘッダーを使います。 

|Key|値|
|--|--|
|`Ocp-Apim-Subscription-Key`| リソースへのキー。 API 要求の認証に使われます。|

### <a name="body"></a>Body

要求では次の JSON を使います。 次のプレースホルダーの値を実際の値に置き換えてください。 [サンプル データ](https://github.com/Azure-Samples/cognitive-services-sample-data-files)のタブで利用できるタグ ファイルを使います

```json
{
    "api-version": "2021-11-01-preview",
    "metadata": {
        "name": "MyProject",
        "multiLingual": true,
        "description": "Tryong out custom text classification",
        "modelType": "Extraction",
        "language": "string",
        "storageInputContainerName": "YOUR-CONTAINER-NAME",
        "settings": {}
    },
    "assets": {
        "extractors": [
        {
            "name": "Entity1"
        },
        {
            "name": "Entity2"
        }
    ],
    "documents": [
        {
            "location": "doc1.txt",
            "language": "en-us",
            "extractors": [
                {
                    "regionOffset": 0,
                    "regionLength": 500,
                    "labels": [
                        {
                            "extractorName": "Entity1",
                            "offset": 25,
                            "length": 10
                        },                    
                        {
                            "extractorName": "Entity2",
                            "offset": 120,
                            "length": 8
                        }
                    ]
                }
            ]
        },
        {
            "location": "doc2.txt",
            "language": "en-us",
            "extractors": [
                {
                    "regionOffset": 0,
                    "regionLength": 100,
                    "labels": [
                        {
                            "extractorName": "Entity2",
                            "offset": 20,
                            "length": 5
                        }
                    ]
                }
            ]
        }
    ]
    }
}
```
メタデータ キーの場合: 

|Key  |値  | 例 |
|---------|---------|---------|
| `modelType  `    | モデルの種類。 | 抽出 |
|`storageInputContainerName`   | Azure Blob Storage コンテナーの名前。   | `myContainer` |

次の場合、この要求からはエラーが返されます。

* 選んだリソースに、ストレージ アカウントに対する適切なアクセス許可がありません。 

## <a name="start-training-your-model"></a>モデルのトレーニングを開始する

プロジェクトが作成されたら、カスタム NER モデルのトレーニングを始めることができます。 NER モデルのトレーニングを始めるには、次の URL、ヘッダー、JSON 本文を使って **POST** 要求を作成します。
テキスト分類モデルのトレーニングを始めるには、次の URL、ヘッダー、JSON 本文を使って **POST** 要求を作成します。

### <a name="request-url"></a>要求 URL

API 要求を作るときは、次の URL を使います。 次のプレースホルダーの値を実際の値に置き換えてください。 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{PROJECT-NAME}/:train
```

|プレースホルダー  |値  | 例 |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | API 要求を認証するためのエンドポイント。   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | プロジェクトの名前。 この値は、大文字と小文字が区別されます。  | `myProject` |

### <a name="parameters"></a>パラメーター

次のパラメーターを要求と一緒に渡します。

|キー|説明|値|
|--|--|--|
|`api-version`| 使用されている API バージョン。| `2021-11-01-preview` |

パラメーターを渡す場合は、要求 URL の末尾に `?api-version=2021-11-01-preview` を追加します。

### <a name="headers"></a>ヘッダー

要求を認証するには、次のヘッダーを使います。 

|Key|値|
|--|--|
|`Ocp-Apim-Subscription-Key`| リソースへのキー。 API 要求の認証に使われます。|

### <a name="request-body"></a>要求本文

要求では次の JSON を使います。 トレーニングが完了すると、モデルに `MyModel` という名前が付けられます。  

```json
{
  "modelLabel": "MyModel",
  "runValidation": true
}
```

|Key  |値  | 例 |
|---------|---------|---------|
|`modelLabel  `    | モデルの名前。   | MyModel |
|`runValidation`     | テスト セットで検証を実行するためのブール値。   | はい |

API 要求を送信すると、成功を示す `202` 応答が返されます。 応答ヘッダーで、`location` の値を抽出します。 それは次のように書式設定されています。 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{YOUR-PROJECT-NAME}/train/jobs/{JOB-ID}?api-version=xxxx-xx-xx-xxxxxxx
``` 

この操作は非同期であるため、`JOB-ID` を使って要求が識別されます。 次のステップではこの URL を使ってトレーニングの状態を取得します。 

## <a name="get-training-status"></a>トレーニング状態の取得

モデルのトレーニング プロセスの状態を照会するには、次の **GET** 要求を使います。 前のステップで取得した URL を使うことも、下のプレースホルダーの値を実際の値に置き換えることもできます。 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{YOUR-PROJECT-NAME}/train/jobs/{JOB-ID}
```

|プレースホルダー  |値  | 例 |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | API 要求を認証するためのエンドポイント。   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | プロジェクトの名前。 この値は、大文字と小文字が区別されます。  | `myProject` |
|`{JOB-ID}`     | モデルのトレーニングの状態を取得するための ID。 これは、前のステップで受け取った `location` ヘッダーの値に含まれています。  | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx` |

### <a name="parameters"></a>パラメーター

次のパラメーターを要求と一緒に渡します。 

|キー|説明|値|
|--|--|--|
|`api-version`| 使用されている API バージョン。| `2021-11-01-preview` |

パラメーターを渡す場合は、要求 URL の末尾に `?api-version=2021-11-01-preview` を追加します。

### <a name="headers"></a>ヘッダー

要求を認証するには、次のヘッダーを使います。 

|Key|値|
|--|--|
|`Ocp-Apim-Subscription-Key`| リソースへのキー。 API 要求の認証に使われます。|

### <a name="response-body"></a>応答本文

要求を送信すると、次の応答を受け取ります。 

```json
{
  "jobs": [
    {
      "result": {
        "trainedModelLabel": "MyModel",
        "trainStatus": {
          "percentComplete": 0,
          "elapsedTime": "string"
        },
        "evaluationStatus": {
          "percentComplete": 0,
          "elapsedTime": "string"
        }
      },
      "jobId": "string",
      "createdDateTime": "2021-10-19T23:24:41.572Z",
      "lastUpdatedDateTime": "2021-10-19T23:24:41.572Z",
      "expirationDateTime": "2021-10-19T23:24:41.572Z",
      "status": "unknown",
      "errors": [
        {
          "code": "unknown",
          "message": "string"
        }
      ]
    }
  ],
  "nextLink": "string"
}
```
## <a name="deploy-your-model"></a>モデルをデプロイする

カスタム NER モデルのデプロイを始めるには、次の URL、ヘッダー、JSON 本文を使って **PUT** 要求を作成します。

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{PROJECT-NAME}/deployments/{DEPLOYMENT-NAME}
```

|プレースホルダー  |値  | 例 |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | API 要求を認証するためのエンドポイント。   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | プロジェクトの名前。 この値は、大文字と小文字が区別されます。  | `myProject` |
|`{DEPLOYMENT-NAME}`     | デプロイの名前。 この値は、大文字と小文字が区別されます。  | `prod` |

### <a name="parameters"></a>パラメーター

次のパラメーターを要求と一緒に渡します。 

|キー|説明|値|
|--|--|--|
|`api-version`| 使用されている API バージョン。| `2021-11-01-preview` |

パラメーターを渡す場合は、要求 URL の末尾に `?api-version=2021-11-01-preview` を追加します。

### <a name="headers"></a>ヘッダー

要求を認証するには、次のヘッダーを使います。 

|Key|値|
|--|--|
|`Ocp-Apim-Subscription-Key`| リソースへのキー。 API 要求の認証に使われます。|

### <a name="request-body"></a>要求本文

要求では次の JSON を使います。 トレーニングが完了すると、モデルに `MyModel` という名前が付けられます。  

```json
{
{
  "trainedModelLabel": "MyModel"
}
```

API 要求を送信すると、成功を示す `202` 応答が返されます。 応答ヘッダーで、`location` の値を抽出します。 それは次のように書式設定されています。 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{YOUR-PROJECT-NAME}/deployments/{DEPLOYMENT-NAME}/jobs/{JOB-ID}?api-version=xxxx-xx-xx-xxxxxxx
``` 

この操作は非同期であるため、`JOB-ID` を使って要求が識別されます。 次のステップではこの URL を使って発行の状態を取得します。

## <a name="get-the-deployment-status"></a>デプロイの状態を取得する

モデルの発行プロセスの状態を照会するには、次の **GET** 要求を使います。 前のステップで取得した URL を使うことも、下のプレースホルダーの値を実際の値に置き換えることもできます。 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{YOUR-PROJECT-NAME}/deployments/{DEPLOYMENT-NAME}/jobs/{JOB-ID}
```

|プレースホルダー  |値  | 例 |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | API 要求を認証するためのエンドポイント。   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | プロジェクトの名前。 この値は、大文字と小文字が区別されます。  | `myProject` |
|`{DEPLOYMENT-NAME}`     | デプロイの名前。 この値は、大文字と小文字が区別されます。  | `prod` |
|`{JOB-ID}`     | モデルのトレーニングの状態を取得するための ID。 これは、前のステップで受け取った `location` ヘッダーの値に含まれています。  | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx` |

### <a name="parameters"></a>パラメーター

次のパラメーターを要求と一緒に渡します。 

|キー|説明|値|
|--|--|--|
|`api-version`| 使用されている API バージョン。| `2021-11-01-preview` |

パラメーターを渡す場合は、要求 URL の末尾に `?api-version=2021-11-01-preview` を追加します。

### <a name="headers"></a>ヘッダー

要求を認証するには、次のヘッダーを使います。 

|Key|値|
|--|--|
|`Ocp-Apim-Subscription-Key`| リソースへのキー。 API 要求の認証に使われます。|


### <a name="submit-custom-ner-task"></a>カスタム NER タスクを送信する

モデルがデプロイされたので、それへのエンティティ認識タスクの送信を始めることができます 

> [!NOTE]
> プロジェクト名は大文字と小文字が区別されます。

エンティティ抽出タスクを始めるには、この **POST** 要求を使います。 `{projectName}` は、使うモデルが含まれるプロジェクトの名前に置き換えます。

`{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze`

#### <a name="headers"></a>ヘッダー

|Key|値|
|--|--|
|Ocp-Apim-Subscription-Key| この API へのアクセスを提供するサブスクリプション キー。|

#### <a name="body"></a>Body

```json
    {
    "displayName": "MyJobName",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1", 
                "text": "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc tempus, felis sed vehicula lobortis, lectus ligula facilisis quam, quis aliquet lectus diam id erat. Vivamus eu semper tellus. Integer placerat sem vel eros iaculis dictum. Sed vel congue urna."
            },
            {
                "id": "doc2",
                "text": "Mauris dui dui, ultricies vel ligula ultricies, elementum viverra odio. Donec tempor odio nunc, quis fermentum lorem egestas commodo. Class aptent taciti sociosqu ad litora torquent per conubia nostra, per inceptos himenaeos."
            }
        ]
    },
    "tasks": {
        "customEntityRecognitionTasks": [      
            {
                "parameters": {
                      "project-name": "MyProject",
                      "deployment-name": "MyDeploymentName"
                      "stringIndexType": "TextElements_v8"
                }
            }
        ]
    }
}
```

|Key|値の例|説明|
|--|--|--|
|displayName|"MyJobName"|ジョブの名前|
|ドキュメント|[{},{}]|タスクを実行するドキュメントのリスト|
|id|"doc1"|文字列のドキュメント識別子|
|text|"Lorem ipsum dolor sit amet"| 文字列形式のドキュメント|
|"tasks"|[]| 実行するタスクのリスト。|
|--|customEntityRecognitionTasks|実行するタスクのタスク識別子。 |
|parameters|[]|タスクに渡すパラメーターのリスト|
|project-name| "MyProject"| プロジェクトの名前。 プロジェクト名は大文字と小文字が区別されます。|
|deployment-name| "MyDeploymentName"| デプロイの名前|

#### <a name="response"></a>Response

成功を示す 202 応答を受け取ります。 応答の **ヘッダー** で、`operation-location` を抽出します。
`operation-location` は次のように書式設定されています。

 `{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId>`

次のステップではこのエンドポイントを使って、カスタム認識タスクの結果を取得します。

### <a name="get-task-status-and-results"></a>タスクの状態と結果を取得する

カスタム認識タスクの状態と結果を照会するには、次の **GET** 要求を使います。 前のステップで取得したエンドポイントを使うことができます。

`{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId>`.

#### <a name="headers"></a>ヘッダー

|Key|値|
|--|--|
|Ocp-Apim-Subscription-Key| この API へのアクセスを提供するサブスクリプション キー。|

### <a name="response-body"></a>応答本文

応答は、次のパラメーターを含む JSON ドキュメントです

```json
{
    "createdDateTime": "2021-05-19T14:32:25.578Z",
    "displayName": "MyJobName",
    "expirationDateTime": "2021-05-19T14:32:25.578Z",
    "jobId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
    "lastUpdateDateTime": "2021-05-19T14:32:25.578Z",
    "status": "completed",
    "errors": [],
    "tasks": {
        "details": {
            "name": "MyJobName",
            "lastUpdateDateTime": "2021-03-29T19:50:23Z",
            "status": "completed"
        },
        "completed": 1,
        "failed": 0,
        "inProgress": 0,
        "total": 1,
        "tasks": {
    "customEntityRecognitionTasks": [
        {
            "lastUpdateDateTime": "2021-05-19T14:32:25.579Z",
            "name": "MyJobName",
            "status": "completed",
            "results": {
                "documents": [
                    {
                        "id": "doc1",
                        "entities": [
                            {
                                "text": "Government",
                                "category": "restaurant_name",
                                "offset": 23,
                                "length": 10,
                                "confidenceScore": 0.0551877357
                            }
                        ],
                        "warnings": []
                    },
                    {
                        "id": "doc2",
                        "entities": [
                            {
                                "text": "David Schmidt",
                                "category": "artist",
                                "offset": 0,
                                "length": 13,
                                "confidenceScore": 0.8022353
                            }
                        ],
                        "warnings": []
                    }
                ],
                "errors": [],
                "statistics": {
                    "documentsCount":0,
                    "validDocumentsCount":0,
                    "erroneousDocumentsCount":0,
                    "transactionsCount":0
                }
                    }
                }
            ]
        }
    }
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

プロジェクトが不要になったら、次の **DELETE** 要求で削除できます。 プレースホルダーの値は、実際の値に置き換えます。   

```rest
{YOUR-ENDPOINT}/language/text/authoring/v1.0-preview.2/projects/{PROJECT-NAME}
```

|プレースホルダー  |値  | 例 |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | API 要求を認証するためのエンドポイント。   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | プロジェクトの名前。 この値は、大文字と小文字が区別されます。  | `myProject` |

### <a name="headers"></a>ヘッダー

要求を認証するには、次のヘッダーを使います。 

|Key|値|
|--|--|
|Ocp-Apim-Subscription-Key| リソースへのキー。 API 要求の認証に使われます。|
