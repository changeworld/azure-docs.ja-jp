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
ms.openlocfilehash: 5c9231f400b00f455f30692c58f007bd63d3cc76
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2017
---
# <a name="deploy-azure-stream-analytics-as-an-iot-edge-module---preview"></a>Azure Stream Analytics を IoT Edge モジュールとしてデプロイする - プレビュー

IoT デバイスは、大量のデータを生成できます。 アップロードされるデータの量を減らしたり、アクションにつながる洞察のラウンドトリップ待機時間をなくしたりするため、クラウドに達する前のデータの分析や処理が必要になる場合があります。

Azure IoT Edge は事前構築済みの Azure Service IoT Edge モジュールを利用してすばやくデプロイできます。 [Azure Stream Analytics][azure-stream] はこのようなモジュールの 1 つです。 Azure Stream Analytics ジョブをそのポータルから作成した後、Azure IoT Hub ポータルに移動して IoT Edge モジュールとしてデプロイできます。 

Azure Stream Analytics には、クラウド上と IoT Edge デバイス上の両方でのデータ分析のために十分に構造化されたクエリ構文が用意されています。 IoT Edge での Azure Stream Analytics について詳しくは、[Azure Stream Analytics のドキュメント](../stream-analytics/stream-analytics-edge.md)をご覧ください。

このチュートリアルでは、Azure Stream Analytics ジョブを作成して、IoT Edge デバイスにデプロイする手順について説明します。 それにより、デバイス上で直接ローカル テレメトリ ストリームを処理し、デバイスでの即時アクションを促進するアラートを生成することができます。 

このチュートリアルでは 2 つのモジュールを示します。 
* シミュレートされた温度センサー モジュール (tempSensor) は、20 度から 120 度の範囲で 5 秒ごとに 1 度ずつ上昇する温度データを生成します。 
* Stream Analytics モジュールは、30 秒間の平均温度が 70 度に達した場合に tempSensor をリセットします。 この機能を運用環境で使って、温度が危険なレベルに達したときにマシンを停止させたり予防策を講じたりできます。 

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * エッジでデータを処理するための Azure Stream Analytics ジョブを作成します。
> * 新しい Azure Stream Analytics ジョブを他の IoT Edge モジュールと接続します。
> * Azure Stream Analytics ジョブを IoT Edge デバイスにデプロイします。

## <a name="prerequisites"></a>前提条件

* IoT Hub。 
* [Windows][lnk-tutorial1-win] または [Linux][lnk-tutorial1-lin] のシミュレートされたデバイスへの Azure IoT Edge のデプロイに関するクイックスタートまたは記事で作成および構成したデバイス。 デバイスの接続キーとデバイス ID を把握しておく必要があります。 
* IoT Edge デバイス上で実行される Docker。
    * [Windows に Docker をインストールする][lnk-docker-windows]。
    * [Linux に Docker をインストールする][lnk-docker-linux]。
* IoT Edge デバイス上の Python 2.7.x。
    * [Python 2.7 を Windows にインストールします][lnk-python]。
    * Ubuntu を含め、ほとんどの Linux ディストリビューションには既に Python 2.7 がインストールされています。 pip がインストールされていることを確認するには、次のコマンドを使います。`sudo apt-get install python-pip`

## <a name="create-an-azure-stream-analytics-job"></a>Azure Stream Analytics ジョブの作成

このセッションでは、Azure Stream Analytics ジョブを作成して IoT ハブからデータを取得し、デバイスから送信されたテレメトリ データに対してクエリを実行した後、その結果を Azure Blob Storage コンテナーに転送します。 詳しくは、「[Stream Analytics のドキュメント][azure-stream]」の「概要」セクションをご覧ください。 

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

Azure Stream Analytics ジョブの出力として使うエンドポイントを提供するには、Azure Storage アカウントが必要です。 このセクションの例では、BLOB ストレージ タイプを使います。 詳しくは、「[Azure Storage のドキュメント][azure-storage]」の「BLOB」セクションをご覧ください。

1. Azure Portal で **[リソースの作成]** に移動し、[検索] ボックスに「**ストレージ アカウント**」と入力して、**[ストレージ アカウント - Blob、File、Table、Queue]** を選びます。

2. **[ストレージ アカウントの作成]** ウィンドウで、ストレージ アカウントの名前を入力し、IoT Hub が格納されるのと同じ場所を選んで、**[作成]** を選びます。 後で使うので名前をメモしておきます。

    ![ストレージ アカウントの作成][1]

3. 作成したストレージ アカウントに移動し、**[BLOB の参照]** を選びます。 

4. Azure Stream Analytics モジュールがデータを保存するための新しいコンテナーを作成し、アクセス レベルを **[コンテナー]** に設定して、**[OK]** を選びます。

    ![ストレージの設定][10]

### <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成

1. Azure Portal で、**[リソースの作成]** > **[モノのインターネット]** に移動し、**[Stream Analytics ジョブ]** を選びます。

