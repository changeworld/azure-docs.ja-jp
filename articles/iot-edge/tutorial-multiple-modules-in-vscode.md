---
title: VS Code で複数の Azure IoT Edge モジュールを管理する | Microsoft Docs
description: Visual Studio Code を使用して、複数のモジュールを使用する IoT Edge ソリューションを開発します。
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4d9caa7aa95c99fa30e8ec76c5b6362615725622
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166354"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code---preview"></a>Visual Studio Code で複数のモジュールを含む IoT Edge ソリューションを開発する - プレビュー
Visual Studio Code を使用して、複数のモジュールを含むご自身の IoT Edge ソリューションを開発できます。 この記事では、Visual Studio Code でシミュレートされた IoT Edge デバイスで、センサー データをパイプ処理する IoT Edge ソリューションを作成、更新、展開する手順について説明します。 

## <a name="prerequisites"></a>前提条件

この記事のすべての手順を完了するには、次の前提条件を満たす必要があります。

- [Visual Studio Code](https://code.visualstudio.com/) 
- [Visual Studio Code 用の Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
- [Visual Studio Code 用の C# (OmniSharp を使用) 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) 
- AzureIoTEdgeModule テンプレート (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- 少なくとも 1 つの IoT Edge デバイスを含むアクティブな IoT ハブ


* [Docker for VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)。イメージとコンテナーを管理するためにエクスプローラーが統合されている必要があります。


## <a name="prepare-your-first-iot-edge-solution"></a>最初の IoT Edge ソリューションを準備する
1. VS Code コマンド パレットで、**Edge: New IoT Edge solution** コマンドを入力して実行します。 次に、ご自身のワークスペース フォルダーを選択し、ソリューション名 (既定の名前は **EdgeSolution**) を指定して、C# モジュール (**SampleModule**) をこのソリューションの最初のユーザー モジュールとして作成します。 また、この最初のモジュールの Docker イメージ リポジトリを指定する必要もあります。 既定のイメージ リポジトリは、ローカル Docker レジストリに基づきます (`localhost:5000/<first module name>`)。 これは、Azure コンテナー レジストリまたは Docker Hub に変更することもできます。

   > [!NOTE]
   > ローカル Docker レジストリを使用する場合は、ご自身のコンソール ウィンドウで `docker run -d -p 5000:5000 --restart=always --name registry registry:2` コマンドを入力して、レジストリが実行されていることを確認してください。

2. VS Code ウィンドウによって、ご自身の IoT Edge ソリューション ワークスペースが読み込まれます。 ルート フォルダーには、`modules` フォルダー、`.vscode` フォルダー、および配置マニフェスト テンプレート ファイルがあります。 `.vscode` フォルダーではデバッグ構成を確認できます。 ユーザー モジュール コードはすべて、`modules` フォルダーのサブフォルダーになります。 `deployment.template.json` は、配置マニフェスト テンプレートです。 このファイルのパラメーターの中には、すべてのモジュール フォルダーにある `module.json` から解析されるものがあります。

3. このソリューション プロジェクトに 2 つ目のモジュールを追加します。 今回は、**Edge: Add IoT Edge module** を入力して実行し、更新する配置テンプレート ファイルを選択します。 次に、**SampleFunction** という名前の **Azure Function - C#** と、その Docker イメージ リポジトリを選択します。

4. `deployment.template.json` ファイルを開き、ランタイムに加えて 3 つのモジュールが宣言されていることを確認します。 メッセージは `tempSensor` モジュールから生成され、`SampleModule` と `SampleFunction` を介して直接パイプ処理された後、使用している IoT ハブに送信されます。 
5. 以下の内容でこれらのモジュールのルートを更新します。
   ```json
   "routes": {
      "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
      "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
      "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
   },
   ```

6. このファイルを保存します。

## <a name="build-and-deploy-your-iot-edge-solution"></a>IoT Edge ソリューションをビルドして展開する
1. VS Code コマンド パレットで、**Edge: Build IoT Edge solution** コマンドを入力して実行します。 このコマンドは、各モジュール フォルダーの `module.json` ファイルに基づいて、各モジュールの Docker イメージのビルド、コンテナー化、プッシュを開始します。 その後、必要な値を `deployment.template.json` に渡し、`config` フォルダーからの情報を使って `deployment.json` ファイルを生成します。 ビルドの進行状況は、VS Code 統合ターミナルで確認できます。

2. Azure IoT Hub Device Explorer で、IoT Edge デバイス ID を右クリックし、**[Create deployment for Edge device]\(Edge デバイスの展開の作成\)** を選択します。 `config` フォルダーの `deployment.json` を選択します。 これにより、VS Code 統合ターミナルで、展開が正常に作成されていることを配置 ID によって確認できます。

3. 開発用コンピューター上で IoT Edge デバイスをシミュレートしている場合は、数分後にすべてのモジュール イメージ コンテナーが開始されたことが表示されます。

## <a name="view-generated-data"></a>生成されたデータを表示する

1. IoT ハブに到着するデータを監視するには、**[表示]** > **[コマンド パレット...]** を選択し、**[IoT: Start monitoring D2C message]\(IoT: D2C メッセージの監視を開始\)** を検索します。 
2. データの監視を停止するには、コマンド パレットで **[IoT: Stop monitoring D2C message]\(IoT: D2C メッセージの監視を停止\)** を使用します。 

## <a name="next-steps"></a>次の手順

Visual Studio Code での Azure IoT Edge の開発に関する他のシナリオについては、次の記事をご覧ください。

* [VS Code での C# モジュールのデバッグ](how-to-vscode-debug-csharp-module.md)
* [VS Code での C# 関数のデバッグ](how-to-vscode-debug-azure-function.md)