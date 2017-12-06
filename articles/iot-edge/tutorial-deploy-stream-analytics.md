---
title: "Azure IoT Edge で Azure Stream Analytics をデプロイする | Microsoft Docs"
description: "Azure Stream Analytics をモジュールとしてエッジ デバイスにデプロイする"
services: iot-edge
keywords: 
author: msebolt
manager: timlt
ms.author: v-masebo
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0d19d1142cf15221f84692f7e613edd6b46b4083
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2017
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Azure Stream Analytics を IoT Edge モジュールとしてデプロイする - プレビュー

IoT デバイスは、大量のデータを生成できます。 クラウドにアップロードする前にこのデータを分析または処理してアップロードするデータのサイズを小さくするまたはラウンド トリップ遅延時間のアクションにつながる洞察を推測する必要がある場合があります。

[Azure Stream Analytics][azure-stream] (ASA) には、クラウド上と IoT Edge デバイス上の両方でのデータ分析のために十分に構造化されたクエリ構文が用意されています。 IoT Edge 上の ASA について詳しくは、「[ASA ドキュメント](../stream-analytics/stream-analytics-edge.md)」を参照してください。

このチュートリアルでは、デバイス上で直接ローカル テレメトリ ストリームを処理し、アラートを生成してデバイスに対してすぐにアクションできるようにするための Azure Stream Analytics のジョブの作成、および IoT Edge デバイスへのジョブのデプロイについて説明します。  このチュートリアルには 2 つのモジュールがあります。 20 ～ 120 度の温度データ (5 秒ごとに 1 度上昇) を生成するシミュレートされた温度センサー モジュール (tempSensor) と、100 度よりも高い温度を除外する ASA モジュールです。 ASA モジュールは、30 秒間の平均値が 100 に達した場合も tempSensor をリセットします。

学習内容は次のとおりです。

> [!div class="checklist"]
> * ASA ジョブを作成して Edge でデータを処理する
> * 新しい ASA ジョブを他の IoT Edge モジュールと接続する
> * ASA ジョブを IoT Edge デバイスにデプロイする

## <a name="prerequisites"></a>前提条件

* IoT Hub 
* [Windows][lnk-tutorial1-win] および [Linux][lnk-tutorial1-lin] のシミュレートされたデバイス上でクイックスタートまたは Deploy Azure IoT Edge で作成および構成されたデバイス。
* IoT Edge デバイス上の Docker
    * [Docker を Windows にインストールし][lnk-docker-windows]、稼働していることを確認します。
    * [Docker を Linux にインストールし][lnk-docker-linux]、稼働していることを確認します。
* IoT Edge デバイス上の Python 2.7.x
    * [Python 2.7 を Windows にインストールします][lnk-python]。
    * Ubuntu を含め、ほとんどの Linux ディストリビューションには既に Python 2.7 がインストールされています。  次のコマンドを使用して、pip がインストールされていることを確認します `sudo apt-get install python-pip`。

> [!NOTE]
> このチュートリアルでは、デバイスの接続文字列と IoT Edge デバイス ID が必要になります。

IoT Edge は構築済みの Azure Service IoT Edge モジュールを利用して素早くデプロイできます。Azure Stream Analytics (ASA) はこのようなモジュールの 1 つです。 そのポータルから ASA ジョブを作成し、IoT Hub ポータルに移動して IoT Edge モジュールとしてデプロイできます。  

Azure Stream Analytics について詳しくは、「[Stream Analytics のドキュメント][azure-stream]」の「**概要**」セクションをご覧ください。

## <a name="create-an-asa-job"></a>ASA ジョブを作成する

このセッションでは、Azure Stream Analytics ジョブを作成し、IoT ハブからデータを取得し、デバイスから送信されたテレメトリ データをクエリし、その結果を Azure Storage Container (BLOB) に転送します。 詳細については、「[Stream Analytics のドキュメント][azure-stream]」の「**概要**」セクションをご覧ください。 

> [!NOTE]
> ASA ジョブの出力として使用するエンドポイントを提供するには Azure Storage アカウントが必要です。 次の例では BLOB ストレージ タイプを使用します。  詳細については、「[Azure Storage のドキュメント][azure-storage]」の「**Blob**」セクションを参照してください。

1. Azure Portal で、**[Create a resource (リソースの作成)] -> [ストレージ]** に移動し、**[すべて表示]** をクリックし、**[Storage account - blob, file, table, queue (ストレージ アカウント - Blob、ファイル、テーブル、キュー)]** をクリックします。

2. ストレージ アカウントの名前を入力し、IoT Hub の保存場所を選択します。 **Create** をクリックしてください。 後で使用するために名前をメモします。

    ![新しいストレージ アカウント][1]

3. Azure Portal で、作成したストレージ アカウントに移動します。 **[Blob Service]** で **[BLOB の参照]** をクリックします。 
4. ASA モジュールがデータを保存するための新しいコンテナーを作成します。 アクセス レベルは_コンテナー_に設定します。 **[OK]**をクリックします。

    ![ストレージの設定][10]

5. Azure Portal で、**[リソースの作成]** > **[モノのインターネット]** に移動し、**[Stream Analytics ジョブ]** を選択します。

2. 名前を入力し、ホスティング環境として **[Edge]** を選んで、残りは既定値を使います。  **Create** をクリックしてください。

    >[!NOTE]
    >現在、IoT Edge 上の ASA ジョブは、米国西部 2 リージョンではサポートされていません。 別の場所を選んでください。

    ![ASA の作成][5]

