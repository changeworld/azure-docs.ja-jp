---
title: Azure IoT Edge の Node.js モジュールをデバッグする | Microsoft Docs
description: Visual Studio Code を使用して Azure IoT Edge の Node.js モジュールを開発およびデバッグする
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: a5ab49beed79a8ea3a7ded0848c09acad27a5fb1
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390539"
---
# <a name="develop-and-debug-nodejs-modules-with-azure-iot-edge-for-visual-studio-code"></a>Visual Studio Code の Azure IoT Edge で Node.js モジュールを開発およびデバッグする

ビジネス ロジックを送信して、Azure IoT Edge 用のモジュールにこのロジックを変換することで、エッジで処理できます。 この記事では、C# モジュールを開発するための主要な開発ツールとして Visual Studio Code (VS Code) を使用するための詳細な手順について説明します。

## <a name="prerequisites"></a>前提条件
この記事では、Windows または Linux を実行しているコンピューターまたは仮想マシンを開発用マシンとして使用していることを前提としています。 別の物理デバイスを IoT Edge デバイスとして使用することも、開発用マシンで IoT Edge デバイスをシミュレートすることもできます。

> [!NOTE]
> このデバッグに関するチュートリアルでは、モジュール コンテナーのプロセスをアタッチして、これを VS Code でデバッグする方法について説明します。 Node.js モジュールは linux-amd64、windows、および arm32 のコンテナーでデバッグできます。 Visual Studio Code のデバッグ機能を使い慣れていない場合は、[デバッグ](https://code.visualstudio.com/Docs/editor/debugging)に関する記事をご覧ください。 

この記事ではメインの開発ツールとして Visual Studio Code を使用しているため、VS Code をインストールしてから必要な拡張機能を追加してください。
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Docker 拡張機能](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

モジュールを作成するには、プロジェクト フォルダーをビルドする npm が含まれる Node.js、モジュール イメージを構築する Docker、モジュール イメージを保持するコンテナー レジストリが必要です。
* [Node.js](https://nodejs.org)
* [Docker](https://docs.docker.com/engine/installation/)
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) または [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   >[!TIP]
   >プロトタイプおよびテスト目的で、クラウド レジストリの代わりに Docker のローカル レジストリを使用できます。 

デバイス上でお使いのモジュールをテストするには、1 つ以上の IoT Edge デバイスを使用するアクティブな IoT ハブが必要になります。 お使いのコンピューターを IoT Edge デバイスとして使用する場合は、[Windows](quickstart.md) または [Linux](quickstart-linux.md) 向けのチュートリアルの手順に従って、これを実現できます。 

## <a name="create-a-new-solution-template"></a>新しいソリューション テンプレートを作成する

以下の手順では、Visual Studio Code と Azure IoT Edge 拡張機能を使用して、.NET Core 2.0 に基づく IoT Edge モジュールを作成する方法を示します。 まずはソリューションを作成し、次にそのソリューション内に最初のモジュールを生成します。 各ソリューションには複数のモジュールを含めることができます。 

1. Visual Studio Code で、**[ビュー]** > **[統合端末]** の順に選択します。
2. 統合端末で、次のコマンドを入力して、Node.js の最新バージョンの Azure IoT Edge モジュール テンプレートをインストール (または更新) します。

   ```cmd/sh
   npm install -g yo generator-azure-iot-edge-module
   ```
3. Visual Studio Code で、**[ビュー]** > **[コマンド パレット]** の順に選択します。 
4. コマンド パレットで、**Azure IoT Edge: New IoT Edge Solution** コマンドを入力して実行します。

   ![新しい IoT Edge ソリューションを実行する](./media/how-to-develop-csharp-module/new-solution.png)

5. 新しいソリューションを作成するフォルダーを参照し、**[フォルダーの選択]** をクリックします。 
6. ソリューションの名前を指定します。 
7. ソリューション内の最初のモジュールのテンプレートとして、**Node.js モジュール**を選択します。
8. モジュールの名前を指定します。 コンテナー レジストリ内に一意の名前を選択します。 
9. モジュールのイメージ リポジトリを指定します。 モジュール名は VS Code によって自動的に入力されるため、**localhost:5000** を独自のレジストリ情報に置き換えるだけです。 テスト用に Docker のローカル レジストリを使用する場合、localhost で問題ありません。 Azure Container Registry を使用する場合、お使いのレジストリの設定のログイン サーバーを使用します。 ログイン サーバーは **\<レジストリ名\>.azurecr.io** のようになります。

VS Code は指定した情報を取り、IoT Edge ソリューションを作成して、それを新しいウィンドウに読み込みます。

ソリューション内には次の 3 つの項目があります。 
* **.vscode** フォルダーにはデバッグ構成が含まれています。
* **modules** フォルダーにはモジュールごとのサブフォルダーが含まれています。 現在は 1 つだけですが、コマンド パレットでコマンド **Azure IoT Edge: Add IoT Edge Module** を実行することで追加できます。 
* **.env** ファイルには環境変数の一覧が表示されます。 レジストリが ACR である場合、現在 ACR のユーザー名とパスワードが入っています。 

   >[!NOTE]
   >環境ファイルは、モジュールのイメージ リポジトリを指定した場合にのみ作成されます。 localhost の既定値を受け入れてローカルでテストおよびデバッグする場合は、環境変数を宣言する必要はありません。 

* **deployment.template.json** ファイルには新しいモジュールと、テストに使用できるデータをシミュレートする **tempSensor** のサンプル モジュールの一覧が表示されます。 配置マニフェストのしくみについて詳しくは、「[IoT Edge モジュールをどのように使用、構成、および再利用できるかを理解する](module-composition.md)」をご覧ください。

## <a name="devlop-your-module"></a>モジュールを開発する

ソリューションに付属する既定の Azure 関数コードは、**modules** > **\<お使いのモジュール名\>** > **app.js** にあります。 モジュールと deployment.template.json ファイルが設定されます。これでソリューションをビルドし、それをコンテナー レジストリにプッシュして、デバイスに配置することで、コードを操作することなくテストを開始できます。 モジュールはソース (このケースでは、データをシミュレートする tempSensor モジュール) から入力を取得して、IoT Hub に送信します。 

Node.js テンプレートを独自のコードでカスタマイズする準備ができたら、[Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md) を使用して、セキュリティ、デバイス管理、信頼性など、IoT ソリューションの主なニーズに対処するモジュールをビルドします。 

## <a name="build-and-deploy-your-module-for-debugging"></a>デバッグ用にモジュールをビルドしてデプロイする

各モジュール フォルダーには、コンテナー タイプが異なる複数の Docker ファイルが含まれています。 テスト用のモジュールのビルドには、拡張子が **.debug** で終わるファイルであればどれでも使用できます。 現時点では、C# のモジュールは linux-amd64 コンテナーのデバッグのみをサポートします。

1. VS Code で、`deployment.template.json` ファイルに移動します。 末尾に **.debug** を追加し、モジュール画像の URL を更新します。
2. **deployment.template.json** 内の Node.js モジュールの createOptions を以下のコンテンツに置き換え、このファイルを保存します。 
    ```json
    "createOptions": "{\"ExposedPorts\":{\"9229/tcp\":{}},\"HostConfig\":{\"PortBindings\":{\"9229/tcp\":[{\"HostPort\":\"9229\"}]}}}"
    ```

2. VS Code コマンド パレットで、**Azure IoT Edge: Build IoT Edge solution** コマンドを入力して実行します。
3. コマンド パレットからお使いのソリューションの `deployment.template.json` ファイルを選択します。 
4. Azure IoT Hub Device Explorer で、IoT Edge デバイス ID を右クリックし、**[Create deployment for IoT Edge device]\(IoT Edge デバイスのデプロイの作成\)** を選択します。 
5. お使いのソリューションの **config** フォルダーを開き、`deployment.json` ファイルを選択します。 **[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** をクリックします。 

これにより、VS Code 統合ターミナルで、展開が正常に作成されていることを配置 ID によって確認できます。

VS Code Docker エクスプローラーで、あるいは端末で `docker ps` コマンドを実行することでコンテナーの状態を確認できます。

## <a name="start-debugging-nodejs-module-in-vs-code"></a>VS Code で Node.js モジュールのデバッグを開始する

VS Code では、ワークスペースの `.vscode` フォルダーにある `launch.json` ファイルにデバッグ構成情報が保管されます。 この `launch.json` ファイルは、新しい IoT Edge ソリューションを作成するときに生成されたものです。 デバッグに対応している新しいモジュールを追加するたびに更新されます。 

1. VS Code のデバッグ ビューに移動し、お使いのモジュール用のデバッグ構成ファイルを選択します。

2. `app.js` に移動します。 このファイルにブレークポイントを追加します。

3. **[デバッグ開始]** ボタンをクリックするか **F5** キーを押して、アタッチするプロセスを選択します。

4. VS Code デバッグ ビューの左側のパネルに変数が表示されます。 

前の例は、コンテナー上の Node.js IoT Edge モジュールをデバッグする方法を示しています。 モジュール コンテナーの createOptions 内に公開ポートを追加しました。 Node.js モジュールのデバッグを終了したら、稼働準備済みの IoT Edge モジュールではこれらの公開ポートを削除することをお勧めします。

## <a name="next-steps"></a>次の手順

モジュールのビルドが終ったら、[Visual Studio Code から Azure IoT Edge モジュールをデプロイする](how-to-deploy-modules-vscode.md)方法を確認してください。

お使いの IoT Edge デバイス用のモジュールを開発する方法について詳しくは、「[Azure IoT Hub SDK の概要と使用方法](../iot-hub/iot-hub-devguide-sdks.md)」をご覧ください。
