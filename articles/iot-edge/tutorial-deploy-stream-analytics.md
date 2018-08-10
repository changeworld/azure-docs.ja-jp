---
title: チュートリアル - ASA ジョブを Azure IoT Edge デバイスに展開する | Microsoft Docs
description: このチュートリアルでは、Azure Stream Analytics をモジュールとして IoT Edge デバイスに展開します
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/25/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: afbdf2171c1fc1eef95514526a509d171e262d4a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435684"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module-preview"></a>チュートリアル: Azure Stream Analytics を IoT Edge モジュールとして展開する (プレビュー)

IoT ソリューションの多くが、分析サービスを使用して、IoT デバイスからクラウドに送信されたデータの分析情報を得ます。 Azure IoT Edge を使用すると、[Azure Stream Analytics][azure-stream] ロジックを取得し、そのロジックをデバイス自体に移動できます。 エッジで利用統計情報のストリームを処理することで、アップロードされるデータの量を削減し、アクションにつながる分析情報への対応にかかる時間を短縮できます。

Azure IoT Edge と Azure Stream Analytics の統合により、Azure portal で Azure Stream Analytics ジョブを作成し、そのジョブを、追加のコードなしに IoT Edge モジュールとして展開できます。  

Azure Stream Analytics には、クラウド上と IoT Edge デバイス上の両方でのデータ分析のために十分に構造化されたクエリ構文が用意されています。 IoT Edge での Azure Stream Analytics について詳しくは、[Azure Stream Analytics のドキュメント](../stream-analytics/stream-analytics-edge.md)をご覧ください。

このチュートリアルの Stream Analytics モジュールでは、30 秒にわたって平均気温が繰り返し計算され、 その平均値が 70 に達すると、モジュールからアラートが送信され、デバイスでアクションが実行されます。 ここでは、シミュレートされた温度センサーをリセットするというアクションです。 この機能を運用環境で使って、温度が危険なレベルに達したときにマシンを停止させたり予防策を講じたりできます。 

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * エッジでデータを処理するための Azure Stream Analytics ジョブを作成します。
> * 新しい Azure Stream Analytics ジョブを他の IoT Edge モジュールと接続します。
> * Azure Stream Analytics ジョブを Azure portal から IoT Edge デバイスに展開します。

>[!NOTE]
>IoT Edge の Azure Stream Analytics モジュールは[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)段階にあります。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

Azure IoT Edge デバイス:

* [Linux デバイス](quickstart-linux.md) または [Windows デバイス](quickstart.md)のクイック スタートに記載された手順に従って開発マシンまたは仮想マシンをエッジ デバイスとして使用できます。
* Azure Machine Learning モジュールでは、ARM プロセッサをサポートしていません。

クラウド リソース:

* Azure の Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。 


## <a name="create-an-azure-stream-analytics-job"></a>Azure Stream Analytics ジョブの作成

このセッションでは、Azure Stream Analytics ジョブを作成して IoT ハブからデータを取得し、デバイスから送信されたテレメトリ データに対してクエリを実行した後、その結果を Azure Blob Storage コンテナーに転送します。 詳しくは、「[Stream Analytics のドキュメント][azure-stream]」の「概要」セクションをご覧ください。 

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

Azure Stream Analytics ジョブには Azure ストレージ アカウントが必要です。このアカウントは、そのジョブ出力のエンドポイントとして動作します。 このセクションの例では、BLOB ストレージ タイプを使います。 詳しくは、「[Azure Storage のドキュメント][azure-storage]」の「BLOB」セクションをご覧ください。

1. Azure Portal で **[リソースの作成]** に移動し、[検索] ボックスに「**ストレージ アカウント**」と入力して、**[ストレージ アカウント - Blob、File、Table、Queue]** を選びます。

