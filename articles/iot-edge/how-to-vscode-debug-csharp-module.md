---
title: Azure IoT Edge を使用して C# モジュールをデバッグする | Microsoft Docs
description: Visual Studio Code を使用して Azure IoT Edge により C# モジュールをデバッグします。
author: shizn
manager: ''
ms.author: xshi
ms.date: 03/18/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5d521a4e5b22222f0b0a5505f14684598d7d5b0a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631396"
---
# <a name="use-visual-studio-code-to-debug-a-c-module-with-azure-iot-edge"></a>Visual Studio Code を使用して Azure IoT Edge で C# モジュールをデバッグする
この記事では、主要開発ツールとして [Visual Studio (VS) Code](https://code.visualstudio.com/) を使用して、Azure IoT Edge モジュールをデバッグする手順について詳しく説明します。

## <a name="prerequisites"></a>前提条件
この記事では、Windows または Linux を実行しているコンピューターまたは仮想マシンを開発用マシンとして使用していることを前提としています。 別の物理デバイスを IoT Edge デバイスとして使用することも、開発用マシンで IoT Edge デバイスをシミュレートすることもできます。

> [!NOTE]
> デバッグできるのは、linux-amd64 コンテナー内の C# モジュールだけです。

この記事のガイダンスに従う前に、「[Visual Studio Code で複数のモジュールを含む IoT Edge ソリューションを開発する](tutorial-multiple-modules-in-vscode.md)」の手順を完了してください。 その後、次の項目を準備する必要があります。
- 開発用マシンで実行されているローカル Docker レジストリ。 プロトタイプ作成とテストのために、ローカル Docker レジストリを使用することをお勧めします。 各モジュール フォルダーの `module.json` ファイルでコンテナー レジストリを更新できます。
- C# モジュール サブフォルダーを含む IoT Edge ソリューション プロジェクト ワークスペース。
- 最新のモジュール コードが含まれる `Program.cs` ファイル。
- 開発用マシンで実行されている Edge ランタイム。

## <a name="build-your-iot-edge-c-module-for-debugging"></a>デバッグのために IoT Edge C# モジュールをビルドする
1. デバッグを開始するには、**Dockerfile.amd64.debug** を使用して Docker イメージをリビルドし、Edge ソリューションをもう一度配置する必要があります。 VS Code エクスプローラーで `deployment.template.json` ファイルに移動します。 末尾に `.debug` を追加し、関数画像の URL を更新します。

2. ソリューションをリビルドします。 VS Code コマンド パレットで、**Edge: Build IoT Edge solution** コマンドを入力して実行します。

3. Azure IoT Hub Device Explorer で、IoT Edge デバイス ID を右クリックし、**[Create deployment for Edge device]\(Edge デバイスの展開の作成\)** を選択します。 `config` フォルダー内の `deployment.json` ファイルを選びます。 これにより、VS Code 統合ターミナルで、展開が正常に作成されていることを配置 ID によって確認できます。

VS Code Docker エクスプローラーで、あるいは端末で `docker images` コマンドを実行することでコンテナーの状態を確認できます。

## <a name="start-debugging-c-module-in-vs-code"></a>VS Code で C# モジュールのデバッグを開始する
1. VS Code では、ワークスペースの `.vscode` フォルダーにある `launch.json` ファイルにデバッグ構成情報が保管されます。 この `launch.json` ファイルは、新しい IoT Edge ソリューションを作成するときに生成されたものです。 デバッグに対応している新しいモジュールを追加するたびに更新されます。 デバッグ ビューに移動し、該当する構成ファイルを選択します。
    ![デバッグ構成を選択する](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. `program.cs` に移動します。 このファイルにブレークポイントを追加します。

3. **[デバッグ開始]** ボタンをクリックするか **F5** キーを押して、アタッチするプロセスを選択します。

4. VS Code デバッグ ビューの左側のパネルに変数が表示されます。 

> [!NOTE]
> 前の例は、コンテナー上の .NET Core IoT Edge モジュールをデバッグする方法を示しています。 これは、コンテナー イメージのビルド中に VSDBG (.NET Core コマンド ライン デバッガー) を含める、デバッグ バージョンの `Dockerfile.debug` に基づいています。 C# モジュールのデバッグを終了した後、稼働準備済みの IoT Edge モジュールでは、VSDBG が含まれていない `Dockerfile` を直接使用するか、カスタマイズすることをお勧めします。

## <a name="next-steps"></a>次の手順

[Visual Studio Code を使用して Azure IoT Edge で Azure Functions をデバッグする](how-to-vscode-debug-azure-function.md)

