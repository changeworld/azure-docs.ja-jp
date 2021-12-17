---
title: 'チュートリアル: 多変量異常検出について 1 時間で学習する'
titleSuffix: Azure Cognitive Services
description: 多変量異常検出の全体的なチュートリアル。
services: cognitive-services
author: juaduan
manager: conhua
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 06/27/2021
ms.author: juaduan
ms.openlocfilehash: bdebf242dcea8e7640d68afcb21d7ba7e2e49576
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292652"
---
# <a name="tutorial-learn-multivariate-anomaly-detection-in-one-hour"></a>チュートリアル: 多変量異常検出について 1 時間で学習する

多変量異常検出 (MVAD) を備えた Anomaly Detector は、**教師なし** の方法でメトリックのグループから異常を検出するための高度な AI ツールです。

一般に、MVAD を使用するには、次の手順を実行します。

  1. Azure 上で MVAD をサポートする Anomaly Detector リソースを作成します。
  1. データを準備します。
  1. MVAD モデルをトレーニングします。
  1. モデルの状態のクエリを実行します。
  1. トレーニング済みの MVAD モデルを使用して異常を検出します。
  1. 推論結果を取得して解釈します。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * 正しい形式でデータを準備する方法について説明します。
> * MVAD を使用してトレーニングと推論を行う方法について説明します。
> * 入力パラメーターと、推論結果の出力を解釈する方法について説明します。

## <a name="1-create-an-anomaly-detector-resource-that-supports-mvad"></a>1. MVAD をサポートする Anomaly Detector リソースを作成する

