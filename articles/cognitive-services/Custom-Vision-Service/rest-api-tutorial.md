---
title: Custom Vision Service REST API の使用 - Azure Cognitive Services | Microsoft Docs
description: REST API を使用してカスタム ビジョン モデルを作成、トレーニング、テスト、およびエクスポートします。
services: cognitive-services
author: blackmist
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 08/07/2018
ms.author: larryfr
ms.openlocfilehash: 44fa4d45c33f3064c089724ee761a70d0a8421ab
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2018
ms.locfileid: "40250253"
---
# <a name="tutorial-use-the-custom-vision-rest-api"></a>チュートリアル: Custom Vision REST API の使用

Custom Vision REST API を使用してモデルを作成、トレーニング、テスト、およびエクスポートする方法について説明します。

このドキュメントの情報は、REST クライアントを使用して、Custom Vision サービスをトレーニングするための REST API を操作する方法を示します。 例では、bash 環境から `curl` ユーティリティを、また Windows PowerShell から `Invoke-WebRequest` を使用して API を使用する方法を示します。

> [!div class="checklist"]
> * キーの取得
> * プロジェクトの作成
> * タグの作成
> * 画像の追加
> * モデルのトレーニングとテスト
> * モデルのエクスポート

## <a name="prerequisites"></a>前提条件

* Representational State Transfer (REST) の基本的な知識。 このドキュメントでは、HTTP 動詞、JSON、REST でよく使われるその他の要素などについて詳しく説明しません。

