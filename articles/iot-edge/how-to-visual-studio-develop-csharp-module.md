---
title: Visual Studio 2017 で Azure IoT Edge 用の C# モジュールを開発してデバッグする | Microsoft Docs
description: Visual Studio 2017 を使用して Azure IoT Edge 用の C# モジュールを開発してデバッグします
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/24/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: ab4dd1186715fde51fbf188ace902c8092d192d0
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647189"
---
# <a name="use-visual-studio-2017-to-develop-and-debug-c-modules-for-azure-iot-edge-preview"></a>Visual Studio 2017 を使用して Azure IoT Edge 用の C# モジュールを開発してデバッグする (プレビュー)

ビジネス ロジックを Azure IoT Edge のモジュールにすることができます。 この記事では、主なツールとして Visual Studio 2017 を使用して C# モジュールを開発してデバッグする方法を説明します。

Azure IoT Edge Tools for Visual Studio の利点は次のとおりです。

- ローカル開発用コンピューターで Azure IoT Edge ソリューションとモジュールの作成、編集、ビルド、実行、デバッグを行うことができます。
- Azure IoT Hub を使用して Azure IoT Edge ソリューションを Azure IoT Edge デバイスにデプロイできます。
- Visual Studio での開発のすべての利点を利用しながら、C# で Azure IoT モジュールをコーディングできます。
- Azure IoT Edge デバイスとモジュールを UI で管理できます。 

この時事では、Azure IoT Edge Tools for Visual Studio 2017 を使用して、C# で IoT Edge モジュールを開発する方法を示します。 Azure IoT Edge デバイスにプロジェクトをデプロイする方法についても説明します。

## <a name="prerequisites"></a>前提条件
この記事では、Windows を実行しているコンピューターまたは仮想マシンを開発用マシンとして使用していることを前提としています。 IoT Edge デバイスとして、別の物理デバイスを指定できます。

この記事ではメインの開発ツールとして Visual Studio 2017 を使うため、Visual Studio をインストールします。 また、Visual Studio 2017 のインストールには、必ず **Azure 開発ワークロード**を含めてください。 [Visual Studio 2017 を変更](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017)して、Azure 開発ワークロードを追加できます。

Visual Studio 2017 の準備ができたら、以下も必要です。

