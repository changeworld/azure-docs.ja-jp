---
title: Azure IoT Edge のモジュールを開発およびデバッグする | Microsoft Docs
description: Visual Studio Code を使用して、Azure IoT Edge のモジュールを C#、Python、Node.js、Java、C で開発、ビルド、デバッグする
services: iot-edge
keywords: ''
author: shizn
manager: philmea
ms.author: xshi
ms.date: 12/14/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: fb3d19d35a15d5476594948b035a39ae703f1c3a
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550965"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Visual Studio Code を使用して Azure IoT Edge のモジュールを開発およびデバッグする

ビジネス ロジックを Azure IoT Edge のモジュールにすることができます。 この記事では、主なツールとして Visual Studio Code を使用してモジュールを開発およびデバッグする方法を説明します。

## <a name="prerequisites"></a>前提条件

Windows、macOS、または Linux を実行しているコンピューターまたは仮想マシンを開発用マシンとして使用できます。 IoT Edge デバイスとして、別の物理デバイスを使用できます。

C#、Node.js、または Java で記述されたモジュールの場合、Visual Studio Code でモジュールをデバッグする方法は 2 つあります。モジュール コンテナー内にプロセスをアタッチするか、モジュール コードをデバッグ モードで起動することができます。 Python または C で記述されたモジュールの場合は、それらをデバッグできるのは、Linux amd64 コンテナー内のプロセスにアタッチする方法だけです。

