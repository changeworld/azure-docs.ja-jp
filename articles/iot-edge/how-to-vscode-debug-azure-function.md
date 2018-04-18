---
title: Visual Studio Code を使用して Azure IoT Edge で Azure Functions をデバッグする | Microsoft Docs
description: VS Code で Azure IoT Edge を使用して C# Azure Functions をデバッグします。
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 3/20/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 8c266a01375bf74fd4df9290255e84bc28e6089c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="use-visual-studio-code-to-debug-azure-functions-with-azure-iot-edge"></a>Visual Studio Code を使用して Azure IoT Edge で Azure Functions をデバッグする

この記事では、主要開発ツールとして [Visual Studio Code](https://code.visualstudio.com/) を使用して、IoT Edge で Azure Functions をデバッグする手順について詳しく説明します。

## <a name="prerequisites"></a>前提条件
この記事では、Windows または Linux を実行しているコンピューターまたは仮想マシンを開発用マシンとして使用していることを前提としています。 別の物理デバイスを IoT Edge デバイスとして使用することも、開発用マシンで IoT Edge デバイスをシミュレートすることもできます。

この記事のガイダンスに従う前に、「[Visual Studio Code で複数のモジュールを含む IoT Edge ソリューションを開発する](tutorial-multiple-modules-in-vscode.md)」の手順を完了してください。 その後、次の項目を準備する必要があります。
- 開発用マシンで実行されているローカル Docker レジストリ。 プロトタイプ作成とテストのために、ローカル Docker レジストリを使用することをお勧めします。 各モジュール フォルダーの `module.json` ファイルでコンテナー レジストリを更新できます。
- Azure 関数モジュール サブフォルダーを含む IoT Edge ソリューション プロジェクト ワークスペース。
- 関数コードを含む `run.csx` ファイル。
- 開発用マシンで実行されている Edge ランタイム。

## <a name="build-your-iot-edge-function-module-for-debugging-purpose"></a>デバッグのために IoT Edge 関数モジュールをビルドする
1. デバッグを開始するには、**Dockerfile.amd64.debug** を使用して Docker イメージをリビルドし、Edge ソリューションをもう一度配置する必要があります。 VS Code エクスプローラーで `deployment.template.json` ファイルに移動します。 末尾に `.debug` を追加し、関数画像の URL を更新します。

    ![デバッグ イメージのビルド](./media/how-to-debug-csharp-function/build-debug-image.png)

2. ソリューションをリビルドします。 VS Code コマンド パレットで、**Edge: Build IoT Edge solution** コマンドを入力して実行します。

3. Azure IoT Hub Device Explorer で、IoT Edge デバイス ID を右クリックし、**[Create deployment for Edge device]\(Edge デバイスの展開の作成\)** を選択します。 `config` フォルダーの `deployment.json` を選択します。 これにより、VS Code 統合ターミナルで、展開が正常に作成されていることを配置 ID によって確認できます。

> [!NOTE]
> VS Code Docker エクスプローラーで、あるいは端末で `docker images` コマンドを実行することでコンテナーの状態を確認できます。

## <a name="start-debugging-c-function-in-vs-code"></a>VS Code で C# 関数のデバッグを開始する
1. VS Code では、ワークスペースの `.vscode` フォルダーにある `launch.json` ファイルにデバッグ構成情報が保管されます。 この `launch.json` ファイルは、新しい IoT Edge ソリューションを作成したときに生成されました。 デバッグに対応している新しいモジュールを追加するたびに更新されます。 デバッグ ビューに移動し、該当する構成ファイルを選択します。
    ![デバッグ構成を選択する](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. `run.csx` に移動します。 関数にブレークポイントを追加します。

3. [デバッグ開始] ボタンをクリックするか **F5** キーを押して、アタッチするプロセスを選択します。

4. VS Code デバッグ ビューの左側のパネルに変数が表示されます。 


> [!NOTE]
> 上記の例は、コンテナー上の .Net Core IoT Edge 関数をデバッグする方法を示しています。 これは、コンテナー イメージに VSDBG (.NET Core コマンド ライン デバッガー) が含まれ、イメージをビルドする、デバッグ バージョンの `Dockerfile.amd64.debug` に基づいています。 C# 関数のデバッグを終了したら、稼働準備済みの IoT Edge 関数では、VSDBG が含まれていない `Dockerfile` を直接使用するか、カスタマイズすることをお勧めします。

## <a name="next-steps"></a>次の手順


[Visual Studio Code を使用して Azure IoT Edge で C# モジュールをデバッグする](how-to-vscode-debug-csharp-module.md)