- Visual Studio Marketplace から [Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)をダウンロードしてインストールし、Visual Studio 2017 で IoT Edge プロジェクトを作成します。
- モジュール イメージをビルドして実行するための、開発用コンピューター上の [Docker Community Edition](https://docs.docker.com/install/)。 Linux コンテナー モードまたは Windows コンテナー モードで実行する Docker CE を適切に設定する必要があります。
- デバッグのためのローカルの開発環境をセットアップし、IoT Edge ソリューションを実行してテストするには、[Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/) が必要です。 [Python (2.7/3.6) と Pip](https://www.python.org/) をインストールします。 次に、ターミナルで以下のコマンドを実行して **iotedgehubdev** をインストールします。 Azure IoT EdgeHub Dev Tool のバージョンが 0.3.0 より大きいことを確認します。

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) または [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   - プロトタイプおよびテスト目的で、クラウド レジストリの代わりに Docker のローカル レジストリを使用できます。 

- モジュールをテストするには、少なくとも 1 つの IoT Edge デバイス ID が作成されているアクティブな IoT ハブが必要です。 開発コンピューター上で IoT Edge セキュリティ デーモンを実行している場合は、Visual Studio での開発を始める前に、EdgeHub と EdgeAgent を停止することが必要な場合があります。 

### <a name="check-your-tools-version"></a>ツールのバージョンを確認する

1. **[ツール]** メニューの **[拡張機能と更新プログラム]** を選択します。 **[インストール済み] > [ツール]** を展開すると、**Azure IoT Edge** と **Cloud Explorer** が表示されます。

2. インストールされているバージョンを確認します。 このバージョンと、Visual Studio Marketplace での最新バージョンを比較できます ([Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)、[Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.azureiotedgetools))

3. インストールされているバージョンが古い場合は、次のセクションの説明に従って Visual Studio でツールを更新します。

### <a name="update-your-tools"></a>ツールを更新する

1. **[拡張機能と更新プログラム]** ダイアログで、**[更新プログラム] >[Visual Studio Marketplace]** を展開し、**[Azure IoT Edge]** または **[Cloud Explorer]** を選択して、**[更新]** を選択します。

2. ツールの更新プログラムをダウンロードしたら、Visual Studio を終了し、VSIX インストーラーを使用してツールの更新プログラムを起動します。

3. インストーラーで **[OK]** を選択して開始し、[変更] を選択してツールを更新します。

4. 更新が完了したら、[閉じる] を選択して Visual Studio を再起動します。

### <a name="create-an-azure-iot-edge-project"></a>Azure IoT Edge プロジェクトを作成する

Visual Studio の Azure IoT Edge プロジェクト テンプレートでは、Azure IoT Hub の Azure IoT Edge デバイスにデプロイできるプロジェクトが作成されます。 最初に Azure IoT Edge ソリューションを作成した後、そのソリューションで最初の C# モジュールを生成します。 各 IoT Edge ソリューションには、複数のモジュールを含めることができます。 

1. Visual Studio で、**[ファイル]** メニューから **[新規]** > **[プロジェクト]** の順に選択します。

2. **[新しいプロジェクト]** ダイアログで、**[インストール済み]** を選択し、**[Visual C#] > [クラウド]** の順に展開して、**[Azure IoT Edge]** を選択します。プロジェクトの **[名前]** を入力し、場所を指定して、**[OK]** をクリックします。 既定のプロジェクト名は **AzureIoTEdgeApp1** です。 

   ![[新しいプロジェクト]](./media/how-to-visual-studio-develop-csharp-module/create-new.jpg)

3. **[IoT Edge Module Configuration]\(IoT Edge モジュールの構成\)** ウィンドウで **[C# Module]\(C# モジュール\)** タイプを選択し、モジュール名とモジュール イメージ リポジトリを指定します。  VS によってモジュール名に **localhost:5000** と自動的に入力されます。 独自のレジストリ情報に置き換えます。 テスト用に Docker のローカル レジストリを使用する場合、localhost で問題ありません。 Azure Container Registry を使用する場合、お使いのレジストリの設定のログイン サーバーを使用します。 ログイン サーバーは **<registry name>.azurecr.io** のようになります。 文字列の localhost 部分のみを置き換え、モジュール名は削除しないでください。 既定のモジュール名は **IoTEdgeModule1** です。

4. **[OK]** をクリックして、C# モジュールを含む Azure IoT Edge プロジェクトを作成します。

ソリューションには **AzureIoTEdgeApp1** プロジェクトと **IoTEdgeModule1** プロジェクトが含まれるようになっています。 **AzureIoTEdgeApp1** プロジェクトには **deployment.template.json** ファイルが含まれます。 このファイルでは、IoT Edge ソリューション用にビルドしてデプロイするモジュールと、モジュール間のルートが定義されています。 既定のソリューションには、**tempSensor** モジュールと **IoTEdgeModule1** モジュールが含まれます。 **tempSensor** モジュールでは、**IoTEdgeModule1** モジュールに対するシミュレートされたデータが生成されます。**IoTEdgeModule1** モジュールの既定のコードはパイプ メッセージ モジュールであり、受け取ったメッセージを IoT Hub に直接パイプします。

**IoTEdgeModule1** プロジェクトは、.Net Core 2.1 のコンソール アプリケーションです。 IoT Edge デバイスが Windows コンテナーまたは Linux コンテナーで実行されるために必要な **Dockerfile** が含まれています。 **module.json** ファイルでは、モジュールのメタデータが記述されています。 **program.cs** はモジュールの実際のコードであり、依存関係として Azure IoT Device SDK を取得します。

## <a name="develop-your-module"></a>モジュールの開発

ソリューションに付属する既定の C# モジュール コードは、**IoTEdgeModule1** > **Program.cs** にあります。 モジュールと deployment.template.json ファイルが設定されます。これでソリューションをビルドし、それをコンテナー レジストリにプッシュして、デバイスにデプロイすることで、コードを操作することなくテストを開始できます。 モジュールはソース (このケースでは、データをシミュレートする tempSensor モジュール) から入力を取得して、IoT Hub に送信します。 

C# テンプレートを独自のコードでカスタマイズする準備ができたら、[Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md) を使用して、セキュリティ、デバイス管理、信頼性など、IoT ソリューションの主なニーズに対処するモジュールをビルドします。 

## <a name="initialize-iotegehubdev-with-iot-edge-device-connection-string"></a>IoT Edge デバイス接続文字列を使用して **iotegehubdev** を初期化する

1. 任意の IoT Edge デバイスの接続文字列を取得する必要があります。Visual Studio 2017 の Cloud Explorer から [プライマリ接続文字列] の値をコピーします。 非 Edge デバイスの接続文字列はコピーしないでください。IoT Edge デバイスのアイコンは非 Edge デバイスのアイコンとは異なります。

   ![Edge デバイスの接続文字列をコピーする](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

2. **AzureIoTEdgeApp1** プロジェクトを右クリックしてコンテキスト メニューを開き、**[Set Edge Device Connection String]\(Edge デバイスの接続文字列の設定\)** をクリックすると、Azure IoT Edge の設定画面が表示されます。

   ![[Set Edge Connection String]\(Edge の接続文字列の設定\) 画面を開く](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

3. 設定画面で、最初の手順で取得した接続文字列を入力し、**[OK]** ボタンをクリックします。

>[!NOTE]
>これは 1 回限りの作業で、この手順は 1 つのマシンにつき 1 回のみ実行する必要があります。後続のすべての Azure IoT Edge ソリューションは追加の手順なしに使用できます。 もちろん、接続文字列が無効な場合や、別の接続文字列に変更する必要がある場合はこの手順を再度実行できます。

## <a name="build-and-debug-single-c-module"></a>1 つの C# モジュールをビルドしてデバッグする

通常は、複数のモジュールを含むソリューション全体の中で実行させる前に、各モジュールをテスト/デバッグします。

1. コンテキスト メニューでスタートアップ プロジェクトとして **IoTEdgeModule1** を選択します。

   ![スタートアップ プロジェクトを設定する](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

2. **F5** キーを押すか、下のボタンをクリックして、モジュールを実行します。初めての場合は、10 秒から 20 秒かかることがあります。

   ![モジュールを実行する](./media/how-to-visual-studio-develop-csharp-module/run-module.png)


3. モジュールが正常に初期化された場合、.Net Core コンソール アプリが起動したことが示されます。

   ![実行されているモジュール](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

4. **Program.cs** の **PipeMessage** にブレークポイントを設定してから、**Git Bash** または **WSL Bash** で次のコマンドを実行して、メッセージを送信できます (CMD または PowerShell では実行しないでください) (このコマンドは VS 出力ウィンドウでも見つかります)。

    ```cmd
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![1 つのモジュールをデバッグする](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    ブレークポイントがトリガーされるはずです。 Visual Studio の [ローカル] ウィンドウで変数を確認できます。

   > [!TIP]
   > `curl` の代わりに [PostMan](https://www.getpostman.com/) またはその他の API ツールを使用してメッセージを送信することもできます。

5. デバッグを停止するには、**Ctrl + F5** キーを押すか、停止ボタンをクリックします。

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>複数のモジュールで IoT Edge ソリューションをビルドしてデバッグする

1 つのモジュールの開発が終了したら、次に、複数のモジュールでソリューション全体を実行してデバッグします。

1. 2 つ目の C# モジュールをソリューションに追加します。 **AzureIoTEdgeApp1** を右クリックして、**[追加]** -> **[New IoT Edge Module]\(新しい IoT Edge モジュール\)** を選択します。 2 つ目のモジュールの既定の名前は **IoTEdgeModule2** であり、やはりパイプ モジュールです。

2. **deployment.template.json** に移動します。 **IoTEdgeModule2** が **modules** セクションに追加されていることがわかります。 **routes** セクションを次の内容に置き換えます。 モジュール名をカスタマイズしている場合は、置き換えた後に以下で名前を更新してください。

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

3. コンテキスト メニューでスタートアップ プロジェクトとして **AzureIoTEdgeApp1** プロジェクトを設定します。

4. ブレークポイントを設定して F5 キーを押すと、複数のモジュールを同時に実行してデバッグできます。 複数の .Net Core コンソール アプリ ウィンドウが表示され、それぞれのウィンドウで C# モジュールが示されます。 

   ![複数のモジュールをデバッグする](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

5. デバッグを停止するには、**Ctrl + F5** キーを押すか、停止ボタンをクリックします。

## <a name="build-and-push-images"></a>イメージをビルドしてプッシュする

1. **AzureIoTEdgeApp1** がスタートアップ プロジェクトになっていることを確認します。 ビルドするモジュール イメージの構成として **[デバッグ]** または **[リリース]** を選択します。

    > [!NOTE]
    > **[デバッグ]** を選択すると、VS による Docker イメージのビルドに `Dockerfile.debug` が使用されます。 これにより、コンテナー イメージのビルド中に .NET Core コマンド ライン デバッガー VSDBG が組み込まれます。 運用可能な IoT Edge モジュールの場合は、VSDBG を含まない `Dockerfile` が使用される**リリース**構成を使用することをお勧めします。

2. Azure Container Registry などのプライベート レジストリを使用する場合は、ターミナルで次のコマンドを使用して Docker ログを実行します。 ローカル レジストリを使用している場合は、[ローカル レジストリを実行する](https://docs.docker.com/registry/deploying/#run-a-local-registry)ことができます。

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server> 
    ```

3. Azure Container Registry などをプライベート レジストリを使用している場合は、以下の内容のランタイム設定の `deployment.template.json` にレジストリのユーザー名とパスワードを追加する必要があります。 忘れずに、プレースホルダーを実際の管理者のユーザー名とパスワードに置き換えてください。

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

4. **AzureIoTEdgeApp1** を右クリックしてコンテキスト メニューの項目 **[Build and Push Edge Solution]\(Edge ソリューションのビルドとプッシュ\)** を選択すると、各モジュールの Docker イメージがビルドされてプッシュされます。

   ![イメージをビルドしてプッシュする](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)


## <a name="deploy-the-solution"></a>ソリューションのデプロイ方法

IoT Edge デバイスの設定に使用したクイック スタートの記事では、Azure portal を使用してモジュールをデプロイしました。 Visual Studio の Cloud Explorer を使用してモジュールをデプロイすることもできます。 シナリオ用の配置マニフェストである `deployment.json` ファイルは、既に用意されています。 ここで行う必要があるのは、デプロイを受け取るデバイスの選択だけです。

1. **[表示]** > **[Cloud Explorer]** をクリックして、**Cloud Explorer** を開きます。 自分のアカウントで Visual Studio 2017 にログインしていることを確認します。

2. **Cloud Explorer** でサブスクリプションを展開し、Azure IoT Hub とデプロイする Azure IoT Edge デバイスを探します。

3. IoT Edge デバイスを右クリックしてデプロイを作成します。`$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json` にある配置マニフェスト ファイルを選択する必要があります。

>>[!NOTE]
>>`$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json` は選択しないでください

4. 更新ボタンをクリックします。 新しいモジュールが、**TempSensor** モジュール、**$edgeAgent** および **$edgeHub** と一緒に実行されていることが表示されます。

## <a name="view-generated-data"></a>生成されたデータを表示する

1. 特定のデバイスの D2C のメッセージを監視するには、一覧でデバイスをクリックし、**[アクション]** ウィンドウで **[Start Monitoring D2C Messages]\(D2C メッセージの監視を開始\)** をクリックします。

2. データの監視を停止するには、一覧でデバイスをクリックし、**[アクション]** ウィンドウで **[Stop Monitoring D2C Messages]\(D2C メッセージの監視を停止\)** を選択します。

## <a name="next-steps"></a>次の手順

お使いの IoT Edge デバイス用のモジュールを開発する方法について詳しくは、「[Azure IoT Hub SDK の概要と使用方法](../iot-hub/iot-hub-devguide-sdks.md)」をご覧ください。
