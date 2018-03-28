---
title: Azure IoT Edge で Machine Learning を展開する | Microsoft Docs
description: Azure Machine Learning をモジュールとしてエッジ デバイスに展開します
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/12/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4201395085dd72eb92b774eaed5980737b2e5de0
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Azure Machine Learning を IoT Edge モジュールとして展開する - プレビュー

IoT Edge モジュールを使用して、ビジネス ロジックを実装するコードを IoT Edge デバイスに直接展開できます。 このチュートリアルでは、シミュレートされた IoT Edge デバイス上のセンサー データに基づいて、デバイスが故障するタイミングを予測する Azure Machine Learning モジュールを展開します。この IoT Edge デバイスは、[Windows][lnk-tutorial1-win] または [Linux][lnk-tutorial1-lin] のシミュレートされたデバイスに Azure IoT Edge を展開するチュートリアルで作成しています。 

このチュートリアルで学習する内容は次のとおりです。 

> [!div class="checklist"]
> * Azure Machine Learning モジュールを作成する
> * Azure コンテナー レジストリにモジュール コンテナーをプッシュする
> * Azure Machine Learning モジュールを IoT Edge デバイスに展開する
> * 生成されたデータを表示する

このチュートリアルで作成した Azure Machine Learning モジュールは、デバイスによって生成された環境データを読み取り、メッセージに異常か否かのラベル付けを行います。 

## <a name="prerequisites"></a>前提条件

