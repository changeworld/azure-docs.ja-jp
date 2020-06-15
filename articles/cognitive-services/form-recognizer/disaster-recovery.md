---
title: Azure Form Recognizer のディザスター リカバリーに関するガイダンス
titleSuffix: Azure Cognitive Services
description: モデルのコピー API を使用して、ご使用の Form Recognizer リソースをバックアップする方法について説明します。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: 2e5b32421a04e09bd32d2bba21ff4faf920d84dd
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/30/2020
ms.locfileid: "84221834"
---
# <a name="back-up-and-recover-your-form-recognizer-models"></a>ご使用の Form Recognizer モデルのバックアップと復旧

Azure portal 内で Form Recognizer リソースを作成するときに、リージョンを指定します。 それ以降、ご使用のリソースとそのすべての操作はその特定の Azure サーバー リージョンに関連付けられたままになります。 リージョン全体に影響が及ぶネットワークの問題が発生することはまれですが、まったくないわけではありません。 ご自分のソリューションを常に使用できるようにする必要がある場合は、別のリージョンにフェールオーバーするか、複数のリージョン間でワークロードを分割するように設計する必要があります。 どちらの方法でも、リージョンごとに少なくとも 2 つの Form Recognizer リソースと、リージョン間で[カスタム モデル](./quickstarts/curl-train-extract.md)を同期する機能が必要です。

コピー API を使用して、1 つの Form Recognizer アカウントから、またはサポートされている任意の地理的リージョン内に存在できる別のアカウントに、カスタム モデルをコピーできるようにすることで、このシナリオを実現できます。 このガイドでは、cURL と共にコピー REST API を使用する方法について説明します。 Postman のような HTTP 要求サービスを使用して、要求を発行することもできます。

## <a name="business-scenarios"></a>ビジネス シナリオ

ご自分のアプリまたはビジネスが Form Recognizer カスタム モデルの使用に依存している場合は、別のリージョン内の別の Form Recognizer アカウントにご使用のモデルをコピーすることをお勧めします。 地域的な障害が発生した場合は、コピー先のリージョン内のご自分のモデルにアクセスできます。

##  <a name="prerequisites"></a>前提条件

1. 異なる Azure リージョン内にある 2 つの Form Recognizer Azure リソース。 それらがない場合は、Azure portal に移動し、<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="新しい Form Recognizer リソースを作成します" target="_blank">新しい Form Recognizer リソースを作成します <span class="docon docon-navigate-external x-hidden-focus"></span></a>。
1. ご使用の Form Recognizer リソースのサブスクリプション キー、エンドポイント URL、およびサブスクリプション ID。 これらの値は Azure portal 上のリソースの **[概要]** タブにあります。


## <a name="copy-api-overview"></a>コピー API の概要

カスタム モデルをコピーするプロセスは、次の手順で構成されています。

1. まず、ターゲット リソース (コピーされたモデルを受け取るリソース) に対してコピー承認要求を発行します。 新しく作成されたターゲット モデルの URL が返されます。この URL により、コピーされたデータを取得します。
1. 次に、ソース リソース (コピーするモデルが含まれているリソース) にコピー要求を送信します。 クエリを実行して操作の進行状況を追跡できる URL が返されます。
1. 操作が成功するまで、ご自分のソース リソースの資格情報を使用して、進行状況の URL に対してクエリを実行します。 また、ターゲット リソース内の新しいモデル ID に対してクエリを実行して、新しいモデルの状態を取得することもできます。

## <a name="generate-copy-authorization-request"></a>コピー承認要求を生成する

次の HTTP 要求では、ターゲット リソースからコピー承認を取得します。 ご自分のターゲット リソースのエンドポイントとキーをヘッダーとして入力する必要があります。

