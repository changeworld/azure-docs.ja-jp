---
title: Visual Studio でモジュールを開発してデバッグする - Azure IoT Edge
description: Visual Studio と Azure IoT Tools を使用して、デプロイ マニフェストによる構成に従い、C または C# IoT Edge モジュールを開発し、それを IoT Hub から IoT デバイスにプッシュします。
services: iot-edge
author: kgremban
manager: lizross
ms.author: kgremban
ms.date: 08/24/2021
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 5f546acb48a84ddddeb822601d9284818d2211fb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423073"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Visual Studio 2019 を使用して Azure IoT Edge 用のモジュールを開発してデバッグする

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

この記事では、Visual Studio 2019 を使用して Azure IoT Edge モジュールを開発し、デバッグする方法を示します。

Azure IoT Edge Tools for Visual Studio 拡張機能の利点は次のとおりです。

* ローカル開発用コンピューター上で IoT Edge ソリューションとモジュールの作成、編集、ビルド、実行、デバッグを行うことができます。
* Azure IoT Hub を使用して IoT Edge ソリューションを IoT Edge デバイスにデプロイできます。
* Visual Studio での開発のすべての利点を利用しながら、C または C# で Azure IoT モジュールをコーディングできます。
* IoT Edge デバイスとモジュールを UI で管理できます。

この記事では、Azure IoT Edge Tools for Visual Studio 2019 を使用して、IoT Edge モジュールを開発する方法を示します。 IoT Edge デバイスにプロジェクトをデプロイする方法についても説明します。 現在、Visual Studio 2019 では、C および C# で記述されたモジュールをサポートしています。 サポートされるデバイス アーキテクチャは、Windows X64 と、Linux X64 または ARM32 です。 サポートされているオペレーティング システム、言語、およびアーキテクチャの詳細については、「[Language and architecture support (言語とアーキテクチャのサポート)](module-development.md#language-and-architecture-support)」を参照してください。
  
## <a name="prerequisites"></a>前提条件

この記事では、Windows を実行しているマシンを開発用マシンとして使用していることを前提としています。 Windows コンピューターでは、Windows または Linux のいずれかのモジュールを開発できます。

* **Windows コンテナー** を使用してモジュールを開発するには、バージョン 1809/ビルド 17763 以降を実行している Windows コンピューターを使用します。
* **Linux コンテナー** を使用してモジュールを開発するには、[Docker Desktop の要件](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)を満たす Windows コンピューターを使用します。

開発マシンに Visual Studio をインストールします。 Visual Studio 2019 のインストールには、必ず **Azure の開発** と **C++ によるデスクトップ開発** のワークロードを含めてください。 [Visual Studio 2019 を変更](/visualstudio/install/modify-visual-studio?view=vs-2019&preserve-view=true)して、必須のワークロードを追加することができます。

Visual Studio 2019 の準備ができたら、次のツールとコンポーネントも必要になります。

* Visual Studio 2019 で IoT Edge プロジェクトを作成するために、Visual Studio Marketplace から [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) をダウンロードしてインストールします。

  > [!TIP]
  > Visual Studio 2017 を使用している場合は、Visual Studio Marketplace から [VS 2017 用の Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) をダウンロードしてインストールします

* モジュール イメージを作成して実行するために、開発用マシンに [Docker Community Edition](https://docs.docker.com/install/) をダウンロードしてインストールします。 開発しているモジュールの種類に応じて、Linux コンテナー モードまたは Windows コンテナー モードで実行するように Docker CE を設定する必要があります。

* [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/) をインストールして、IoT Edge ソリューションをデバッグ、実行、テストするようにローカルの開発環境を設定します。 [Python (3.5/3.6/3.7/3.8) と Pip](https://www.python.org/) をインストールした後、ターミナルで次のコマンドを実行して **iotedgehubdev** パッケージをインストールします。 Azure IoT EdgeHub Dev Tool のバージョンが 0.3.0 より大きいことを確認します。

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

* Vcpkg ライブラリ マネージャーをインストールしてから、Windows 用の **azure-iot-sdk-c パッケージ** をインストールします。

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

* モジュール イメージを格納する [Azure Container Registry](../container-registry/index.yml) または [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) のインスタンスを作成します。

  > [!TIP]
  > プロトタイプおよびテスト目的で、クラウド レジストリの代わりに Docker のローカル レジストリを使用できます。

* デバイス上でモジュールをテストするには、1 つ以上の IoT Edge デバイスがあるアクティブな IoT ハブが必要です。 テストの目的で IoT Edge デバイスを迅速に作成するには、[Linux](quickstart-linux.md) または [Windows](quickstart.md) 用のクイック スタートの手順に従ってください。 開発用マシン上で IoT Edge デーモンを実行している場合は、Visual Studio で開発を始める前に、EdgeHub と EdgeAgent を停止することが必要になる可能性があります。

### <a name="check-your-tools-version"></a>ツールのバージョンを確認する

1. **[拡張機能]** メニューで、 **[拡張機能の管理]** を選択します。 **[インストール済み] > [ツール]** の順に展開すると、 **[Azure IoT Edge Tools for Visual Studio]** と **[Cloud Explorer for Visual Studio]** が見つかります。

1. インストールされているバージョンを確認します。 このバージョンと、Visual Studio Marketplace での最新バージョンを比較できます ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019)、[Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools))

