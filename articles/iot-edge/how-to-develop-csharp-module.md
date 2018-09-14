---
title: Azure IoT Edge の C# モジュールをデバッグする | Microsoft Docs
description: Visual Studio Code を使用して Azure IoT Edge の C# モジュールを開発、ビルド、およびデバッグする
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/04/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: b1c6209c4d589093d7a29cd8a883d3e5d4ca12f9
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782304"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>Visual Studio Code を使用して Azure IoT Edge の C# モジュールを開発およびデバッグする

ビジネス ロジックを Azure IoT Edge のモジュールにすることができます。 この記事では、主なツールとして Visual Studio Code (VS Code) を使用して、C# モジュールを開発してデバッグする方法を説明します。

## <a name="prerequisites"></a>前提条件
この記事では、Windows、macOS、または Linux を実行しているコンピューターまたは仮想マシンを開発用マシンとして使用していることを前提としています。 IoT Edge デバイスとして、別の物理デバイスを指定できます。

> [!NOTE]
> このデバッグに関する記事では、VS Code で C# モジュールをデバッグする 2 つの一般的な方法を示します。 1 つ目の方法ではモジュール コンテナーのプロセスをアタッチし、2 つ目の方法ではデバッグ モードでモジュール コードを起動します。 Visual Studio Code のデバッグ機能をよく知らない場合は、[デバッグ](https://code.visualstudio.com/Docs/editor/debugging)に関するページを確認してください。

