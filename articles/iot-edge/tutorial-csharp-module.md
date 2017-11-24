---
title: "Azure IoT Edge C# モジュール | Microsoft Docs"
description: "C# コードで IoT Edge モジュールを作成し、エッジ デバイスにデプロイする"
services: iot-edge
keywords: 
author: JimacoMS2
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: fb7674d8c292e7d571a94ac4625b0858a90704b3
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>C# IoT Edge モジュールを開発して、シミュレートしたデバイスにデプロイする - プレビュー

IoT Edge モジュールを使用して、ビジネス ロジックを実装するコードを IoT Edge デバイスに直接デプロイできます。 このチュートリアルでは、[Windows][lnk-tutorial1-win] または [Linux][lnk-tutorial1-lin] のシミュレートされたデバイスで Azure IoT Edge をデプロイするチュートリアルで作成した、シミュレートされた IoT Edge デバイスで、センサー データをフィルター処理する IoT Edge モジュールを作成およびデプロイします。 このチュートリアルで学習する内容は次のとおりです。    

> [!div class="checklist"]
> * Visual Studio Code を使用して .NET core 2.0 に基づく IoT Edge モジュールを作成する
> * Visual Studio Code と Docker を使用して Docker イメージを作成し、レジストリに発行する 
> * モジュールを IoT Edge デバイスにデプロイする
> * 生成されたデータを表示する


このチュートリアルで作成する IoT Edge モジュールは、デバイスによって生成される温度データをフィルター処理し、指定されたしきい値を温度が上回っているときにのみ、メッセージを上流に送信します。 

## <a name="prerequisites"></a>前提条件

