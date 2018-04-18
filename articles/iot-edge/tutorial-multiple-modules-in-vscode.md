---
title: Visual Studio Code で複数の IoT Edge モジュールを操作する | Microsoft Docs
description: Azure Machine Learning をモジュールとしてエッジ デバイスに展開します
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 6c94701507f86f6ecab2875f952215cc3e4cc719
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code---preview"></a>Visual Studio Code で複数のモジュールを含む IoT Edge ソリューションを開発する - プレビュー
Visual Studio Code を使用して、複数のモジュールを含むご自身の IoT Edge ソリューションを開発できます。 この記事では、Visual Studio Code でシミュレートされた IoT Edge デバイスで、センサー データをパイプ処理する IoT Edge ソリューションを作成、更新、展開する手順について説明します。 この記事では、次のことについて説明します:

* Visual Studio Code を使用して IoT Edge ソリューションを作成する
* VS Code を使用して、ご自身の動作している IoT Edge ソリューションに新しいモジュールを追加する。 
* 使用している IoT Edge デバイスに IoT Edge ソリューション (複数のモジュール) を展開する
* 生成されたデータを表示する

## <a name="prerequisites"></a>前提条件
* 以下のチュートリアルを完了する必要があります
  * [C# モジュールを展開する](tutorial-csharp-module.md)
  * [C# 関数を展開する](tutorial-deploy-function.md)
  * [Python モジュールを展開する](tutorial-python-module.md)
* [Docker for VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)。イメージとコンテナーを管理するためにエクスプローラーが統合されている必要があります。


## <a name="prepare-your-first-iot-edge-solution"></a>最初の IoT Edge ソリューションを準備する
1. VS Code コマンド パレットで、**Edge: New IoT Edge solution** コマンドを入力して実行します。 次に、ご自身のワークスペース フォルダーを選択し、ソリューション名 (既定の名前は **EdgeSolution**) を指定して、C# モジュール (**SampleModule**) をこのソリューションの最初のユーザー モジュールとして作成します。 また、この最初のモジュールの Docker イメージ リポジトリを指定する必要もあります。 既定のイメージ リポジトリは、ローカル Docker レジストリに基づきます (`localhost:5000/<first module name>`)。 これは、Azure コンテナー レジストリまたは Docker Hub に変更することもできます。

> [!NOTE]
> ローカル Docker レジストリを使用する場合は、ご自身のコンソール ウィンドウで `docker run -d -p 5000:5000 --restart=always --name registry registry:2` コマンドを入力して、レジストリが実行されていることを確認してください。

2. VS Code ウィンドウによって、ご自身の IoT Edge ソリューション ワークスペースが読み込まれます。 ルート フォルダーには、`modules` フォルダー、`.vscode` フォルダー、および配置マニフェスト テンプレート ファイルがあります。 `.vscode` フォルダーではデバッグ構成を確認できます。 ユーザー モジュール コードはすべて、`modules` フォルダーのサブフォルダーになります。 `deployment.template.json` は、配置マニフェスト テンプレートです。 このファイルのパラメーターの中には、すべてのモジュール フォルダーにある `module.json` から解析されるものがあります。

3. このソリューション プロジェクトに 2 つ目のモジュールを追加します。 今回は、**Edge: Add IoT Edge module** を入力して実行し、更新する配置テンプレート ファイルを選択します。 次に、**SampleFunction** という名前の **Azure Function - C#** と、追加するその Docker イメージ リポジトリを選択します。

4. これで、2 つの基本的なモジュールを含む最初の IoT Edge ソリューションの準備ができました。 既定の C# モジュールはパイプ メッセージ モジュールとして、C# 関数はパイプ メッセージ関数として機能します。 `deployment.template.json` では、このソリューションに 3 つのモジュールが含まれていることがわかります。 メッセージは `tempSensor` モジュールから生成され、`SampleModule` と `SampleFunction` を介して直接パイプ処理された後、使用している IoT ハブに送信されます。 以下の内容でこれらのモジュールのルートを更新します。 
   ```json
        "routes": {
          "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
          "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
          "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
        },
   ```

5. このファイルを保存します。

## <a name="build-and-deploy-your-iot-edge-solution"></a>IoT Edge ソリューションをビルドして展開する
1. VS Code コマンド パレットで、**Edge: Build IoT Edge solution** コマンドを入力して実行します。 このコマンドは、各モジュール フォルダーの `module.json` ファイルに基づいて、それぞれのモジュールの Docker イメージを確認し、ビルド、コンテナー格納、およびプッシュを開始します。 その後、`deployment.template.json` に必要な値を解析し、`config` フォルダーにある実際の値を使用して `deployment.json` を生成します。 ビルドの進行状況は、VS Code 統合ターミナルで確認できます。

2. Azure IoT Hub Device Explorer で、IoT Edge デバイス ID を右クリックし、**[Create deployment for Edge device]\(Edge デバイスの展開の作成\)** を選択します。 `config` フォルダーの `deployment.json` を選択します。 これにより、VS Code 統合ターミナルで、展開が正常に作成されていることを配置 ID によって確認できます。

3. 開発マシンで [IoT Edge デバイスをシミュレート](tutorial-simulate-device-linux.md)している場合は、 すべてのモジュール イメージ コンテナーが数分で起動するのを確認できます。

## <a name="view-generated-data"></a>生成されたデータを表示する

1. IoT ハブに到着するデータを監視するには、**[表示]** > **[コマンド パレット...]** を選択し、**[IoT: Start monitoring D2C message]\(IoT: D2C メッセージの監視を開始\)** を検索します。 
2. データの監視を停止するには、コマンド パレットで **[IoT: Stop monitoring D2C message]\(IoT: D2C メッセージの監視を停止\)** を使用します。 

## <a name="next-steps"></a>次の手順

次のいずれかの記事に進み、Visual Studio Code で Azure IoT Edge を開発するときの他のシナリオを確認できます。

* [VS Code での C# モジュールのデバッグ](how-to-vscode-debug-csharp-module.md)
* [VS Code での C# 関数のデバッグ](how-to-vscode-debug-azure-function.md)