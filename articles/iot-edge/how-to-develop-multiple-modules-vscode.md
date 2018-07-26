---
title: VS Code で複数の Azure IoT Edge モジュールを使用する | Microsoft Docs
description: Visual Studio Code に IoT 拡張機能を使用して、Azure IoT Edge の複数のモジュールを開発します
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 31fe210b87a052438956d813db0d104e0f2cdb6e
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041254"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code"></a>Visual Studio Code で複数のモジュールを含む IoT Edge ソリューションを開発する

Visual Studio Code を使用して、複数のモジュールを含むご自身の Azure IoT Edge ソリューションを開発できます。 この記事では、VS Code でシミュレートされた IoT Edge デバイスのセンサー データをパイプする IoT Edge ソリューションを作成、更新、展開する方法を示します。 

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次の前提条件を満たす必要があります。

- [Visual Studio Code](https://code.visualstudio.com/)
- [Visual Studio Code 用の Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
- [Visual Studio Code 用の C# (OmniSharp を使用) 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)
- 少なくとも 1 つの IoT Edge デバイスを含むアクティブな IoT ハブ

イメージとコンテナーを管理するには、[Docker for VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) と Azure IoT Hub Device Explorer の統合も必要です。

## <a name="create-your-iot-edge-solution"></a>IoT Edge ソリューションの作成

1. Visual Studio コードで、**[表示]** > **[統合ターミナル]** の順に選択して、統合ターミナルを開きます。 

1. VS Code **コマンド パレット**で、**Azure IoT Edge: New IoT Edge solution** コマンドを入力して実行します。 ワークスペース フォルダーを選択し、ソリューション名 (既定値は EdgeSolution) を指定します。 このソリューションの最初のユーザー モジュールとして、C# モジュール (名前は **PipeModule**) を作成します。 C# モジュールの既定のテンプレートはパイプ モジュールであり、アップストリームからダウンストリームへメッセージを直接パイプします。 また、この最初のモジュールの Docker イメージ リポジトリを指定する必要もあります。 既定のイメージ リポジトリは、ローカル Docker レジストリに基づきます (**localhost:5000/<first module name>**)。 これは、Azure Container Registry または Docker Hub に変更できます。 

2. VS Code ウィンドウによって、ご自身の IoT Edge ソリューション ワークスペースが読み込まれます。 ルート フォルダーには、**modules** フォルダー、**.vscode** フォルダー、および配置マニフェスト テンプレート ファイルがあります。 デバッグ構成は、.vscode フォルダーにあります。 すべてのユーザー モジュール コードは、modules フォルダーのサブフォルダーです。 deployment.template.json ファイルは、配置マニフェスト テンプレートです。 このファイルのパラメーターの中には、すべてのモジュール フォルダーにある module.json ファイルから解析されるものがあります。

3. このソリューション プロジェクトに 2 つ目のモジュールを追加します。 現在のソリューションに新しいモジュールを追加する方法は複数あります。 コマンド **Azure IoT Edge: Add IoT Edge module** を入力して実行します。 更新する配置テンプレート ファイルを選択します。 または、モジュール フォルダーを右クリックするか、deployment.template.json ファイルを右クリックして、**[IoT Edge モジュールを追加する]** を選択します。 モジュールの種類を選択するためのドロップダウン リストが表示されます。 **PipeFunction** という名前の **Azure Functions - C#** モジュールと、その Docker イメージ リポジトリを選択します。 C# 関数の既定のテンプレートはパイプ モジュールであり、アップストリームからダウンストリームへメッセージを直接パイプします。

4. deployment.template.json ファイルを開きます。 ファイルで 3 つのモジュールとランタイムが宣言されていることを確認します。 メッセージは、tempSensor モジュールから生成されます。 メッセージは、SampleModule および SampleFunction モジュールを介して直接パイプされた後、IoT hub に送信されます。 

5. 以下の内容でこれらのモジュールのルートを更新します。

   ```json
        "routes": {
          "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/PipeModule/inputs/input1\")",
          "PipeModuleToPipeFunction": "FROM /messages/modules/PipeModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/PipeFunction/inputs/input1\")",
          "PipeFunctionToIoTHub": "FROM /messages/modules/PipeFunction/outputs/output1 INTO $upstream"
        },
   ```

5. このファイルを保存します。

## <a name="build-and-deploy-your-iot-edge-solution"></a>IoT Edge ソリューションをビルドして展開する

1. VS Code **コマンド パレット**で、**Azure IoT Edge: Build IoT Edge solution** コマンドを入力して実行します。 このコマンドは、各モジュール フォルダーの module.json ファイルに基づいて、各モジュールの Docker イメージのビルド、コンテナー化、プッシュを開始します。 その後、コマンドは deployment.template.json ファイルに必要な値を渡し、config フォルダーからの情報で deployment.json ファイルを生成します。 VS Code の統合ターミナルに、ビルドの進行状況が表示されます。 

2. Azure IoT Hub **Device Explorer** で、IoT Edge デバイス ID を右クリックし、**[Create deployment for Edge device]\(Edge デバイスの展開の作成\)** コマンドを選択します。 config フォルダーの deployment.json ファイルを選択します。 VS Code の統合ターミナルに、正常に作成された展開と配置 ID が表示されます。

3. 開発用コンピューター上で IoT Edge デバイスをシミュレートしている場合は、数分後にすべてのモジュール イメージ コンテナーが開始されたことを確認できます。

## <a name="view-the-generated-data"></a>生成されたデータを表示する

1. IoT ハブに到着するデータを監視するには、**[表示]** > **[コマンド パレット]** の順に選択します。 **[IoT: Start monitoring D2C message]\(IoT: D2C メッセージの監視を開始する\)** コマンドを選択します。 
2. データの監視を停止するには、**コマンド パレット**で **[IoT: Stop monitoring D2C message]\(IoT: D2C メッセージの監視を停止\)** を使用します。 

## <a name="next-steps"></a>次の手順

Visual Studio Code での Azure IoT Edge の開発に関する他のシナリオについては、次の記事をご覧ください。

* VS Code のモジュールを開発するには、[C#](how-to-develop-csharp-module.md) または [Node.js](how-to-develop-node-module.md) を使用します。
* VS Code の Azure Functions を開発するには、[C#](how-to-develop-csharp-function.md) を使用します。

お使いの IoT Edge デバイス用のモジュールを開発する方法について詳しくは、「[Azure IoT Hub SDK の概要と使用方法](../iot-hub/iot-hub-devguide-sdks.md)」をご覧ください。