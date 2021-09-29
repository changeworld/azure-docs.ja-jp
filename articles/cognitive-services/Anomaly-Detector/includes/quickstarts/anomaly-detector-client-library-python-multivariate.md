---
title: Anomaly Detector (多変量) Python クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2021
ms.author: mbullwin
ms.openlocfilehash: 0ac9f337ed24a3e440fe877998a40181853d5657
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128910788"
---
Python 用 Anomaly Detector (多変量) クライアント ライブラリを使ってみましょう。 サービスによって提供されるアルゴリズムを使用してパッケージをインストールするには、次の手順に従います。 新しい多変量異常検出 API を使用すると、機械学習の知識やラベル付けされたデータがなくても、一連のメトリックから異常を検出できる高度な AI を開発者が容易に統合することができます。 異なる信号間の依存関係や相互相関が自動的に主要な要因として考慮されます。 これにより、複雑なシステムを障害から予防的に保護することができます。

Python 用 Anomaly Detector (多変量) クライアント ライブラリは、次の目的に使用します。

* 時系列のグループからシステム レベルの異常を検出する。
* 個々の時系列では得られる情報が少なく、すべての信号に着目して問題を検出する必要がある。
* システム正常性をさまざまな側面から測定する数十個から数百個にのぼる各種センサーを使用して高価な物理資産の予測メンテナンスを行う。

