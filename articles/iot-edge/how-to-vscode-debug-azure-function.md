---
title: "Visual Studio Code を使用して Azure IoT Edge で Azure Functions をデバッグする | Microsoft Docs"
description: "VS Code で Azure IoT Edge を使用して C# Azure Functions をデバッグします。"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: db86a08a19e97f8f415849aa060fe87d77cccf68
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2018
---
# <a name="use-visual-studio-code-to-debug-azure-functions-with-azure-iot-edge"></a>Visual Studio Code を使用して Azure IoT Edge で Azure Functions をデバッグする

この記事では、主要開発ツールとして [Visual Studio Code](https://code.visualstudio.com/) を使用して、IoT Edge で Azure Functions をデバッグする手順について詳しく説明します。

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、Windows または Linux を実行しているコンピューターまたは仮想マシンを開発用マシンとして使用していることを前提としています。 別の物理デバイスを IoT Edge デバイスとして使用することも、開発用マシンで IoT Edge デバイスをシミュレートすることもできます。

このガイダンスを開始する前に、次のチュートリアルを完了していることを確認してください。
- [Visual Studio Code を使用して Azure IoT Edge で Azure Functions を開発およびデプロイする](how-to-vscode-develop-azure-function.md)

上記のチュートリアルを終了したら、次のものを準備してください。
- 開発用マシンで実行されているローカル Docker レジストリ。 プロトタイプ作成とテストのために、ローカル Docker レジストリを使用することをお勧めします。
- 最新のフィルター関数コードが含まれた `run.csx` ファイル。
- センサー モジュールとフィルター関数モジュールの最新の `deployment.json` ファイル。
- 開発用マシンで実行されている Edge ランタイム。

## <a name="build-your-iot-edge-module-for-debugging-purpose"></a>デバッグのために IoT Edge モジュールをビルドする
1. デバッグを開始するには、**dockerfile.debug** を使用して Docker イメージをリビルドし、Edge ソリューションをもう一度配置する必要があります。 VS Code エクスプローラーで、Docker フォルダーをクリックして開きます。 次に、`linux-x64` フォルダーをクリックし、**Dockerfile.debug** を右クリックして、**[Build IoT Edge module Docker image]\(IoT Edge モジュール Docker イメージのビルド\)** をクリックします。

    ![デバッグ イメージのビルド](./media/how-to-debug-csharp-function/build-debug-image.png)

2. **[フォルダーの選択]** ウィンドウで、**FilterFunction** プロジェクトに移動し、**[Select Folder as EXE_DIR]\(EXE_DIR としてフォルダーを選択\)** をクリックします。
3. VS Code ウィンドウの上部にあるポップアップ テキスト ボックスで、イメージの名前を入力します。 たとえば、「 `<your container registry address>/filterfunction:latest`」のように入力します。 ローカル レジストリにデプロイする場合は、`localhost:5000/filterfunction:latest` のようになります。

    ![イメージのプッシュ](./media/how-to-debug-csharp-function/push-image.png)

4. イメージを Docker リポジトリにプッシュします。 **[Edge: Push IoT Edge module Docker image]\(Edge: IoT Edge モジュール Docker イメージをプッシュ\)** コマンドを使用し、VS Code ウィンドウの上部にあるポップアップ テキスト ボックスにイメージの URL を入力します。 前の手順で使用したイメージの URL を使用してください。
5. `deployment.json` を再利用して再デプロイできます。 コマンド パレットで、「**Edge: Restart Edge\(Edge: Edge の再起動\)**」と入力して選択し、フィルター関数をデバッグ バージョンで実行します。

## <a name="start-debugging-in-vs-code"></a>VS Code でデバッグを開始する
1. VS Code デバッグ ウィンドウに移動します。 **F5** キーを押し、**[IoT Edge(.Net Core)]** を選択します。

    ![F5 キーを押す](./media/how-to-debug-csharp-function/f5-debug-option.png)

2. `launch.json` で、**Debug IoT Edge Function (.NET Core)** セクションに移動し、`pipeArgs` の下に `<container_name>` を入力します。このチュートリアルでは、`filterfunction` になります。

    ![launch.json を更新する](./media/how-to-debug-csharp-function/update-launch-json.png)

3. run.csx に移動します。 関数にブレークポイントを追加します。
4. [デバッグ] ウィンドウに移動し (Ctrl + Shift + D キー)、ドロップ ダウンリストから **[Debug IoT Edge Function (.NET Core)]\(IoT Edge 関数のデバッグ (.NET Core)\)** を選択します。 

    ![デバッグ モードを選択する](./media/how-to-debug-csharp-function/choose-debug-mode.png)

5. [デバッグ開始] ボタンをクリックするか **F5** キーを押して、アタッチするプロセスを選択します。

    ![関数プロセスをアタッチする](./media/how-to-debug-csharp-function/attach-function-process.png)

6. VS Code デバッグ ウィンドウの左側のパネルに変数が表示されます。 

> [!NOTE]
> 上記の例は、コンテナー上の .Net Core IoT Edge 関数をデバッグする方法を示しています。 これは、コンテナー イメージに VSDBG (.NET Core コマンド ライン デバッガー) が含まれ、イメージをビルドする、デバッグ バージョンの `Dockerfile.debug` に基づいています。 C# 関数のデバッグを終了したら、稼働準備済みの IoT Edge 関数では、VSDBG が含まれていない `Dockerfile` を直接使用するか、カスタマイズすることをお勧めします。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Function を作成してデバッグ用に IoT Edge にデプロイし、VS Code でデバッグを開始しました。 次のチュートリアルに進み、VS Code で Azure IoT Edge を開発するときの他のシナリオを確認できます。 

> [!div class="nextstepaction"]
> [VS Code での C# モジュールの開発とデプロイ](how-to-vscode-develop-csharp-module.md)

