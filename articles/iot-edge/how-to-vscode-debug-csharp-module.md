---
title: "Visual Studio Code を使用して Azure IoT Edge で C# モジュールをデバッグする | Microsoft Docs"
description: "Visual Studio Code で Azure IoT Edge を使用して C# モジュールをデバッグします。"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 5ed517cf8d70cd279a55b79ad448709116cf511b
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/24/2018
---
# <a name="use-visual-studio-code-to-debug-a-c-module-with-azure-iot-edge"></a>Visual Studio Code を使用して Azure IoT Edge で C# モジュールをデバッグする
この記事では、主要開発ツールとして [Visual Studio Code](https://code.visualstudio.com/) を使用して、Azure IoT Edge モジュールをデバッグする手順について詳しく説明します。

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、Windows または Linux を実行しているコンピューターまたは仮想マシンを開発用マシンとして使用していることを前提としています。 別の物理デバイスを IoT Edge デバイスとして使用することも、開発用マシンで IoT Edge デバイスをシミュレートすることもできます。

このガイダンスを開始する前に、次のチュートリアルを完了してください。
- [Visual Studio Code を使用して Azure IoT Edge で C# モジュールを開発する](how-to-vscode-develop-csharp-module.md)

上記のチュートリアルを終了したら、次のものを準備してください。
- 開発用マシンで実行されているローカル Docker レジストリ。 これは、プロトタイプの作成とテストの実行で使用します。
- 最新のフィルター モジュール コードが含まれた `Program.cs` ファイル。
- センサー モジュールとフィルター モジュールの最新の `deployment.json` ファイル。
- 開発用マシンで実行される IoT Edge ランタイム。

## <a name="build-your-iot-edge-module-for-debugging"></a>デバッグのために IoT Edge モジュールをビルドする
1. デバッグを開始するには、**dockerfile.debug** を使用して Docker イメージをリビルドし、IoT Edge ソリューションをもう一度配置します。 Visual Studio Code エクスプローラーで、Docker フォルダーを選択して開きます。 次に、**linux-x64** フォルダーを選択します。**Dockerfile.debug** を右クリックし、**[Build IoT Edge module Docker image]\(IoT Edge モジュール Docker イメージのビルド\)** を選択します。

    ![VS Code エクスプローラーのスクリーンショット](./media/how-to-debug-csharp-module/build-debug-image.png)

3. **[フォルダーの選択]** ウィンドウで、**./bin/Debug/netcoreapp2.0/publish** を参照するか入力します。 次に、**[Select Folder as EXE_DIR]\(EXE_DIR としてフォルダーを選択\)** を選択します。
4. VS Code ウィンドウの上部にあるポップアップ テキスト ボックスで、イメージの名前を入力します。 たとえば、「 `<your container registry address>/filtermodule:latest`」のように入力します。 ローカル レジストリにデプロイする場合は、`localhost:5000/filtermodule:latest` のようになります。
5. イメージを Docker リポジトリにプッシュします。 **[Edge: Push IoT Edge module Docker image]\(Edge: IoT Edge モジュール Docker イメージをプッシュ\)** コマンドを使用し、VS Code ウィンドウの上部にあるポップアップ テキスト ボックスにイメージの URL を入力します。 前の手順で使用したのと同じイメージの URL を使用してください。
6. `deployment.json` を再利用して再デプロイできます。 コマンド パレットで、「**Edge: Restart Edge\(Edge: Edge の再起動\)**」と入力して選択し、フィルター モジュールをデバッグ バージョンで実行します。

## <a name="start-debugging-in-vs-code"></a>VS Code でデバッグを開始する
1. VS Code デバッグ ウィンドウに移動します。 **F5** キーを押し、**[IoT Edge(.NET Core)]** を選択します。

    ![VS Code デバッグ ウィンドウのスクリーンショット](./media/how-to-debug-csharp-module/f5-debug-option.png)

2. `launch.json` で、**[Debug IoT Edge Custom Module (.NET Core)]\(IoT Edge カスタム モジュールのデバッグ (.NET Core)\)** セクションに移動します。 **[pipeArgs]** で、`<container_name>` を入力します。 このチュートリアルでは `filtermodule` になります。

    ![VS Code launch.json のスクリーンショット](./media/how-to-debug-csharp-module/add-container-name.png)

3. **Program.cs** に移動します。 `method static async Task<MessageResponse> FilterModule(Message message, object userContext)` にブレークポイントを追加します。
4. もう一度 **F5** を押し、アタッチ先のプロセスを選択します。 このチュートリアルでは、プロセス名は `FilterModule.dll` になります。

    ![VS Code デバッグ ウィンドウのスクリーンショット](./media/how-to-debug-csharp-module/attach-process.png)

5. VS Code デバッグ ウィンドウの左側のパネルに変数が表示されます。 

> [!NOTE]
> 前の例は、コンテナー上の .NET Core IoT Edge モジュールをデバッグする方法を示しています。 これは、コンテナー イメージのビルド中に VSDBG (.NET Core コマンド ライン デバッガー) を含める、デバッグ バージョンの `Dockerfile.debug` に基づいています。 C# モジュールのデバッグを終了した後、稼働準備済みの IoT Edge モジュールでは、VSDBG が含まれていない `Dockerfile` を直接使用するか、カスタマイズすることをお勧めします。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、デバッグ用の IoT Edge モジュールを作成し、IoT Edge デバイスにデプロイしました。 VS Code でデバッグを開始しました。 Azure IoT Edge を VS Code で開発する際の他のシナリオの詳細については、次の記事を参照してください。 

> [!div class="nextstepaction"]
> [VS Code での C# モジュールの開発とデプロイ](how-to-vscode-develop-csharp-module.md)
