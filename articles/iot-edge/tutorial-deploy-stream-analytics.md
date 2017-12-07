---
title: "Azure IoT Edge で Azure Stream Analytics をデプロイする | Microsoft Docs"
description: "Azure Stream Analytics をモジュールとしてエッジ デバイスにデプロイする"
services: iot-edge
keywords: 
author: msebolt
manager: timlt
ms.author: v-masebo
ms.date: 11/28/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 5a143bbf7abb5304ac51782d517c02ec184a05a2
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/29/2017
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Azure Stream Analytics を IoT Edge モジュールとしてデプロイする - プレビュー

IoT デバイスは、大量のデータを生成できます。 クラウドにアップロードする前にこのデータを分析または処理してアップロードするデータのサイズを小さくするまたはラウンド トリップ遅延時間のアクションにつながる洞察を推測する必要がある場合があります。

IoT Edge は事前構築済みの Azure Service IoT Edge モジュールを利用してすばやくデプロイできます。[Azure Stream Analytics][azure-stream] (ASA) はこのようなモジュールの 1 つです。 そのポータルから ASA ジョブを作成し、IoT Hub ポータルに移動して IoT Edge モジュールとしてデプロイできます。  

Azure Stream Analytics には、クラウド上と IoT Edge デバイス上の両方でのデータ分析のために十分に構造化されたクエリ構文が用意されています。 IoT Edge 上の ASA について詳しくは、「[ASA ドキュメント](../stream-analytics/stream-analytics-edge.md)」を参照してください。

このチュートリアルでは、デバイス上で直接ローカル テレメトリ ストリームを処理し、アラートを生成してデバイスに対してすぐにアクションできるようにするための Azure Stream Analytics のジョブの作成、および IoT Edge デバイスへのジョブのデプロイについて説明します。  このチュートリアルには 2 つのモジュールがあります。 シミュレートされた温度センサー モジュール (tempSensor) は、20 ～ 120 度の範囲で 5 秒ごとに 1 度ずつ上昇する温度データを生成します。 Stream Analytics モジュールは、30 秒間の平均温度が 70 度に達した場合に tempSensor をリセットします。 この機能を運用環境で使用して、温度が危険なレベルに達したときにマシンを停止させたり予防策を講じたりできます。 

学習内容は次のとおりです。

> [!div class="checklist"]
> * ASA ジョブを作成して Edge でデータを処理する
> * 新しい ASA ジョブを他の IoT Edge モジュールと接続する
> * ASA ジョブを IoT Edge デバイスにデプロイする

## <a name="prerequisites"></a>前提条件

* IoT Hub 
* [Windows][lnk-tutorial1-win] および [Linux][lnk-tutorial1-lin] のシミュレートされたデバイス上でクイックスタートまたは Deploy Azure IoT Edge で作成および構成されたデバイス。 デバイスの接続キーとデバイス ID を把握しておく必要があります。 
* IoT Edge デバイス上で実行される Docker
    * [Windows に Docker をインストールする][lnk-docker-windows]
    * [Linux に Docker をインストールする][lnk-docker-linux]
* IoT Edge デバイス上の Python 2.7.x
    * [Python 2.7 を Windows にインストールします][lnk-python]。
    * Ubuntu を含め、ほとんどの Linux ディストリビューションには既に Python 2.7 がインストールされています。  次のコマンドを使用して、pip がインストールされていることを確認します `sudo apt-get install python-pip`。


## <a name="create-an-asa-job"></a>ASA ジョブを作成する

このセッションでは、Azure Stream Analytics ジョブを作成し、IoT ハブからデータを取得し、デバイスから送信されたテレメトリ データをクエリし、その結果を Azure Storage Container (BLOB) に転送します。 詳細については、「[Stream Analytics のドキュメント][azure-stream]」の「**概要**」セクションをご覧ください。 

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

ASA ジョブの出力として使用するエンドポイントを提供するには Azure Storage アカウントが必要です。 次の例では BLOB ストレージ タイプを使用します。  詳細については、「[Azure Storage のドキュメント][azure-storage]」の「**Blob**」セクションを参照してください。

1. Azure Portal で、**[リソースの作成]** に移動し、検索バーに「`Storage account`」と入力します。 **[ストレージ アカウント - Blob、File、Table、Queue]** を選択します。

2. 自分のストレージ アカウントの名前を入力し、IoT ハブの保存場所と同じ場所を選択します。 **Create** をクリックしてください。 後で使用できるように、名前を忘れないでください。

    ![新しいストレージ アカウント][1]

