---
title: Azure IoT Edge C# モジュール | Microsoft Docs
description: C# コードで IoT Edge モジュールを作成し、エッジ デバイスにデプロイする
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/14/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 1da3a246a2ad33a4563f491058f5d4d115f3954d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631073"
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>C# IoT Edge モジュールを開発して、シミュレートしたデバイスにデプロイする - プレビュー

IoT Edge モジュールを使用して、ビジネス ロジックを実装するコードを IoT Edge デバイスに直接展開できます。 このチュートリアルでは、センサー データをフィルター処理する IoT Edge モジュールを作成および展開する方法について説明します。 [Windows][lnk-tutorial1-win] または [Linux][lnk-tutorial1-lin] のシミュレートされたデバイスに Azure IoT Edge を展開するチュートリアルで作成した、シミュレートされた IoT Edge デバイスを使用します。 このチュートリアルで学習する内容は次のとおりです。    

> [!div class="checklist"]
> * Visual Studio Code を使用して .NET core 2.0 に基づく IoT Edge モジュールを作成する
> * Visual Studio Code と Docker を使用して Docker イメージを作成し、レジストリに発行する 
> * モジュールを IoT Edge デバイスにデプロイする
> * 生成されたデータを表示する


このチュートリアルで作成する IoT Edge モジュールは、デバイスによって生成される温度データをフィルター処理します。 これは、指定されたしきい値を温度が上回っているときにのみ、メッセージを上流に送信します。 エッジでのこの種の分析は、クラウドに送信および保存されるデータ量を削減するうえで役に立ちます。 

## <a name="prerequisites"></a>前提条件

