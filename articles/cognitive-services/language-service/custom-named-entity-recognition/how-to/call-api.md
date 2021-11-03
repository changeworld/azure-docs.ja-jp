---
title: カスタム固有表現認識 (NER) タスクを送信する
titleSuffix: Azure Cognitive Services
description: カスタム固有表現認識 (NER) の要求の送信について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 874d7c93a4e884c5bd449c6d49d676147e673978
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091299"
---
# <a name="deploy-a-model-and-extract-entities-from-text-using-the-runtime-api"></a>モデルをデプロイし、ランタイム API を使ってテキストからエンティティを抽出する

モデルのパフォーマンスに問題がなければ、デプロイして、テキスト内のエンティティの認識に使うことができます。 エンティティ認識タスクは API からのみ送信でき、Language Studio からはできません。

## <a name="prerequisites"></a>前提条件

* 構成済みの Azure Blob Storage アカウントで正常に[作成されたプロジェクト](create-project.md)
    * ストレージ アカウントに[アップロードされた](create-project.md#prepare-training-data)テキスト データ。
* [タグ付けされたデータ](tag-data.md)
* [正常にトレーニングされたモデル](train-model.md)
* [モデルの評価の詳細](view-model-evaluation.md)を調べて、モデルのパフォーマンスを確認した。
    * (省略可能) パフォーマンスが十分でない場合、モデルを[改善した](improve-model.md)。

詳しくは、[アプリケーション開発ライフサイクル](../overview.md#application-development-lifecycle)に関する記事をご覧ください。

## <a name="deploy-your-model"></a>モデルをデプロイする

1. [Language Studio](https://aka.ms/custom-extraction) でプロジェクトに移動します

2. 左側のメニューから **[Deploy model]\(モデルのデプロイ\)** を選びます。

3. デプロイするモデルを選び、 **[Deploy model]\(モデルのデプロイ\)** を選びます。

> [!TIP]
> Language Studio でテキストのサンプルを送信して分類することで、モデルをテストできます。 
> 1. Language Studio で、プロジェクトの左側にあるメニューから **[Test model]\(モデルのテスト\)** を選びます。
> 2. テストするモデルを選びます。
> 3. テキストをテキスト ボックスに追加します。`.txt` ファイルをアップロードすることもできます。 
> 4. **[Run the test]\(テストの実行\)** をクリックします。
> 5. **[Result]\(結果\)** タブで、テキストから抽出されたエンティティを確認できます。 **[JSON]** タブで JSON 応答を表示することもできます。

## <a name="send-a-text-classification-request-to-your-model"></a>モデルにテキスト分類要求を送信する

# <a name="using-language-studio"></a>[Language Studio を使用する](#tab/language-studio)

### <a name="using-language-studio"></a>Language Studio を使用する

1. デプロイが終わったら、使うモデルを選び、上部のメニューで **[Get prediction URL]\(予測 URL の取得\)** をクリックして、URL と本文をコピーします。

    :::image type="content" source="../../custom-classification/media/get-prediction-url-1.png" alt-text="推論の実行" lightbox="../../custom-classification/media/get-prediction-url-1.png":::

2. 表示されたウィンドウの **[Submit]\(送信\)** ピボットで、サンプルの要求をコマンド ラインにコピーします

3. `<YOUR_DOCUMENT_HERE>` を、分類する実際のテキストに置き換えます。

    :::image type="content" source="../../custom-classification/media/get-prediction-url-2.png" alt-text="推論の実行 2" lightbox="../../custom-classification/media/get-prediction-url-2.png":::

4. 要求を送信します。

5. 応答ヘッダーで、`{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId}>` という形式の `operation-location` から抽出の `jobId` を受け取ります。

6. 取得要求をコピーし、`jobId` を置き換えて、要求を送信します。

    :::image type="content" source="../../custom-classification/media/get-prediction-url-3.png" alt-text="推論の実行 3" lightbox="../../custom-classification/media/get-prediction-url-3.png":::
    
 ## <a name="retrieve-the-results-of-your-job"></a>ジョブの結果を取得する

1. 前に要求の例を取得したのと同じウィンドウで **[Retrieve]\(取得\)** を選び、サンプル要求をテキスト エディターにコピーします。 

    :::image type="content" source="../media/get-prediction-retrieval-url.png" alt-text="予測の取得要求と URL を示すスクリーンショット" lightbox="../media/get-prediction-retrieval-url.png":::

2. `<OPERATION_ID>` を、前のステップの `jobId` に置き換えます。 

3. ターミナルまたはコマンド プロンプトで、`GET` cURL 要求を送信します。 要求が成功した場合は、次のような 202 応答と JSON を受け取ります。

結果について詳しくは、次のセクションをご覧ください。

# <a name="using-the-api"></a>[API を使用する](#tab/api)

### <a name="using-the-api"></a>API を使用する

### <a name="get-your-resource-keys-endpoint"></a>リソース キー エンドポイントを取得する

1. [Azure portal](https://ms.portal.azure.com/#home) でリソースの概要ページに移動します

2. 左側のメニューで **[キーとエンドポイント]** を選びます。 API 要求にエンドポイントを使い、`Ocp-Apim-Subscription-Key` ヘッダーのキーが必要です。

    :::image type="content" source="../../custom-classification/media/get-endpoint-azure.png" alt-text="Azure エンドポイントを取得する" lightbox="../../custom-classification/media/get-endpoint-azure.png":::

### <a name="submit-custom-ner-task"></a>カスタム NER タスクを送信する

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

結果について詳しくは、次のセクションをご覧ください。

---

#### <a name="custom-extraction-task-results"></a>カスタム抽出タスクの結果

GET 結果呼び出しから返される応答は、次のパラメーターを含む JSON ドキュメントになります。

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
                    "erroneousDocumentsCount":0,
                    "transactionsCount":0
                }
                    }
                }
            ]
        }
    }
```