3. 先ほど作成したストレージ アカウントに移動します。 **[BLOB の参照]** をクリックします。 
4. ASA モジュールがデータを保存するための新しいコンテナーを作成します。 アクセス レベルは**コンテナー**に設定します。 **[OK]**をクリックします。

    ![ストレージの設定][10]

### <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成

1. Azure Portal で、**[リソースの作成]** > **[モノのインターネット]** に移動し、**[Stream Analytics ジョブ]** を選択します。

2. 名前を入力し、ホスティング環境として **[Edge]** を選んで、残りは既定値を使います。  **Create** をクリックしてください。

    >[!NOTE]
    >現在、IoT Edge 上の ASA ジョブは、米国西部 2 リージョンではサポートされていません。 

3. 作成したジョブに移動します。 **[入力]** を選択してから **[追加]** をクリックします。

4. 入力のエイリアスとして「`temperature`」と入力し、ソースの種類を **[データ ストリーム]** に設定します。他のパラメーターには既定値を使用します。 **Create** をクリックしてください。

   ![ASA の入力](./media/tutorial-deploy-stream-analytics/asa_input.png)

5. **[出力]** を選択してから **[追加]** をクリックします。

6. 出力のエイリアスとして「`alert`」と入力し、他のパラメーターには既定値を使用します。 **Create** をクリックしてください。

   ![ASA の出力](./media/tutorial-deploy-stream-analytics/asa_output.png)


7. **[クエリ]** を選択します。
8. 既定のテキストを次のクエリで置き換えます。

    ```sql
    SELECT  
        'reset' AS command 
    INTO 
       alert 
    FROM 
       temperature TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second,30) 
    HAVING Avg(machine.temperature) > 70
    ```
9. **[Save]** をクリックします。

## <a name="deploy-the-job"></a>ジョブのデプロイ

IoT Edge デバイスに ASA ジョブをデプロイする準備が整いました。

1. Azure Portal の IoT ハブで、**IoT Edge (プレビュー)** に移動し、自分の IoT Edge デバイスの詳細ページを開きます。
1. **[Set modules]\(モジュールの設定\)** を選びます。
1. 以前、このデバイスに tempSensor モジュールを展開したことがある場合は、自動入力されている可能性があります。 展開していない場合は、次の手順を使用してモジュールを追加します。
   1. **[Add IoT Edge Module]\(IoT Edge モジュールの追加\)** をクリックします。
   1. 名前として「`tempSensor`」、イメージの URI として「`microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`」と入力します。 
   1. 他の設定はそのままにして、**[保存]** をクリックします。
1. ASA Edge ジョブを追加するには、**[Import Azure Stream Analytics IoT Edge Module]\(Azure Stream Analytics IoT Edge モジュールのインポート\)** を選択します。
1. 自分のサブスクリプションと、作成した ASA Edge ジョブを選択します。 
1. 自分のサブスクリプションと、作成したストレージ アカウントを選択します。 **[Save]** をクリックします。

    ![モジュールの設定][6]

1. 自動的に生成された、ASA モジュールの名前をコピーします。 

    ![温度モジュール][11]

1. **[次へ]** をクリックしてルートを構成します。
1. 以下を**ルート**にコピーします。  _{moduleName}_ を、コピーしたモジュール名で置き換えます。

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

1. デバイスの詳細ページに戻り、**[更新]** をクリックします。  新しい Stream Analytics モジュールが、**IoT Edge エージェント** モジュールおよび **IoT Edge ハブ**と共に実行されていることがわかります。

    ![モジュールの出力][7]

## <a name="view-data"></a>データの表示

ここで、IoT Edge デバイスに移動し、ASA モジュールと tempSensor モジュールの間のやり取りを確認できます。

すべてのモジュールが Docker で実行されていることを確認します。

   ```cmd/sh
   docker ps  
   ```

   ![docker の出力][8]

すべてのシステム ログとメトリック データを表示します。 Stream Analytics モジュールの名前を使用してください。

   ```cmd/sh
   docker logs -f {moduleName}  
   ```

30 秒間の温度が 70 度に達するまで、マシンの温度が徐々に上昇するのを観察できます。 次に、Stream Analytics モジュールによってリセットがトリガーされ、マシンの温度が 21 度まで下降します。 

   ![docker のログ][9]


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Storage コンテナーおよび Streaming Analytics ジョブを構成して IoT Edge デバイスのデータを分析しました。  その後、カスタム ASA モジュールを読み込み、ストリームを介して、データをデバイスからダウンロード用の BLOB に移動しました。  他のチュートリアルに進み、Azure IoT Edge がビジネス ソリューションを作成する方法をさらに確認できます。

> [!div class="nextstepaction"] 
> [モジュールとして Azure Machine Learning モデルをデプロイする][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
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