[ライブラリのリファレンス ドキュメント](/python/api/azure-ai-anomalydetector/azure.ai.anomalydetector) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/anomalydetector/azure-ai-anomalydetector) | [パッケージ (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/3.0.0b3/) | [サンプル コード](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/samples/sample_multivariate_detect.py)

## <a name="prerequisites"></a>前提条件

* [Python 3.x](https://www.python.org/)
* [Pandas データ分析ライブラリ](https://pandas.pydata.org/)
* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Anomaly Detector リソースを作成"  target="_blank">Anomaly Detector リソースを作成</a>し、キーとエンドポイントを取得します。 デプロイするまで待ち、 **[リソースに移動]** ボタンをクリックします。
    * 対象のアプリケーションを Anomaly Detector API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。


## <a name="setting-up"></a>設定

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

Python をインストールしたら、次を使用してクライアント ライブラリをインストールすることができます。

```console
pip install pandas
pip install --upgrade azure-ai-anomalydetector
```

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

 新しい Python ファイルを作成して次のライブラリをインポートします。

```python
import os
import time
from datetime import datetime

from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectionRequest, ModelInfo
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import HttpResponseError
```

環境変数としてのキー、時系列データ ファイルへのパス、およびサブスクリプションの Azure の場所の変数を作成します。 

> [!NOTE]
> 常に、2 つのキーのいずれかを使用できます。 これは、セキュリティ保護されたキーのローテーションを可能にするためです。 このクイックスタートでは、1 番目のキーを使用します。 

```python
subscription_key = "ANOMALY_DETECTOR_KEY"
anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"
```



## <a name="code-examples"></a>コード例

以下のコード スニペットは、Python 用 Anomaly Detector クライアント ライブラリを使用して次のことを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [モデルのトレーニング](#train-the-model)
* [異常を検出する](#detect-anomalies)
* [モデルをエクスポートする](#export-model)
* [モデルを削除する](#delete-model)

## <a name="authenticate-the-client"></a>クライアントを認証する

新しい Anomaly Detector クライアントをインスタンス化するには、Anomaly Detector のサブスクリプション キーおよび関連するエンドポイントを渡す必要があります。 また、データソースも設定します。  

Anomaly Detector 多変量 API シリーズを使用するには、最初に独自のモデルをトレーニングする必要があります。 トレーニング データは、次の要件を満たす複数の時系列のセットです。

各時系列は、ヘッダー行として "timestamp" と "value" (すべて小文字) の 2 つの列のみを含む CSV ファイルである必要があります。 "timestamp" の値は、ISO 8601 に準拠している必要があります。"value" は、整数または小数点以下の桁数が任意の小数にすることができます。 次に例を示します。

|timestamp | value|
|-------|-------|
|2019-04-01T00:00:00Z| 5|
|2019-04-01T00:01:00Z| 3.6|
|2019-04-01T00:02:00Z| 4|
|`...`| `...` |

各 CSV ファイルには、モデルのトレーニングに使用する異なる変数に基づいて名前を付ける必要があります。 たとえば、"temperature.csv" や "humidity.csv" などです。 すべての CSV ファイルは、サブフォルダーを使用しないで 1 つの ZIP ファイルに圧縮する必要があります。 ZIP ファイルには任意の名前を付けることができます。 ZIP ファイルは Azure Blob Storage にアップロードする必要があります。 その ZIP ファイルの BLOB SAS (Shared Access Signature) URL を生成したら、それをトレーニングに使用できます。 Azure Blob Storage から SAS URL を生成する方法については、このドキュメントを参照してください。

```python
class MultivariateSample():

def __init__(self, subscription_key, anomaly_detector_endpoint, data_source=None):
    self.sub_key = subscription_key
    self.end_point = anomaly_detector_endpoint

    # Create an Anomaly Detector client

    # <client>
    self.ad_client = AnomalyDetectorClient(AzureKeyCredential(self.sub_key), self.end_point)
    # </client>

    self.data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS"
```

## <a name="train-the-model"></a>モデルをトレーニングする

まず、モデルをトレーニングし、その間、モデルの状態をチェックしてトレーニングの完了を確認した後、最新のモデル ID を取得します。検出フェーズに進んだ際に、この ID が必要となります。

```python
def train(self, start_time, end_time):
    # Number of models available now
    model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models before training.".format(len(model_list)))
    
    # Use sample data to train the model
    print("Training new model...(it may take a few minutes)")
    data_feed = ModelInfo(start_time=start_time, end_time=end_time, source=self.data_source)
    response_header = \
    self.ad_client.train_multivariate_model(data_feed, cls=lambda *args: [args[i] for i in range(len(args))])[-1]
    trained_model_id = response_header['Location'].split("/")[-1]
    
    # Model list after training
    new_model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    
    # Wait until the model is ready. It usually takes several minutes
    model_status = None
    while model_status != ModelStatus.READY and model_status != ModelStatus.FAILED:
        model_info = self.ad_client.get_multivariate_model(trained_model_id).model_info
        model_status = model_info.status
        time.sleep(10)

    if model_status == ModelStatus.FAILED:
        print("Creating model failed.")
        print("Errors:")
        if model_info.errors:
            for error in model_info.errors:
                print("Error code: {}. Message: {}".format(error.code, error.message))
        else:
            print("None")
        return None

    if model_status == ModelStatus.READY:
        # Model list after training
        new_model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
        print("Done.\n--------------------")
        print("{:d} available models after training.".format(len(new_model_list)))

    # Return the latest model id
    return trained_model_id
```

## <a name="detect-anomalies"></a>異常を検出する

データソース内に異常があるかどうかを判断するには、`detect_anomaly` と `get_dectection_result` を使用します。 先ほどトレーニングしたモデルの ID を渡す必要があります。

```python
def detect(self, model_id, start_time, end_time):
    # Detect anomaly in the same data source (but a different interval)
    try:
        detection_req = DetectionRequest(source=self.data_source, start_time=start_time, end_time=end_time)
        response_header = self.ad_client.detect_anomaly(model_id, detection_req,
                                                        cls=lambda *args: [args[i] for i in range(len(args))])[-1]
        result_id = response_header['Location'].split("/")[-1]
    
        # Get results (may need a few seconds)
        r = self.ad_client.get_detection_result(result_id)
        while r.summary.status != DetectionStatus.READY and r.summary.status != DetectionStatus.FAILED:
            r = self.ad_client.get_detection_result(result_id)
            time.sleep(2)

        if r.summary.status == DetectionStatus.FAILED:
            print("Detection failed.")
            print("Errors:")
            if r.summary.errors:
                for error in r.summary.errors:
                    print("Error code: {}. Message: {}".format(error.code, error.message))
            else:
                print("None")
            return None
    except HttpResponseError as e:
        print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
    except Exception as e:
        raise e
    return r
```

## <a name="export-model"></a>モデルをエクスポートする

> [!NOTE]
> エクスポート コマンドは、コンテナー化された環境で Anomaly Detector 多変量モデルを実行できるようにするために使用することを目的としています。 現在、これは多変量ではサポートされていませんが、今後サポートが追加される予定です。

モデルをエクスポートしたい場合は、`export_model` を使用します。その際、エクスポートするモデルの ID を渡します。

```python
def export_model(self, model_id, model_path="model.zip"):

    # Export the model
    model_stream_generator = self.ad_client.export_model(model_id)
    with open(model_path, "wb") as f_obj:
        while True:
            try:
                f_obj.write(next(model_stream_generator))
            except StopIteration:
                break
            except Exception as e:
                raise e
```

## <a name="delete-model"></a>モデルを削除する

モデルを削除するには、`delete_multivariate_model` を使用します。その際、削除するモデルの ID を渡します。

```python
def delete_model(self, model_id):

    # Delete the mdoel
    self.ad_client.delete_multivariate_model(model_id)
    model_list_after_delete = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models after deletion.".format(len(model_list_after_delete)))
```

## <a name="run-the-application"></a>アプリケーションの実行

アプリケーションを実行する前に、新しく作成した関数を呼び出すためのコードを追加する必要があります。

```python
if __name__ == '__main__':
    subscription_key = "ANOMALY_DETECTOR_KEY"
    anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"

    # Create a new sample and client
    sample = MultivariateSample(subscription_key, anomaly_detector_endpoint, data_source=None)

    # Train a new model
    model_id = sample.train(datetime(2021, 1, 1, 0, 0, 0), datetime(2021, 1, 2, 12, 0, 0))

    # Reference
    result = sample.detect(model_id, datetime(2021, 1, 2, 12, 0, 0), datetime(2021, 1, 3, 0, 0, 0))
    print("Result ID:\t", result.result_id)
    print("Result summary:\t", result.summary)
    print("Result length:\t", len(result.results))

    # Export model
    sample.export_model(model_id, "model.zip")

    # Delete model
    sample.delete_model(model_id)

```

実行する前に、このクイックスタートの派生元である[完全なサンプル コード](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/API%20Sample/Multivariate%20API%20Demo%20Notebook.ipynb)と自分のプロジェクトを照合しておくと役立つ場合があります。

また、作業の開始を支援するための[詳細な Jupyter Notebook](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/API%20Sample/Multivariate%20API%20Demo%20Notebook.ipynb) も用意されています。

`python` コマンドとファイル名を使用してアプリケーションを実行します。


## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、そのリソース グループに関連付けられている他のリソースも削除されます。

* [ポータル](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次のステップ

* [Anomaly Detector API とは](../../overview-multivariate.md)
* [Anomaly Detector API を使用する場合のベスト プラクティス](../../concepts/best-practices-multivariate.md)
