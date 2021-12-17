---
title: Machine Learning Studio (クラシック) で IoT Hub データを使用して行う天気予報
description: ML Studio (クラシック) を使用して、IoT ハブがセンサーから収集した気温と湿度のデータに基づき、降水確率を予測します。
author: eross-msft
keywords: 機械学習を使用した天気予報
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/26/2021
ms.author: lizross
ms.openlocfilehash: 1656e055316ecde439793fe4afdd25933db476ce
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132554619"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-machine-learning-studio-classic"></a>Machine Learning Studio (クラシック) で IoT ハブからのセンサー データを使用して天気予報を行う

![エンド ツー エンド ダイアグラム](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

機械学習は、将来の動き、結果、傾向を予測するためにコンピューターで既存のデータからの学習を行う、データ サイエンスの手法の 1 つ です。 ML Studio (クラシック) は、クラウドの予測分析サービスであり、分析ソリューションとして予測モデルを迅速に作成し、デプロイできるようにします。 この記事では、ML Studio (クラシック) で Azure IoT Hub から取得した気温と湿度のデータを使用して、天気 (降水確率) を予測する方法について説明します。 降水確率は、準備された天気予報モデルから出力されます。 このモデルは、気温と湿度に基づいて降水確率を予測するための履歴データを基に構築されています。

## <a name="prerequisites"></a>前提条件

- [Raspberry Pi オンライン シミュレーター](iot-hub-raspberry-pi-web-simulator-get-started.md)のチュートリアル、またはいずれかのデバイス チュートリアルを完了してください。 たとえば、[node.js での Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md)に関するページや、[テレメトリの送信](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp)に関するいずれかのクイックスタートにアクセスできます。 これらの記事では、次の要件について取り上げています。
  - 有効な Azure サブスクリプション
  - サブスクリプションの Azure IoT Hub。
  - Azure IoT Hub にメッセージを送信するクライアント アプリケーション。
- [ML Studio (クラシック)](https://studio.azureml.net/) アカウント。
- [Azure Storage アカウント](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts)、**汎用 v2** アカウントが推奨されていますが、Azure Blob Storage をサポートするすべての Azure Storage アカウントも機能します。

> [!Note]
> この記事では、Azure Stream Analytics と他のいくつかの有料サービスを使用します。 Azure リージョン間でデータを転送する必要がある場合、Azure Stream Analytics に追加料金が発生します。 このため、リソース グループ、IoT Hub、Azure Storage アカウント、およびこのチュートリアルの後半で追加した Machine Learning Studio (classic) ワークスペースと Azure Stream Analytics ジョブがすべて同じ Azure リージョンにあることを確認することをお勧めします。 ML Studio (クラシック) とその他の Azure サービスのリージョン サポートについては、[リージョン別の Azure 製品の可用性](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-studio&regions=all)に関するページで確認できます。

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>天気予報モデルを Web サービスとしてデプロイする

このセクションでは、Azure AI ライブラリから天気予報モデルを取得します。 その後、R スクリプト モジュールをモデルに追加して、気温と湿度のデータをクリーニングします。 最後に、モデルを予測 Web サービスとしてデプロイします。

### <a name="get-the-weather-prediction-model"></a>天気予報モデルを取得する

このセクションでは、Azure AI Gallery から天気予報モデルを取得し、ML Studio (クラシック) で開きます。

1. [[weather prediction model] \(天気予報モデル) ページ](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1)に移動します。

   ![Azure AI Gallery の [Weather prediction model]\(天気予報モデル\) ページを開く](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. **[Studio (クラシック) で開く]** をクリックして、Microsoft ML Studio (クラシック) でモデルを開きます。 IoT ハブの近くのリージョンを選択し、[ **Azure product availability by region (ギャラリーからコピーする)\]** ポップアップから、適切なワークスペースを選択します。

   ![ML Studio (クラシック) で天気予報モデルを開く](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>R スクリプト モジュールを追加して、気温と湿度のデータをクリーニングする

モデルを正しく動作させるには、気温と湿度のデータが数値データに変換可能である必要があります。 このセクションでは、R スクリプト モジュールを天気予報モデルに追加して、数値に変換できない気温または湿度のデータ値のある行を削除します。

1. [ML Studio (クラシック)] ウィンドウの左側にある矢印をクリックして、ツール パネルを展開します。 検索ボックスに「実行」と入力します。 **R スクリプトの実行** モジュールを選択します。

   ![R スクリプトの実行モジュールを選択する](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. ダイアグラム上の **見つからないデータのクリーンアップ** モジュールと既存の **R スクリプトの実行** モジュールの近くにある **R スクリプトの実行** モジュールをドラッグします。 **見つからないデータのクリーンアップ** モジュールおよび **R スクリプトの実行** モジュールとの間の接続を削除してから、新しいモジュールの入力と出力を次のように接続します。

   ![R スクリプトの実行モジュールを追加する](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. 新しい **R スクリプトの実行** モジュールを選択して、その [プロパティ] ウィンドウを開きます。 次のコードをコピーして **[R スクリプト]** ボックスに貼り付けます。

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   完了すると、プロパティ ウィンドウは次のようになります。

   ![R スクリプトの実行モジュールにコードを追加する](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>予測 Web サービスをデプロイする

このセクションでは、モデルを検証し、モデルに基づいて予測 Web サービスを設定してから、Web サービスをデプロイします。

1. **[Run (実行)]** をクリックしてモデルの手順を検証します。 この手順は完了までに数分かかることがあります。

   ![手順を検証するために実験を実行する](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. **[SET UP WEB SERVICE (Web サービスの設定)]**  >  **[Predictive Web Service (予測 Web サービス)]** の順にクリックします。 予測実験のダイアグラムが開きます。

   ![ML Studio (クラシック) で天気予報モデルを展開する](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. 予測実験のダイアグラムで、**Web service input (Web サービス入力)** モジュールと上部にある **[Select Columns in Dataset (データセットの列を選択する)]** の間の接続を削除します。 次に、**Web サービスの入力** モジュールを **モデルのスコア付け** モジュールの近くにドラッグし、次のように接続します。

   ![ML Studio (クラシック) で 2 つのモジュールを接続する](media/iot-hub-weather-forecast-machine-learning/connect-modules-azure-machine-learning-studio.png)

1. **[Run (実行)\]** をクリックしてモデルの手順を検証します。

1. **[DEPLOY WEB SERVICE]\(Web サービスのデプロイ)** をクリックして、モデルを Web サービスとしてデプロイします。

1. モデルのダッシュボードで、**要求/応答** 用に **Excel 2010 以前のブック** をダウンロードします。

   > [!Note]
   > ご自分のコンピューターで新しいバージョンの Excel を実行している場合でも、**Excel 2010 以前のブック** をダウンロードしてください。

   ![要求/応答エンドポイントに Excel をダウンロードする](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. Excel ブックを開いて、**Web サービス URL** と **アクセス キー** をメモします。

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Stream Analytics ジョブの作成、構成、実行

### <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成

1. [Azure Portal](https://portal.azure.com/) で、 **[リソースの作成]** を選択します。 検索ボックスに「stream analytics job 」と入力し、結果のドロップダウンから **Stream Analytics ジョブ** を選択します。 **[Stream Analytics job (Stream Analytics ジョブ)]** ペインが開いたら、 **[作成]** をクリックします。
1. 次の情報をジョブに入力します。

   **ジョブ名**:ジョブの名前。 名前はグローバルに一意である必要があります。

   **サブスクリプション**:サブスクリプションが既定値と異なる場合は、サブスクリプションを選択します。

   **[リソース グループ]** :IoT ハブと同じリソース グループを使用します。

   **[場所]** :リソース グループと同じ場所を使用します。

   他のすべてのフィールドは既定値のままにします。

   ![Azure での Stream Analytics ジョブの作成](media/iot-hub-weather-forecast-machine-learning/create-stream-analytics-job.png)

1. **［作成］** を選択します

### <a name="add-an-input-to-the-stream-analytics-job"></a>Stream Analytics ジョブへの入力の追加

1. Stream Analytics ジョブを開きます。
1. **[ジョブ トポロジ]** で、 **[入力]** を選択します。
1. **[Input (入力)\]** ペインで、 **[Add stream input]\(ストリーム入力の追加)** を選択し、ドロップダウン リストから **[IoT Hub]\(IoT ハブ)** を選択します。 **[New Input (新しい入力)]** ペインで、 **[Select IoT Hub from your subscriptions (サブスクリプションから IoT Hub を選択 )]** を選択し、次の情報を入力します。

   **[入力のエイリアス]**: 入力の一意のエイリアス。

   **サブスクリプション**:サブスクリプションが既定値と異なる場合は、サブスクリプションを選択します。

   **IoT Hub**:サブスクリプションから IoT ハブ を選択します。

   **共有アクセス ポリシー名**: **[Service (サービス)]** を選択します。 **iothubowner (IoT ハブ オーナー** を使用することもできます)。

   **[コンシューマー グループ]**: 作成したコンシューマー グループを選択します。

   他のすべてのフィールドは既定値のままにします。

   ![Azure で Stream Analytics ジョブに入力を追加する](media/iot-hub-weather-forecast-machine-learning/add-input-stream-analytics-job.png)

1. **[保存]** を選択します。

### <a name="add-an-output-to-the-stream-analytics-job"></a>Stream Analytics ジョブへの出力の追加

1. **[ジョブ トポロジ]** で、 **[出力]** を選択します。
1. **[Output (出力)]** ペインで、 **[Add (追加)]** を選択し、ドロップダウンから **[Blob storage/Data Lake Storage]** (Blob ストレージ/Data Lake Store) を選択します。 **[New Onput (新しい出力)]** ペインで、 **[Select IoT Hub from your subscriptions (サブスクリプションからストレージを選択 )]** を選択し、次の情報を入力します。

   **出力のエイリアス**:出力の一意のエイリアス。

   **サブスクリプション**:サブスクリプションが既定値と異なる場合は、サブスクリプションを選択します。

   **[ストレージ アカウント]**: Blob Storage のストレージ アカウント。 ストレージ アカウントを作成するか、既存のストレージ アカウントを使用できます。

   **[コンテナー]**: BLOB を保存するコンテナー。 コンテナーを作成するか、既存のコンテナーを使用できます。

   **[イベントのシリアル化の形式]**: **[CSV]** を選択します。

   ![Azure で Stream Analytics ジョブに出力を追加する](media/iot-hub-weather-forecast-machine-learning/add-output-stream-analytics-job.png)

1. **[保存]** を選択します。

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>デプロイした Web サービスを呼び出すための関数を Stream Analytics ジョブに追加する

1. **[Job Topology (ジョブ トポロジ)]** で、 **[Functions (関数)]** を選択します。
1. **[Functions (関数)]** ペインで、 **[Add (追加)]** を選択し、ドロップダウンから **[Azure ML Studio]** を選択します。 (**Azure ML Service** ではなく、**Azure ML Studio** を選択していることを確認してください。) **[New Function (新しい関数)]** ペインで、 **[Provide Azure Machine Learning function settings manually (Azure Machine Learning 関数の設定を手動で指定)]** を選択し、次の情報を入力します。

   **[関数のエイリアス]**: 「`machinelearning`」を入力します。

   **[URL]**: Excel ブックからメモした Web サービス URL を入力します。

   **[キー]**: Excel ブックからメモしたアクセス キー を入力します。

   ![Azure で Stream Analytics ジョブに関数を追加する](media/iot-hub-weather-forecast-machine-learning/add-function-stream-analytics-job.png)

1. **[保存]** を選択します。

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Stream Analytics ジョブのクエリの構成

1. **[ジョブ トポロジ]** で、 **[クエリ]** を選択します。
1. 既存のコードを次のコードに置き換えます。

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[scored probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   `[YourInputAlias]` をジョブの入力エイリアスに置き換えます。

   `[YourOutputAlias]` をジョブの出力エイリアスに置き換えます。

1. **[クエリの保存]** を選択します。

> [!Note]
> **[Test query (クエリのテスト)]** を選択すると、次のメッセージが表示されます。Machine Learning 機能を使用したクエリのテストはサポートされていません。 クエリを変更して、もう一度やり直してください。 このメッセージを無視し、 **[OK]** を選択してメッセージ ボックスを閉じることができます。 次のセクションに進む前に、必ずクエリを保存してください。

### <a name="run-the-stream-analytics-job"></a>Stream Analytics ジョブの実行

Stream Analytics (ストリーム解析) ジョブで、左ペインの **Overview (概要)** を選択してください。 次に、[**Start(スタート)]**  >  **[Now (今)]**  >  **[Start** (スタート)] を選択します。 ジョブが正常に開始されると、ジョブの状態が **[停止済み]** から **[実行中]** に変わります。

![Stream Analytics ジョブの実行](media/iot-hub-weather-forecast-machine-learning/run-stream-analytics-job.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Microsoft Azure Storage Explorer を使用して、天気予報を表示する

クライアント アプリケーションを実行して、IoT Hub に対する気温と湿度のデータの収集と送信を開始します。 IoT Hub が受信するメッセージごとに、Stream Analytics ジョブは天気予報の Web サービスを呼び出して降水確率を生成します。 結果は Azure Blob Storage に保存されます。 Azure Storage Explorer を使用して、この結果を表示することができます。

1. [Microsoft Azure Storage Explorer をダウンロードしてインストールします](https://storageexplorer.com/)。
1. Azure Storage Explorer を開きます。
1. Azure アカウントにサインインします。
1. サブスクリプションを選択します。
1. Azure サブスクリプション > **[Storage Accounts (ストレージ アカウント)]** > 自分のストレージ アカウント > **[Blob Containers (BLOB コンテナー)]** > 自分のコンテナーの順に選択します。
1. .csv ファイルをダウンロードして結果を表示します。 降水確率は最後の列に記録されています。

   ![ML Studio (クラシック) で天気予報の結果を取得する](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>まとめ

ML Studio (クラシック) を正しく使用して、IoT ハブが受信した気温と湿度のデータに基づく降水確率が生成されました。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