1. インストールされているバージョンが Visual Studio Marketplace で入手できるものより古い場合は、次のセクションの説明に従って Visual Studio でツールを更新します。

### <a name="update-your-tools"></a>ツールを更新する

1. **[拡張機能の管理]** ウィンドウで、 **[更新プログラム] > [Visual Studio Marketplace]** の順に展開し、 **[Azure IoT Edge Tools]** または **[Cloud Explorer for Visual Studio]** を選択して、 **[更新]** を選択します。

1. ツールの更新プログラムをダウンロードしたら、Visual Studio を終了し、VSIX インストーラーを使用してツールの更新プログラムを起動します。

1. インストーラーで **[OK]** を選択して開始し、 **[変更]** を選択してツールを更新します。

1. 更新が完了したら、 **[閉じる]** を選択して Visual Studio を再起動します。

## <a name="create-an-azure-iot-edge-project"></a>Azure IoT Edge プロジェクトを作成する

Visual Studio の IoT Edge プロジェクト テンプレートでは、IoT Edge デバイスにデプロイできるソリューションが作成されます。 最初に Azure IoT Edge ソリューションを作成した後、そのソリューションで最初のモジュールを生成します。 各 IoT Edge ソリューションには、複数のモジュールを含めることができます。

> [!TIP]
> Visual Studio によって作成された IoT Edge プロジェクト構造は、Visual Studio Code と同じではありません。

1. Visual Studio で、新しいプロジェクトを作成します。

1. **[新しいプロジェクトの作成]** ページで、**Azure IoT Edge** を検索します。 IoT Edge デバイスのプラットフォームとアーキテクチャに一致するプロジェクトを選択し、 **[次へ]** をクリックします。

   :::image type="content" source="./media/how-to-visual-studio-develop-module/create-new-project.png" alt-text="新しいプロジェクトを作成":::

1. **[新しいプロジェクトの構成]** ページで、プロジェクトの名前を入力し、場所を指定して、 **[作成]** を選択します。

1. **[モジュールの追加]** ウィンドウで、開発するモジュールの種類を選択します。 **[既存のモジュール]** を選択して、既存の IoT Edge モジュールをデプロイに追加することもできます。 モジュール名とモジュール イメージ リポジトリを指定します。

   Visual Studio により、リポジトリ URL には自動的に **localhost:5000/<モジュール名\>** が設定されます。 テスト用に Docker のローカル レジストリを使用する場合、**localhost** で問題ありません。 Azure Container Registry を使用する場合、**localhost:5000** をお使いのレジストリの設定のログイン サーバーに置き換えます。 ログイン サーバーは **_\<registry name\>_ .azurecr.io** のようになります。最終的な結果は **\<*registry name*\>.azurecr.io/ _\<module name\>_** のようになります。

   **[追加]** を選択して、お使いのモジュールをプロジェクトに追加します。

   ![アプリケーションとモジュールの追加](./media/how-to-visual-studio-develop-csharp-module/add-module.png)

これで、IoT Edge プロジェクトと IoT Edge モジュールが Visual Studio ソリューションに作成されました。

モジュール フォルダーには、選択した言語に応じて、`program.cs` または `main.c` という名前のモジュール コード用ファイルが含まれています。 このフォルダーには、モジュールのメタデータを記述する `module.json` という名前のファイルも含まれています。 さまざまな Docker ファイルによって、モジュールをビルドするために必要な情報が、Windows または Linux コンテナーとして提供されます。

