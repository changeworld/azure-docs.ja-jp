---
title: VS Code で Azure IoT Edge の複数のモジュールをデバッグする | Microsoft Docs
description: Visual Studio Code を使用して Azure IoT Edge で複数のモジュールをデバッグする
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 38c66129ac8244d18b0f94c1485c785015e29ab4
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049593"
---
# <a name="use-visual-studio-code-to-debug-multiple-modules-with-azure-iot-edge"></a>Visual Studio Code を使用して Azure IoT Edge で複数のモジュールをデバッグする
この記事では、[Visual Studio (VS) Code](https://code.visualstudio.com/) を使用して、IoT Edge 上の複数のモジュールをデバッグする手順について詳しく説明します。

## <a name="prerequisites"></a>前提条件
チュートリアル「[Visual Studio Code で複数のモジュールを含む IoT Edge ソリューションを開発する](tutorial-multiple-modules-in-vscode.md)」を完了し、IoT Edge デバイスで少なくとも 2 つのモジュールを実行しておきます。

## <a name="multi-target-and-remote-debugging-in-vs-code"></a>VS Code での複数ターゲットとリモート デバッグ
VS Code と Azure IoT Edge 拡張機能では、コンテナーが開発用コンピューターまたはリモート物理 IoT Edge デバイスのどちらで実行していても、コンテナー内のモジュール プロセスをアタッチできます。 コンテナーで実行する複数のモジュールのデバッグでは、異なるコンテナー内の複数のプロセスを実際にアタッチします。 VS Code の[複数ターゲット デバッグ](https://code.visualstudio.com/docs/editor/debugging#_multitarget-debugging)は、複数のモジュールをデバッグするときに使用できます。

   > [!TIP]
   > モジュール コンテナーがリモート物理 IoT Edge デバイスで実行されている場合は、開発用コンピューター上の Docker エンジンがリモート Docker ホストと通信できるように、[Docker マシン](https://docs.docker.com/machine/overview/)を設定することが必要な場合があります。

### <a name="build-your-iot-edge-modules-for-debugging-purpose"></a>デバッグのために IoT Edge モジュールをビルドする
1. 複数モジュール デバッグを開始するには、**Dockerfile.amd64.debug** を使用して Docker イメージをリビルドし、Edge ソリューションをもう一度デプロイする必要があります。 VS Code エクスプローラーで `deployment.template.json` ファイルに移動します。 末尾に `.debug` を追加し、イメージの URL を更新します。 `.debug` のモジュール イメージが少なくとも 2 つ必要です。 前のチュートリアルのソリューションで作業している場合は、C# 関数モジュールと C# モジュールがあるはずです。 最後に `.debug` を追加することによってこれら 2 つのイメージの URL を更新、このファイルを保存します。 
2. ソリューションをリビルドします。 VS Code コマンド パレットで、**Azure IoT Edge: Build IoT Edge solution** コマンドを入力して実行します。
3. Azure IoT Hub Device Explorer で、IoT Edge デバイス ID を右クリックし、**[Create deployment for Edge device]\(Edge デバイスの展開の作成\)** を選択します。 `config` フォルダーにある `deployment.json` ファイルを選びます。 これにより、VS Code 統合ターミナルで、展開が正常に作成されていることを配置 ID によって確認できます。

VS Code Docker エクスプローラーを使用して、または端末で `docker ps` コマンドを実行して、コンテナーの状態を確認できます。

### <a name="start-debugging-c-function-in-vs-code"></a>VS Code で C# 関数のデバッグを開始する
1. VS Code では、ワークスペースの `.vscode` フォルダーにある `launch.json` ファイルにデバッグ構成情報が保管されます。 この `launch.json` ファイルは、新しい IoT Edge ソリューションを作成するときに生成されたものです。 デバッグに対応している新しいモジュールを追加するたびに更新されます。 デバッグ ビューに移動し、C# 関数モジュールのリモート デバッグ用に該当する構成ファイルを選択します。
2. `run.csx` に移動します。 関数にブレークポイントを追加します。
3. **[デバッグ開始]** ボタンをクリックするか **F5** キーを押して、アタッチするプロセスを選択します。
4. VS Code デバッグ ビューの左側のパネルに変数が表示されます。 

### <a name="start-debugging-c-module-at-the-same-time-in-vs-code"></a>VS Code で C# モジュールのデバッグを同時に開始する
1. VS Code コマンド パレットで、「Workspace: Duplicate Workspace in New Window」コマンドを入力して実行します。 同じワークスペースで新しい VS Code ウィンドウが開始します。
2. デバッグ ビューに移動し、C# モジュールのリモート デバッグ用に該当する構成ファイルを選択します。
3. `program.cs` に移動します。 C# モジュールにブレークポイントを追加します。
4. **[デバッグ開始]** ボタンをクリックするか **F5** キーを押して、アタッチするプロセスを選択します。
5. VS Code デバッグ ビューの左側のパネルに変数が表示されます。 

### <a name="see-variables-in-multiple-debugging-windows"></a>複数のデバッグ ウィンドウで変数を表示する
1. これで、少なくとも 2 つのデバッグ セッションが 2 つの VS Code ウィンドウで実行するようになりました。 ブレークポイントのいずれかにヒットするはずです。
2. `F10` キーを押すか、**デバッグ ツール バー**の [ステップ オーバー] ボタンをクリックします。
3. 別の VS Code ウィンドウでブレークポイントがヒットするはずです。 
4. 上記の 2 つの手順を続けると、複数の VS Code デバッグ ウィンドウで複数のモジュールの変数を確認できます。

> [!NOTE]
> 上の例では、Azure IoT Edge で複数のモジュールをデバッグする方法が示されています。 これは、コンテナー イメージに VSDBG (.NET Core コマンド ライン デバッガー) が含まれ、イメージをビルドする、デバッグ バージョンの `Dockerfile.amd64.debug` に基づいています。 C# 関数のデバッグを終了したら、稼働準備済みの IoT Edge 関数では、VSDBG が含まれていない `Dockerfile` を直接使用するか、カスタマイズすることをお勧めします。

## <a name="next-steps"></a>次の手順

モジュールの構築が終ったら、[Visual Studio Code から Azure IoT Edge モジュールを配置する](how-to-deploy-modules-vscode.md)方法を確認してください