* bash (Bourne Again Shell) と [curl](https://curl.haxx.se) ユーティリティ、または Windows PowerShell 3.0 (以上)。

* Custom Vision アカウント。 詳細については、「[分類子の構築](getting-started-build-a-classifier.md)」ドキュメントを参照してください。

## <a name="get-keys"></a>キーの取得

REST API を使用するときは、キーを使用して認証する必要があります。 管理またはトレーニング操作を実行するときは、__トレーニング キー__を使用します。 モデルを使用して予測を行うときは、__予測キー__を使用します。

要求を行うとき、キーが要求ヘッダーとして送信されます。

お使いのアカウント用のキーを取得するには、[Custom Vision Web ページ](https://customvision.ai)にアクセスし、右上にある__歯車アイコン__を選択します。 __[アカウント]__ セクションで、__[Training Key]\(トレーニング キー\)__ フィールドと __[Prediction Key]\(予測キー\)__ フィールドから値をコピーします。

![キー UI の画像](./media/rest-api-tutorial/training-prediction-keys.png)

> [!IMPORTANT]
> キーはすべての要求を認証するために使用されるため、このドキュメントの例ではキー値が環境変数に含まれていることを想定しています。 このドキュメントの他のスニペットを使用する前に、次のコマンドを使用してキーを環境変数に保存してください。
>
> ```bash
> read -p "Enter the training key: " TRAININGKEY
> read -p "Enter the prediction key: " PREDICTIONKEY
> ```
>
> ```powershell
> $trainingKey = Read-Host 'Enter the training key'
> $predictionKey = Read-Host 'Enter the prediction key'
> ```

## <a name="create-a-new-project"></a>新しいプロジェクトを作成する

次の例では、`myproject` という名前の新しいプロジェクトを Custom Vision サービス インスタンスに作成します。 このサービスは既定で `General` ドメインになります。

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

要求への応答は、次の JSON ドキュメントのようになります。

```json
{
  "id":"45d1b19b-69b6-4a22-8e7e-d1ca37504686",
  "name":"myproject",
  "description":"",
  "settings":{
    "domainId":"ee85a72c-405e-4adc-bb47-ffa8ca0c9f31",
    "useNegativeSet":true,
    "classificationType":"Multilabel",
    "detectionParameters":null
  },
  "created":"2018-08-10T17:39:02.5633333",
  "lastModified":"2018-08-10T17:39:02.5633333",
  "thumbnailUri":null
}
```

> [!TIP]
> 応答内の `id` エントリは新しいプロジェクトの ID です。 これは、このドキュメントの後半にある他の例で使用されます。

この要求の詳細については、[CreateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8290) を参照してください。

### <a name="specific-domains"></a>特定のドメイン

特定のドメイン用のプロジェクトを作成するために、__ドメイン ID__ をオプションのパラメーターとして指定できます。 次の例では、利用可能なドメインの一覧を取得する方法を示します。

```bash
curl -X GET "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/domains" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'GET' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/domains" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

要求への応答は、次の JSON ドキュメントのようになります。

```json
[
    {
        "id": "ee85a74c-405e-4adc-bb47-ffa8ca0c9f31",
        "name": "General",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    {
        "id": "c151d5b5-dd07-472a-acc8-15d29dea8518",
        "name": "Food",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    {
        "id": "ca455789-012d-4b50-9fec-5bb63841c793",
        "name": "Landmarks",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    ...
]
```

この要求の詳細については、[GetDomains](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a827d) を参照してください。

次の例は、__Landmarks__ ドメインを使用する新しいプロジェクトの作成を示しています。

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject&domainId=ca455789-012d-4b50-9fec-5bb63841c793" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject&domainId=ca455789-012d-4b50-9fec-5bb63841c793" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

## <a name="create-tags"></a>タグの作成

画像にタグを付けるには、タグ ID を使用する必要があります。 次の例は、`cat` という名前の新しいタグを作成してタグ ID を取得する方法を示しています。 `{projectId}` はプロジェクトの ID に置き換えます。 `name=` パラメーターを使用してタグの名前を指定します。

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/tags?name=cat" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/tags?name=cat" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

要求への応答は、次の JSON ドキュメントのようになります。 

```json
{"id":"ed6f7ab6-5132-47ad-8649-3ec42ee62d43","name":"cat","description":null,"imageCount":0}
```

`id` の値を保存します。この値は画像にタグを付けるときに使用します。

この要求の詳細については、[CreateTag](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829d) を参照してください。

## <a name="add-images"></a>画像の追加

次の例は、URL からのファイルの追加を示しています。 `{projectId}` はプロジェクトの ID に置き換えます。 `{tagId}` は画像のタグの ID に置き換えます。

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/images/urls" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii '{"images": [{"url": "http://myimages/cat.jpg","tagIds": ["{tagId}"],"regions": [{"tagId": "{tagId}","left": 119.0,"top": 94.0,"width": 240.0,"height": 140.0}]}], "tagIds": ["{tagId}"]}'
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/images/urls" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } `
    -Body '{"images": [{"url": "http://myimages/cat.jpg","tagIds": ["{tagId}"],"regions": [{"tagId": "{tagId}","left": 119.0,"top": 94.0,"width": 240.0,"height": 140.0}]}], "tagIds": ["{tagId}"]}'
$resp.Content
```

要求への応答は、次の JSON ドキュメントのようになります。

```json
{
    "isBatchSuccessful": true,
    "images": [
        {
            "sourceUrl": "http://myimages/cat.jpg",
            "status": "OK",
            "image": {
                "id": "081adaee-a76b-4d94-a70e-e4fd0935a28f",
                "created": "2018-08-13T13:24:22.0815638",
                "width": 640,
                "height": 480,
                "imageUri": "https://linktoimage",
                "thumbnailUri": "https://linktothumbnail",
                "tags": [
                    {
                        "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
                        "tagName": null,
                        "created": "2018-08-13T13:24:22.104936"
                    }
                ],
                "regions": [
                    {
                        "regionId": "40f206a1-3f8a-4de7-a6c3-c7b4643117df",
                        "tagName": null,
                        "created": "2018-08-13T13:24:22.104936",
                        "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
                        "left": 119,
                        "top": 94,
                        "width": 240,
                        "height": 140
                    }
                ]
            }
        }
    ]
}
```

この要求の詳細については、[CreateImagesFromUrls](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8287) を参照してください。

## <a name="train-the-model"></a>モデルをトレーニングする

次の例は、モデルをトレーニングする方法を示しています。 `{projectId}` はプロジェクトの ID に置き換えます。

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/train" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/train" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } 
$resp.Content
```

要求への応答は、次の JSON ドキュメントのようになります。

```json
{
    "id": "23de09d6-42a1-413e-839e-8db6ee6d3496",
    "name": "Iteration 1",
    "isDefault": false,
    "status": "Training",
    "created": "2018-08-10T17:39:02.5766667",
    "lastModified": "2018-08-16T17:15:07.5250661",
    "projectId": "45d1b19b-69b8-4b22-8e7e-d1ca37504686",
    "exportable": false,
    "domainId": null
}
```

`id` の値を保存します。この値はモデルのテストとエクスポートに使用します。

詳細については、[TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8294) を参照してください。

## <a name="test-the-model"></a>モデルのテスト

次の例は、モデルのテストを実行する方法を示しています。 `{projectId}` はプロジェクトの ID に置き換えます。 `{iterationId}` は、モデルのトレーニングから返される ID に置き換えます。 `https://linktotestimage` はテスト画像のパスに置き換えます。

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/quicktest/url?iterationId={iterationId}" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii '{"url":"https://linktotestimage"}'
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/quicktest/url?iterationId={iterationId}" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } `
    -Body '{"url":"https://linktotestimage"}'
$resp.Content
```

要求への応答は、次の JSON ドキュメントのようになります。

```json
{
    "id": "369b010b-2a92-4f48-a918-4c1a0af91888",
    "project": "45d1b19b-69b8-4b22-8e7e-d1ca37504686",
    "iteration": "23de09d6-42a1-413e-839e-8db6ee6d3496",
    "created": "2018-08-16T17:39:20.7944508Z",
    "predictions": [
        {
            "probability": 0.8390652,
            "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
            "tagName": "cat"
        }
    ]
}
```

詳細については、[QuickTestImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a828d) を参照してください。

## <a name="export-the-model"></a>モデルのエクスポート

モデルのエクスポートは 2 段階のプロセスです。 まず、モデルの形式を指定する必要があります。次に、エクスポートしたモデルの URL を要求します。

### <a name="request-a-model-export"></a>モデル エクスポートの要求

次の例は、`coreml` モデルをエクスポートする方法を示しています。 `{projectId}` はプロジェクトの ID に置き換えます。 `{iterationId}` は、モデルのトレーニングから返される ID に置き換えます。

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export?platform=coreml" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ''
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export?platform=coreml" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" }
$resp.Content
```

要求への応答は、次の JSON ドキュメントのようになります。

```json
{
    "platform": "CoreML",
    "status": "Exporting",
    "downloadUri": null,
    "flavor": null
}
```

詳細については、[ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829b) を参照してください。

### <a name="download-the-exported-model"></a>エクスポートしたモデルのダウンロード

次の例は、エクスポートしたモデルの URL を取得する方法を示しています。 `{projectId}` はプロジェクトの ID に置き換えます。 `{iterationId}` は、モデルのトレーニングから返される ID に置き換えます。

```bash
curl -X GET "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ''
```

```powershell
$resp = Invoke-WebRequest -Method 'GET' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" }
$resp.Content
```

要求への応答は、次の JSON ドキュメントのようになります。

```json
[
    {
        "platform": "CoreML",
        "status": "Done",
        "downloadUri": "https://linktoexportedmodel",
        "flavor": null
    }
]
```

詳細については、[GetExports](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829a) を参照してください。
