---
title: "Visual Studio Code を使用して Azure IoT Edge で C# モジュールを開発する | Microsoft Docs"
description: "コンテキストを切り替えずに、VS Code で Azure IoT Edge を使用して C# モジュールを開発およびデプロイします。"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 269f77e5015175e45e0078926ef06699811889a4
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2017
---
# <a name="use-visual-studio-code-to-develop-c-module-with-azure-iot-edge"></a>Visual Studio Code を使用して Azure IoT Edge で C# モジュールを開発する
この記事では、主要開発ツールとして [Visual Studio Code](https://code.visualstudio.com/) を使用して、IoT Edge モジュールを開発およびデプロイする手順について詳しく説明します。 

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、Windows または Linux を実行しているコンピューターまたは仮想マシンを開発用マシンとして使用していることを前提としています。 別の物理デバイスを IoT Edge デバイスとして使用することも、開発用マシンで IoT Edge デバイスをシミュレートすることもできます。

このガイダンスを開始する前に、次のチュートリアルを完了していることを確認してください。
- [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) または [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux) のシミュレートされたデバイスに Azure IoT Edge をデプロイする
- [C# IoT Edge モジュールを開発して、シミュレートされたデバイスにデプロイする](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module)

次のチェックリストは、上記のチュートリアルの終了後に準備する必要があるものを示しています。

- [Visual Studio Code](https://code.visualstudio.com/)。 
- [Visual Studio Code 用の Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)  
- [Visual Studio Code 用の C# (OmniSharp を使用) 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)  
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)。 
- [Python 2.7](https://www.python.org/downloads/)
- [IoT Edge 制御スクリプト](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- AzureIoTEdgeModule テンプレート (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- 少なくとも 1 つの IoT Edge デバイスを含むアクティブな IoT ハブ

モジュール イメージとコンテナーを効率的に管理するために、[Docker Support for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) もインストールすることをお勧めします。

## <a name="deploy-an-azure-iot-edge-module-in-vs-code"></a>VS Code での Azure IoT Edge モジュールのデプロイ

### <a name="list-your-iot-hub-devices"></a>IoT ハブ デバイスの一覧を表示する
VS Code で IoT ハブ デバイスの一覧を表示する方法は 2 つあります。 どちらの方法を選択しても構いません。

#### <a name="sign-in-your-azure-account-in-vscode-and-choose-your-iot-hub"></a>VSCode で Azure アカウントにサインインし、IoT ハブを選択します。
1. コマンド パレット (F1 キーまたは Ctrl + Shift + P キー) で、「**Azure: Sign in\(Azure: サインイン\)**」と入力して選択します。 ポップアップで **[Copy* & Open]\(コピー & 開く)** をクリックします。 ブラウザーにコードを貼り付け (Ctrl + V キー)、[続行] ボタンをクリックします。 次に、Azure アカウントを使用してログインします。 VS Code のステータス バーにアカウント情報が表示されます。
2. コマンド パレット (F1 キーまたは Ctrl + Shift + P キー) で、「**IoT: Select IoT Hub\(IoT: IoT ハブの選択\)**」と入力して選択します。 まず、前のチュートリアルで IoT ハブを作成したサブスクリプションを選択します。 次に、IoT Edge デバイスを含む IoT ハブを選択します。


#### <a name="set-iot-hub-connection-string"></a>IoT ハブ接続文字列を設定する
1. コマンド パレット (F1 キーまたは Ctrl + Shift + P キー) で、「**IoT: Set IoT Hub Connection String\(IoT: IoT ハブ接続文字列の設定\)**」と入力して選択します。 必ず、**iothubowner** ポリシーの接続文字列を貼り付けます (このポリシーは、Azure Portal の IoT ハブの共有アクセス ポリシーにあります)。
 

左側のサイド バーにある IoT Hub Device Explorer にデバイスの一覧が表示されます。

### <a name="start-your-iot-edge-runtime-and-deploy-a-module"></a>IoT Edge ランタイムを開始してモジュールをデプロイする
Azure IoT Edge ランタイムをデバイスにインストールして開始します。 また、テレメトリ データを IoT ハブに送信するシミュレートされたセンサー モジュールをデプロイします。
1. コマンド パレットで、**[Edge: Setup Edge]\(Edge: Edge のセットアップ\)** を選択し、IoT Edge デバイス ID を選択します。 または、デバイス一の覧で Edge デバイス ID を右クリックし、**[Setup Edge]\(Edge のセットアップ\)** を選択します。
2. コマンド パレットで、**[Edge: Start Edge]\(Edge: Edge の開始\)** を選択して Edge ランタイムを開始します。 対応する出力が統合ターミナルに表示されます。
3. Docker エクスプローラーで Edge ランタイムの状態を確認します。 緑色は実行中であることを示しています。 IoT Edge ランタイムが正常に開始されました。
4. 現在、Edge ランタイムが実行されています。これは、PC が Edge デバイスをシミュレートしていることを意味します。 次に、Edge デバイスにメッセージを送信し続ける sensorthing をシミュレートします。 コマンド パレットで、「**Edge: Generate Edge configuration file\(Edge: Edge 構成ファイルの生成\)**」と入力して選択します。 このファイルを作成するフォルダーを選択します。 生成された deployment.json ファイルで、"<registry>/<image>:<tag>" 行を `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview` に置き換えます。
5. **[Edge: Create deployment for Edge device]\(Edge: Edge デバイスのデプロイの作成\)** を選択し、Edge デバイス ID を選択して新しいデプロイを作成します。 または、デバイスの一覧で Edge デバイス ID を右クリックし、**[Create deployment for Edge device]\(Edge デバイスのデプロイの作成\)** を選択することもできます。 
6. シミュレートされたセンサーで IoT Edge の実行が開始されたことを Docker エクスプローラーで確認する必要があります。 Docker エクスプローラーでコンテナーを右クリックします。 各モジュールの Docker ログを確認できます。
7. Edge デバイス ID を右クリックし、VS Code で D2C メッセージを監視できます。
8. IoT Edge ランタイムとセンサー モジュールを停止するには、コマンド パレットで「**Edge: Stop Edge\(Edge: Edge の停止\)**」と入力して選択します。

## <a name="develop-and-deploy-a-c-module-in-vs-code"></a>VS Code での C# モジュールの開発とデプロイ
[C# モジュールの開発](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module)に関するチュートリアルでは、VS Code でモジュール イメージを更新、ビルドし、発行した後、Azure Portal にアクセスして C# モジュールをデプロイしました。 このセクションでは、VS Code を使用して C# モジュールをデプロイし、監視する方法を紹介します。

### <a name="start-a-local-docker-registry"></a>ローカルDocker レジストリを起動する
このチュートリアルでは、Docker と互換性のある任意のレジストリをご利用いただけます。 クラウドで使用できる 2 つの一般的な Docker レジストリ サービスは、[Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) と [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) です。 このセクションでは、初期開発段階でのテストに使いやすい[ローカル Docker レジストリ](https://docs.docker.com/registry/deploying/)を使用します。
VS Code **統合ターミナル** (Ctrl + ` キー) で、次のコマンドを実行してローカル レジストリを起動します。  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> 上記の例は、テストにのみ適したレジストリ構成を示しています。 稼働準備済みのレジストリは TLS で保護する必要があり、アクセス制御メカニズムを使用するのが理想的です。 稼働準備済みの IoT Edge モジュールをデプロイするときは、[Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) または [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) を使用することをお勧めします。

### <a name="create-an-iot-edge-module-project"></a>IoT Edge モジュール プロジェクトを作成する
以下の手順では、Visual Studio Code と Azure IoT Edge 拡張機能を使用して、.NET core 2.0 に基づく IoT Edge モジュールを作成する方法を示します。 前のチュートリアルでこのセクションを完了している場合は、このセクションをスキップしても問題ありません。
1. Visual Studio Code で、**[表示]** > **[統合ターミナル]** を選択し、VS Code 統合ターミナルを開きます。
3. 統合ターミナルで、次のコマンドを入力して、dotnet で **AzureIoTEdgeModule** テンプレートをインストール (または更新) します。

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. 新しいモジュールのプロジェクトを作成します。 次のコマンドにより、現在の作業フォルダーにプロジェクト フォルダー **FilterModule** が作成されます。

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```
 
3. **[ファイル]** > **[フォルダーを開く]** を選択します。
4. **FilterModule** フォルダーに移動し、**[フォルダーの選択]** をクリックして、VS Code でプロジェクトを開きます。
5. VS Code エクスプローラーで **[Program.cs]** をクリックして開きます。
6. `temperatureThreshold` 変数を **Program** クラスに追加します。 この変数により、データが IoT Hub に送信される基準値が設定されます。データは、測定温度がこの値を超えると送信されます。 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

7. `MessageBody`、`Machine`、および `Ambient` クラスを **Program** クラスに追加します。 これらのクラスは、受信メッセージの本文に対して予期されるスキーマを定義します。

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
       public double temperature {get; set;}
       public double pressure {get; set;}         
    }
    class Ambient
    {
       public double temperature {get; set;}
       public int humidity {get; set;}         
    }
    ```

8. **Init** メソッドでは、コードによって **DeviceClient** オブジェクトが作成され、構成されます。 このオブジェクトにより、モジュールはローカルの Azure IoT Edge ランタイムに接続し、メッセージを送受信することができます。 **Init** メソッドで使用される接続文字列は、IoT Edge ランタイムによってモジュールに提供されます。 **DeviceClient** の作成後、コードによって、IoT Edge ハブから **input1** エンドポイントを介してメッセージを受信するためのコールバックが登録されます。 `SetInputMessageHandlerAsync` メソッドを新しいメソッドで置き換え、対象プロパティの更新のために `SetDesiredPropertyUpdateCallbackAsync` メソッドを追加します。 この変更を行うには、**Init** メソッドの最後の行を次のコードに置き換えます。

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

9. `onDesiredPropertiesUpdate` メソッドを **Program** クラスに追加します。 このメソッドは、モジュール ツインから対象プロパティの更新を受け取り、それに合わせて **temperatureThreshold** 変数を更新します。 すべてのモジュールに独自のモジュール ツインがあり、これにより、モジュール内で実行されているコードをクラウドから直接構成できます。

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

10. `PipeMessage` メソッドを `FilterMessages` メソッドで置き換えます。 このメソッドは、モジュールが IoT Edge ハブからメッセージを受け取るたびに呼び出されます。 これにより、モジュール ツインで設定されているしきい値を下回る温度を報告するメッセージは除外されます。 また、**MessageType** プロパティを、値が **Alert** に設定されたメッセージに追加します。 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        int counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            byte[] messageBytes = message.GetBytes();
            string messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get message body
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await deviceClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

11. プロジェクトをビルドするには、エクスプローラーで **FilterModule.csproj** ファイルを右クリックし、**[Build IoT Edge module]\(IoT Edge モジュールのビルド\)** をクリックします。 このプロセスにより、モジュールがコンパイルされ、バイナリとその依存関係が、Docker イメージの作成に使用するフォルダーにエクスポートされます。


### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Docker イメージを作成してレジストリに発行する

1. VS Code エクスプローラーで、**Docker** フォルダーを展開します。 次に、コンテナー プラットフォームのフォルダー、**linux-x64** または **windows-nano** を展開します。
2. **[Dockerfile]** ファイルを右クリックし、**[Build IoT Edge module Docker image] (IoT Edge モジュール Docker イメージのビルド)** をクリックします。 
3. **[フォルダーの選択]** ウィンドウで、`./bin/Debug/netcoreapp2.0/publish` を参照するか入力します。 **[Select Folder as EXE_DIR] (EXE_DIR としてフォルダーを選択)** をクリックします。
4. VS Code ウィンドウの上部にあるポップアップ テキスト ボックスで、イメージの名前を入力します。 たとえば、「 `<your container registry address>/filtermodule:latest`」のように入力します。 ローカル レジストリにデプロイする場合は、`localhost:5000/filtermodule:latest` のようになります。
5. イメージを Docker リポジトリにプッシュします。 **[Edge: Push IoT Edge module Docker image]\(Edge: IoT Edge モジュール Docker イメージをプッシュ\)** コマンドを使用し、VS Code ウィンドウの上部にあるポップアップ テキスト ボックスにイメージの URL を入力します。 前の手順で使用したイメージの URL を使用してください。

### <a name="deploy-your-iot-edge-modules"></a>IoT Edge モジュールをデプロイする

1. `deployment.json` ファイルを開き、**modules** セクションを次の内容に置き換えます。
    ```json
    "tempSensor": {
        "version": "1.0",
        "type": "docker",
        "status": "running",
        "restartPolicy": "always",
        "settings": {
            "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
            "createOptions": ""
        }
    },
    "filtermodule": {
        "version": "1.0",
        "type": "docker",
        "status": "running",
        "restartPolicy": "always",
        "settings": {
            "image": "localhost:5000/filtermodule:latest",
            "createOptions": ""
        }
    }
    ```

2. **routes** セクションを次の内容に置き換えます。
    ```json
    {
        "routes": {
            "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
            "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        }
    }
    ```
   > [!NOTE]
   > それらのメッセージが流れる場所は、ランタイム内の宣言型ルールによって定義されます。 このチュートリアルでは、2 つのルートが必要です。 1 つ目のルートは、"input1" エンドポイント経由で温度センサーからフィルター モジュールにメッセージを転送します。このエンドポイントは、FilterMessages ハンドラーで構成したものです。 2 つ目のルートは、フィルター モジュールから IoT ハブにメッセージを転送します。 このルートでは、上流は、IoT ハブにメッセージを送信するよう Edge Hub に指示する特別な転送先です。

3. このファイルを保存します。
4. コマンド パレットで、**[Edge: Create deployment for Edge device]\(Edge: Edge デバイスのデプロイの作成\)** を選択します。 次に、IoT Edge デバイス ID を選択してデプロイを作成します。 または、デバイスの一覧でデバイス ID を右クリックし、**[Create deployment for Edge device]\(Edge デバイスのデプロイの作成\)** を選択します。
5. 更新した `deployment.json` を選択します。 出力ウィンドウに、デプロイの対応する出力が表示されます。
6. コマンド パレットで Edge ランタイムを開始します  (**[Edge: Start Edge]\(Edge: Edge の開始\)**)。
7. シミュレートされたセンサーとフィルター モジュールで IoT Edge ランタイムの実行が開始されたことを Docker エクスプローラーで確認できます。
8. Edge デバイス ID を右クリックし、VS Code で D2C メッセージを監視できます。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、VS Code で IoT Edge モジュールを作成し、IoT Edge デバイスにデプロイしました。 次のチュートリアルに進み、VS Code で Azure IoT Edge を開発するときの他のシナリオを確認できます。

> [!div class="nextstepaction"]
> [VS Code での C# モジュールのデバッグ](how-to-vscode-debug-csharp-module.md)