2. 作成したジョブに移動し、**ジョブ トポロジ**で **[入力]** を選択し、**[追加]** をクリックします。

3. 名前を入力し`temperature`、ソースの種類として **[データ ストリーム]** を選択し、その他のパラメーターには既定値を使用します。 **Create** をクリックしてください。

    ![ASA の入力][2]

    > [!NOTE]
    > 追加の入力には、IoT Edge 固有のエンドポイントを含めることができます。

4. **[ジョブ トポロジ]** で**[出力]** を選択し、**[追加]** をクリックします。

5. 名前 `alert` を入力し、既定値を使用します。 **Create** をクリックしてください。

    ![ASA の出力][3]

6. **[ジョブ トポロジ]** で、**[クエリ]** を選択し、以下を入力します。

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 100
    ```

## <a name="deploy-the-job"></a>ジョブのデプロイ

IoT Edge デバイスに ASA ジョブをデプロイする準備が整いました。

1. Azure Portal の IoT Hub で、**IoT Edge (プレビュー)** に移動し、*{deviceId}* のブレードを開きます。

1. **[Set modules]\(モジュールを設定\)**、**[Import Azure Service IoT Edge Module]\(Azure Service IoT Edge モジュールのインポート\)** の順に選択します。

1. サブスクリプションと、作成した ASA Edge ジョブを選択します。 次に、ストレージ アカウントを選択します。 **[Save]** をクリックします。

    ![モジュールの設定][6]

1. **[Add IoT Edge Module (IoT Edge モジュールの追加)]** をクリックして温度センサー モジュールを追加します。 名前として _tempSensor_、イメージの URL として `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview` を入力します。 他の設定はそのままにして、**[保存]** をクリックします。

    ![温度モジュール][11]

1. ASA モジュールの名前をコピーします。 **[次へ]** をクリックしてルートを構成します。

1. 以下を**ルート**にコピーします。  _{moduleName}_ をコピーしたモジュール名に置き換えます。

    ```json
    {
        "routes": {                                                               
          "telemetryToCloud": "FROM /messages/modules/tempSensor/* INTO $upstream", 
          "alertsToCloud": "FROM /messages/modules/{moduleName}/* INTO $upstream", 
          "alertsToReset": "FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")", 
          "telemetryToAsa": "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/{moduleName}/inputs/temperature\")" 
        }
    }
    ```

1. **[次へ]** をクリックします。

1. **[Review Template] (テンプレートのレビュー)** の手順で、**[送信]** をクリックします。

1. デバイスの詳細ページに戻り、**[更新]** をクリックします。  新しい _{moduleName}_ モジュールが **IoT Edge エージェント** モジュールおよび **IoT Edge ハブ**と連動していることがわかります。

    ![モジュールの出力][7]

## <a name="view-data"></a>データの表示

ここで、IoT Edge デバイスに移動し、ASA モジュールと tempSensor モジュールの間のやり取りを確認できます。

1. コマンド プロンプトで、IoT Edge デバイス接続文字列を使用してランタイムを構成します。

    ```cmd/sh
    iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords  
    ```

1. 次のコマンドを実行してランタイムを起動します。

    ```cmd/sh
    iotedgectl start  
    ```

1. 次のコマンドを実行してモジュールが動作していることを確認します。

    ```cmd/sh
    docker ps  
    ```

    ![docker の出力][8]

1. コマンドを実行してすべてのシステム ログとメトリック データを表示します。 上記のモジュール名を使用します。

    ```cmd/sh
    docker logs -f {moduleName}  
    ```

    ![docker のログ][9]

1. Azure Portal のストレージ アカウントの**[Blob Service]** で、**[Browse blobs (Blob の参照)]** をクリックし、コンテナーを選択し、新しく作成した JSON ファイルを選択します。

1. **[ダウンロード]** をクリックして結果を表示します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Storage コンテナーおよび Streaming Analytics ジョブを構成して IoT Edge デバイスのデータを分析しました。  その後、カスタム ASA モジュールを読み込み、ストリームを介して、データをデバイスからダウンロード用の BLOB に移動しました。  他のチュートリアルに進み、Azure IoT Edge がビジネス ソリューションを作成する方法をさらに確認できます。

> [!div class="nextstepaction"] 
> [モジュールとして Azure Machine Learning モデルをデプロイする][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[2]: ./media/tutorial-deploy-stream-analytics/asa_input.png
[3]: ./media/tutorial-deploy-stream-analytics/asa_output.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output.png
[8]: ./media/tutorial-deploy-stream-analytics/docker_output.png
[9]: ./media/tutorial-deploy-stream-analytics/docker_log.png
[10]: ./media/tutorial-deploy-stream-analytics/storage_settings.png
[11]: ./media/tutorial-deploy-stream-analytics/temp_module.png


<!-- Links -->
[lnk-what-is-iot-edge]: what-is-iot-edge.md
[lnk-module-dev]: module-development.md
[iot-hub-get-started-create-hub]: ../../includes/iot-hub-get-started-create-hub.md
[azure-iot]: https://docs.microsoft.com/en-us/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/en-us/azure/storage/
[azure-stream]: https://docs.microsoft.com/en-us/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

[lnk-docker-windows]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-linux]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
[lnk-python]: https://www.python.org/downloads/