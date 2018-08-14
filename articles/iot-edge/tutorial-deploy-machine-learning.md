---
title: Azure IoT Edge で Machine Learning を展開する | Microsoft Docs
description: このチュートリアルでは、Azure Machine Learning をモジュールとしてエッジ デバイスに展開します
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/25/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 28b963922b423bb776aa97e9b76392bc484ddcd6
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627809"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>チュートリアル: Azure Machine Learning を IoT Edge モジュールとして展開する (プレビュー)

IoT Edge モジュールを使用して、ビジネス ロジックを実装するコードを IoT Edge デバイスに直接展開できます。 このチュートリアルでは、シミュレートされたマシンの温度データに基づいてデバイスが失敗するタイミングを予測する、Azure Machine Learning モジュールを展開する方法について説明します。 IoT Edge 上の Azure ML の詳細については、「[Azure Machine Learning のドキュメントに関するページ」](../machine-learning/desktop-workbench/use-azure-iot-edge-ai-toolkit.md)を参照してください。

このチュートリアルで作成した Azure Machine Learning モジュールは、デバイスによって生成された環境データを読み取り、メッセージに異常か否かのラベル付けを行います。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure Machine Learning モジュールを作成する
> * Azure コンテナー レジストリにモジュール コンテナーをプッシュする
> * Azure Machine Learning モジュールを IoT Edge デバイスに展開する
> * 生成されたデータを表示する

>[!NOTE]
>Azure IoT Edge 上の Azure Machine Learning モジュールはパブリック プレビュー段階にあります。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>前提条件

Azure IoT Edge デバイス:

* [Linux デバイス](quickstart-linux.md) または [Windows デバイス](quickstart.md)のクイック スタートに記載された手順に従って開発マシンまたは仮想マシンをエッジ デバイスとして使用できます。
* Azure Machine Learning モジュールでは、ARM プロセッサをサポートしていません。

クラウド リソース:

* Azure の Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。 
* Azure Machine Learning アカウント。 [Azure Machine Learning アカウントの作成と Azure Machine Learning Workbench のインストール](../machine-learning/service/quickstart-installation.md#create-azure-machine-learning-services-accounts)に関するセクションの手順に従います。 このチュートリアルを進めるために Workbench アプリケーションをインストールする必要はありません。 

開発リソース:

* Azure ML のモデル管理。 環境をセットアップしてアカウントを作成するには、「[モデル管理のセットアップ](../machine-learning/desktop-workbench/deployment-setup-configuration.md)」の手順に従います。 デプロイのセットアップ中に、可能であれば、クラスターではなく、ローカルの手順を選択することをお勧めします。

### <a name="disable-process-identification"></a>プロセス ID を無効にする

>[!NOTE]
>
> プレビュー段階の Azure Machine Learning では、IoT Edge で既定で有効になっているプロセス ID セキュリティ機能はサポートされていません。 
> これを無効にする手順は次のとおりです。 ただし、これは、運用環境での使用には適していません。 これらの手順は、Windows Edge ランタイムのインストールでは完了しているため、Linux の場合にのみ必要です。

IoT Edge デバイス上でプロセス ID を無効にするには、IoT Edge デーモン構成の **connect** セクションで、**workload_uri** と **management_uri** の IP アドレスとポートを指定する必要があります。

最初に、IP アドレスを取得します。 コマンド ラインで「`ifconfig`」と入力し、**docker0** インターフェイスの IP アドレスをコピーします。

IoT Edge デーモン構成ファイルを編集します。

```cmd/sh
sudo nano /etc/iotedge/config.yaml
```

構成の **connect** セクションをお使いの IP アドレスで更新します。 例: 
```yaml
connect:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

構成の **listen** セクションに同じアドレスを入力します。 例: 

```yaml
listen:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

management_uri アドレスで環境変数 IOTEDGE_HOST を作成します (永続的に設定するには、それを `/etc/environment` に追加します)。次に例を示します。

```cmd/sh
export IOTEDGE_HOST="http://172.17.0.1:15580"
```


## <a name="create-the-azure-ml-container"></a>Azure ML コンテナーを作成する
このセクションでは、トレーニング済みのモデル ファイルをダウンロードして、Azure ML コンテナーに変換します。

Azure ML のモデル管理を実行しているマシンで、GitHub の Azure ML IoT Toolkit から [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) と [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl) をダウンロードして保存します。 これらのファイルにはトレーニング済みの機械学習モデルが定義されており、このモデルを IoT Edge デバイスに展開します。

このトレーニング済みモデルを使用して、IoT Edge デバイスに展開できるコンテナーを作成します。 次のコマンドを使用します。

   * モデルを登録する。
   * マニフェストを作成します。
   * *machinelearningmodule* という名前の Docker コンテナー イメージを作成する。
   * Azure Kubernetes Service (AKS) クラスターにイメージをデプロイする。

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```

### <a name="view-the-container-repository"></a>コンテナー リポジトリを表示する

コンテナー イメージが正常に作成され、ご自身の機械学習環境に関連付けられている Azure コンテナー レジストリに格納されたことを確認してください。

1. [Azure Portal](https://portal.azure.com) の **[すべてのサービス]** に移動し、**[コンテナー レジストリ]** を選択します。
2. お使いのレジストリを選択します。 名前の先頭に **mlcr** と付くはずで、モジュール管理のセットアップで使用したリソース グループ、場所、サブスクリプションに属します。
3. **[アクセス キー]** を選択します。
4. **ログイン サーバー**、**ユーザー名**、および **パスワード** をコピーします。  Edge デバイスからレジストリへのアクセスに必要となります。
5. **[リポジトリ]** を選択します。
6. **machinelearningmodule** を選択します。
7. これでコンテナー イメージの完全なパスが取得できました。 次のセクションで使用するために、イメージのパスを書き留めておきます。 これは次のようになります: **<registry_name>.azurecr.io/machinelearningmodule:1**

## <a name="deploy-to-your-device"></a>デバイスにデプロイする

1. [Azure Portal](https://portal.azure.com) で、IoT ハブに移動します。

1. **[IoT Edge]** に移動し、IoT Edge デバイスを選びます。

1. **[Set modules]\(モジュールの設定\)** を選びます。

1. **[レジストリ設定]** セクションで、Azure コンテナー レジストリからコピーした資格情報を追加します。 

   ![レジストリ資格情報を追加する](./media/tutorial-deploy-machine-learning/registry-settings.png)

1. 以前 IoT Edge デバイスに tempSensor モジュールを展開したことがある場合は、自動で入力されていることがあります。 モジュールの一覧にない場合は追加します。

    1. **[追加]** をクリックし、**[IoT Edge モジュール]** を選択します。
    2. **[名前]** フィールドに「`tempSensor`」と入力します。
    3. **[イメージの URI]** フィールドに「`mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`」と入力します。
    4. **[保存]** を選択します。

1. 作成済みの機械学習モジュールを追加します。

    1. **[追加]** をクリックし、**[IoT Edge モジュール]** を選択します。
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

1. **[次へ]** を選択します。

1. **[Review Deployment]\(展開のレビュー\)** ステップで、**[送信]** を選びます。

1. デバイスの詳細ページに戻り、**[更新]** を選びます。  新しい **machinelearningmodule** が、**tempSensor** モジュールおよび IoT Edge ランタイム モジュールと共に実行されていることがわかります。

## <a name="view-generated-data"></a>生成されたデータを表示する

各 IoT Edge モジュールによって生成されているメッセージ、およびご自身の IoT ハブに配信されたメッセージを表示することができます。

### <a name="view-data-on-your-iot-edge-device"></a>IoT Edge デバイスのデータを表示する

IoT Edge デバイスでは、すべてのモジュールそれぞれから送信されているメッセージを表示できます。 

Linux デバイスでこれらのコマンドを実行する場合、管理者特権のアクセス許可には `sudo` の使用が必要になることがあります。

1. IoT Edge デバイス上のすべてのモジュールを確認します。

   ```cmd/sh
   iotedge list
   ```

2. 特定のデバイスから送信されているメッセージを確認します。 前のコマンド出力のモジュール名を使用します。

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>IoT ハブに届くデータを表示する

IoT Hub が受信する device-to-cloud メッセージは、[Visual Studio Code 用の Azure IoT Toolkit の拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)を使用して表示できます。

次の手順は、IoT ハブに届く device-to-cloud メッセージを監視するように Visual Studio Code を設定する方法を示しています。 

1. Visual Studio Code で、**[IoT Hub Devices]\(IoT Hub デバイス\)** を選択します。

2. **[...]** を選択した後、メニューで **[Set IoT Hub Connection String]\(IoT Hub の接続文字列の設定\)** を選択します。

   ![IoT Hub デバイスのその他のメニュー](./media/tutorial-deploy-machine-learning/set-connection.png)

3. ページの上部に表示されるテキスト ボックスに、IoT Hub の iothubowner の接続文字列を入力します。 IoT Hub デバイスの一覧に、お使いの IoT Edge デバイスが表示されます。

4. もう一度 **[...]** を選択した後で、**[Start monitoring D2C message]\(D2C メッセージの監視を開始する\)** を選択します。

5. 5 秒ごとに tempSensor から送られるメッセージを確認します。 メッセージ本文には、machinelearningmodule によって true または false 値が提供される **anomaly** というプロパティが含まれています。 **AzureMLResponse** プロパティには、モジュールの実行に成功した場合、"OK" という値が含まれます。

   ![メッセージ本文の Azure ML の応答](./media/tutorial-deploy-machine-learning/ml-output.png)

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

2. **[名前でフィルター処理してください]** ボックスに、IoT ハブが含まれているリソース グループの名前を入力します。 

3. 結果一覧のでリソース グループの **[...]** をクリックし、**[リソース グループの削除]** をクリックします。

<!--
   ![Delete](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)
-->
4. リソース グループの削除の確認を求めるメッセージが表示されます。 確認のためにリソース グループの名前を再度入力し、**[削除]** をクリックします。 しばらくすると、リソース グループとそこに含まれているすべてのリソースが削除されます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Machine Learning を使用する IoT Edge モジュールを展開しました。 引き続き他のチュートリアルを実行し、Azure IoT Edge が、エッジでデータをビジネス上の洞察に変えるうえで、どのように役立つかを確認できます。

> [!div class="nextstepaction"]
> [C# コードを使用してセンサー データをフィルター処理する](tutorial-csharp-module.md)

<!--Links-->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