* Azure サブスクリプションがない場合は、作成します - [無料で作成する](https://azure.microsoft.com/free/cognitive-services)
* Azure サブスクリプションを入手したら、Azure portal で [Anomaly Detector リソースを作成し](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)、API キーと API エンドポイントを取得します。

> [!NOTE]
> プレビュー段階では、MVAD は限られたリージョンでのみ使用できます。 「[Anomaly Detector の新機能](../whats-new.md)」をブックマークして、常に MVAD リージョンのロールアウトに関する最新情報を確認してください。 また、GitHub で問題を報告したり、[AnomalyDetector@microsoft.com](mailto:AnomalyDetector@microsoft.com) に連絡して特定のリージョンを要求することもできます。

## <a name="2-data-preparation"></a>2. データの準備

次に、トレーニング データ (および推論データ) を準備する必要があります。

[!INCLUDE [mvad-data-schema](../includes/mvad-data-schema.md)]

### <a name="tools-for-zipping-and-uploading-data"></a>データを圧縮およびアップロードするためのツール

このセクションでは、MVAD の入力データを処理する自分のアプリケーション ロジックにコピー、編集して追加することができるサンプル コードとツールについて説明します。

#### <a name="compressing-csv-files-in-nix"></a>\*nix での CSV ファイルの圧縮

```bash
zip -j series.zip series/*.csv
```

#### <a name="compressing-csv-files-in-windows"></a>Windows での CSV ファイルの圧縮

* すべての CSV ファイルを含むフォルダー "*内に*" 移動します。
* 必要なすべての CSV ファイルを選択します。
* CSV ファイルの 1 つを右クリックし、`Send to` を選択します。
* ドロップダウン リストから `Compressed (zipped) folder` を選択します。
* 必要に応じて zip ファイルの名前を変更します。

#### <a name="python-code-zipping--uploading-data-to-azure-blob-storage"></a>Azure Blob Storage にデータを圧縮してアップロードする Python コード

Azure Blob にファイルをアップロードする方法については、[このドキュメント](../../../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)を参照してください。

または、以下のサンプル コードを参照して、圧縮とアップロードを行うこともできます。 このセクションの Python コードをコピーして .py ファイル (例: `zipAndUpload.py`) として保存し、次のようなコマンド ラインを使用して実行できます。

* `python zipAndUpload.py -s "foo\bar" -z test123.zip -c {azure blob connection string} -n container_xxx`

    このコマンドは、`foo\bar` 内のすべての CSV ファイルを `test123.zip` という名前の単一の zip ファイルに圧縮します。 `test123.zip` が BLOB 内のコンテナー `container_xxx` にアップロードされます。
* `python zipAndUpload.py -s "foo\bar" -z test123.zip -c {azure blob connection string} -n container_xxx -r` 

    このコマンドは上記と同じことを行いますが、アップロードが正常に完了すると zip ファイル `test123.zip` が削除されます。 

引数:

* `--source-folder`、`-s`、CSV ファイルを含むソース フォルダーへのパス
* `--zipfile-name`、`-z`、zip ファイルの名前
* `--connection-string`、`-c`、BLOB への接続文字列
* `--container-name`、`-n`、コンテナーの名前
* `--remove-zipfile`、`-r`、オンの場合 zip ファイルを削除

```python
import os
import argparse
import shutil
import sys

from azure.storage.blob import BlobClient
import zipfile


class ZipError(Exception):
    pass


class UploadError(Exception):
    pass


def zip_file(root, name):
    try:
        z = zipfile.ZipFile(name, "w", zipfile.ZIP_DEFLATED)
        for f in os.listdir(root):
            if f.endswith("csv"):
                z.write(os.path.join(root, f), f)
        z.close()
        print("Compress files success!")
    except Exception as ex:
        raise ZipError(repr(ex))


def upload_to_blob(file, conn_str, cont_name, blob_name):
    try:
        blob_client = BlobClient.from_connection_string(conn_str, container_name=cont_name, blob_name=blob_name)
        with open(file, "rb") as f:
            blob_client.upload_blob(f, overwrite=True)
        print("Upload Success!")
    except Exception as ex:
        raise UploadError(repr(ex))


if __name__ == "__main__":
    parser = argparse.ArgumentParser()
    parser.add_argument("--source-folder", "-s", type=str, required=True, help="path to source folder")
    parser.add_argument("--zipfile-name", "-z", type=str, required=True, help="name of the zip file")
    parser.add_argument("--connection-string", "-c", type=str, help="connection string")
    parser.add_argument("--container-name", "-n", type=str, help="container name")
    parser.add_argument("--remove-zipfile", "-r", action="store_true", help="whether delete the zip file after uploading")
    args = parser.parse_args()

    try:
        zip_file(args.source_folder, args.zipfile_name)
        upload_to_blob(args.zipfile_name, args.connection_string, args.container_name, args.zipfile_name)
    except ZipError as ex:
        print(f"Failed to compress files. {repr(ex)}")
        sys.exit(-1)
    except UploadError as ex:
        print(f"Failed to upload files. {repr(ex)}")
        sys.exit(-1)
    except Exception as ex:
        print(f"Exception encountered. {repr(ex)}")

    try:
        if args.remove_zipfile:
            os.remove(args.zipfile_name)
    except Exception as ex:
        print(f"Failed to delete the zip file. {repr(ex)}")
```

## <a name="3-train-an-mvad-model"></a>3. MVAD モデルをトレーニングする

MVAD モデルをトレーニングするサンプル要求本文と Python のサンプル コードを次に示します。

```json
// Sample Request Body
{
    "slidingWindow": 200,
    "alignPolicy": {
        "alignMode": "Outer",
        "fillNAMethod": "Linear", 
        "paddingValue": 0
    },
    // This could be your own ZIP file of training data stored on Azure Blob and a SAS url could be used here
    "source": "https://aka.ms/AnomalyDetector/MVADSampleData", 
    "startTime": "2021-01-01T00:00:00Z", 
    "endTime": "2021-01-02T12:00:00Z", 
    "displayName": "Contoso model"
}
```

```python
# Sample Code in Python
########### Python 3.x #############
import http.client, urllib.request, urllib.parse, urllib.error, base64

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '{API key}',
}

params = urllib.parse.urlencode({})

try:
    conn = http.client.HTTPSConnection('{endpoint}')
    conn.request("POST", "/anomalydetector/v1.1-preview/multivariate/models?%s" % params, "{request body}", headers)
    response = conn.getresponse()
    data = response.read()
    print(data)
    conn.close()
except Exception as e:
    print("[Errno {0}] {1}".format(e.errno, e.strerror))

####################################
```

応答コード `201` は要求の成功を示します。

[!INCLUDE [mvad-input-params](../includes/mvad-input-params.md)]

## <a name="4-get-model-status"></a>4. モデルの状態を取得する

トレーニング API は非同期です。トレーニング API を呼び出してもすぐにはモデルを取得することはできません。 ただし、すべてのモデルを一覧表示する API キー、または特定のモデルに関する情報を一覧表示するモデル ID を使用して、モデルの状態を照会できます。

### <a name="list-all-the-models"></a>すべてのモデルを一覧表示する

要求 URL と要求ヘッダーについては、[このページ](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector-v1-1-preview/operations/ListMultivariateModel)を参照してください。 更新時間順に 10 個のモデルのみが返されていることに注意してください。ただし、要求 URL で `$skip` と `$top` のパラメーターを設定することで、他のモデルにアクセスできます。 たとえば、要求 URL が `https://{endpoint}/anomalydetector/v1.1-preview/multivariate/models?$skip=10&$top=20` の場合は、最新の 10 個のモデルをスキップし、次の 20 個のモデルが返されます。

応答のサンプルは次のとおりです 

```json
{
    "models": [
         {
             "createdTime":"2020-12-01T09:43:45Z",
             "displayName":"DevOps-Test",
             "lastUpdatedTime":"2020-12-01T09:46:13Z",
             "modelId":"b4c1616c-33b9-11eb-824e-0242ac110002",
             "status":"READY",
             "variablesCount":18
         },
         {
             "createdTime":"2020-12-01T09:43:30Z",
             "displayName":"DevOps-Test",
             "lastUpdatedTime":"2020-12-01T09:45:10Z",
             "modelId":"ab9d3e30-33b9-11eb-a3f4-0242ac110002",
             "status":"READY",
             "variablesCount":18
         }
    ],
    "currentCount": 1,
    "maxCount": 50, 
    "nextLink": "<link to more models>"
}
```

応答には、`models`、`currentCount`、`maxCount`、`nextLink` の 4 つのフィールドが含まれています。

* `models` には、作成時刻、最終更新時刻、モデル ID、表示名、変数の数、各モデルの状態が含まれます。
* `currentCount` には、トレーニング済みの多変量モデルの数が含まれます。
* `maxCount` は、この Anomaly Detector リソースでサポートされるモデルの最大数です。
* `nextLink` を使用して、より多くのモデルをフェッチできます。

### <a name="get-models-by-model-id"></a>モデル ID でモデルを取得する

[このページ](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector-v1-1-preview/operations/GetMultivariateModel)は、モデル ID でモデル情報のクエリを実行する要求 URL について説明しています。 サンプルの応答は次のようになります

```json
{
        "modelId": "45aad126-aafd-11ea-b8fb-d89ef3400c5f",
        "createdTime": "2020-06-30T00:00:00Z",
        "lastUpdatedTime": "2020-06-30T00:00:00Z",
        "modelInfo": {
          "slidingWindow": 300,
          "alignPolicy": {
            "alignMode": "Outer",
            "fillNAMethod": "Linear",
            "paddingValue": 0
          },
          "source": "<TRAINING_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS>",
          "startTime": "2019-04-01T00:00:00Z",
          "endTime": "2019-04-02T00:00:00Z",
          "displayName": "Devops-MultiAD",
          "status": "READY",
          "errors": [],
          "diagnosticsInfo": {
            "modelState": {
              "epochIds": [10, 20, 30, 40, 50, 60, 70, 80, 90, 100],
              "trainLosses": [0.6291328072547913, 0.1671326905488968, 0.12354248017072678, 0.1025966405868533, 
                              0.0958492755889896, 0.09069952368736267,0.08686016499996185, 0.0860302299260931,
                              0.0828735455870684, 0.08235538005828857],
              "validationLosses": [1.9232804775238037, 1.0645641088485718, 0.6031560301780701, 0.5302737951278687, 
                                   0.4698025286197664, 0.4395163357257843, 0.4182931482799006, 0.4057914316654053, 
                                   0.4056498706340729, 0.3849248886108984],
              "latenciesInSeconds": [0.3398594856262207, 0.3659665584564209, 0.37360644340515137, 
                                     0.3513407707214355, 0.3370304107666056, 0.31876277923583984, 
                                     0.3283309936523475, 0.3503587245941162, 0.30800247192382812,
                                     0.3327946662902832]
            },
            "variableStates": [
              {
                "variable": "ad_input",
                "filledNARatio": 0,
                "effectiveCount": 1441,
                "startTime": "2019-04-01T00:00:00Z",
                "endTime": "2019-04-02T00:00:00Z",
                "errors": []
              },
              {
                "variable": "ad_ontimer_output",
                "filledNARatio": 0,
                "effectiveCount": 1441,
                "startTime": "2019-04-01T00:00:00Z",
                "endTime": "2019-04-02T00:00:00Z",
                "errors": []
              },
              // More variables
            ]
          }
        }
      }
```

クエリされたモデルに関する詳細な情報を受け取ります。 応答には、モデルに関するメタ情報、そのトレーニング パラメーター、診断情報が含まれます。 診断情報は、デバッグとトレーニングの進行状況のトレースに役立ちます。

* `epochIds` は、モデルが合計 100 エポックでトレーニングされたエポックの数を示します。 たとえば、モデルがまだトレーニング状態の場合、`epochId` が `[10, 20, 30, 40, 50]` であれば、50 回目のトレーニング エポックが完了し、残り半分を残していることを意味します。
* `trainLosses` と `validationLosses` は、最適化の進行状況が収束したかどうかを確認するために使用されます。その場合、2 つの損失は徐々に減少します。
* `latenciesInSeconds` にはエポックごとに時間コストが含まれており、10 エポックごとに記録されます。 この例では、10 回目のエポックは約 0.34 秒です。 これは、トレーニングの完了時間を見積もる場合に役立ちます。
* `variableStates` は、各変数に関する情報の概要を示します。 これは `filledNARatio` により降順で順位付けされたリストです。 各変数にいくつのデータ ポイントが使用されているかを示し、`filledNARatio` はいくつのポイントが欠けているかを示します。 通常、できる限り `filledNARatio` を小さくする必要があります。
欠落しているデータ ポイントが多すぎると、モデルの精度が低下します。
* データ処理中のエラーは `errors` フィールドに含まれます。

## <a name="5-inference-with-mvad"></a>5. MVAD を使用した推論

推論を実行するには、推論データ、開始時刻、終了時刻を含む zip ファイルに対し BLOB ソースを用意するだけです。

推論も非同期なので、結果はすぐには返されません。 後で結果を取得できる場所がわかるように、`resultId` を含む **応答ヘッダー** 内の結果のリンクを変数に保存する必要があることに注意してください。

通常、エラーはモデルの問題またはデータの問題によって発生します。 モデルの準備ができていない場合、またはデータ リンクが無効な場合は、推論を実行できません。 トレーニング データと推論データに一貫性があることを確認してください。つまり、それらは **まったく** 同じ変数であるが、タイムスタンプが異なっている必要があります。 変数の数が増加、変数の数が減少、または異なる変数セットを使用した推論では、データ検証フェーズに合格できずに、エラーが発生します。 結果のクエリを実行した場合にのみエラー メッセージが表示されるように、データ検証は遅延されます。

## <a name="6-get-inference-results"></a>6. 推論結果を取得する

結果を取得するには `resultId` が必要です。 `resultId` は、推論の要求を送信するときに応答ヘッダーから取得されます。 [このページ](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector-v1-1-preview/operations/GetDetectionResult)には、推論結果に対してクエリを実行する手順が記載されています。 

サンプルの応答は次のようになります

```json
 {
        "resultId": "663884e6-b117-11ea-b3de-0242ac130004",
        "summary": {
          "status": "READY",
          "errors": [],
          "variableStates": [
            {
              "variable": "ad_input",
              "filledNARatio": 0,
              "effectiveCount": 26,
              "startTime": "2019-04-01T00:00:00Z",
              "endTime": "2019-04-01T00:25:00Z",
              "errors": []
            },
            {
              "variable": "ad_ontimer_output",
              "filledNARatio": 0,
              "effectiveCount": 26,
              "startTime": "2019-04-01T00:00:00Z",
              "endTime": "2019-04-01T00:25:00Z",
              "errors": []
            },
            // more variables
          ],
          "setupInfo": {
            "source": "https://aka.ms/AnomalyDetector/MVADSampleData",
            "startTime": "2019-04-01T00:15:00Z",
            "endTime": "2019-04-01T00:40:00Z"
          }
        },
        "results": [
          {
            "timestamp": "2019-04-01T00:15:00Z",
            "errors": [
              {
                "code": "InsufficientHistoricalData",
                "message": "historical data is not enough."
              }
            ]
          },
          // more results
          {
            "timestamp": "2019-04-01T00:20:00Z",
            "value": {
              "contributors": [],
              "isAnomaly": false,
              "severity": 0,
              "score": 0.17805261260751692
            }
          },
          // more results
          {
            "timestamp": "2019-04-01T00:27:00Z",
            "value": {
              "contributors": [
                {
                  "contributionScore": 0.0007775013367514271,
                  "variable": "ad_ontimer_output"
                },
                {
                  "contributionScore": 0.0007989604079048129,
                  "variable": "ad_series_init"
                },
                {
                  "contributionScore": 0.0008900927229851369,
                  "variable": "ingestion"
                },
                {
                  "contributionScore": 0.008068144477478554,
                  "variable": "cpu"
                },
                {
                  "contributionScore": 0.008222036467507165,
                  "variable": "data_in_speed"
                },
                {
                  "contributionScore": 0.008674941549594993,
                  "variable": "ad_input"
                },
                {
                  "contributionScore": 0.02232242629793674,
                  "variable": "ad_output"
                },
                {
                  "contributionScore": 0.1583773213660846,
                  "variable": "flink_last_ckpt_duration"
                },
                {
                  "contributionScore": 0.9816531517495176,
                  "variable": "data_out_speed"
                }
              ],
              "isAnomaly": true,
              "severity": 0.42135109874230336,
              "score": 1.213510987423033
            }
          },
          // more results
        ]
      }
```

応答には、結果の状態、変数情報、推論パラメーター、推論結果が含まれます。

* `variableStates` は、推論の要求内にある各変数の情報のリストを示します。
* `setupInfo` は、この推論のために送信された要求本文です。
* `results` には検出結果が含まれています。 検出結果には、典型的なものが 3 種類あります。
    1. エラー コード `InsufficientHistoricalData`。 これは通常、最初のいくつかのタイムスタンプでのみ発生します。モデルによるデータの推論はウィンドウ ベースの方法であり、判断を下すためには履歴データが必要だからです。 最初のいくつかのタイムスタンプでは、履歴データが不十分なので、それらに対して推論を実行することはできません。 この場合、エラー メッセージは無視できます。
    1. `"isAnomaly": false` は、現在のタイムスタンプが異常でないことを示します。
        * `severity ` は異常の相対的な重大度を示し、通常のデータの場合は常に 0 です。
        * `score` は、モデルによる判断でのモデルからの未加工の出力で、通常のデータ ポイントでも 0 以外になる可能性があります。
    1. `"isAnomaly": true` は、現在のタイムスタンプでの異常を示します。
        * `severity ` は異常の相対的な重大度を示し、異常なデータの場合は常に 0 を超える値です。
        * `score` は、モデルによる判断でのモデルからの未加工の出力です。 `severity` は、`score` からの派生値です。 すべてのデータ ポイントには、`score` があります。
        * `contributors` は、各変数のコントリビューション スコアを含むリストです。 投稿スコアが高いほど、根本原因の可能性が高いことを示しています。 このリストは、異常を解釈し、根本原因を診断するためによく使用されます。

> [!NOTE]
> よくある落とし穴は、`isAnomaly`=`true` のデータ ポイントをすべて異常とみなすことです。 その結果、擬陽性が過多となる可能性があります。
> `isAnomaly` と `severity` (または `score`) の両方を使用して、重大ではない異常をふるいにかけ、(必要に応じて) グループ化を使用して異常の期間を確認し、ランダム ノイズを抑制する必要があります。 `severity` と `score` の違いについては、ベスト プラクティス ドキュメントの [FAQ](../concepts/best-practices-multivariate.md#faq) を参照してください。

## <a name="next-steps"></a>次のステップ

* [ベスト プラクティス: 多変量 Anomaly Detector API を使用する際に従う推奨プラクティス](../concepts/best-practices-multivariate.md)
* [クイックスタート: Anomaly Detector (多変量) クライアント ライブラリを使用する](../quickstarts/client-libraries-multivariate.md)