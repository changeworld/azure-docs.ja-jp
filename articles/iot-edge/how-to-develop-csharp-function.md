---
title: Azure IoT Edge の Functions モジュールをデバッグする | Microsoft Docs
description: Visual Studio Code を使用して Azure IoT Edge で C# Azure Functions をデバッグする
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5ee5d368bde0f176442a01ba266791f8a9c2cbc3
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052829"
---
# <a name="use-visual-studio-code-to-develop-and-debug-azure-functions-for-azure-iot-edge"></a>Visual Studio Code を使用して Azure IoT Edge 用の Azure Functions を開発およびデバッグする

この記事では、[Visual Studio (VS) Code](https://code.visualstudio.com/) を使用して、IoT Edge 上の Azure Functions をデバッグする手順について詳しく説明します。

## <a name="prerequisites"></a>前提条件
この記事では、Windows または Linux を実行しているコンピューターまたは仮想マシンを開発用マシンとして使用していることを前提としています。 別の物理デバイスを IoT Edge デバイスとして使用することも、開発用マシンで IoT Edge デバイスをシミュレートすることもできます。

> [!NOTE]
> このデバッグに関するチュートリアルでは、モジュール コンテナーにプロセスをアタッチして、これを VS Code でデバッグする方法について説明します。 デバッグできるのは、linux-amd64 コンテナー内の C# モジュールだけです。 Visual Studio Code のデバッグ機能をよく知らない場合は、[デバッグ](https://code.visualstudio.com/Docs/editor/debugging)に関するページを確認してください。 

この記事では、開発ツールとして主に Visual Studio Code を使います。 VS Code をインストールした後、必要な拡張機能を追加します。 

* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [C# 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Docker 拡張機能](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

モジュールを作成するには、プロジェクト フォルダーを構築する .NET、モジュール イメージを構築する Docker、およびモジュール イメージを保持するコンテナー レジストリが必要です。
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。
* 開発用マシン上の [Docker CE](https://docs.docker.com/install/)。 
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) または [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > プロトタイプおよびテスト目的で、クラウド レジストリの代わりに Docker のローカル レジストリを使用できます。 

デバイス上でお使いのモジュールをテストするには、1 つ以上の IoT Edge デバイスを使用するアクティブな IoT ハブが必要になります。 お使いのコンピューターを IoT Edge デバイスとして使用する場合は、[Windows](quickstart.md) または [Linux](quickstart-linux.md) 向けのチュートリアルの手順に従って、これを実現できます。 

## <a name="create-a-new-solution-template"></a>新しいソリューション テンプレートを作成する

次の手順では、C# Function モジュールを 1 つ含む IoT Edge ソリューションを作成する方法を示します。 各ソリューションには複数のモジュールを含めることができます。

1. Visual Studio Code で、**[ビュー]** > **[統合端末]** の順に選択します。
3. **[ビュー]** > **[コマンド パレット]** の順に選択します。
4. コマンド パレットで、**Azure IoT Edge: New IoT Edge Solution** コマンドを入力して実行します。 

   ![新しい IoT Edge ソリューションを実行する](./media/how-to-develop-csharp-module/new-solution.png)

5. 新しいソリューションを作成するフォルダーを参照し、**[フォルダーの選択]** をクリックします。 
6. ソリューションの名前を指定します。 
7. ソリューション内の最初のモジュールのテンプレートとして、**Azure Functions - C#** を選択します。
8. モジュールの名前を指定します。 コンテナー レジストリ内に一意の名前を選択します。 
9. モジュールのイメージ リポジトリを指定します。 モジュール名は VS Code によって自動的に入力されるため、**localhost:5000** を独自のレジストリ情報に置き換えるだけです。 テスト用に Docker のローカル レジストリを使用する場合、localhost で問題ありません。 Azure Container Registry を使用する場合、お使いのレジストリの設定のログイン サーバーを使用します。 ログイン サーバーは **\<レジストリ名\>.azurecr.io** のようになります。

VS Code は指定された情報を取得し、Function プロジェクトを含む IoT Edge ソリューションを作成して、それを新しいウィンドウに読み込みます。

ソリューション内には次の 3 つの項目があります。 

* **.vscode** フォルダーにはデバッグ構成が含まれています。
* **modules** フォルダーにはモジュールごとのサブフォルダーが含まれています。 現在は 1 つだけですが、コマンド パレットでコマンド **Azure IoT Edge: Add IoT Edge Module** を実行することで追加できます。
* **.env** ファイルには環境変数の一覧が表示されます。 レジストリが ACR である場合、現在 ACR のユーザー名とパスワードが入っています。 
* **deployment.template.json** ファイルには新しいモジュールと、テストに使用できるデータをシミュレートする **tempSensor** のサンプル モジュールの一覧が含まれます。 配置マニフェストのしくみについて詳しくは、「[IoT Edge モジュールをどのように使用、構成、および再利用できるかを理解する](module-composition.md)」をご覧ください。

## <a name="build-your-iot-edge-function-module-for-debugging-purpose"></a>デバッグのために IoT Edge 関数モジュールをビルドする
1. デバッグを開始するには、**Dockerfile.amd64.debug** を使用して Docker イメージをリビルドし、Edge ソリューションをもう一度デプロイする必要があります。 VS Code エクスプローラーで `deployment.template.json` ファイルに移動します。 末尾に `.debug` を追加し、関数画像の URL を更新します。

    ![デバッグ イメージのビルド](./media/how-to-debug-csharp-function/build-debug-image.png)

2. ソリューションをリビルドします。 VS Code コマンド パレットで、**Azure IoT Edge: Build IoT Edge solution** コマンドを入力して実行します。
3. Azure IoT Hub Device Explorer で、IoT Edge デバイス ID を右クリックし、**[Create deployment for Edge device]\(Edge デバイスの展開の作成\)** を選択します。 `config` フォルダーにある `deployment.json` ファイルを選びます。 これにより、VS Code 統合ターミナルで、展開が正常に作成されていることを配置 ID によって確認できます。

VS Code Docker エクスプローラーを使用して、または端末で `docker images` コマンドを実行して、コンテナーの状態を確認できます。

## <a name="start-debugging-c-function-in-vs-code"></a>VS Code で C# 関数のデバッグを開始する
1. VS Code では、ワークスペースの `.vscode` フォルダーにある `launch.json` ファイルにデバッグ構成情報が保管されます。 この `launch.json` ファイルは、新しい IoT Edge ソリューションを作成するときに生成されたものです。 デバッグに対応している新しいモジュールを追加するたびに更新されます。 デバッグ ビューに移動し、該当する構成ファイルを選択します。 デバッグ オプション名は、**ModuleName Remote Debug (.NET Core)** のようになります。![デバッグ構成を選択する](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. `run.csx` に移動します。 関数にブレークポイントを追加します。
3. **[デバッグ開始]** ボタンをクリックするか **F5** キーを押して、アタッチするプロセスを選択します。
4. VS Code デバッグ ビューの左側のパネルに変数が表示されます。 


> [!NOTE]
> 上記の例は、コンテナー上の .Net Core IoT Edge 関数をデバッグする方法を示しています。 これは、コンテナー イメージに VSDBG (.NET Core コマンド ライン デバッガー) が含まれ、イメージをビルドする、デバッグ バージョンの `Dockerfile.amd64.debug` に基づいています。 C# 関数のデバッグを終了したら、稼働準備済みの IoT Edge 関数では、VSDBG が含まれていない `Dockerfile` を直接使用するか、カスタマイズすることをお勧めします。

## <a name="next-steps"></a>次の手順

モジュールの構築が終ったら、[Visual Studio Code から Azure IoT Edge モジュールをデプロイする](how-to-deploy-modules-vscode.md)方法を確認してください。