プロジェクト フォルダーには、そのプロジェクトに含まれるすべてのモジュールの一覧が含まれています。 現時点で表示されるモジュールは 1 つだけですが、さらに追加できます。 プロジェクトへのモジュールの追加の詳細については、この記事で後述する「[複数のモジュールのビルドとデバッグ](#build-and-debug-multiple-modules)」セクションを参照してください。

プロジェクト フォルダーには、`deployment.template.json` という名前のファイルも含まれています。 このファイルは、IoT Edge デプロイ マニフェストのテンプレートです。これにより、デバイス上で実行されるモジュールと、相互に通信する方法が定義されます。 デプロイ マニフェストの詳細については、[モジュールをデプロイしてルートを確立する方法](module-composition.md)に関するページを参照してください。 このデプロイ テンプレートを開いた場合、**edgeAgent** と **edgeHub** の2 つのランタイム モジュールと、この Visual Studio プロジェクト内で作成したカスタム モジュールが含まれていることを確認できます。 **SimulatedTemperatureSensor** という名前の 4 番目のモジュールも含まれています。 この既定のモジュールでは、モジュールのテストに使用できるシミュレートされたデータが生成されます。必要ない場合は削除します。 シミュレートされた温度センサーの仕組みについては、[SimulatedTemperatureSensor.csproj のソース コード](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor)を確認してください。

### <a name="set-iot-edge-runtime-version"></a>IoT Edge ランタイム バージョンを設定する

IoT Edge 拡張機能は、デプロイ アセットを作成するときに、IoT Edge ランタイムの最新の安定バージョンを既定として使用します。 現在、最新の安定バージョンはバージョン 1.2 です。 1\.1 の長期サポート バージョンまたはそれより前の 1.0 バージョンを実行しているデバイスのモジュールを開発している場合は、Visual Studio の IoT Edge ランタイム バージョンを更新して一致させます。

1. ソリューション エクスプローラーで、プロジェクト名を右クリックし、 **[Set IoT Edge Runtime version]\(IoT Edge ランタイム バージョンの設定\)** を選択します。

   :::image type="content" source="./media/how-to-visual-studio-develop-module/set-iot-edge-runtime-version.png" alt-text="プロジェクト名を右クリックし、[Set IoT Edge Runtime version]\(IoT Edge ランタイム バージョンの設定\) を選択します。":::

1. ドロップダウン メニューを使用して、IoT Edge デバイスが実行されているランタイム バージョンを選択し、 **[OK]** を選択して変更を保存します。

1. 新しいランタイム バージョンで配置マニフェストを再生成します。 プロジェクト名を右クリックし、 **[Generate Deployment for IoT Edge]\(IoT Edge の配置の生成\)** を選択します。

## <a name="develop-your-module"></a>モジュールの開発

新しいモジュールを追加すると、コードに触れることなくテストを開始できるよう、ビルドしてデバイスにデプロイする準備ができている既定のコードが付属します。 モジュール コードは、モジュール フォルダー内の `Program.cs` (C# の場合) または `main.c` (C の場合) という名前のファイルにあります。

既定のソリューションは、**SimulatedTemperatureSensor** モジュールのシミュレートされたデータがモジュールにルーティングされ、そこで入力が受け取られて IoT Hub に送信されるように構築されています。

モジュール テンプレートを独自のコードでカスタマイズする準備ができたら、[Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md) を使用して、セキュリティ、デバイス管理、信頼性など、IoT ソリューションの主なニーズに対処するモジュールをビルドします。

## <a name="set-up-the-iotedgehubdev-testing-tool"></a>iotedgehubdev テスト ツールを設定する

IoT edgeHub 開発ツールは、ローカルでの開発およびデバッグ エクスペリエンスを提供します。 このツールにより、IoT Edge ランタイムなしに IoT Edge モジュールを起動することができ、IoT Edge モジュールとソリューションのローカルでの作成、開発、テスト、実行、デバッグを行えます。 テストするために、イメージをコンテナー レジストリにプッシュしたり、それらをデバイスに配置したりする必要はありません。

詳細については、「[Azure IoT EdgeHub 開発ツール」](https://pypi.org/project/iotedgehubdev/)を参照してください。

ツールを初期化するには、IoT Hub からの IoT Edge デバイス接続文字列を指定します。

1. Azure portal、Azure CLI、または Visual Studio Cloud Explorer から IoT Edge デバイスの接続文字列を取得します。 

1. **[ツール]** メニューから **[Azure IoT Edge Tools]**  >  **[Setup IoT Edge Simulator]\(IoT Edge シミュレーターのセットアップ\)** の順に選択します。

1. 接続文字列を貼り付け、 **[OK]** をクリックします。

> [!NOTE]
> 結果は後続の Azure IoT Edge ソリューションすべてに自動的に適用されるため、お使いの開発用コンピューターではこの手順に一度だけ従う必要があります。 この手順にもう一度従うことができるのは、別の接続文字列への変更が必要になった場合のみです。

## <a name="build-and-debug-a-single-module"></a>1 つのモジュールをビルドしてデバッグする

通常は、複数のモジュールを含むソリューション全体の中で実行する前に、各モジュールをテストしてデバッグします。

>[!TIP]
>開発している IoT Edge モジュールの種類に応じて、適切な Docker コンテナー モード (Linux コンテナー モードまたは Windows コンテナー モード) に切り替えておいてください。 2 種類のモードの切り替えは、Docker Desktop メニューから行うことができます。 Windows コンテナーを使用する場合は **[Switch to Windows containers]\(Windows コンテナーに切り替える\)** を、Linux コンテナーを使用する場合は **[Switch to Linux containers]\(Linux コンテナーに切り替える\)** を選択します。 

1. **ソリューション エクスプローラー** でモジュール フォルダーを右クリックし、コンテキスト メニューから **[スタートアップ プロジェクトに設定]** を選択します。

   ![スタートアップ プロジェクトを設定する](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. **F5** キーを押すか、ツール バーの [実行] ボタンをクリックしてモジュールを実行します。 初めて行う場合は、10&ndash;20 秒かかる場合があります。

   ![モジュールを実行する](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. モジュールが正常に初期化された場合は .Net Core コンソール アプリが起動したことが示されます。

1. モジュールを検査するためのブレークポイントを設定します。

   * C# で開発する場合は **Program.cs** 内の `PipeMessage()` 関数にブレークポイントを設定します。
   * C を使用する場合は **main.c** 内の `InputQueue1Callback()` 関数にブレークポイントを設定します。

1. **Git Bash** または **WSL Bash** シェルで次のコマンドを実行してメッセージを送信することで、モジュールをテストします。 (`curl` コマンドを PowerShell またはコマンド プロンプトから実行することはできません)。

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![1 つのモジュールをデバッグする](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

   ブレークポイントがトリガーされるはずです。 Visual Studio の **[ローカル]** ウィンドウで変数を確認できます。

   > [!TIP]
   > `curl` の代わりに [PostMan](https://www.getpostman.com/) またはその他の API ツールを使用してメッセージを送信することもできます。

1. デバッグを停止するには、**Ctrl + F5** キーを押すか、停止ボタンをクリックします。

## <a name="build-and-debug-multiple-modules"></a>複数のモジュールのビルドとデバッグ

1 つのモジュールの開発が完了したら、複数のモジュールを含むソリューション全体を実行してデバッグします。

1. **ソリューション エクスプローラー** で、プロジェクト フォルダーを右クリックして、ソリューションに 2 つ目のモジュールを追加します。 メニューの **[追加]**  >  **[New IoT Edge Module]\(新しい IoT Edge モジュール\)** を選択します。

   ![新しいモジュールを既存の IoT Edge プロジェクトに追加する](./media/how-to-visual-studio-develop-csharp-module/add-new-module.png)

1. `deployment.template.json` ファイルを開くと、新しいモジュールが **modules** セクションに追加されていることがわかります。 新しいモジュールから IoT Hub にメッセージを送信するための新しいルートも **routes** セクションに追加されました。 シミュレートされた温度センサーから新しいモジュールにデータを送信する場合は、次の例のような別のルートを追加します。 

    ```json
   "sensorTo<NewModuleName>": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/<NewModuleName>/inputs/input1\")"
    ```

1. プロジェクト フォルダーを右クリックし、コンテキスト メニューから **[スタートアップ プロジェクトに設定]** を選択します。

1. ブレークポイントを作成した後、**F5** キーを押して、複数のモジュールを同時に実行してデバッグします。 複数の .Net Core コンソール アプリ ウィンドウが表示されます。各ウィンドウは異なるモジュールを表します。

   ![複数のモジュールをデバッグする](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. **Ctrl + F5** キーを押すか停止ボタンをクリックして、デバッグを停止します。

## <a name="build-and-push-images"></a>イメージをビルドしてプッシュする

1. IoT Edge プロジェクトが個別のモジュールではなく、スタートアップ プロジェクトであることを確認します。 モジュール イメージに対して作成する構成として **[デバッグ]** または **[リリース]** を選択します。

    > [!NOTE]
    > **[デバッグ]** を選択すると、Visual Studio では、Docker イメージのビルドに `Dockerfile.(amd64|windows-amd64).debug` が使用されます。 これにより、コンテナー イメージのビルド中に .NET Core コマンド ライン デバッガー VSDBG が組み込まれます。 運用環境対応の IoT Edge モジュールの場合は、 **[リリース]** 構成を使用することをお勧めします。これには、VSDBG が含まれていない `Dockerfile.(amd64|windows-amd64)` が使用されます。

1. Azure Container Registry (ACR) のようなプライベート レジストリを使用している場合は、次の Docker コマンドを使用してサインインします。  ユーザー名とパスワードは、Azure portal のレジストリの **[アクセス キー]** ページから取得できます。 ローカル レジストリを使用している場合は、[ローカル レジストリを実行する](https://docs.docker.com/registry/deploying/#run-a-local-registry)ことができます。

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Azure Container Registry のようなプライベート レジストリを使用している場合は、`deployment.template.json` ファイルにあるランタイム設定にレジストリのログイン情報を追加する必要があります。 プレースホルダーを実際の ACR 管理者ユーザー名、パスワード、レジストリ名に置き換えてください。

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

   >[!NOTE]
   >この記事では、開発とテストのシナリオに便利な、Azure Container Registry の管理者ログイン資格情報を使用します。 運用環境のシナリオに向けて準備ができたら、サービス プリンシパルのような最小限の特権で認証できるオプションを使用することをお勧めします。 詳細については、「[コンテナー レジストリへのアクセスを管理する](production-checklist.md#manage-access-to-your-container-registry)」を参照してください。

1. **ソリューション エクスプローラー** でプロジェクト フォルダーを右クリックし、 **[Build and Push IoT Edge Modules]\(IoT Edge モジュールをビルドしてプッシュする\)** を選択して、各モジュールの Docker イメージをビルドしてプッシュします。

## <a name="deploy-the-solution"></a>ソリューションのデプロイ方法

IoT Edge デバイスの設定に使用したクイック スタートの記事では、Azure portal を使用してモジュールをデプロイしました。 Visual Studio の Cloud Explorer を使用してモジュールをデプロイすることもできます。 実際のシナリオに合わせて既に配置マニフェスト (`deployment.json` ファイル) の準備が完了しているため、デプロイを受け取るデバイスを選択するだけで済みます。

1. **[表示]**  >  **[Cloud Explorer]** をクリックして、**Cloud Explorer** を開きます。 Visual Studio 2019 にログインしていることを確認します。

1. **Cloud Explorer** でサブスクリプションを展開し、Azure IoT Hub とデプロイする Azure IoT Edge デバイスを探します。

1. IoT Edge デバイスを右クリックして、デプロイを作成します。 `deployment.arm32v7.json` など、Visual Studio ソリューションの **config** フォルダーにある、プラットフォーム用に構成されている配置マニフェストに移動します。

1. 更新ボタンをクリックすると、新しいモジュールが、**SimulatedTemperatureSensor** モジュール、 **$edgeAgent** および **$edgeHub** と一緒に実行されていることが表示されます。

## <a name="view-generated-data"></a>生成されたデータを表示する

1. 特定の IoT Edge デバイスの D2C メッセージを監視するには、**Cloud Explorer** の IoT ハブでそれを選択し、 **[アクション]** ウィンドウで **[Start Monitoring Built-in Event Endpoint]\(組み込みイベント エンドポイントの監視を開始する\)** をクリックします。

1. データの監視を停止するには、 **[アクション]** ウィンドウで **[Stop Monitoring Built-in Event Endpoint]\(組み込みイベントのエンドポイントの監視を停止する\)** を選択します。

## <a name="next-steps"></a>次のステップ

お使いの IoT Edge デバイス用にカスタム モジュールを開発するには、「[Azure IoT Hub SDK の概要と使用方法](../iot-hub/iot-hub-devguide-sdks.md)」を参照してください。
