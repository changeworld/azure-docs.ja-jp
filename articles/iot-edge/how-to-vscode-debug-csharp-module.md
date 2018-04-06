---
title: Visual Studio Code を使用して Azure IoT Edge で C# モジュールをデバッグする | Microsoft Docs
description: Visual Studio Code で Azure IoT Edge を使用して C# モジュールをデバッグします。
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: c2a1acd2c249bdbc92119bc92f055b095f318f00
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
# <a name="use-visual-studio-code-to-debug-a-c-module-with-azure-iot-edge"></a>Visual Studio Code を使用して Azure IoT Edge で C# モジュールをデバッグする
この記事では、主要開発ツールとして [Visual Studio Code](https://code.visualstudio.com/) を使用して、Azure IoT Edge モジュールをデバッグする手順について詳しく説明します。

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、Windows または Linux を実行しているコンピューターまたは仮想マシンを開発用マシンとして使用していることを前提としています。 別の物理デバイスを IoT Edge デバイスとして使用することも、開発用マシンで IoT Edge デバイスをシミュレートすることもできます。

このガイダンスを開始する前に、次のチュートリアルを完了してください。
- [Visual Studio Code で複数のモジュールを含む IoT Edge ソリューションを開発する](tutorial-multiple-modules-in-vscode.md)

上記のチュートリアルを終了したら、次のものを準備してください。
- 開発用マシンで実行されているローカル Docker レジストリ。 プロトタイプ作成とテストのために、ローカル Docker レジストリを使用することをお勧めします。 各モジュール フォルダーの `module.json` ファイルでコンテナー レジストリを更新できます。
- C# モジュール サブフォルダーを含む IoT Edge ソリューション プロジェクト ワークスペース。
- 最新のモジュール コードが含まれる `Program.cs` ファイル。
- 開発用マシンで実行されている Edge ランタイム。

## <a name="build-your-iot-edge-c-module-for-debugging"></a>デバッグのために IoT Edge C# モジュールをビルドする
1. デバッグを開始するには、**Dockerfile.amd64.debug** を使用して Docker イメージをリビルドし、Edge ソリューションをもう一度配置する必要があります。 VS Code エクスプローラーで `deployment.template.json` ファイルに移動します。 末尾に `.debug` を追加し、関数画像の URL を更新します。

2. ソリューションを再ビルドします。 VS Code コマンド パレットで、**Edge: Build IoT Edge solution** コマンドを入力して実行します。

3. Azure IoT Hub Device Explorer で、IoT Edge デバイス ID を右クリックし、**[Create deployment for Edge device]\(Edge デバイスの展開の作成\)** を選択します。 `config` フォルダーの `deployment.json` を選択します。 これにより、VS Code 統合ターミナルで、展開が正常に作成されていることを配置 ID によって確認できます。

> [!NOTE]
> VS Code Docker エクスプローラーで、あるいは端末で `docker images` コマンドを実行することでコンテナーの状態を確認できます。

## <a name="start-debugging-c-module-in-vs-code"></a>VS Code で C# モジュールのデバッグを開始する
1. VS Code では、ワークスペースの `.vscode` フォルダーにある `launch.json` ファイルにデバッグ構成情報が保管されます。 この `launch.json` ファイルは、新しい IoT Edge ソリューションを作成したときに生成されました。 デバッグに対応している新しいモジュールを追加するたびに更新されます。 デバッグ ビューに移動し、該当する構成ファイルを選択します。
    ![デバッグ構成を選択する](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. `program.cs` に移動します。 このファイルにブレークポイントを追加します。

3. [デバッグ開始] ボタンをクリックするか **F5** キーを押して、アタッチするプロセスを選択します。

4. VS Code デバッグ ビューの左側のパネルに変数が表示されます。 

> [!NOTE]
> 前の例は、コンテナー上の .NET Core IoT Edge モジュールをデバッグする方法を示しています。 これは、コンテナー イメージのビルド中に VSDBG (.NET Core コマンド ライン デバッガー) を含める、デバッグ バージョンの `Dockerfile.debug` に基づいています。 C# モジュールのデバッグを終了した後、稼働準備済みの IoT Edge モジュールでは、VSDBG が含まれていない `Dockerfile` を直接使用するか、カスタマイズすることをお勧めします。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、デバッグ用の IoT Edge モジュールを作成し、IoT Edge デバイスにデプロイしました。 VS Code でデバッグを開始しました。 Azure IoT Edge を VS Code で開発する際の他のシナリオの詳細については、次の記事を参照してください。 

> [!div class="nextstepaction"]
> [Visual Studio Code で複数のモジュールを含む IoT Edge ソリューションを開発する](tutorial-multiple-modules-in-vscode.md)