```
POST https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/copyAuthorization HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

本文に `modelId` 値を含む `201\Created` 応答が返されます。 この文字列は、新しく作成された (空の) モデルの ID です。 `accessToken` は API がこのリソースにデータをコピーするために必要です。`expirationDateTimeTicks` の値はトークンの有効期限です。 これら 3 つの値すべてを安全な場所に保存しておきます。

```
HTTP/1.1 201 Created
Location: https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d
{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","accessToken":"1855fe23-5ffc-427b-aab2-e5196641502f","expirationDateTimeTicks":637233481531659440}
```

## <a name="start-copy-operation"></a>コピー操作を開始する

次の HTTP 要求では、ソース リソースのコピー操作を開始します。 ご自分のソース リソースのエンドポイントとキーをヘッダーとして入力する必要があります。 コピーするソース モデルのモデル ID が要求 URL に含まれていることに注目してください。

```
POST https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copy HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

実際の要求の本文は、次の形式である必要があります。 ご自分のターゲット リソースのリソース ID とリージョン名を入力する必要があります。 また、前の手順でコピーしたモデル ID、アクセス トークン、有効期限の値も必要です。

```json
{
   "targetResourceId": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}",  
   "targetResourceRegion": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}",
   "copyAuthorization": {"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","accessToken":"1855fe23-5ffc-427b-aab2-e5196641502f","expirationDateTimeTicks":637233481531659440}
}
```

Operation-Location ヘッダーを含む `202\Accepted` 応答が返されます。 この値は、操作の進行状況を追跡するために使用する URL です。 次の手順のために、これを一時的な場所に保存します。

```
HTTP/1.1 202 Accepted
Operation-Location: https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1
```

## <a name="track-copy-progress"></a>コピーの進行状況を追跡する

ソース リソース エンドポイントに対して **Get Copy Model Result** API のクエリを実行して、進行状況を追跡します。

```
GET https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1 HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

実際の応答は、操作の状態によって異なります。 JSON 本文内の `"status"` フィールドを探します。 スクリプト内でこの API 呼び出しを自動化する場合は、1 秒ごとに操作に対してクエリを実行することをお勧めします。

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"status":"succeeded","createdDateTime":"2020-04-23T18:18:01.0275043Z","lastUpdatedDateTime":"2020-04-23T18:18:01.0275048Z","copyResult":{}}
```

### <a name="optional-track-the-target-model-id"></a>[省略可能] ターゲット モデル ID を追跡する 

また、**Get Custom Model** API を使用して、ターゲット モデルに対してクエリを実行することによって、操作の状態を追跡することもできます。 最初の手順でコピーしたターゲット モデル ID を使用して、この API を呼び出します。

```
GET https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

応答本文には、モデルの詳細に関する情報が表示されます。 `"status"` フィールドでモデルの状態を確認します。

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"modelInfo":{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","status":"ready","createdDateTime":"2020-02-26T16:59:28Z","lastUpdatedDateTime":"2020-02-26T16:59:34Z"},"trainResult":{"trainingDocuments":[{"documentName":"0.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"1.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"2.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"3.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"4.pdf","pages":1,"errors":[],"status":"succeeded"}],"errors":[]}}
```

## <a name="curl-sample-code"></a>cURL サンプル コード

次のコード スニペットでは、cURL を使用して、上記の手順で説明した API 呼び出しを行います。 この場合も、ご自分のリソースに固有のモデル ID とサブスクリプション情報を入力する必要があります。

### <a name="generate-copy-authorization-request"></a>コピー承認要求を生成する

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/copyAuthorization" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" 
```

### <a name="start-copy-operation"></a>コピー操作を開始する

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/copyAuthorization" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" --data-ascii "{ \"targetResourceId\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}\",   \"targetResourceRegion\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}\", \"copyAuthorization\": "{\"modelId\":\"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d\",\"accessToken\":\"1855fe23-5ffc-427b-aab2-e5196641502f\",\"expirationDateTimeTicks\":637233481531659440}"}"
```

### <a name="track-copy-progress"></a>コピーの進行状況を追跡する

```bash
curl -i GET "https://<SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT>/formrecognizer/v2.0-preview/custom/models/{SOURCE_MODELID}/copyResults/{RESULT_ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}"
```

## <a name="next-steps"></a>次のステップ

このガイドでは、コピー API を使用して、ご使用のカスタム モデルをセカンダリ Form Recognizer リソースにバックアップする方法について説明しました。 次に、API リファレンス ドキュメントを参照して、Form Recognizer を使用して他にできることを確認します。
* [REST API リファレンス ドキュメント](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)