---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 882355f0c4e6a44300182ffd27eb4cc422422f5f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091829"
---
## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)。
* 最新バージョンの [cURL](https://curl.haxx.se/)。
* 言語リソース。 ない場合は、[Azure portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) を使って作ることができます。 新しいリソースを作る場合は、リンクをクリックし、手順に従って、デプロイが完了するまで待ちます。 次に、 **[リソースに移動]** をクリックします。

## <a name="get-your-resource-keys-and-endpoint"></a>リソースのキーとエンドポイントを取得する

1. [Azure portal](https://ms.portal.azure.com/#home) でリソースの概要ページに移動します。
2. 左側のメニューで **[キーとエンドポイント]** を選びます。 API 要求のエンドポイントとキーを使います 

    :::image type="content" source="../../../media/azure-portal-resource-credentials.png" alt-text="Azure portal のキーとエンドポイントのページを示すスクリーンショット" lightbox="../../../media/azure-portal-resource-credentials.png":::

## <a name="import-a-project"></a>プロジェクトをインポートする

開始するには、CLU JSON をサービスにインポートします。 クイックスタートでは、"EmailProject" という名前のメール アプリケーションにいくつかの意図とエンティティを設定する、以下のサンプル JSON が提供されます。 

プロジェクトを作るには、次の URL、ヘッダー、JSON 本文を使って **POST** 要求を作ります。

### <a name="request-url"></a>要求 URL

API 要求を作るときは、次の URL を使います。 次のプレースホルダーの値を実際の値に置き換えてください。 

```rest
{YOUR-ENDPOINT}/language/analyze-conversation/projects/EmailProject/:import?api-version=2021-11-01-preview
```

|プレースホルダー  |値  | 例 |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | API 要求を認証するためのエンドポイント。   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

### <a name="headers"></a>ヘッダー

要求を認証するには、次のヘッダーを使います。 

|Key|値|
|--|--|
|`Ocp-Apim-Subscription-Key`| リソースへのキー。 API 要求の認証に使われます。|

### <a name="body"></a>Body

次のサンプル JSON を本文として使います。

```json
{
    "api-version": "2021-11-01-preview",
    "metadata": {
        "name": "EmailProject",
        "description": "",
        "type": "Conversation",
        "multilingual": true,
        "language": "en-us"
    },
    "assets": {
        "intents": [
            {
                "name": "Read"
            },
            {
                "name": "Delete"
            },
            {
                "name": "Attach"
            }
        ],
        "entities": [
            {
                "name": "Sender"
            },
            {
                "name": "FileName"
            },
            {
                "name": "FileType"
            }
        ],
        "examples": [
            {
                "text": "add the pdf file with the name signed contract",
                "language": "en-us",
                "intent": "Attach",
                "entities": [
                    {
                        "entity": "FileName",
                        "offset": 31,
                        "length": 15
                    },
                    {
                        "entity": "FileType",
                        "offset": 8,
                        "length": 3
                    }
                ]
            },
            {
                "text": "attach the powerpoint file",
                "language": "en-us",
                "intent": "Attach",
                "entities": [
                    {
                        "entity": "FileType",
                        "offset": 11,
                        "length": 10
                    }
                ]
            },
            {
                "text": "attach the excel file called reports q1",
                "language": "en-us",
                "intent": "Attach",
                "entities": [
                    {
                        "entity": "FileName",
                        "offset": 29,
                        "length": 10
                    },
                    {
                        "entity": "FileType",
                        "offset": 11,
                        "length": 5
                    }
                ]
            },
            {
                "text": "move this to the deleted folder",
                "language": "en-us",
                "intent": "Delete",
                "entities": []
            },
            {
                "text": "remove this one",
                "language": "en-us",
                "intent": "Delete",
                "entities": []
            },
            {
                "text": "delete this",
                "language": "en-us",
                "intent": "Delete",
                "entities": []
            },
            {
                "text": "delete my last email from martha",
                "language": "en-us",
                "intent": "Delete",
                "entities": [
                    {
                        "entity": "Sender",
                        "offset": 26,
                        "length": 6
                    }
                ]
            },
            {
                "text": "what did the email from matt say",
                "language": "en-us",
                "intent": "Read",
                "entities": [
                    {
                        "entity": "Sender",
                        "offset": 24,
                        "length": 4
                    }
                ]
            },
            {
                "text": "read john's email for me",
                "language": "en-us",
                "intent": "Read",
                "entities": [
                    {
                        "entity": "Sender",
                        "offset": 5,
                        "length": 4
                    }
                ]
            },
            {
                "text": "read the email from carol",
                "language": "en-us",
                "intent": "Read",
                "entities": [
                    {
                        "entity": "Sender",
                        "offset": 20,
                        "length": 5
                    }
                ]
            }
        ]
    }
}
```

## <a name="start-training-your-model"></a>モデルのトレーニングを開始する

プロジェクトがインポートされたら、モデルのトレーニングを開始できます。 トレーニングを始めるには、次の URL、ヘッダー、JSON 本文を使って **POST** 要求を作ります。

### <a name="request-url"></a>要求 URL

API 要求を作るときは、次の URL を使います。 次のプレースホルダーの値を実際の値に置き換えてください。 

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/:train?api-version=2021-11-01-preview
```

|プレースホルダー  |値  | 例 |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | API 要求を認証するためのエンドポイント。   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

### <a name="headers"></a>ヘッダー

要求を認証するには、次のヘッダーを使います。 

|Key|値|
|--|--|
|`Ocp-Apim-Subscription-Key`| リソースへのキー。 API 要求の認証に使われます。|

### <a name="request-body"></a>要求本文

要求では次のオブジェクトを使います。 トレーニングが完了すると、モデルに `MyModel` という名前が付けられます。  

```json
{
  "modelLabel":"MyModel",
  "RunVerification":false
}
```

API 要求を送信すると、成功を示す `202` 応答が返されます。 応答ヘッダーで、`location` の値を抽出します。 それは次のように書式設定されています。 

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/train/jobs/{JOB-ID}?api-version=2021-11-01-preview
``` 

この操作は非同期であるため、`JOB-ID` を使って要求が識別されます。 次のステップではこの URL を使ってトレーニングの状態を取得します。 

## <a name="get-training-status"></a>トレーニング状態の取得

モデルのトレーニング プロセスの状態を照会するには、次の **GET** 要求を使います。 前のステップで取得した URL を使うことも、下のプレースホルダーの値を実際の値に置き換えることもできます。 

### <a name="request-url"></a>要求 URL

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/train/jobs/{JOB-ID}?api-version=2021-11-01-preview
```

|プレースホルダー  |値  | 例 |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | API 要求を認証するためのエンドポイント。   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{JOB-ID}`     | モデルのトレーニングの状態を取得するための ID。 これは、前のステップで受け取った `location` ヘッダーの値に含まれています。  | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx` |

### <a name="headers"></a>ヘッダー

要求を認証するには、次のヘッダーを使います。 

|Key|値|
|--|--|
|`Ocp-Apim-Subscription-Key`| リソースへのキー。 API 要求の認証に使われます。|


### <a name="response-body"></a>応答本文

要求を送信すると、次の応答を受け取ります。 **[status]\(状態\)** パラメーターが [succeeded]\(成功\) に変更されるまで、このエンドポイントのポーリングを続けます。 

```json
{
    "result":
    {
          "trainedModelLabel":"MyModel",
          "trainStatus":{"percentComplete":0,"elapsedTime":null},
          "evaluationStatus":{"percentComplete":0,"elapsedTime":null}
     },
    "jobId":"{JOB-ID}",
    "createdDateTime":"{CREATED-TIME}",
    "lastUpdatedDateTime":"{UPDATED-TIME}",
    "expirationDateTime":"{EXPIRATION-TIME}",
    "status":"running"
}
```

## <a name="deploy-your-model"></a>モデルをデプロイする

トレーニングが完了したら、モデルを予測用にデプロイできます。 

会話言語理解モデルのデプロイを始めるには、次の URL、ヘッダー、JSON 本文を使って **POST** 要求を作ります。


### <a name="request-url"></a>要求 URL

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/deployments/production?api-version=2021-11-01-preview
```

|プレースホルダー  |値  | 例 |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | API 要求を認証するためのエンドポイント。   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |


### <a name="headers"></a>ヘッダー

要求を認証するには、次のヘッダーを使います。 

|Key|値|
|--|--|
|`Ocp-Apim-Subscription-Key`| リソースへのキー。 API 要求の認証に使われます。|


### <a name="request-body"></a>要求本文

```json
{
  "trainedModelLabel":"MyModel",
  "deploymentName":"production"
}
```

API 要求を送信すると、成功を示す `202` 応答が返されます。 応答ヘッダーで、`location` の値を抽出します。 それは次のように書式設定されています。 

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/deployments/production/jobs/{JOB-ID}?api-version=2021-11-01-preview
``` 

この操作は非同期であるため、`JOB-ID` を使って要求が識別されます。 次のステップではこの URL を使ってトレーニングの状態を取得します。

## <a name="get-deployment-status"></a>展開状態の取得

モデルのデプロイ プロセスの状態を照会するには、次の **GET** 要求を使います。 前のステップで取得した URL を使うことも、下のプレースホルダーの値を実際の値に置き換えることもできます。 

### <a name="request-url"></a>要求 URL

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/deployments/production/jobs/{JOB-ID}?api-version=2021-11-01-preview
```

|プレースホルダー  |値  | 例 |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | API 要求を認証するためのエンドポイント。   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{JOB-ID}`     | モデルのトレーニングの状態を取得するための ID。 これは、前のステップで受け取った `location` ヘッダーの値に含まれています。  | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx` |

### <a name="headers"></a>ヘッダー

要求を認証するには、次のヘッダーを使います。 

|Key|値|
|--|--|
|`Ocp-Apim-Subscription-Key`| リソースへのキー。 API 要求の認証に使われます。|


### <a name="response-body"></a>応答本文

要求を送信すると、次の応答を受け取ります。 **[status]\(状態\)** パラメーターが [succeeded]\(成功\) に変更されるまで、このエンドポイントのポーリングを続けます。 

```json
{
    "jobId":"{JOB-ID}",
    "createdDateTime":"{CREATED-TIME}",
    "lastUpdatedDateTime":"{UPDATED-TIME}",
    "expirationDateTime":"{EXPIRATION-TIME}",
    "status":"running"
}
```

## <a name="query-model"></a>モデルにクエリを実行する

デプロイが成功すると、予測用のプロジェクトにクエリの実行を開始できます。 

会話言語理解モデルのデプロイを始めるには、次の URL、ヘッダー、JSON 本文を使って **POST** 要求を作ります。

### <a name="request-url"></a>要求 URL

```rest
{YOUR-ENDPOINT}/language/:analyze-conversations?projectName=EmailProject&deploymentName=production&api-version=2021-11-01-preview
```

|プレースホルダー  |値  | 例 |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | API 要求を認証するためのエンドポイント。   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

### <a name="headers"></a>ヘッダー

要求を認証するには、次のヘッダーを使います。 

|Key|値|
|--|--|
|`Ocp-Apim-Subscription-Key`| リソースへのキー。 API 要求の認証に使われます。|

### <a name="request-body"></a>要求本文

```json
{
  "query":"attach a docx file"
}
```

### <a name="response-body"></a>応答本文

要求を送信すると、予測に対して次の応答を受け取ります。

```json
{
    "query":"attach a docx file",
    "prediction": {
        "topIntent":"Attach",
        "projectKind":"conversation",
        "intents":[{"category":"Attach","confidenceScore":0.9998592},{"category":"Read","confidenceScore":0.00010551753},{"category":"Delete","confidenceScore":3.5209276E-05}],
        "entities":[{"category":"FileType","text":"docx","offset":9,"length":4,"confidenceScore":1}]
     }
}
```