* クイック スタートまたは最初のチュートリアルで作成した Azure IoT Edge デバイス。
* IoT Edge デバイスの主キー接続文字列。  
* [Visual Studio Code](https://code.visualstudio.com/)。 
* [Visual Studio Code 用の Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)  
* [Visual Studio Code 用の C# (OmniSharp を使用) 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)  
* [Docker](https://docs.docker.com/engine/installation/)。Visual Studio Code が含まれるコンピューターに必要です。 このチュートリアルには、Community Edition (CE) で十分です。 
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)。 

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成
このチュートリアルでは、VS Code 用の Azure IoT Edge 拡張機能を使用してモジュールをビルドし、ファイルから**コンテナー イメージ**を作成します。 その後、このイメージを**レジストリ**にプッシュし、格納および管理します。 最後に、レジストリからイメージを展開し、IoT Edge デバイスで実行します。  

このチュートリアルでは、Docker と互換性のある任意のレジストリをご利用いただけます。 クラウドで使用できる 2 つの一般的な Docker レジストリ サービスは、[Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) と [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) です。 このチュートリアルでは、Azure Container Registry を使用します。 

1. [Azure Portal](https://portal.azure.com) で、**[リソースの作成]** > **[コンテナー]** > **[Azure Container Registry]** の順に選択します。
2. レジストリに名前を付けて、サブスクリプション、リソース グループを選択し、SKU を **[Basic]** に設定します。 
3. **[作成]** を選択します。
4. コンテナー レジストリが作成されたら、そこに移動し、**[アクセス キー]** を選択します。 
5. **[管理者ユーザー]** を **[有効]** に切り替えます。
6. **ログイン サーバー**、**ユーザー名**、および**パスワード**の値をコピーします。 これらの値は、後ほどこのチュートリアルで、レジストリに Docker イメージを発行するときと、Edge ランタイムにレジストリの資格情報を追加するときに使用します。 

## <a name="create-an-iot-edge-module-project"></a>IoT Edge モジュール プロジェクトを作成する
以下の手順では、Visual Studio Code と Azure IoT Edge 拡張機能を使用して、.NET core 2.0 に基づく IoT Edge モジュールを作成する方法を示します。
1. Visual Studio Code で、**[表示]** > **[統合ターミナル]** を選択し、VS Code 統合ターミナルを開きます。
2. 統合ターミナルで、次のコマンドを入力して、dotnet で **AzureIoTEdgeModule** テンプレートをインストール (または更新) します。

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

3. 新しいモジュールのプロジェクトを作成します。 次のコマンドにより、プロジェクト フォルダー **FilterModule** とご自身のコンテナー リポジトリが作成されます。 Azure コンテナー レジストリを使用している場合、2 番目のパラメーターの形式は `<your container registry name>.azurecr.io` にする必要があります。 現在の作業フォルダーで次のコマンドを入力します。

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule -r <your container registry address>/filtermodule
    ```
 
4. **[ファイル]** > **[フォルダーを開く]** を選択します。
5. **FilterModule** フォルダーに移動し、**[フォルダーの選択]** をクリックして、VS Code でプロジェクトを開きます。
6. VS Code エクスプローラーで **[Program.cs]** をクリックして開きます。

   ![Program.cs を開く][1]

7. **[FilterModule]** 名前空間の上部で、あとで使用する型として 3 つの `using` ステートメントを追加します。

    ```csharp
    using System.Collections.Generic;     // for KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // for TwinCollection
    using Newtonsoft.Json;                // for JsonConvert
    ```

8. `temperatureThreshold` 変数を **Program** クラスに追加します。 この変数により、データが IoT Hub に送信される基準値が設定されます。データは、測定温度がこの値を超えると送信されます。 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

9. `MessageBody`、`Machine`、および `Ambient` クラスを **Program** クラスに追加します。 これらのクラスは、受信メッセージの本文に対して予期されるスキーマを定義します。

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

10. **Init** メソッドでは、コードによって **DeviceClient** オブジェクトが作成され、構成されます。 このオブジェクトにより、モジュールはローカルの Azure IoT Edge ランタイムに接続し、メッセージを送受信することができます。 **Init** メソッドで使用される接続文字列は、IoT Edge ランタイムによってモジュールに提供されます。 **DeviceClient** の作成後、コードによって、Module Twin の目的のプロパティから TemperatureThreshold が読み取られ、IoT Edge ハブから **input1** エンドポイントを介してメッセージを受信するためのコールバックが登録されます。 `SetInputMessageHandlerAsync` メソッドを新しいメソッドで置き換え、対象プロパティの更新のために `SetDesiredPropertyUpdateCallbackAsync` メソッドを追加します。 この変更を行うには、**Init** メソッドの最後の行を次のコードに置き換えます。

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read TemperatureThreshold from Module Twin Desired Properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    var moduleTwinCollection = moduleTwin.Properties.Desired;
    try {
        temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
    } catch(ArgumentOutOfRangeException e) {
        Console.WriteLine("Property TemperatureThreshold not exist");
    }

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

11. `onDesiredPropertiesUpdate` メソッドを **Program** クラスに追加します。 このメソッドは、モジュール ツインから対象プロパティの更新を受け取り、それに合わせて **temperatureThreshold** 変数を更新します。 すべてのモジュールに独自のモジュール ツインがあり、これにより、モジュール内で実行されているコードをクラウドから直接構成できます。

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

12. `PipeMessage` メソッドを `FilterMessages` メソッドで置き換えます。 このメソッドは、モジュールが IoT Edge ハブからメッセージを受け取るたびに呼び出されます。 これにより、モジュール ツインで設定されているしきい値を下回る温度を報告するメッセージは除外されます。 また、**MessageType** プロパティを、値が **Alert** に設定されたメッセージに追加します。 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
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
            var deviceClient = (DeviceClient)userContext;
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

13. このファイルを保存します。

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Docker イメージを作成してレジストリに発行する

1. VS Code 統合ターミナルで次のコマンドを入力して、Docker にサインインします。 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   このコマンドで使用するユーザー名、パスワード、ログイン サーバーを見つけるには、[Azure Portal] (https://portal.azure.com) に移動します。 **[すべてのリソース]** から、Azure Container Registry のタイルをクリックして、プロパティを開き、**[アクセス キー]** をクリックします。 **ユーザー名**、**パスワード**、**ログイン サーバー**の各フィールドの値をコピーします。 

2. VS Code エクスプローラーで、**module.json** ファイルを右クリックし、**[Build and Push IoT Edge module Docker image]\(IoT Edge モジュール Docker イメージをビルドしてプッシュ\)** をクリックします。 VS Code ウィンドウの上部にあるポップアップ ドロップダウン ボックスで、コンテナー プラットフォームを選択します。Linux コンテナーの場合は **[Amd64]**、Windows コンテナーの場合は **[windows-amd64]** を選択します。 VS Code によってコードがビルドされ、`FilterModule.dll` がコンテナーに格納されて、指定したコンテナー レジストリにプッシュされます。


3. VS Code 統合ターミナルで、タグを含む完全なコンテナー イメージ アドレスを取得できます。 ビルドおよびプッシュ定義の詳細については、`module.json` ファイルを参照してください。

## <a name="add-registry-credentials-to-edge-runtime"></a>Edge ランタイムにレジストリの資格情報を追加する
Edge デバイスを実行しているコンピューターの Edge ランタイムに、レジストリの資格情報を追加します。 この資格情報により、コンテナーをプルするためのアクセス権がランタイムに付与されます。 

- Windows の場合は、次のコマンドを実行します。
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- Linux の場合は、次のコマンドを実行します。
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>ソリューションを実行する

1. [Azure Portal](https://portal.azure.com) で、IoT ハブに移動します。
2. **[IoT Edge (preview)]\(IoT Edge \(プレビュー\)\)** に移動し、IoT Edge デバイスを選びます。
3. **[Set Modules] \(モジュールの設定)** を選択します。 
4. **tempSensor** モジュールが自動的に設定されていることを確認します。 設定されていない場合は、次の手順を使用して追加します。
    1. **[Add IoT Edge Module]\(IoT Edge モジュールの追加\)** を選びます。
    2. **[名前]** フィールドに「`tempSensor`」と入力します。
    3. **[イメージの URI]** フィールドに「`microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`」と入力します。
    4. 他の設定はそのままにして、**[保存]** をクリックします。
5. 前のセクションで作成した **filterModule** モジュールを追加します。 
    1. **[Add IoT Edge Module]\(IoT Edge モジュールの追加\)** を選びます。
    2. **[名前]** フィールドに「`filterModule`」と入力します。
    3. **[イメージの URI]** フィールドに、イメージ アドレスを入力します (`<your container registry address>/filtermodule:0.0.1-amd64` など)。 完全なイメージ アドレスは、前のセクションから見つけることができます。
    4. **[有効]** ボックスをオンにして、モジュール ツインを編集できるようにします。 
    5. モジュール ツインのテキスト ボックスの JSON を、次の JSON に置き換えます。 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. **[Save]** をクリックします。
6. **[次へ]** をクリックします。
7. **[Specify Routes] \(ルートの指定)** の手順で、下記の JSON をテキスト ボックスにコピーします。 モジュールによって、すべてのメッセージが Edge ランタイムに発行されます。 メッセージが流れる場所は、ランタイム内の宣言型ルールによって定義されます。 このチュートリアルでは、2 つのルートが必要です。 1 つ目のルートは、**FilterMessages** ハンドラーで構成された "input1" エンドポイント経由で、温度センサーからフィルター モジュールにメッセージを転送します。 2 つ目のルートは、フィルター モジュールから IoT ハブにメッセージを転送します。 このルートでは、`upstream` は特別な転送先で、Edge Hub に対して、メッセージを IoT ハブに送信するように指示します。 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

8. **[次へ]** をクリックします。
9. **[Review Template] \(テンプレートのレビュー)** の手順で、**[送信]** をクリックします。 
10. IoT Edge デバイスの詳細ページに戻り、**[更新]** をクリックします。 新しい **filtermodule** が、**tempSensor** モジュールおよび **IoT Edge ランタイム**と一緒に実行されていることが表示されます。 

## <a name="view-generated-data"></a>生成されたデータを表示する

IoT Edge デバイスから IoT ハブに送信されたデバイスからクラウドへのメッセージを監視するには:
1. Azure IoT ツールキット拡張機能を IoT ハブの接続文字列で構成します。 
    1. **[表示]** > **[エクスプローラー]** の順に選択して、VS Code エクスプローラーを開きます。 
    2. エクスプローラーで、**[IOT HUB DEVICES]\(IoT ハブ デバイス\)**、**[...]** の順にクリックします。**[Set IoT Hub Connection String]\(IoT ハブ接続文字列の設定\)** をクリックし、ポップアップ ウィンドウに、IoT Edge デバイスの接続先 IoT ハブの接続文字列を入力します。 

        接続文字列を見つけるには、Azure Portal で IoT ハブのタイルをクリックし、**[共有アクセス ポリシー]** をクリックします。 **[共有アクセス ポリシー]** で、**[iothubowner]** ポリシーをクリックし、**[iothubowner]** ウィンドウで IoT ハブ接続文字列をコピーします。   

2. IoT ハブに届くデータを監視するには、**[表示]** > **[コマンド パレット]** の順に選択し、**[IoT: Start monitoring D2C message]\(IoT: D2C メッセージの監視を開始\)** メニュー コマンドを検索します。 
3. データの監視を停止するには、**[IoT: Stop monitoring D2C message] \(IoT: D2C メッセージの監視を停止)** メニュー コマンドを使用します。 

## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Edge デバイスによって生成された生データをフィルター処理するコードを含む IoT Edge モジュールを作成しました。 引き続き以下のチュートリアルのいずれかを実行して、エッジでデータをビジネス上の洞察に変えることに Azure IoT Edge がどのように役立つかを学習できます。

> [!div class="nextstepaction"]
> [Azure 関数をモジュールとしてデプロイする](tutorial-deploy-function.md)
> [Azure Stream Analytics をモジュールとしてデプロイする](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