* クイック スタートまたは最初のチュートリアルで作成した Azure IoT Edge デバイス。
* IoT Edge デバイスの接続先 IoT ハブに対する IoT ハブ接続文字列。
* Azure Machine Learning アカウント。 アカウントを作成するには、[Azure Machine Learning アカウントの作成と Azure Machine Learning Workbench のインストール](../machine-learning/preview/quickstart-installation.md#create-azure-machine-learning-services-accounts)に関するセクションの手順に従ってください。 このチュートリアルを進めるために Workbench アプリケーションをインストールする必要はありません。 
* お使いのマシン上での Azure ML のモジュール管理。 環境をセットアップしてアカウントを作成するには、「[モデル管理のセットアップ](https://docs.microsoft.com/azure/machine-learning/preview/deployment-setup-configuration)」の手順に従います。

## <a name="create-the-azure-ml-container"></a>Azure ML コンテナーを作成する
このセクションでは、トレーニング済みのモデル ファイルをダウンロードして、Azure ML コンテナーに変換します。  

Azure ML のモジュール管理を実行しているマシンで、GitHub の Azure ML IoT Toolkit から [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) と [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl) をダウンロードして保存します。 これらのファイルにはトレーニング済みの機械学習モデルが定義されており、このモデルを IoT Edge デバイスに展開します。 

このトレーニング済みモデルを使用して、IoT Edge デバイスに展開できるコンテナーを作成します。 次のコマンドを使用します。

   * モデルを登録する。
   * マニフェストを作成する。
   * *machinelearningmodule* という名前の Docker コンテナー イメージを作成する。
   * Azure Container Service (AKS) クラスターにイメージをデプロイする。

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```

### <a name="view-the-container-repository"></a>コンテナー リポジトリを表示する

コンテナー イメージが正常に作成され、機械学習環境に関連付けられている Azure のコンテナー リポジトリに格納されたことを確認してください。

1. [Azure Portal](https://portal.azure.com) の **[すべてのサービス]** に移動し、**[コンテナー レジストリ]** を選択します。
2. お使いのレジストリを選択します。 名前の先頭に **mlcr** と付くはずで、モジュール管理のセットアップで使用したリソース グループ、場所、サブスクリプションに属します。
3. **[アクセス キー]** を選択します。
4. **ログイン サーバー**、**ユーザー名**、および **パスワード** をコピーします。  Edge デバイスからレジストリへのアクセスに必要となります。
5. **[リポジトリ]** を選択します。
6. **machinelearningmodule** を選択します。
7. これでコンテナー イメージの完全なパスが取得できました。 次のセクションで使用するために、イメージのパスを書き留めておきます。 これは次のようになります: **< registry_name >.azureacr.io/machinelearningmodule:1**

## <a name="add-registry-credentials-to-your-edge-device"></a>レジストリ資格情報を Edge デバイスに追加する

Edge デバイスを実行しているコンピューターの Edge ランタイムに、レジストリの資格情報を追加します。 このコマンドにより、コンテナーを取得するためのアクセス権がランタイムに付与されます。

Linux:
   ```cmd
   sudo iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password> 
   ```

Windows:
   ```cmd
   iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password> 
   ```

## <a name="run-the-solution"></a>ソリューションを実行する

1. [Azure Portal](https://portal.azure.com) で、IoT ハブに移動します。
1. **IoT Edge (プレビュー)** に移動し、IoT Edge デバイスを選択します。
1. **[Set modules]\(モジュールの設定\)** を選びます。
1. 以前 IoT Edge デバイスに tempSensor モジュールを展開したことがある場合は、自動で入力されていることがあります。 モジュールの一覧にない場合は追加します。
    1. **[Add IoT Edge Module]\(IoT Edge モジュールの追加\)** を選びます。
    2. **[名前]** フィールドに「`tempSensor`」と入力します。
    3. **[イメージの URI]** フィールドに「`microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`」と入力します。
    4. **[保存]** を選択します。
1. 作成済みの機械学習モジュールを追加します。
    1. **[Add IoT Edge Module]\(IoT Edge モジュールの追加\)** を選びます。
    1. **[名前]** フィールドに「`machinelearningmodule`」と入力します。
    1. **[イメージ]** フィールドに、イメージ アドレスを入力します (`<registry_name>.azurecr.io/machinelearningmodule:1` など)。
    1. **[保存]** を選択します。
1. **[モジュールの追加]** 手順に戻り、**[次へ]** を選択します。
1. **[Specify Routes] \(ルートの指定)** の手順で、下記の JSON をテキスト ボックスにコピーします。 1 つ目のルートは、すべての Azure Machine Learning モジュールが使用する "amlInput" エンドポイント経由で、温度センサーから機械学習モジュールにメッセージを転送します。 2 つ目のルートは、機械学習モジュールから IoT ハブにメッセージを転送します。 このルートでは、"amlOutput" はエンドポイントであり、すべての Azure Machine Learning モジュールでデータの出力に使用されます。"$upstream" は IoT Hub を指します。 

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ``` 

1. **[次へ]**を選択します。 
1. **[Review template]\(テンプレートのレビュー\)** ステップで、**[送信]** を選びます。 
1. デバイスの詳細ページに戻り、**[更新]** を選びます。  新しい **machinelearningmodule** が、**tempSensor** モジュールおよび IoT Edge ランタイム モジュールと共に実行されていることがわかります。

## <a name="view-generated-data"></a>生成されたデータを表示する

[IoT Hub エクスプローラー](https://github.com/azure/iothub-explorer)または Visual Studio Code 用の Azure IoT Toolkit の拡張機能を使用して、IoT Edge デバイスから送信される device-to-cloud メッセージを表示できます。 

1. Visual Studio Code で、**[IoT Hub Devices]\(IoT Hub デバイス\)** を選択します。 
2. **[...]** を選択した後、メニューで **[Set IoT Hub Connection String]\(IoT Hub の接続文字列の設定\)** を選択します。 

   ![IoT Hub デバイスのその他のメニュー](./media/tutorial-deploy-machine-learning/set-connection.png)

3. ページの上部に表示されるテキスト ボックスに、IoT Hub の iothubowner の接続文字列を入力します。 IoT Hub デバイスの一覧に、お使いの IoT Edge デバイスが表示されます。
4. もう一度 **[...]** を選択した後で、**[Start monitoring D2C message]\(D2C メッセージの監視を開始する\)** を選択します。
5. 5 秒ごとに tempSensor から送られるメッセージを確認します。 メッセージ本文には、machinelearningmodule によって true または false 値が提供される **anomaly** というプロパティが含まれています。 **AzureMLResponse** プロパティには、モジュールの実行に成功した場合、"OK" という値が含まれます。 

   ![メッセージ本文の Azure ML の応答](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Machine Learning を使用する IoT Edge モジュールを展開しました。 引き続き他のチュートリアルを実行し、Azure IoT Edge が、エッジでデータをビジネス上の洞察に変えるうえで、どのように役立つかを確認できます。

> [!div class="nextstepaction"]
> [Azure 関数をモジュールとして展開する](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
