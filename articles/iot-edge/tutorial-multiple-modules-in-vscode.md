---
title: VS Code で複数の Azure IoT Edge モジュールを管理する | Microsoft Docs
description: Visual Studio Code を使用して、複数のモジュールを使用する Azure IoT Edge ソリューションを開発します。
author: shizn
manager: ''
ms.author: xshi
ms.date: 03/18/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 4a624994f93e7a3cbb04db2a0ec0b2b823a12ee7
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763036"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code-preview"></a>Visual Studio Code で複数のモジュールを含む IoT Edge ソリューションを開発する (プレビュー)

Visual Studio Code を使用して、複数のモジュールを含むご自身の Azure IoT Edge ソリューションを開発できます。 この記事では、VS Code でシミュレートされた IoT Edge デバイスのセンサー データをパイプする IoT Edge ソリューションを作成、更新、展開する方法を示します。 

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次の前提条件を満たす必要があります。

- [Visual Studio Code](https://code.visualstudio.com/)
- [Visual Studio Code 用の Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
- [Visual Studio Code 用の C# (OmniSharp を使用) 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)
- AzureIoTEdgeModule テンプレート (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- 少なくとも 1 つの IoT Edge デバイスを含むアクティブな IoT ハブ

イメージとコンテナーを管理するには、[Docker for VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) と Azure IoT Hub Device Explorer の統合も必要です。

## <a name="prepare-your-first-iot-edge-solution"></a>最初の IoT Edge ソリューションを準備する

1. VS Code **コマンド パレット**で、**Edge: New IoT Edge solution** コマンドを入力して実行します。 ワークスペース フォルダーを選択し、ソリューション名 (既定値は EdgeSolution) を指定します。 このソリューションの最初のユーザー モジュールとして、C# モジュール (名前は **SampleModule**) を作成します。 また、この最初のモジュールの Docker イメージ リポジトリを指定する必要もあります。 既定のイメージ リポジトリは、ローカル Docker レジストリに基づきます (**localhost:5000/<first module name>**)。 これは、Azure Container Registry または Docker Hub に変更できます。

   > [!NOTE]
   > ローカル Docker レジストリを使用している場合は、レジストリが実行されていることを確認します。 コンソール ウィンドウで次のコマンドを入力します。
   > 
   > `docker run -d -p 5000:5000 --restart=always --name registry registry:2`

2. VS Code ウィンドウによって、ご自身の IoT Edge ソリューション ワークスペースが読み込まれます。 ルート フォルダーには、**modules** フォルダー、**.vscode** フォルダー、および配置マニフェスト テンプレート ファイルがあります。 デバッグ構成は、.vscode フォルダーにあります。 すべてのユーザー モジュール コードは、modules フォルダーのサブフォルダーです。 deployment.template.json ファイルは、配置マニフェスト テンプレートです。 このファイルのパラメーターの中には、すべてのモジュール フォルダーにある module.json ファイルから解析されるものがあります。

3. このソリューション プロジェクトに 2 つ目のモジュールを追加します。 コマンド **Edge: Add IoT Edge module** を入力して実行します。 更新する配置テンプレート ファイルを選択します。 **SampleFunction** という名前の **Azure Function - C#** モジュールと、その Docker イメージ リポジトリを選択します。

4. deployment.template.json ファイルを開きます。 ファイルで 3 つのモジュールとランタイムが宣言されていることを確認します。 メッセージは、tempSensor モジュールから生成されます。 メッセージは、SampleModule および SampleFunction モジュールを介して直接パイプされた後、IoT hub に送信されます。 

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

1. VS Code **コマンド パレット**で、**Edge: Build IoT Edge solution** コマンドを入力して実行します。 このコマンドは、各モジュール フォルダーの module.json ファイルに基づいて、各モジュールの Docker イメージのビルド、コンテナー化、プッシュを開始します。 その後、コマンドは deployment.template.json ファイルに必要な値を渡し、config フォルダーからの情報で deployment.json ファイルを生成します。 VS Code の統合ターミナルに、ビルドの進行状況が表示されます。

2. Azure IoT Hub **Device Explorer** で、IoT Edge デバイス ID を右クリックし、**[Create deployment for Edge device]\(Edge デバイスの展開の作成\)** コマンドを選択します。 config フォルダーの deployment.json ファイルを選択します。 VS Code の統合ターミナルに、正常に作成された展開と配置 ID が表示されます。

3. 開発用コンピューター上で IoT Edge デバイスをシミュレートしている場合は、数分後にすべてのモジュール イメージ コンテナーが開始されたことが表示されます。

## <a name="view-the-generated-data"></a>生成されたデータを表示する

1. IoT ハブに到着するデータを監視するには、**[表示]** > **[コマンド パレット]** の順に選択します。 **[IoT: Start monitoring D2C message]\(IoT: D2C メッセージの監視を開始する\)** コマンドを選択します。 
2. データの監視を停止するには、**コマンド パレット**で **[IoT: Stop monitoring D2C message]\(IoT: D2C メッセージの監視を停止\)** を使用します。 

## <a name="next-steps"></a>次の手順

Visual Studio Code での Azure IoT Edge の開発に関する他のシナリオについては、次の記事をご覧ください。

* [VS Code での C# モジュールのデバッグ](how-to-vscode-debug-csharp-module.md)
* [VS Code での C# 関数のデバッグ](how-to-vscode-debug-azure-function.md)