> [!TIP]
> Visual Studio Code のデバッグ機能をよく知らない場合は、[デバッグ](https://code.visualstudio.com/Docs/editor/debugging)に関するページを確認してください。

まず [Visual Studio Code](https://code.visualstudio.com/) をインストールしてから、次の拡張機能を追加します。

- [Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
- [Docker 拡張機能](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- 開発に使用している言語固有の Visual Studio 拡張機能
  - C# (Azure Functions を含む): [C# 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  - Python: [Python 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java:[Visual Studio Code 向け Java 拡張機能パック](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [C/C++ 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

また、モジュールを開発するために、言語固有のツールをいくつか追加でインストールする必要もあります。

- C# (Azure Functions を含む): [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)

- Python: Python パッケージをインストールするための [Python](https://www.python.org/downloads/) と [Pip](https://pip.pypa.io/en/stable/installing/#installation) (通常は、Python のインストールに含まれています)。 Pip がインストールされたら、次のコマンドを使用して **Cookiecutter** パッケージをインストールしてください。

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

- Node.js:[Node.js](https://nodejs.org)。 また、[Yeoman](https://www.npmjs.com/package/yo) と [Azure IoT Edge Node.js Module Generator](https://www.npmjs.com/package/generator-azure-iot-edge-module) もインストールする必要があります。

- Java:[Java SE Development Kit 10](https://aka.ms/azure-jdks) と [Maven](https://maven.apache.org/)。 JDK インストールを指すように [`JAVA_HOME` 環境変数を設定する](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/)必要があります。

モジュール イメージを作成してデプロイするには、モジュール イメージを作成する Docker とモジュール イメージを保持するコンテナー レジストリが必要です。

- お使いの開発用マシン上の [Docker Community Edition](https://docs.docker.com/install/)。

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) または [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > プロトタイプおよびテスト目的で、クラウド レジストリの代わりに Docker のローカル レジストリを使用できます。

C でモジュールを開発している場合を除き、IoT Edge ソリューションをデバッグ、実行、テストするようにローカルの開発環境を設定するには、Python ベースの [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/) も必要になります。 まだ行っていない場合は、[Python (2.7/3.6) と Pip](https://www.python.org/) をインストールした後、ターミナルで次のコマンドを実行して **iotedgehubdev** をインストールしてください。

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

> [!NOTE]
> デバイス上でモジュールをテストするには、1 つ以上の IoT Edge デバイスがあるアクティブな IoT ハブが必要です。 お使いのコンピューターを IoT Edge デバイスとして使用するには、[Linux](quickstart-linux.md) または [Windows](quickstart.md) 用のクイック スタートの手順に従ってください。 開発用マシン上で IoT Edge デーモンを実行している場合は、次の手順に進む前に EdgeHub と EdgeAgent を停止することが必要になる可能性があります。

## <a name="create-a-new-solution-template"></a>新しいソリューション テンプレートを作成する

次の手順では、Visual Studio Code と Azure IoT Edge 拡張機能を使用して好きな開発言語 (C# で記述されている Azure Functions を含む) で IoT Edge モジュールを作成する方法を説明します。 まずはソリューションを作成し、次にそのソリューション内に最初のモジュールを生成します。 各ソリューションには複数のモジュールを含めることができます。

1. **[ビュー]** > **[コマンド パレット]** の順に選択します。

1. コマンド パレットで、**Azure IoT Edge:New IoT Edge solution** コマンドを入力して実行します。

   ![新しい IoT Edge ソリューションを実行する](./media/how-to-develop-csharp-module/new-solution.png)

1. 新しいソリューションを作成するフォルダーを参照し、**[フォルダーの選択]** を選択します。

1. ソリューションの名前を入力します。

1. ソリューション内の最初のモジュールになるように、好きな開発言語用のモジュール テンプレートを選択します。

1. モジュールの名前を入力します。 コンテナー レジストリ内に一意の名前を選択します。

1. モジュールのイメージ リポジトリの名前を指定します。 Visual Studio Code により、モジュール名には自動的に **localhost:5000/<対象のモジュール名\>** が設定されます。 独自のレジストリ情報に置き換えます。 テスト用に Docker のローカル レジストリを使用する場合、**localhost** で問題ありません。 Azure Container Registry を使用する場合、お使いのレジストリの設定のログイン サーバーを使用します。 ログイン サーバーは ***\<レジストリ名\>*.azurecr.io** のようになります。 この文字列の **localhost:5000** 部分だけを置き換えて、最終的な結果が **\<* レジストリ名*\>.azurecr.io/\<* 対象のモジュール名*\>** になるようにします。

   ![Docker イメージ リポジトリを指定する](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code は、指定された情報を取得し、IoT Edge ソリューションを作成して、それを新しいウィンドウに読み込みます。

ソリューション内には 4 つの項目があります。

- **.vscode** フォルダーにはデバッグ構成が含まれています。

- **modules** フォルダーにはモジュールごとのサブフォルダーが含まれています。 この時点では 1 つだけです。 ただし、コマンド パレットでコマンド **Azure IoT Edge:Add IoT Edge Module** を実行することで追加できます。

- **.env** ファイルには環境変数の一覧が表示されます。 レジストリが Azure Container Registry の場合、Azure Container Registry のユーザー名とパスワードがあります。

  > [!NOTE]
  > 環境ファイルは、モジュールのイメージ リポジトリを指定した場合にのみ作成されます。 localhost の既定値を受け入れてローカルでテストおよびデバッグする場合は、環境変数を宣言する必要はありません。

- **deployment.template.json** ファイルには新しいモジュールと、テストに使用できるデータをシミュレートする **tempSensor** のサンプル モジュールの一覧が表示されます。 配置マニフェストがどのように機能するかについて詳しくは、「[配置マニフェストを使ってモジュールをデプロイしルートを確立する](module-composition.md)」をご覧ください。

## <a name="add-additional-modules"></a>他のモジュールを追加する

ソリューションに他のモジュールを追加するには、コマンド **[Azure IoT Edge: Add IoT Edge Module]\(Azure IoT Edge: IoT Edge モジュールを追加する\)** をコマンド パレットから実行します。 また、Visual Studio Code の [エクスプローラー] ビューで **modules** フォルダーまたは `deployment.template.json` ファイルを右クリックし、**[IoT Edge モジュールを追加する]** を選択することもできます。

## <a name="develop-your-module"></a>モジュールの開発

ソリューションに付属する既定のモジュール コードは次の場所にあります。

- Azure Functions (C#): **modules > *&lt;対象のモジュール名&gt;* > *&lt;対象のモジュール名&gt;*.cs**
- C#: **modules > *&lt;対象のモジュール名&gt;* > Program.cs**
- Python: **modules > *&lt;対象のモジュール名&gt;* > main.py**
- Node.js: **modules > *&lt;対象のモジュール名&gt;* > app.js**
- Java: **modules > *&lt;対象のモジュール名&gt;* > src > main > java > com > edgemodulemodules > App.java**
- C: **modules > *&lt;対象のモジュール名&gt;* > main.c**

モジュールと deployment.template.json ファイルが設定されます。これでソリューションをビルドし、それをコンテナー レジストリにプッシュして、デバイスにデプロイすることで、コードを操作することなくテストを開始できます。 モジュールはソース (このケースでは、データをシミュレートする tempSensor モジュール) から入力を取得して、IoT Hub に送信するようにビルドされています。

テンプレートを独自のコードでカスタマイズする準備ができたら、[Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md) を使用して、セキュリティ、デバイス管理、信頼性など、IoT ソリューションの主なニーズに対処するモジュールをビルドします。

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>コンテナーを使用せずにモジュールをデバッグする (C#、Node.js、Java)

C#、Node.js、または Java で開発している場合、モジュールでは、メッセージを開始、実行、ルーティングできるように既定のモジュール コード内で **ModuleClient** オブジェクトを使用する必要があります。 また、モジュールがメッセージを受信したときに対処するために、既定の入力チャネル **input1** も使用します。

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>IoT Edge ソリューション用に IoT Edge シミュレーターを設定する

開発用マシンでは、IoT Edge ソリューションを実行できるように IoT Edge セキュリティ デーモンをインストールする代わりに IoT Edge シミュレーターを起動できます。

1. 左側のデバイス エクスプローラーで、IoT Edge デバイス ID を右クリックして **[Setup IoT Edge Simulator]\(IoT Edge シミュレーターの設定\)** を選択し、デバイスの接続文字列を使用してシミュレーターを起動します。
1. 統合ターミナルで進捗の詳細を確認すると、IoT Edge シミュレーターが正常に設定されたことがわかります。

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>シングル モジュール アプリ用に IoT Edge シミュレーターを設定する

シミュレーターを設定して起動するには、コマンド **[Azure IoT Edge: Start IoT Edge Hub Simulator for Single Module]\(Azure IoT Edge: シングル モジュール用の IoT Edge Hub シミュレーターを起動\)** を Visual Studio Code のコマンド パレットから実行します。 メッセージが表示されたら、既定のモジュール コードの値 **input1** (または使用しているコードの同等の値) をアプリケーションの入力名として使用します。 このコマンドは **iotedgehubdev** CLI をトリガーしてから、IoT Edge シミュレーターとテスト用ユーティリティ モジュール コンテナーを起動します。 シミュレーターがシングル モジュール モードで正しく起動された場合は、統合ターミナルで以下の出力を確認できます。 メッセージの送信に役立つ `curl` コマンドも表示されます。 これは後で使用します。

   ![シングル モジュール アプリ用に IoT Edge シミュレーターを設定する](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Visual Studio Code の Docker Explorer ビューを使用して、モジュールの実行状態を確認できます。

   ![シミュレーター モジュールの状態](media/how-to-develop-csharp-module/simulator-status.png)

   **edgeHubDev** コンテナーはローカルの IoT Edge シミュレーターの中心です。 これは、IoT Edge セキュリティ デーモンを使用せずに開発用マシンで実行可能であり、ネイティブ モジュール アプリまたはモジュール コンテナー用の環境設定を提供します。 **入力**コンテナーは、モジュール上の対象となる入力チャネルへのメッセージのブリッジに役立つ REST API シリーズを公開します。

### <a name="debug-module-in-launch-mode"></a>起動モードでモジュールをデバッグする

1. お使いの開発言語の要件に従ってデバッグ用の環境を準備し、モジュール内にブレークポイントを設定して、使用するデバッグ構成を選択します。
   - **C#**
     - Visual Studio Code 統合ターミナルで、ディレクトリを ***&lt;対象のモジュール名&gt;*** フォルダーに変更し、次のコマンドを実行して .Net Core アプリケーションをビルドします。

       ```cmd
       dotnet build
       ```

     - `program.cs` ファイルを開き、ブレークポイントを追加します。

     - **[ビュー] > [デバッグ]** の順に選択して、Visual Studio Code の [デバッグ] ビューに移動します。 ドロップダウンからデバッグ構成として **[*&lt;your module name&gt;* Local Debug (.NET Core)]\(<モジュール名> のローカル デバッグ (.NET Core)\)** を選択します。

        > [!NOTE]
        > .Net Core `TargetFramework` が `launch.json` 内のプログラム パスと一致しない場合は、Visual Studio Code がこのプログラムを正常に起動できるように .csproj ファイル内の `TargetFramework` に合わせて `launch.json` 内のプログラム パスを手動で更新する必要があります。

   - **Node.js**
     - Visual Studio Code 統合ターミナルで、ディレクトリを ***&lt;対象のモジュール名&gt;*** フォルダーに変更し、次のコマンドを実行して Node パッケージをインストールします。

       ```cmd
       npm install
       ```

     - `app.js` ファイルを開き、ブレークポイントを追加します。

     - **[ビュー] > [デバッグ]** の順に選択して、Visual Studio Code の [デバッグ] ビューに移動します。 ドロップダウンからデバッグ構成として **[*&lt;your module name&gt;* Local Debug (Node.js)]\(<モジュール名> のローカル デバッグ (Node.js)\)** を選択します。
   - **Java**
     - `App.java` ファイルを開き、ブレークポイントを追加します。

     - **[ビュー] > [デバッグ]** の順に選択して、Visual Studio Code の [デバッグ] ビューに移動します。 ドロップダウンからデバッグ構成として **[*&lt;your module name&gt;* Local Debug (Java)]\(<モジュール名> のローカル デバッグ (Java)\)** を選択します。

1. **[デバッグの開始]** をクリックするか **F5** キーを押して、デバッグ セッションを開始します。

1. Visual Studio Code 統合ターミナルで、次のコマンドを実行して **Hello World** メッセージをモジュールに送信します。 これは、前述の手順で IoT Edge シミュレーターを設定したときに表示されたコマンドです。

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Windows を使用している場合は、Visual Studio Code 統合ターミナルのシェルが **Git Bash** または **WSL Bash** であることを確認してください。 `curl` コマンドを PowerShell またはコマンド プロンプトから実行することはできません。
   > [!TIP]
   > `curl` の代わりに [PostMan](https://www.getpostman.com/) またはその他の API ツールを使用してメッセージを送信することもできます。

1. Visual Studio Code の [デバッグ] ビューの左側のパネルに変数が表示されます。

1. デバッグ セッションを停止するには、停止ボタンを選択するか **Shift + F5** キーを押し、コマンド パレットの **[Azure IoT Edge: Stop IoT Edge Simulator]\(Azure IoT Edge: IoT Edge シミュレーターを停止\)** を実行して、シミュレーターを停止してクリーンアップします。

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>IoT Edge シミュレーターを使用してアタッチ モードでデバッグする (C#、Node.js、Java、Azure Functions)

既定のソリューションには 2 つのモジュールが含まれています。1 つはシミュレート済みの温度センサー モジュールで、もう 1 つはパイプ モジュールです。 シミュレート済みの温度センサーがパイプ モジュールにメッセージを送信すると、そのメッセージは IoT Hub にパイプされます。 作成したモジュール フォルダーには、コンテナー タイプが異なるいくつかの Docker ファイルが含まれています。 テスト用のモジュールを作成するには、**.debug** 拡張子で終わるファイルのいずれかを使用します。

現時点では、次のように、アタッチ モードでのデバッグのみがサポートされています。

- C# モジュール (Azure Functions のモジュールを含む) では、Linux amd64 コンテナーでのデバッグをサポートしています
- Node.js モジュールでは、Linux amd64 コンテナーと Linux arm32v7 コンテナーのほか Windows amd64 コンテナーでのデバッグをサポートしています
- Java モジュールでは、Linux amd64 コンテナーと Linux arm32v7 コンテナーでのデバッグをサポートしています

> [!TIP]
> Visual Studio Code のステータス バーの項目をクリックすると、使用している IoT Edge ソリューションの既定のプラットフォームに合わせてオプションを切り替えることができます。

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>IoT Edge ソリューション用に IoT Edge シミュレーターを設定する

開発用マシンでは、IoT Edge ソリューションを実行できるように IoT Edge セキュリティ デーモンをインストールする代わりに IoT Edge シミュレーターを起動できます。

1. 左側のデバイス エクスプローラーで、IoT Edge デバイス ID を右クリックして **[Setup IoT Edge Simulator]\(IoT Edge シミュレーターの設定\)** を選択し、デバイスの接続文字列を使用してシミュレーターを起動します。

1. 統合ターミナルで進捗の詳細を確認すると、IoT Edge シミュレーターが正常に設定されたことがわかります。

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>デバッグ用のコンテナーをビルドして実行し、アタッチ モードでデバッグする

1. モジュール ファイル (`program.cs`、`app.js`、`App.java`、または `<your module name>.cs`) を開いてブレークポイントを追加します。

1. Visual Studio Code の [エクスプローラー] ビューで、対象のソリューションの `deployment.debug.template.json` ファイルを右クリックし、**[Build and Run IoT Edge solution in Simulator]\(シミュレーターでの IoT Edge ソリューションのビルドおよび実行\)** を選択します。 モジュール コンテナーのすべてのログを同じウィンドウで確認できます。 また、[Docker] ビューに移動してコンテナーの状態を確認することもできます。

   ![watch 変数](media/how-to-develop-csharp-module/view-log.png)

1. Visual Studio Code の [デバッグ] ビューに移動し、対象のモジュール用のデバッグ構成ファイルを選択します。 デバッグ オプション名は、***&lt;対象のモジュール名&gt;* Remote Debug** のようになります。

1. **[デバッグの開始]** を選択するか、**F5** キーを押します。 アタッチするプロセスを選択します。

1. Visual Studio Code の [デバッグ] ビューの左側のパネルに変数が表示されます。

1. デバッグ セッションを停止するには、最初に停止ボタンを選択するか **Shift + F5** キーを押してから、**[Azure IoT Edge: Stop IoT Edge Simulator]\(Azure IoT Edge: IoT Edge シミュレーターを停止\)** をコマンド パレットから選択します。

> [!NOTE]
> 前の例は、コンテナー上の IoT Edge モジュールをデバッグする方法を示しています。 これにより、公開ポートがモジュールのコンテナーの `createOptions` 設定に追加されました。 モジュールのデバッグが終了したら、運用環境対応の IoT Edge モジュールではこれらの公開ポートを削除することをお勧めします。
>
> C# (Azure Functions を含む) で記述されたモジュールの場合、この例は、コンテナー イメージの作成中に .NET Core コマンドライン デバッガー (VSDBG) を含める、デバッグ バージョンの `Dockerfile.amd64.debug` に基づいています。 C# モジュールをデバッグした後、稼働準備済みの IoT Edge モジュールでは、VSDBG が含まれていない Dockerfile を直接使用することをお勧めします。

## <a name="debug-a-module-with-iot-edge-runtime-python-c"></a>IoT Edge ランタイムを使用してモジュールをデバッグする (Python、C)

各モジュール フォルダーには、コンテナー タイプが異なるいくつかの Docker ファイルが含まれています。 テスト用のモジュールを作成するには、**.debug** 拡張子で終わるファイルのいずれかを使用します。

現在、Python と C モジュールのデバッグのサポートは Linux amd64 コンテナーのみで利用できます。

### <a name="build-and-deploy-your-module"></a>モジュールを作成してデプロイする

1. Visual Studio Code で、`deployment.debug.template.json` ファイルを開きます。これには、適切な `createOptions` 値が設定されているモジュール イメージのデバッグ バージョンが含まれています。

1. Python でモジュールを開発している場合は、続行する前に次の手順に従います。
   - `main.py` ファイルを開き、import セクションの後に次のコードを追加します。

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```
   - デバッグするコールバックに次の 1 行のコードを追加します。

      ```python
      ptvsd.break_into_debugger()
      ```

     たとえば、`receive_message_callback` メソッドをデバッグする場合は、以下に示すようなコード行を挿入します。

      ```python
      def receive_message_callback(message, hubManager):
          ptvsd.break_into_debugger()
          global RECEIVE_CALLBACKS
          message_buffer = message.get_bytearray()
          size = len(message_buffer)
          print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode ('utf-8'), size) )
          map_properties = message.properties()
          key_value_pair = map_properties.get_internals()
          print ( "    Properties: %s" % key_value_pair )
          RECEIVE_CALLBACKS += 1
          print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
          hubManager.forward_event_to_output("output1", message, 0)
          return IoTHubMessageDispositionResult.ACCEPTED
      ```

1. Visual Studio Code のコマンド パレットで:
   1. **Azure IoT Edge: Build IoT Edge solution** コマンドを入力して実行します。

   1. 対象のソリューションの `deployment.debug.template.json` ファイルを選択します。

1. Visual Studio Code の [エクスプローラー] ビューの **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションで:
   1. IoT Edge デバイス ID を右クリックし、**[Create deployment for Single Device]\(単一デバイスのデプロイの作成\)** を選択します。

   1. ソリューションの **config** フォルダーに移動し、`deployment.debug.amd64.json` ファイルを選択して、**[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** を選択します。

統合ターミナルで、デプロイが正常に作成されていることをデプロイ ID によって確認できます。

コンテナーの状態は、Visual Studio Code の Docker ビューで確認するか、ターミナルで `docker ps` コマンドを実行して確認することができます。

### <a name="debug-your-module"></a>モジュールをデバッグする

Visual Studio Code では、ワークスペースの `.vscode` フォルダーにある `launch.json` ファイルでデバッグ構成情報が保持されます。 この `launch.json` ファイルは、新しい IoT Edge ソリューションを作成するときに生成されたものです。 デバッグに対応している新しいモジュールを追加するたびに更新されます。

1. Visual Studio Code の [デバッグ] ビューで、対象のモジュール用のデバッグ構成ファイルを選択します。 デバッグ オプション名は、***&lt;対象のモジュール名&gt;* Remote Debug** のようになります。

1. お使いの開発言語のモジュール ファイルを開いて、ブレークポイントを追加します。
   - **Python**: `main.py` を開いて、コールバック メソッド内の `ptvsd.break_into_debugger()` 行を追加した場所にブレークポイントを追加します。
   - **C**: `main.c` ファイルを開き、ブレークポイントを追加します。

1. **[デバッグの開始]** を選択するか、**F5** を押します。 アタッチするプロセスを選択します。

1. Visual Studio Code の [デバッグ] ビューの左側のパネルに変数が表示されます。

> [!NOTE]
> 前の例は、コンテナー上の IoT Edge モジュールをデバッグする方法を示しています。 これにより、公開ポートがモジュールのコンテナーの `createOptions` 設定に追加されました。 モジュールのデバッグが終了したら、運用環境対応の IoT Edge モジュールではこれらの公開ポートを削除することをお勧めします。

## <a name="next-steps"></a>次の手順

モジュールをビルドしたら、[Visual Studio Code から Azure IoT Edge モジュールをデプロイする](how-to-deploy-modules-vscode.md)方法を確認してください。

お使いの IoT Edge デバイス用のモジュールを開発する方法について詳しくは、「[Azure IoT Hub SDK の概要と使用方法](../iot-hub/iot-hub-devguide-sdks.md)」をご覧ください。