1. **[ストレージ アカウントの作成]** ウィンドウで、ストレージ アカウントの名前を入力し、お使いの IoT ハブが格納されるのと同じ場所を選び、お使いの IoT ハブと同じリソース グループを選んで、**[作成]** を選びます。 後で使うので名前をメモしておきます。

    ![ストレージ アカウントの作成][1]


### <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成

1. Azure Portal で、**[リソースの作成]** > **[モノのインターネット]** に移動し、**[Stream Analytics ジョブ]** を選びます。

1. **[新しい Stream Analytics ジョブ]** ウィンドウで、次の手順を実行します。

   1. **[ジョブ名]** ボックスに、ジョブの名前を入力します。
   
   1. ご自身の IoT ハブと同じ**リソース グループ**および**場所**を使用します。 

      > [!NOTE]
      > 現在、IoT Edge 上の Azure Stream Analytics ジョブは、米国西部 2 リージョンではサポートされていません。 

   1. **[ホスティング環境]** で **[Edge]** を選びます。
    
1. **作成**を選択します。

1. 作成したジョブの **[ジョブ トポロジ]** で **[入力]** を開きます。

   ![Azure Stream Analytics の入力](./media/tutorial-deploy-stream-analytics/asa_input.png)

1. **[ストリーム入力の追加]** を選んでから、**[Edge Hub]** を選びます。

1. **[新しい入力]** ウィンドウで、入力のエイリアスとして「**温度**」と入力します。 

1. **[保存]** を選択します。

1. **[ジョブ トポロジ]** で **[出力]** を開きます。

   ![Azure Stream Analytics の出力](./media/tutorial-deploy-stream-analytics/asa_output.png)

1. **[追加]** を選んでから、**[Edge Hub]** を選びます。

1. **[新しい出力]** ウィンドウで、出力のエイリアスとして「**アラート**」と入力します。 

1. **[保存]** を選択します。

1. **[ジョブ トポロジ]** で、**[クエリ]** を選択し、既定のテキストを次のクエリで置き換えます。このクエリでは、30 秒間のマシン平均温度が 70 度に達した場合にアラートが作成されます。

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

1. **[保存]** を選択します。

1. **[構成]** で、**[IoT Edge の設定]** を選択します。

1. ドロップダウン メニューから **[ストレージ アカウント]** を選択します。

1. **[コンテナー]** フィールドで、**[新規作成]** を選択し、ストレージ コンテナーの名前を指定します。 

1. **[保存]** を選択します。 


## <a name="deploy-the-job"></a>ジョブのデプロイ

IoT Edge デバイスに Azure Stream Analytics ジョブをデプロイする準備が整いました。

1. Azure portal のご自身の IoT ハブで、**[IoT Edge]** に移動し、ご自身の IoT Edge デバイスの詳細ページを開きます。

1. **[Set modules]\(モジュールの設定\)** を選びます。  

   以前、このデバイスに tempSensor モジュールをデプロイしたことがある場合は、自動入力されている可能性があります。 そうでない場合は、次の手順でモジュールを追加します。

   1. **[追加]** をクリックし、**[IoT Edge モジュール]** を選択します。
   1. 名前に「**tempSensor**」と入力します。
   1. イメージ URI として「**mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**」と入力します。 
   1. 他の設定はそのままにします。
   1. **[保存]** を選択します。

1. 次の手順に従って、ご自身の Azure Stream Analytics Edge ジョブを追加します。

   1. **[追加]** をクリックし、**[Azure Stream Analytics モジュール]** を選択します。
   1. 自分のサブスクリプションと、作成した Azure Stream Analytics Edge ジョブを選びます。 
   1. **[保存]** を選択します。

1. **[次へ]** を選択します。

1. **[ルート]** の既定値を以下のコードで置き換えます。 Azure Stream Analytics モジュールの名前で _{moduleName}_ を更新します。 モジュールの名前は、そのモジュールの作成元のジョブと同じである必要があります。 

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

1. **[次へ]** を選択します。

