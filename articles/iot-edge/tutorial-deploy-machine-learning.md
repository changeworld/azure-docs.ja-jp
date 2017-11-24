---
title: "Azure IoT Edge で Machine Learning を展開する | Microsoft Docs"
description: "Azure Machine Learning をモジュールとしてエッジ デバイスに展開します"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 9b8475dcc51fb24fadd1faa4a2008b25a4464080
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>Azure Machine Learning を IoT Edge モジュールとして展開する - プレビュー

IoT Edge モジュールを使用して、ビジネス ロジックを実装するコードを IoT Edge デバイスに直接展開できます。 このチュートリアルでは、[Windows][lnk-tutorial1-win] または [Linux][lnk-tutorial1-lin] のシミュレートされたデバイスに Azure IoT Edge を展開するチュートリアルで作成した、シミュレートされた IoT Edge デバイスのセンサー データに基づいて、デバイスが失敗するタイミングを予測する Azure Machine Learning モジュールを展開します。 学習内容は次のとおりです。 

> [!div class="checklist"]
> * Azure Machine Learning モジュールを IoT Edge デバイスに展開する
> * 生成されたデータを表示する

独自の [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/) モデルをソリューションで使用するには、IoT Edge 用に[モデルを展開](https://aka.ms/aml-iot-edge-doc)し、[Azure Container Registry](../container-registry/index.yml)、Docker などのコンテナー レジストリでホストします。

## <a name="prerequisites"></a>前提条件

* クイック スタートまたは最初のチュートリアルで作成した Azure IoT Edge デバイス。
* IoT Edge デバイスの接続先 IoT ハブに対する IoT ハブ接続文字列。
* Azure ML コンテナー

## <a name="create-the-azure-ml-container"></a>Azure ML コンテナーを作成する
Azure ML コンテナーを作成するには、[Azure IoT Edge の AI ツールキット](https://aka.ms/aml-iot-edge-anomaly-detection)に関するページの手順に従います。

## <a name="run-the-solution"></a>ソリューションを実行する

1. [Azure Portal](https://portal.azure.com) で、IoT ハブに移動します。
1. **IoT Edge (プレビュー)** に移動し、IoT Edge デバイスを選択します。
1. **[Set modules]\(モジュールを設定\)** を選択します。
1. **[Add IoT Edge module]\(IoT Edge モジュールの追加\)** を選択します。
1. **[名前]** フィールドに「`tempSensor`」と入力します。
1. **[イメージの URI]** フィールドに「`microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`」と入力します。
1. 他の設定はそのままにして、**[保存]** を選択します。
1. **モジュールの追加**手順で、もう一度 **[Add IoT Edge module]\(IoT Edge モジュールの追加\)** を選択します。
1. **[名前]** フィールドに、前のセクションで作成したコンテナーの名前を入力します。 名前を見つけるための情報については、[Azure IoT Edge の AI ツールキット](https://aka.ms/aml-iot-edge-anomaly-detection)に関するページをご覧ください。
1. **[イメージ]** フィールドに、前のセクションで作成したコンテナーのイメージ URI を入力します。 イメージを見つけるための情報については、[Azure IoT Edge の AI ツールキット](https://aka.ms/aml-iot-edge-anomaly-detection)に関するページをご覧ください。
1. **[ Save]** をクリックします。
1. **モジュールの追加**手順に戻り、**[次へ]** をクリックします。
1. モジュールのルートを更新します。
1. **ルートの指定**手順で、以下の JSON をテキスト ボックスにコピーします。 モジュールによって、すべてのメッセージが Edge ランタイムに発行されます。 こうしたメッセージがフローする場所は、ランタイムの宣言型ルールによって定義されます。 このチュートリアルでは、2 つのルートが必要です。 1 つ目のルートは、すべての Azure Machine Learning モジュールが使用する "mlInput" エンドポイント経由で、温度センサーから機械学習モジュールにメッセージを転送します。 2 つ目のルートは、機械学習モジュールから IoT ハブにメッセージを転送します。 このルートでは、"mlOutput" は、すべての Azure Machine Learning モジュールがデータを出力するために使用するエンドポイントです。また、"upstream" は特別な転送先で、Edge Hub に対して、メッセージを IoT ハブに送信するように指示します。 

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ``` 

1. **[次へ]** をクリックします。 
1. "テンプレートのレビュー" 手順で、[送信] をクリックします。 
1. デバイスの詳細ページに戻り、[更新] をクリックします。  新しい "machinelearningmodule" が、"tempSensor モジュール" および "IoT Edge ランタイム" と一緒に実行されています。

## <a name="view-generated-data"></a>生成されたデータを表示する

 VS Code で、**[表示]、[コマンド パレット]、[IoT: Start monitoring D2C Message]\(IoT: D2C メッセージの監視を開始\)** メニュー コマンドを使用して、IoT ハブに届くデータを監視します。 

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Machine Learning を使用する IoT Edge モジュールを展開しました。 引き続き他のチュートリアルを実行し、Azure IoT Edge が、エッジでデータをビジネス上の洞察に変えるうえで、どのように役立つかを確認できます。

> [!div class="nextstepaction"]
> [Azure 関数をモジュールとして展開する](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md