2. **[新しい Stream Analytics ジョブ]** ウィンドウで、次のようにします。

    a. **[ジョブ名]** ボックスに、ジョブの名前を入力します。
    
    b. **[ホスティング環境]** で **[Edge]** を選びます。
    
    c. 残りのフィールドについては既定値を使います。

    > [!NOTE]
    > 現在、IoT Edge 上の Azure Stream Analytics ジョブは、米国西部 2 リージョンではサポートされていません。 

3. **[作成]**を選択します。

4. 作成したジョブで、**[ジョブ トポロジ]** の **[入力]** を選び、**[追加]** を選びます。

5. **[新しい入力]** ウィンドウで、次のようにします。

    a. **[入力のエイリアス]** ボックスに、「**temperature**」と入力します。
    
    b. **[ソースの種類]** ボックスで、**[データ ストリーム]** を選びます。
    
    c. 残りのフィールドについては既定値を使います。

   ![Azure Stream Analytics の入力](./media/tutorial-deploy-stream-analytics/asa_input.png)

6. **[作成]**を選択します。

7. **[ジョブ トポロジ]** で **[出力]** を選び、**[追加]** を選びます。

8. **[新しい出力]** ウィンドウで、次のようにします。

    a. **[出力のエイリアス]** ボックスに、「**alert**」と入力します。
    
    b. 残りのフィールドについては既定値を使います。 
    
    c. **[作成]**を選択します。

   ![Azure Stream Analytics の出力](./media/tutorial-deploy-stream-analytics/asa_output.png)


9. **[ジョブ トポロジ]** で **[クエリ]** を選び、既定のテキストを次のクエリに置き換えます。

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

10. **[保存]** を選択します。

## <a name="deploy-the-job"></a>ジョブのデプロイ

IoT Edge デバイスに Azure Stream Analytics ジョブをデプロイする準備が整いました。

1. Azure Portal の IoT Hub で、**[IoT Edge (preview)]\(IoT Edge (プレビュー)\)** に移動し、自分の IoT Edge デバイスの詳細ページを開きます。

2. **[Set modules]\(モジュールの設定\)** を選びます。  
    以前、このデバイスに tempSensor モジュールをデプロイしたことがある場合は、自動入力されている可能性があります。 そうでない場合は、次のようにしてモジュールを追加します。

   a. **[Add IoT Edge Module]\(IoT Edge モジュールの追加\)** を選びます。

   b. 名前に「**tempSensor**」と入力します。
    
   c. イメージの URI には、「**microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview**」と入力します。 

   d. 他の設定はそのままにします。
   
   e. **[保存]** を選択します。

3. Azure Stream Analytics Edge ジョブを追加するには、**[Import Azure Stream Analytics IoT Edge Module]\(Azure Stream Analytics IoT Edge モジュールのインポート\)** を選びます。

4. 自分のサブスクリプションと、作成した Azure Stream Analytics Edge ジョブを選びます。 

5. 自分のサブスクリプションと作成したストレージ アカウントを選び、**[保存]** を選びます。

    ![モジュールの設定][6]

6. 自動的に生成された Azure Stream Analytics モジュールの名前をコピーします。 

    ![Temperature モジュール][11]

7. ルートを構成するには、**[次へ]** を選びます。

8. 次のコードを **[ルート]** にコピーします。 _{moduleName}_ を、コピーしたモジュール名で置き換えます。

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

9. **[次へ]**を選択します。

10. **[Review template]\(テンプレートのレビュー\)** ステップで、**[送信]** を選びます。

11. デバイスの詳細ページに戻り、**[更新]** を選びます。  
    新しい Stream Analytics モジュールが、IoT Edge エージェント モジュールおよび IoT Edge ハブと共に実行されていることがわかります。

    ![モジュールの出力][7]

## <a name="view-data"></a>データの表示

ここで、IoT Edge デバイスに移動し、Azure Stream Analytics モジュールと tempSensor モジュールの間のやり取りを確認できます。

1. すべてのモジュールが Docker で実行されていることを確認します。

   ```cmd/sh
   docker ps  
   ```

   ![Docker の出力][8]

2. すべてのシステム ログとメトリック データを表示します。 Stream Analytics モジュールの名前を使用してください。

   ```cmd/sh
   docker logs -f {moduleName}  
   ```

30 秒間の温度が 70 度に達するまで、マシンの温度が徐々に上昇するのを観察できます。 次に、Stream Analytics モジュールによってリセットがトリガーされ、マシンの温度が 21 度まで下降します。 

   ![Docker のログ][9]


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Storage コンテナーおよび Streaming Analytics ジョブを構成して IoT Edge デバイスのデータを分析しました。 その後、カスタム Azure Stream Analytics モジュールを読み込み、ストリームを介して、データをデバイスからダウンロード用の BLOB に移動しました。 Azure IoT Edge がビジネス ソリューションを作成する方法をさらに確認するには、他のチュートリアルに進んでください。

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
[azure-iot]: https://docs.microsoft.com/azure/iot-hub/
[azure-storage]: https://docs.microsoft.com/azure/storage/
[azure-stream]: https://docs.microsoft.com/azure/stream-analytics/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

[lnk-docker-windows]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-linux]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/
[lnk-python]: https://www.python.org/downloads/
