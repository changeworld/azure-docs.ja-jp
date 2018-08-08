---
title: Azure IoT Edge の Azure 関数モジュールをデバッグする | Microsoft Docs
description: Visual Studio Code を使用して Azure IoT Edge で C# Azure 関数をデバッグする
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: bf2d1af66cc3ecc35dafe3bcd43bf10399d71641
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346717"
---
# <a name="use-visual-studio-code-to-develop-and-debug-azure-functions-for-azure-iot-edge"></a>Visual Studio Code を使用して Azure IoT Edge 用の Azure 関数を開発およびデバッグする

この記事では、[Visual Studio Code (VS Code)](https://code.visualstudio.com/) を使用して Azure IoT Edge の Azure 関数をデバッグする方法について説明します。

## <a name="prerequisites"></a>前提条件
この記事では、Windows または Linux を実行しているコンピューターまたは仮想マシンを開発用マシンとして使用していることを前提としています。 IoT Edge デバイスとして、別の物理デバイスを指定できます。 または、開発マシンで IoT Edge デバイスをシミュレートできます。

> [!NOTE]
> このデバッグに関する記事では、モジュール コンテナーのプロセスをアタッチして、これを VS Code でデバッグする方法を示します。 デバッグできるのは、Linux amd64 コンテナー内の C# 関数だけです。 Visual Studio Code のデバッグ機能をよく知らない場合は、[デバッグ](https://code.visualstudio.com/Docs/editor/debugging)に関するページを確認してください。 

この記事では、開発ツールとして主に Visual Studio Code を使います。 VS Code をインストールします。 その後、必要な拡張機能を追加します。 

* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [C# 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Docker 拡張機能](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

モジュールを作成するには、プロジェクト フォルダーを構築する .NET、モジュール イメージを構築する Docker、およびモジュール イメージを保持するコンテナー レジストリが必要です。
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)
* お使いの開発用マシン上の [Docker Community Edition](https://docs.docker.com/install/) 
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) または [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > プロトタイプおよびテスト目的で、クラウド レジストリの代わりに Docker のローカル レジストリを使用できます。 

デバイス上でお使いのモジュールをテストするには、1 つ以上の IoT Edge デバイスを使用するアクティブな IoT ハブが必要になります。 コンピューターを IoT Edge デバイスとして使用するには、[Windows](quickstart.md) または [Linux](quickstart-linux.md) 用のクイックスタートの手順に従ってください。 

## <a name="create-a-new-solution-template"></a>新しいソリューション テンプレートを作成する

これらの手順を実行して、1 つの C# 関数モジュールを持つ IoT Edge ソリューションを作成します。 各ソリューションは複数のモジュールを持つことができます。

1. Visual Studio Code で、**[ビュー]** > **[統合端末]** の順に選択します。
3. **[ビュー]** > **[コマンド パレット]** の順に選択します。
4. コマンド パレットで、**Azure IoT Edge: New IoT Edge Solution** コマンドを入力して実行します。 

   ![新しい IoT Edge ソリューションを実行する](./media/how-to-develop-csharp-module/new-solution.png)

5. 新しいソリューションの作成先フォルダーを参照します。 **[フォルダーの選択]** を選択します。 
6. ソリューションの名前を入力します。 
7. ソリューション内の最初のモジュールのテンプレートとして、**Azure Functions - C#** を選択します。
8. モジュールの名前を入力します。 コンテナー レジストリ内に一意の名前を選択します。 
9. モジュールのイメージ リポジトリを指定します。 VS Code によってモジュール名に **localhost:5000** と自動的に入力されます。 独自のレジストリ情報に置き換えます。 テスト用に Docker のローカル レジストリを使用する場合、**localhost** で問題ありません。 Azure Container Registry を使用する場合、お使いのレジストリの設定のログイン サーバーを使用します。 ログイン サーバーは **\<レジストリ名\>.azurecr.io** のようになります。

VS Code は指定された情報を取得し、Azure Functions プロジェクトを含む IoT Edge ソリューションを作成して、それを新しいウィンドウに読み込みます。

ソリューション内には 4 つの項目があります。 

* **.vscode** フォルダーにはデバッグ構成が含まれています。
* **modules** フォルダーにはモジュールごとのサブフォルダーが含まれています。 この時点では 1 つだけです。 ただし、コマンド パレットでコマンド **Azure IoT Edge: Add IoT Edge Module** を実行することで追加できます。
* **.env** ファイルには環境変数の一覧が表示されます。 レジストリが Azure Container Registry の場合、Azure Container Registry のユーザー名とパスワードがあります。 

   >[!NOTE]
   >環境ファイルは、モジュールのイメージ リポジトリを指定した場合にのみ作成されます。 localhost の既定値を受け入れてローカルでテストおよびデバッグする場合は、環境変数を宣言する必要はありません。 

* **deployment.template.json** ファイルには新しいモジュールと、テストに使用できるデータをシミュレートする **tempSensor** のサンプル モジュールの一覧が表示されます。 配置マニフェストがどのように機能するかについて詳しくは、「[配置マニフェストを使ってモジュールをデプロイしルートを確立する](module-composition.md)」をご覧ください。

## <a name="develop-your-module"></a>モジュールの開発

ソリューションに付属する既定の Azure 関数コードは、**modules** > **\<お使いのモジュール名\>** > **EdgeHubTrigger-Csharp** > **run.csx** にあります。 モジュールと deployment.template.json ファイルが設定されます。これでソリューションをビルドし、それをコンテナー レジストリにプッシュして、デバイスにデプロイすることで、コードを操作することなくテストを開始できます。 モジュールはソース (このケースでは、データをシミュレートする tempSensor モジュール) から入力を取得して、IoT Hub に送信するようにビルドされています。 

Azure 関数テンプレートを独自のコードでカスタマイズする準備ができたら、[Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md) を使用して、セキュリティ、デバイス管理、信頼性など、IoT ソリューションの主なニーズに対処するモジュールをビルドします。 

## <a name="build-your-module-for-debugging"></a>デバッグ用のモジュールをビルドする
1. デバッグを開始するには、**Dockerfile.amd64.debug** を使用して Docker イメージをリビルドし、Edge ソリューションをもう一度デプロイします。 VS Code エクスプローラーで `deployment.template.json` ファイルに移動します。 末尾に `.debug` を追加し、関数イメージの URL を更新します。

    ![デバッグ イメージのビルド](./media/how-to-debug-csharp-function/build-debug-image.png)

2. ソリューションをリビルドします。 VS Code コマンド パレットで、**Azure IoT Edge: Build IoT Edge solution** コマンドを入力して実行します。
3. Azure IoT Hub Device Explorer で、IoT Edge デバイス ID を右クリックし、**[Create deployment for Edge device]\(Edge デバイスのデプロイの作成\)** を選択します。 `config` フォルダー内の `deployment.json` ファイルを選びます。 VS Code 統合ターミナルで、デプロイが正常に作成されていることをデプロイ ID によって確認できます。

VS Code Docker エクスプローラーを使用して、または端末で `docker images` コマンドを実行して、コンテナーの状態を確認します。

## <a name="start-debugging-c-functions-in-vs-code"></a>VS Code で C# 関数のデバッグを開始する
1. VS Code では、ワークスペースの `.vscode` フォルダーにある `launch.json` ファイルにデバッグ構成情報が保管されます。 この `launch.json` ファイルは、新しい IoT Edge ソリューションを作成するときに生成されたものです。 デバッグに対応している新しいモジュールを追加するたびに更新されます。 デバッグ ビューに移動します。 該当するデバッグ構成ファイルを選択します。 デバッグ オプション名は、**ModuleName Remote Debug (.NET Core)** のようになります。

   ![デバッグ構成を選択します](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. `run.csx` に移動します。 関数にブレークポイントを追加します。
3. **[デバッグの開始]** を選択するか、**F5** を押します。 アタッチするプロセスを選択します。
4. VS Code のデバッグ ビューの左側のパネルに変数が表示されます。 


> [!NOTE]
> この例は、コンテナー上の .Net Core IoT Edge 関数をデバッグする方法を示しています。 これは、コンテナー イメージのビルド中に .NET Core コマンド ライン デバッガー VSDBG を含める、デバッグ バージョンの `Dockerfile.amd64.debug` に基づいています。 C# 関数をデバッグしたら、稼働準備済みの IoT Edge 関数では、VSDBG が含まれていない `Dockerfile` を直接使用するか、カスタマイズすることをお勧めします。

## <a name="next-steps"></a>次の手順

モジュールをビルドしたら、[Visual Studio Code から Azure IoT Edge モジュールをデプロイする](how-to-deploy-modules-vscode.md)方法を確認してください。

お使いの IoT Edge デバイス用のモジュールを開発する方法について詳しくは、「[Azure IoT Hub SDK の概要と使用方法](../iot-hub/iot-hub-devguide-sdks.md)」をご覧ください。