1. **[Review Deployment]\(展開のレビュー\)** ステップで、**[送信]** を選びます。

1. デバイスの詳細ページに戻り、**[更新]** を選びます。  

    新しい Stream Analytics モジュールが、IoT Edge エージェント モジュールおよび IoT Edge ハブと共に実行されていることがわかります。

    ![モジュールの出力][7]

## <a name="view-data"></a>データの表示

ここで、IoT Edge デバイスに移動し、Azure Stream Analytics モジュールと tempSensor モジュールの間のやり取りを確認できます。

1. すべてのモジュールが Docker で実行されていることを確認します。

   ```cmd/sh
   iotedge list  
   ```
<!--
   ![Docker output][8]
-->
1. すべてのシステム ログとメトリック データを表示します。 Stream Analytics モジュールの名前を使用してください。

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

30 秒間の温度が 70 度に達するまで、マシンの温度が徐々に上昇するのを観察できます。 次に、Stream Analytics モジュールによってリセットがトリガーされ、マシンの温度が 21 度まで下降します。 

   ![Docker のログ][9]

## <a name="clean-up-resources"></a>リソースのクリーンアップ 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

次の推奨記事に進む場合は、既に作成したリソースおよび構成を維持して、再利用することができます。

それ以外の場合は、課金されないようにするために、ローカル構成と、この記事で作成した Azure リソースを削除してもかまいません。 

> [!IMPORTANT]
> Azure リソースとリソース グループは、削除すると元に戻すことができません。 いったん削除すると、リソース グループとそこに含まれるすべてのリソースが完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 IoT ハブを、保持したいリソースが含まれている既存のリソース グループ内に作成した場合は、リソース グループを削除するのではなく、IoT Hub リソースだけを削除してください。
>

IoT ハブだけを削除するには、ハブ名とリソース グループ名を指定して次のコマンドを実行します。

```azurecli-interactive
az iot hub delete --name {hub_name} --resource-group IoTEdgeResources
```


名前でリソース グループ全体を削除するには、以下の手順を実行します。

1. [Azure ポータル](https://portal.azure.com) にサインインし、 **[リソース グループ]** をクリックします。

1. **[名前でフィルター処理してください]** ボックスに、IoT ハブが含まれているリソース グループの名前を入力します。 

1. 結果一覧のでリソース グループの **[...]** をクリックし、**[リソース グループの削除]** をクリックします。

<!--
   ![Delete](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)
-->
1. リソース グループの削除の確認を求めるメッセージが表示されます。 確認のためにリソース グループの名前を再度入力し、**[削除]** をクリックします。 しばらくすると、リソース グループとそこに含まれているすべてのリソースが削除されます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Edge デバイスのデータを分析するように、Azure Streaming Analytics ジョブを構成しました。 その後、温度上昇にローカルで対処するために、IoT Edge デバイスでこの Azure Stream Analytics モジュールを読み込みました。また、集計されたデータ ストリームをクラウドに送信しました。 Azure IoT Edge がビジネス ソリューションを作成する方法をさらに確認するには、他のチュートリアルに進んでください。

> [!div class="nextstepaction"] 
> [モジュールとして Azure Machine Learning モデルをデプロイする][lnk-ml-tutorial]

<!-- Images. -->
[1]: ./media/tutorial-deploy-stream-analytics/storage.png
[4]: ./media/tutorial-deploy-stream-analytics/add_device.png
[5]: ./media/tutorial-deploy-stream-analytics/asa_job.png
[6]: ./media/tutorial-deploy-stream-analytics/set_module.png
[7]: ./media/tutorial-deploy-stream-analytics/module_output2.png
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
[lnk-quickstart-win]: quickstart.md
[lnk-quickstart-lin]: quickstart-linux.md
[lnk-module-tutorial]: tutorial-csharp-module.md
[lnk-ml-tutorial]: tutorial-deploy-machine-learning.md

