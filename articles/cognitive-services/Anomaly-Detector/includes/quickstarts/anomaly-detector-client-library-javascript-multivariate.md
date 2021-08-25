---
title: Anomaly Detector (多変量) JavaScript クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2021
ms.author: mbullwin
ms.openlocfilehash: 9bf2b62e59b8320135629cc9fe751d6e4ab0e437
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802766"
---
JavaScript 用 Anomaly Detector (多変量) クライアント ライブラリを使ってみましょう。 これらの手順に従ってパッケージをインストールすれば、サービスによって提供されるアルゴリズムが使用できるようになります。 新しい多変量異常検出 API を使用すると、機械学習の知識やラベル付けされたデータがなくても、一連のメトリックから異常を検出できる高度な AI を開発者が容易に統合することができます。 異なる信号間の依存関係や相互相関が自動的に主要な要因として考慮されます。 これにより、複雑なシステムを障害から予防的に保護することができます。

JavaScript 用 Anomaly Detector (多変量) クライアント ライブラリは、次の目的に使用します。

* 時系列のグループからシステム レベルの異常を検出する。
* 個々の時系列では得られる情報が少なく、すべての信号に着目して問題を検出する必要がある。
* システム正常性をさまざまな側面から測定する数十個から数百個にのぼる各種センサーを使用して高価な物理資産の予測メンテナンスを行う。

[ライブラリのリファレンス ドキュメント](/javascript/api/overview/azure/ai-anomaly-detector-readme) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/anomalydetector/ai-anomaly-detector) | [パッケージ (npm)](https://www.npmjs.com/package/@azure/ai-anomaly-detector) | [サンプル コード](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/anomalydetector/ai-anomaly-detector/samples/v3/javascript/sample_multivariate_detection.js)

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

> [!NOTE]
> 常に、2 つのキーのいずれかを使用できます。 これは、セキュリティ保護されたキーのローテーションを可能にするためです。 このクイックスタートでは、1 番目のキーを使用します。 
   

```javascript
const apiKey = "YOUR_API_KEY";
const endpoint = "YOUR_ENDPOINT";
const data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

Anomaly Detector 多変量 API シリーズを使用するには、最初に独自のモデルをトレーニングする必要があります。 トレーニング データは、次の要件を満たす複数の時系列のセットです。

各時系列は、ヘッダー行として "timestamp" と "value" (すべて小文字) の 2 つの列のみを含む CSV ファイルである必要があります。 "timestamp" の値は、ISO 8601 に準拠している必要があります。"value" は、整数または小数点以下の桁数が任意の小数にすることができます。 次に例を示します。

|timestamp | value|
|-------|-------|
|2019-04-01T00:00:00Z| 5|
|2019-04-01T00:01:00Z| 3.6|
|2019-04-01T00:02:00Z| 4|
|`...`| `...` |

各 CSV ファイルには、モデルのトレーニングに使用する異なる変数に基づいて名前を付ける必要があります。 たとえば、"temperature.csv" や "humidity.csv" などです。 すべての CSV ファイルは、サブフォルダーを使用しないで 1 つの ZIP ファイルに圧縮する必要があります。 ZIP ファイルには任意の名前を付けることができます。 ZIP ファイルは Azure Blob Storage にアップロードする必要があります。 その ZIP ファイルの BLOB SAS (Shared Access Signature) URL を生成したら、それをトレーニングに使用できます。 Azure Blob Storage から SAS URL を生成する方法については、このドキュメントを参照してください。

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

`ms-rest-azure` および `azure-ai-anomalydetector` NPM パッケージをインストールします。 このクイックスタートでは、csv-parse ライブラリも使用します。

```console
npm install @azure/ai-anomaly-detector csv-parse
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
const client = new AnomalyDetectorClient(endpoint, new AzureKeyCredential(apiKey));
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
const train_response = await client.trainMultivariateModel(Modelrequest)
const model_id = train_response.location?.split("/").pop() ?? ""
console.log("New model ID: " + model_id)
```

モデルのトレーニングが完了したかどうかは、モデルの状態を追跡することで確認できます。

```javascript
let model_response = await client.getMultivariateModel(model_id);
let model_status = model_response.modelInfo.status;

while (model_status != 'READY' && model_status != 'FAILED'){
  await sleep(10000).then(() => {});
  model_response = await client.getMultivariateModel(model_id);
  model_status = model_response.modelInfo.status;
}

if (model_status == 'FAILED') {
  console.log("Training failed.\nErrors:");
  for (let error of model_response.modelInfo?.errors ?? []) {
    console.log("Error code: " + error.code + ". Message: " + error.message);
  }
}

console.log("TRAINING FINISHED.");
```

## <a name="detect-anomalies"></a>異常を検出する

データソース内に異常があるかどうかを判断するには、`detectAnomaly` 関数と `getDectectionResult` 関数を使用します。

```javascript
console.log("Start detecting...");
const detect_request = {
  source: data_source,
  startTime: new Date(2021,0,2,12,0,0),
  endTime: new Date(2021,0,3,0,0,0)
};
const result_header = await client.detectAnomaly(model_id, detect_request);
const result_id = result_header.location?.split("/").pop() ?? "";
let result = await client.getDetectionResult(result_id);
let result_status = result.summary.status;

while (result_status != 'READY' && result_status != 'FAILED'){
  await sleep(2000).then(() => {});
  result = await client.getDetectionResult(result_id);
  result_status = result.summary.status;
}

if (result_status == 'FAILED') {
  console.log("Detection failed.\nErrors:");
  for (let error of result.summary.errors ?? []) {
    console.log("Error code: " + error.code + ". Message: " + error.message)
  }
}
console.log("Result status: " + result_status);
console.log("Result Id: " + result.resultId);
```

## <a name="export-model"></a>モデルをエクスポートする

> [!NOTE]
> エクスポート コマンドは、コンテナー化された環境で Anomaly Detector 多変量モデルを実行できるようにするために使用することを目的としています。 現在、これは多変量ではサポートされていませんが、今後サポートが追加される予定です。

トレーニング済みのモデルをエクスポートするには、`exportModel` 関数を使用します。

```javascript
const export_result = await client.exportModel(model_id)
const model_path = "model.zip"
const destination = fs.createWriteStream(model_path)
export_result.readableStreamBody?.pipe(destination)
console.log("New model has been exported to "+model_path+".")
```

## <a name="delete-model"></a>モデルを削除する

現在のリソースで使用可能な既存のモデルを削除するには、`deleteMultivariateModel` 関数を使用します。

```javascript
client.deleteMultivariateModel(model_id)
console.log("New model has been deleted.")
```

## <a name="run-the-application"></a>アプリケーションの実行

アプリケーションを実行する前に、実際のコードを[完全なサンプル コード](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/anomalydetector/ai-anomaly-detector/samples/v3/javascript/sample_multivariate_detection.js)に照らして確認することをお勧めします。

クイック スタート ファイルで `node` コマンドを使用して、アプリケーションを実行します。

```console
node index.js
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、そのリソース グループに関連付けられている他のリソースも削除されます。

* [ポータル](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次のステップ

* [Anomaly Detector API とは](../../overview-multivariate.md)
* [Anomaly Detector API を使用する場合のベスト プラクティス](../../concepts/best-practices-multivariate.md)