この記事ではメインの開発ツールとして Visual Studio Code を使うため、VS Code をインストールします。 その後、必要な拡張機能を追加します。
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [C# 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Docker 拡張機能](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

モジュールを作成するには、プロジェクト フォルダーを構築する .NET、モジュール イメージを構築する Docker、およびモジュール イメージを保持するコンテナー レジストリが必要です。
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。
* お使いの開発用マシン上の [Docker Community Edition](https://docs.docker.com/install/)。 
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) または [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * プロトタイプおよびテスト目的で、クラウド レジストリの代わりに Docker のローカル レジストリを使用できます。 

デバッグのためのローカルの開発環境をセットアップして、IoT Edge ソリューションを実行およびテストするには、[Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/) が必要です。[Python (2.7/3.6) と Pip](https://www.python.org/) をインストールします。 次に、ターミナルで以下のコマンドを実行して **iotedgehubdev** をインストールします。

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

デバイス上でお使いのモジュールをテストするには、1 つ以上の IoT Edge デバイスを使用するアクティブな IoT ハブが必要になります。 コンピューターを IoT Edge デバイスとして使用するには、[Windows](quickstart.md) または [Linux](quickstart-linux.md) 用のクイックスタートの手順に従ってください。 

## <a name="create-a-new-solution-with-c-module"></a>C# モジュールを含む新しいソリューションを作成する

以下の手順では、Visual Studio Code と Azure IoT Edge 拡張機能を使用して、.NET Core 2.0 に基づく IoT Edge モジュールを作成する方法を示します。 まずはソリューションを作成し、次にそのソリューション内に最初のモジュールを生成します。 各ソリューションには複数のモジュールを含めることができます。 

1. Visual Studio Code で、**[ビュー]** > **[統合端末]** の順に選択します。
3. **[ビュー]** > **[コマンド パレット]** の順に選択します。 
4. コマンド パレットで、**Azure IoT Edge: New IoT Edge Solution** コマンドを入力して実行します。

   ![新しい IoT Edge ソリューションを実行する](./media/how-to-develop-csharp-module/new-solution.png)

5. 新しいソリューションの作成先フォルダーを参照します。 **[フォルダーの選択]** を選択します。 
6. ソリューションの名前を入力します。 
7. ソリューション内の最初のモジュールのテンプレートとして、**C# モジュール**を選択します。
8. モジュールの名前を入力します。 コンテナー レジストリ内に一意の名前を選択します。 
9. モジュールのイメージ リポジトリの名前を指定します。 VS Code によってモジュール名に **localhost:5000** と自動的に入力されます。 独自のレジストリ情報に置き換えます。 テスト用に Docker のローカル レジストリを使用する場合、**localhost** で問題ありません。 Azure Container Registry を使用する場合、お使いのレジストリの設定のログイン サーバーを使用します。 ログイン サーバーは **\<レジストリ名\>.azurecr.io** のようになります。 文字列の localhost 部分のみを置き換え、モジュール名は削除しないでください。

   ![Docker イメージ リポジトリを指定する](./media/how-to-develop-csharp-module/repository.png)

VS Code は指定した情報を取り、IoT Edge ソリューションを作成して、それを新しいウィンドウに読み込みます。

   ![IoT Edge ソリューションを表示する](./media/how-to-develop-csharp-module/view-solution.png)

ソリューション内には 4 つの項目があります。 
* **.vscode** フォルダーにはデバッグ構成が含まれています。
* **modules** フォルダーにはモジュールごとのサブフォルダーが含まれています。 この時点では 1 つだけです。 ただし、コマンド パレットでコマンド **Azure IoT Edge: Add IoT Edge Module** を実行することで追加できます。 
* **.env** ファイルには環境変数の一覧が表示されます。 レジストリが Azure Container Registry の場合、Azure Container Registry のユーザー名とパスワードがあります。 

   >[!NOTE]
   >環境ファイルは、モジュールのイメージ リポジトリを指定した場合にのみ作成されます。 localhost の既定値を受け入れてローカルでテストおよびデバッグする場合は、環境変数を宣言する必要はありません。 

* **deployment.template.json** ファイルには新しいモジュールと、テストに使用できるデータをシミュレートする **tempSensor** のサンプル モジュールの一覧が表示されます。 配置マニフェストがどのように機能するかについて詳しくは、「[配置マニフェストを使ってモジュールをデプロイしルートを確立する](module-composition.md)」をご覧ください。 

## <a name="develop-your-module"></a>モジュールの開発

ソリューションに付属する既定の C# モジュール コードは、**modules** > ** [お使いのモジュール名] ** > **Program.cs** にあります。 モジュールと deployment.template.json ファイルが設定されます。これでソリューションをビルドし、それをコンテナー レジストリにプッシュして、デバイスにデプロイすることで、コードを操作することなくテストを開始できます。 モジュールはソース (このケースでは、データをシミュレートする tempSensor モジュール) から入力を取得して、IoT Hub に送信します。 

C# テンプレートを独自のコードでカスタマイズする準備ができたら、[Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md) を使用して、セキュリティ、デバイス管理、信頼性など、IoT ソリューションの主なニーズに対処するモジュールをビルドします。 

VS Code における C# のサポートは、クロスプラットフォーム .NET Core 開発用に最適化されます。 詳しくは、[VS Code で C# を使用する方法](https://code.visualstudio.com/docs/languages/csharp)をご覧ください。

## <a name="launch-and-debug-module-code-without-container"></a>コンテナーを使用せずにモジュール コードを起動およびデバッグする
IoT Edge C# モジュールは .Net Core アプリケーションです。 このモジュールは Azure IoT C# デバイス SDK に依存します。 既定のモジュール コードでは、環境設定と入力名を使用して **ModuleClient** を初期化します。つまり、IoT Edge C# モジュールを起動して実行するには、環境設定が必要です。また、入力チャネルにメッセージを送信またはルーティングする必要があります。 既定の C# モジュールに含まれている入力チャネルは 1 つだけであり、名前は **input1** です。

### <a name="setup-iot-edge-simulator-for-single-module-app"></a>シングル モジュール アプリ用に IoT Edge シミュレーターを設定する

1. シミュレーターを設定して起動するには、VS Code コマンド パレットで、「**Azure IoT Edge: Start IoT Edge Hub Simulator for Single Module (Azure IoT Edge: シングル モジュール用の IoT Edge Hub シミュレーターを起動)**」と入力して選択します。 また、シングル モジュール アプリケーション用の入力名を指定し、「**input1**」と入力して Enter キーを押す必要もあります。 コマンドが **iotedgehubdev** CLI をトリガーして IoT Edge シミュレーターとテスト用ユーティリティ モジュール コンテナーを起動します。 シミュレーターがシングル モジュール モードで正しく起動された場合は、統合ターミナルで以下の出力を確認できます。 メッセージの送信に役立つ `curl` コマンドも表示されます。 これは後で使用します。

   ![シングル モジュール アプリ用に IoT Edge シミュレーターを設定する](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Docker Explorer に移動して、モジュールの実行状態を確認できます。

   ![シミュレーター モジュールの状態](media/how-to-develop-csharp-module/simulator-status.png)

   **edgeHubDev** コンテナーはローカルの IoT Edge シミュレーターの中心です。 このコンテナーは、IoT Edge セキュリティ デーモンを使用せずに開発用マシンで実行可能であり、ネイティブ モジュール アプリまたはモジュール コンテナー用の環境設定を提供します。 **入力**コンテナーは、モジュール上の入力チャネルを対象とするためのメッセージのブリッジに役立つ restAPI を公開しました。

2. VS Code コマンド パレットで、「**Azure IoT Edge: Set Module Credentials to User Settings (Azure IoT Edge: モジュールの資格情報をユーザー設定に設定)**」と入力して選択し、モジュールの環境設定をユーザー設定の `azure-iot-edge.EdgeHubConnectionString` および `azure-iot-edge.EdgeModuleCACertificateFile` に設定します。 **.vscode** > **launch.json** および [VS Code のユーザー設定](https://code.visualstudio.com/docs/getstarted/settings)では、これらの環境設定が参照されていることを確認できます。

### <a name="build-module-app-and-debug-in-launch-mode"></a>起動モードでモジュール アプリをビルドしてデバッグする

1. 統合ターミナルで、ディレクトリを **CSharpModule** フォルダーに変更し、次のコマンドを実行して .Net Core アプリケーションをビルドします。

    ```cmd
    dotnet build
    ```

   > [!TIP]
   > `curl` の代わりに [PostMan](https://www.getpostman.com/) またはその他の API ツールを使用してメッセージを送信することもできます。

2. `program.cs` に移動します。 このファイルにブレークポイントを追加します。

3. VS Code デバッグ ビューに移動します。 デバッグ構成として **[ModuleName Local Debug (.NET Core)]\(<モジュール名> のローカル デバッグ (.NET Core)\)** を選択します。 

4. **[デバッグの開始]** をクリックするか、**F5** キーを押します。 デバッグ セッションが開始されます。

5. VS Code 統合ターミナルで、次のコマンドを実行して「**Hello World**」というメッセージをモジュールに送信します。 これは、前述の手順で IoT Edge シミュレーターの設定に成功すると表示されるコマンドです。

    ```cmd
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Windows を使用する場合は、VS Code 統合ターミナルのシェルが **Git Bash** または **WSL Bash** であることを確認してください。 `curl` コマンドを PowerShell またはコマンド プロンプトで実行することはできません。 
   
   > [!TIP]
   > `curl` の代わりに [PostMan](https://www.getpostman.com/) またはその他の API ツールを使用してメッセージを送信することもできます。

6. VS Code のデバッグ ビューの左側のパネルに変数が表示されます。 

    ![watch 変数](media/how-to-develop-csharp-module/single-module-variables.png)

7. デバッグ セッションを停止するには、[停止] ボタンをクリックするか、**Shift + F5** キーを押します。 VS Code コマンド パレットで、「**Azure IoT Edge: Stop IoT Edge Simulator (Azure IoT Edge: IoT Edge シミュレーターを停止)**」と入力して選択します。

## <a name="build-module-container-for-debugging-and-debug-in-attach-mode"></a>デバッグ用のモジュール コンテナーをビルドしてアタッチ モードでデバッグする

既定のソリューションには 2 つのモジュールが含まれています。1 つはシミュレート済みの温度センサー モジュールで、もう 1 つは C# パイプ モジュールです。 シミュレート済みの温度センサーは C# パイプ モジュールへのメッセージの送信を続け、メッセージは IoT Hub にパイプされます。 作成したモジュール フォルダーには、コンテナー タイプが異なるいくつかの Docker ファイルが含まれています。 テスト用のモジュールのビルドには、拡張子が **.debug** で終わるファイルであればどれでも使用できます。 現時点では、C# モジュールはアタッチ モードでの Linux amd64 コンテナーのみのデバッグをサポートします。 

### <a name="setup-iot-edge-simulator-for-iot-edge-solution"></a>IoT Edge ソリューション用に IoT Edge シミュレーターを設定する

開発用マシンでは、IoT Edge セキュリティ デーモンをインストールする代わりに IoT Edge シミュレーターを起動して IoT Edge ソリューションを実行できます。 

1. 左側のデバイス エクスプローラーで、IoT Edge デバイス ID を右クリックして **[Setup IoT Edge Simulator]\(IoT Edge シミュレーターの設定\)** を選択し、デバイスの接続文字列を使用してシミュレーターを起動します。

2. IoT Edge シミュレーターが正常に設定されたことを統合ターミナルで確認できます。

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>デバッグ用のコンテナーをビルドして実行し、アタッチ モードでデバッグする

1. VS Code で、`deployment.template.json` ファイルに移動します。 末尾に **.debug** を追加し、C# モジュール イメージの URL を更新します。

   ![イメージ名に **.debug** を追加する](./media/how-to-develop-csharp-module/image-debug.png)

2. `program.cs` に移動します。 このファイルにブレークポイントを追加します。
3. VS Code ファイル エクスプローラーでソリューションの `deployment.template.json` ファイルを選択し、コンテキスト メニューの **[Build and Run IoT Edge solution in Simulator]\(シミュレーターでの IoT Edge ソリューションのビルドおよび実行\)** をクリックします。 モジュール コンテナーのすべてのログを同じウィンドウで確認できます。 Docker Explorer に移動して、コンテナーの状態を確認することもできます。

   ![watch 変数](media/how-to-develop-csharp-module/view-log.png)

5. VS Code デバッグ ビューに移動します。 モジュールのデバッグ構成ファイルを選択します。 デバッグ オプション名は、**ModuleName Remote Debug (.NET Core)** のようになります。

   ![構成の選択](media/how-to-develop-csharp-module/debug-config.png)

6. **[デバッグの開始]** を選択するか、**F5** を押します。 アタッチするプロセスを選択します。

7. VS Code のデバッグ ビューの左側のパネルに変数が表示されます。

8. デバッグ セッションを停止するには、[停止] ボタンをクリックするか、**Shift + F5** キーを押します。 VS Code コマンド パレットで、「**Azure IoT Edge: Stop IoT Edge Simulator (Azure IoT Edge: IoT Edge シミュレーターを停止)**」と入力して選択します。

> [!NOTE]
> この例は、コンテナー上の .NET Core IoT Edge モジュールをデバッグする方法を示しています。 これは、コンテナー イメージのビルド中に .NET Core コマンド ライン デバッガー VSDBG を含める、デバッグ バージョンの `Dockerfile.debug` に基づいています。 C# モジュールをデバッグした後、稼働準備済みの IoT Edge モジュールでは、VSDBG が含まれていない `Dockerfile` を直接使用するか、カスタマイズすることをお勧めします。

## <a name="next-steps"></a>次の手順

モジュールをビルドしたら、[Visual Studio Code から Azure IoT Edge モジュールをデプロイする](how-to-deploy-modules-vscode.md)方法を確認してください。

お使いの IoT Edge デバイス用のモジュールを開発する方法について詳しくは、「[Azure IoT Hub SDK の概要と使用方法](../iot-hub/iot-hub-devguide-sdks.md)」をご覧ください。
