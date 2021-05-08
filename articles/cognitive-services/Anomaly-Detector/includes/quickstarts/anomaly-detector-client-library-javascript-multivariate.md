---
title: Anomaly Detector (多変量) JavaScript クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: 4e0f2d1bae07f0814b4f096d8be315bd92cd42fe
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107318766"
---
JavaScript 用 Anomaly Detector (多変量) クライアント ライブラリを使ってみましょう。 これらの手順に従ってパッケージをインストールすれば、サービスによって提供されるアルゴリズムが使用できるようになります。 新しい多変量異常検出 API を使用すると、機械学習の知識やラベル付けされたデータがなくても、一連のメトリックから異常を検出できる高度な AI を開発者が容易に統合することができます。 異なる信号間の依存関係や相互相関が自動的に主要な要因として考慮されます。 これにより、複雑なシステムを障害から予防的に保護することができます。

JavaScript 用 Anomaly Detector (多変量) クライアント ライブラリは、次の目的に使用します。

* 時系列のグループからシステム レベルの異常を検出する。
* 個々の時系列では得られる情報が少なく、すべての信号に着目して問題を検出する必要がある。
* システム正常性をさまざまな側面から測定する数十個から数百個にのぼる各種センサーを使用して高価な物理資産の予測メンテナンスを行う。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* 最新バージョンの [Node.js](https://nodejs.org/)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector リソースを作成"  target="_blank">Anomaly Detector リソースを作成</a>し、キーとエンドポイントを取得します。 デプロイするまで待ち、 **[リソースに移動]** ボタンをクリックします。
    * 対象のアプリケーションを Anomaly Detector API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。 

```console
mkdir myapp && cd myapp
```

`npm init` コマンドを実行し、`package.json` ファイルを使用して node アプリケーションを作成します。 

```console
npm init
```

`index.js` という名前のファイルを作成して次のライブラリをインポートします。
```javascript
'use strict'

const fs = require('fs');
const parse = require("csv-parse/lib/sync");
const { AnomalyDetectorClient } = require('@azure/ai-anomaly-detector');
const { AzureKeyCredential } = require('@azure/core-auth');
```

自分のリソースの Azure エンドポイントおよびキー用の変数を作成します。 サンプル データ ファイル用にもう 1 つの変数を作成します。

```javascript
const apiKey = "YOUR_API_KEY";
const endpoint = "YOUR_ENDPOINT";
const data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

 Anomaly Detector (多変量) API を使用するには、検出を使用する前に独自のモデルをトレーニングする必要があります。 トレーニングに使用するデータは時系列のバッチであり、各時系列は、timestamp と value の 2 つの列を含む CSV 形式である必要があります。 すべての時系列を 1 つの ZIP ファイルに圧縮し、[Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md) にアップロードする必要があります。 既定では、時系列の変数を表すためにこのファイル名が使用されます。 あるいは、変数の名前を .zip ファイル名とは異なるものにしたい場合は、追加の meta.json ファイルを ZIP ファイルに含めることもできます。 [BLOB の SAS (Shared Access Signature) URL](../../../../storage/common/storage-sas-overview.md) を生成したら、ZIP ファイルの URL をトレーニングに使用できます。

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

`ms-rest-azure` および `azure-ai-anomalydetector` NPM パッケージをインストールします。 このクイックスタートでは、csv-parse ライブラリも使用します。

```console
npm install @azure/ai-anomaly-detector @azure/ms-rest-js csv-parse
```

アプリの `package.json` ファイルが依存関係によって更新されます。

## <a name="code-examples"></a>コード例

以下のコード スニペットは、Node.js 用 Anomaly Detector クライアント ライブラリを使用して次のことを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [モデルをトレーニングする](#train-a-model)
* [異常を検出する](#detect-anomalies)
* [モデルをエクスポートする](#export-model)
* [モデルを削除する](#delete-model)

## <a name="authenticate-the-client"></a>クライアントを認証する

ご利用のエンドポイントと資格情報を使用して `AnomalyDetectorClient` オブジェクトをインスタンス化します。

```javascript
const client = new AnomalyDetectorClient(endpoint, new AzureKeyCredential(apiKey)).client;
```

## <a name="train-a-model"></a>モデルをトレーニングする

### <a name="construct-a-model-result"></a>モデルの結果を構築する

まず、モデル要求を構築する必要があります。 開始時刻と終了時刻は、必ずデータソースに合わせて調整してください。

```javascript
const Modelrequest = {
      source: data_source,
      startTime: new Date(2021,0,1,0,0,0),
      endTime: new Date(2021,0,2,12,0,0),
      slidingWindow:200
    };    
```

### <a name="train-a-new-model"></a>新しいモデルをトレーニングする

Anomaly Detector クライアントの `trainMultivariateModel` メソッドにモデル要求を渡す必要があります。

```javascript
console.log("Training a new model...")
var train_response = await client.trainMultivariateModel(Modelrequest)
var model_id = train_response.location.split("/").pop()
console.log("New model ID: " + model_id)
```

モデルのトレーニングが完了したかどうかは、モデルの状態を追跡することで確認できます。

```javascript
var model_response = await client.getMultivariateModel(model_id)
var model_status = model_response.modelInfo.status

while (model_status != 'READY'){
    await sleep(10000).then(() => {});
    var model_response = await client.getMultivariateModel(model_id)
    var model_status = model_response.modelInfo.status
}

console.log("TRAINING FINISHED.")
```

## <a name="detect-anomalies"></a>異常を検出する

データソース内に異常があるかどうかを判断するには、`detectAnomaly` 関数と `getDectectionResult` 関数を使用します。

```javascript
console.log("Start detecting...")
const detect_request = {
    source: data_source,
    startTime: new Date(2021,0,2,12,0,0),
    endTime: new Date(2021,0,3,0,0,0)
};
const result_header = await client.detectAnomaly(model_id, detect_request)
const result_id = result_header.location.split("/").pop()
var result = await client.getDetectionResult(result_id)
var result_status = result.summary.status

while (result_status != 'READY'){
    await sleep(2000).then(() => {});
    var result = await client.getDetectionResult(result_id)
    var result_status = result.summary.status
}
```

## <a name="export-model"></a>モデルをエクスポートする

トレーニング済みのモデルをエクスポートするには、`exportModel` 関数を使用します。

```javascript
const export_result = await client.exportModel(model_id)
const model_path = "model.zip"
const destination = fs.createWriteStream(model_path)
export_result.readableStreamBody.pipe(destination)
console.log("New model has been exported to "+model_path+".")
```

## <a name="delete-model"></a>モデルを削除する

現在のリソースで使用可能な既存のモデルを削除するには、`deleteMultivariateModel` 関数を使用します。

```javascript
client.deleteMultivariateModel(model_id)
console.log("New model has been deleted.")
```

## <a name="run-the-application"></a>アプリケーションの実行

クイック スタート ファイルで `node` コマンドを使用して、アプリケーションを実行します。

```console
node index.js
```

## <a name="next-steps"></a>次のステップ

* [Anomaly Detector (多変量) のベスト プラクティス](../../concepts/best-practices-multivariate.md)