* クイックスタートまたは前のチュートリアルで作成した Azure IoT Edge デバイス。
* IoT Edge デバイスの接続先 IoT Hub に対する IoT Hub 接続文字列。  
* [Visual Studio Code](https://code.visualstudio.com/)。 
* [Visual Studio Code 用の Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)  (拡張機能は Visual Studio Code の拡張機能パネルからインストールできます)。
* [Visual Studio Code 用の C# (OmniSharp を使用) 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)  (拡張機能は Visual Studio Code の拡張機能パネルからインストールできます)。
* Visual Studio Code 用の Azure IoT Edge 拡張機能
* [Docker](https://docs.docker.com/engine/installation/)。 このチュートリアルでは、プラットフォームの Community Edition (CE) で十分です。 VS Code を実行するコンピューターにインストールするようにしてください。
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)。 

## <a name="choose-or-sign-up-for-a-docker-registry"></a>Docker レジストリを選択またはサインアップする
このチュートリアルでは、VS Code 用の Azure IoT Edge 拡張機能を使用してモジュールをビルドし、[Docker イメージ](https://docs.docker.com/glossary/?term=image)を作成します。 次に、この Docker イメージを、[Docker レジストリ](https://docs.docker.com/glossary/?term=registry)でホストされている [Docker リポジトリ](https://docs.docker.com/glossary/?term=repository)にプッシュします。 最後に、[Docker コンテナー](https://docs.docker.com/glossary/?term=container)としてパッケージ化された Docker イメージを、レジストリから IoT Edge デバイスにデプロイします。  

このチュートリアルでは、任意の Docker 対応レジストリを使用できます。 クラウドで使用できる 2 つの一般的な Docker レジストリ サービスは、**Azure Container Registry** と **Docker Hub** です。

- [Azure Container Registry](https://docs.microsoft.com/en-us/azure/container-registry/) は、[有料サブスクリプション](https://azure.microsoft.com/en-us/pricing/details/container-registry/)で使用できます。 このチュートリアルには**基本**サブスクリプションで十分です。 

- [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) では、(無料) Docker ID にサインアップしたときに、無料のプライベート リポジトリが提供されます。 
    1. Docker ID にサインアップするには、Docker サイトで「[Register for a Docker ID (Docker ID の登録)](https://docs.docker.com/docker-id/#register-for-a-docker-id)」の手順に従います。 

    2. プライベート Docker リポジトリを作成するには、Docker サイトで「[Creating a new repository on Docker Hub (Docker Hub での新しいリポジトリの作成)](https://docs.docker.com/docker-hub/repos/#creating-a-new-repository-on-docker-hub)」の手順に従います。

このチュートリアルでは、必要に応じて、Azure Container Registry と Docker Hub の両方のコマンドが提供されます。

## <a name="create-an-iot-edge-module-project"></a>IoT Edge モジュール プロジェクトを作成する
以下の手順では、Visual Studio Code と Azure IoT Edge 拡張機能を使用して、.NET core 2.0 に基づく IoT Edge モジュールを作成する方法を示します。
1. VS Code を開きます。
2. **[表示]、[統合ターミナル]** メニュー コマンドを使用して、VS Code 統合ターミナルを開きます。
3. 統合ターミナルで、次のコマンドを入力して、dotnet で **AzureIoTEdgeModule** テンプレートをインストール (または更新) します。

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. 統合ターミナルで、次のコマンドを入力して、新しいモジュールのプロジェクトを作成します。

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```

    >[!NOTE]
    > このコマンドにより、現在の作業フォルダーにプロジェクト フォルダー **FilterModule** が作成されます。 別の場所に作成する場合は、コマンドを実行する前にディレクトリを変更します。
 
3. **[ファイル]、[フォルダーを開く]** メニュー コマンドを使用して、**FilterModule** フォルダーに移動し、**[フォルダーの選択]** をクリックして、VS Code でプロジェクトを開きます。
4. VS Code エクスプローラーで **[Program.cs]** をクリックして開きます。
5. **Program** クラスに次のフィールドを追加します。

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

1. **Program** クラスに次のクラスを追加します。 これらのクラスは、受信メッセージの本文に対して予期されるスキーマを定義します。

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

1. **Init** メソッドでは、コードによって **DeviceClient** オブジェクトが作成され、構成されます。 このオブジェクトにより、モジュールはローカルの Azure IoT Edge ランタイムに接続し、メッセージを送受信することができます。 **Init** メソッドで使用される接続文字列パラメーターは、IoT Edge ランタイムによって環境変数 **EdgeHubConnectionString** 内のモジュールに提供されます。 **DeviceClient** の作成後、コードによって、IoT Edge ハブから **input1** エンドポイントを介してメッセージを受信するためのコールバックが登録されます。 メッセージを処理するためのコールバックとして使用されていたメソッドを新しいものに置換して、モジュール ツインで必要なプロパティを更新するためのコールバックをアタッチします。 この変更を行うには、**Init** メソッドの最後の行を次のコードに置き換えます。

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

1. 次のメソッドを **Program** クラスに追加し、モジュール ツイン経由でバックエンド サービスによって送信される必要なプロパティに基づいて **temperatureThreshold** フィールドを更新します。 すべてのモジュールに独自のモジュール ツインがあります。 モジュール ツインにより、バックエンド サービスはモジュール内で実行中のコードを構成できます。

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"].exists())
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

1. **PipeMessage** メソッドを次のメソッドに置き換えます。 Edge ハブからモジュールにメッセージが送信されるたびに、このメソッドが呼び出されます。 メッセージの本文に含まれる温度の値と、モジュール ツインを介して設定された温度のしきい値に基づいて、メッセージをフィルター処理します。

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

11. プロジェクトをビルドします。 **[表示]、[エクスプローラー]** メニュー コマンドを使用して、VS Code エクスプローラーを開きます。 エクスプローラーで **FilterModule.csproj** ファイルを右クリックし、**[Build IoT Edge module] (IoT Edge モジュールのビルド)** をクリックしてモジュールをコンパイルし、次の手順で Docker イメージを作成するフォルダーにバイナリとその依存関係をエクスポートします。

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Docker イメージを作成してレジストリに発行する

1. Docker イメージをビルドします。
    1. VS Code エクスプローラーで **[Docker]** フォルダーをクリックして開きます。 コンテナー プラットフォームのフォルダーとして、**linux-x64** または **windows-nano** を選択します。 
    2. **[Dockerfile]** ファイルを右クリックし、**[Build IoT Edge module Docker image] (IoT Edge モジュール Docker イメージのビルド)** をクリックします。 
    3. **[フォルダーの選択]** ボックスで、`./bin/Debug/netcoreapp2.0/publish` に移動するか入力します。 **[Select Folder as EXE_DIR] (EXE_DIR としてフォルダーを選択)** をクリックします。
    4. VS Code ウィンドウの上部にあるポップアップ テキスト ボックスで、イメージの名前を入力します。 たとえば、`<docker registry address>/filtermodule:latest` を入力します。ここで、*docker registry address* は Docker ID (Docker Hub を使用している場合) または `<your registry name>.azurecr.io` のような名前 (Azure Container Registry を使用している場合) です。
 
4. Docker にサインインします。 統合ターミナルで、次のコマンドを入力します。 

    - Docker Hub (メッセージが表示されたら、資格情報を入力):
        
        ```csh/sh
        docker login
        ```

    - Azure Container Registry の場合:
        
        ```csh/sh
        docker login -u <username> -p <password> <Login server>
        ```
        
        このコマンドで使用するユーザー名、パスワード、ログイン サーバーを見つけるには、Azure Portal (https://portal.azure.com) に移動します。 **[すべてのリソース]** から、Azure Container Registry のタイルをクリックして、プロパティを開き、**[アクセス キー]** をクリックします。 **ユーザー名**、**パスワード**、**ログイン サーバー**の各フィールドの値をコピーします。 ログイン サーバーの形式は `<your registry name>.azurecr.io` です。

3. イメージを Docker リポジトリにプッシュします。 **[表示]、[コマンド パレット]、[Edge: Push IoT Edge module Docker image] (Edge: IoT Edge モジュール Docker イメージをプッシュ)** メニュー コマンドを使用して、VS Code ウィンドウの上部にあるポップアップ テキスト ボックスにイメージ名を入力します。 手順 1.c. で使用したのと同じイメージ名を使用してください。

## <a name="add-registry-credentials-to-edge-runtime-on-your-edge-device"></a>Edge デバイスの Edge ランタイムにレジストリの資格情報を追加する
Edge デバイスを実行しているコンピューターの Edge ランタイムに、レジストリの資格情報を追加します。 これにより、コンテナーを取得するためのランタイム アクセスが得られます。 

- Windows の場合は、次のコマンドを実行します。
    
    ```cmd/sh
    iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

- Linux の場合は、次のコマンドを実行します。
    
    ```cmd/sh
    sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

## <a name="run-the-solution"></a>ソリューションを実行する

1. [Azure Portal](https://portal.azure.com) で、IoT ハブに移動します。
2. **IoT Edge (プレビュー)** に移動し、IoT Edge デバイスを選択します。
3. **[Set Modules] (モジュールの設定)** を選択します。 
2. **tempSensor** モジュールを追加します。 この手順は、以前に **tempSensor** モジュールが IoT Edge デバイスにデプロイされていない場合にのみ必要です。
    1. **[Add IoT Edge Module] (IoT Edge モジュールの追加)** を選択します。
    2. **[名前]** フィールドに「`tempSensor`」と入力します。
    3. **[イメージの URL]** フィールドに「`microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`」と入力します。
    4. 他の設定はそのままにして、**[保存]** をクリックします。
9. **filtermodule** を追加します。
    1. もう一度 **[Add IoT Edge Module] (IoT Edge モジュールの追加)** を選択します。
    2. **[名前]** フィールドに「`filtermodule`」と入力します。
    3. **[イメージ]** フィールドに、イメージ アドレスを入力します (`<docker registry address>/filtermodule:latest` など)。
    4. **[Edit module twin] (編集モジュール ツイン)** チェック ボックスをオンにします。
    5. モジュール ツインのテキスト ボックスの JSON を、次の JSON に置き換えます。 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. **[ Save]** をクリックします。
12. **[次へ]** をクリックします。
13. **[Specify Routes] (ルートの指定)** の手順で、下記の JSON をテキスト ボックスにコピーします。 モジュールによって、すべてのメッセージが Edge ランタイムに発行されます。 それらのメッセージが流れる場所は、ランタイム内の宣言型ルールによって定義されます。 このチュートリアルでは、2 つのルートが必要です。 1 つ目のルートは、 "input1" エンドポイント経由で、温度センサーからフィルター モジュールにメッセージを転送します。このエンドポイントは、**FilterMessages** ハンドラーで構成したものです。 2 つ目のルートは、フィルター モジュールから IoT Hub にメッセージを転送します。 このルートでは、`upstream` は、メッセージを IoT Hub に送信するように Edge Hub に指示する特別な転送先です。 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. **[次へ]** をクリックします。
5. **[Review Template] (テンプレートのレビュー)** の手順で、**[送信]** をクリックします。 
6. IoT Edge デバイスの詳細ページに戻り、**[更新]** をクリックします。 新しい **filtermodule** が、**tempSensor** モジュールおよび **IoT Edge ランタイム**と一緒に実行されていることが表示されます。 

## <a name="view-generated-data"></a>生成されたデータを表示する

IoT Edge デバイスから IoT Hub に送信されたデバイスからクラウドへのメッセージを監視するには:
1. IoT Hub の接続文字列を使用して Azure IoT ツールキット拡張機能を構成します。 
    1. **[表示]、[エクスプローラー]** メニュー コマンドを使用して、VS Code エクスプローラーを開きます。 
    3. エクスプローラーで、**[IOT HUB DEVICES] (IoT Hub デバイス)**、**[...]** の順にクリックします。**[Set IoT Hub Connection String] (IoT Hub 接続文字列の設定)** をクリックし、ポップアップ ウィンドウで、IoT Edge デバイスの接続先 IoT Hub の接続文字列を入力します。 

        接続文字列を見つけるには、Azure Portal で IoT Hub のタイルをクリックし、**[共有アクセス ポリシー]** をクリックします。 **[共有アクセス ポリシー]** で、**[iothubowner]** ポリシーをクリックし、**[iothubowner]** ウィンドウで IoT Hub 接続文字列をコピーします。   

1. IoT Hub に届くデータを監視するには、**[表示]、[コマンド パレット]、[IoT: Start monitoring D2C message] (IoT: D2C メッセージの監視を開始)** メニュー コマンドを使用します。 
2. データの監視を停止するには、**[表示]、[コマンド パレット]、[IoT: Stop monitoring D2C message] (IoT: D2C メッセージの監視を停止)** メニュー コマンドを使用します。 

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、IoT Edge デバイスによって生成された生データをフィルター処理するコードを含む IoT Edge モジュールを作成しました。 引き続き以下のチュートリアルのいずれかを実行して、エッジでデータをビジネス上の洞察に変えることに Azure IoT Edge がどのように役立つかを学習できます。

> [!div class="nextstepaction"]
> [Azure 関数をモジュールとしてデプロイする](tutorial-deploy-function.md)
> [Azure Stream Analytics をモジュールとしてデプロイする](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
