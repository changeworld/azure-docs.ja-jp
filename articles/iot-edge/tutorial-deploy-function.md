---
title: "Azure IoT Edge で Azure 関数を展開する | Microsoft Docs"
description: "Azure 関数をモジュールとしてエッジ デバイスに展開します"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: fb295b37819788ed14f54e4123ae0fe1b52d0210
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2017
---
# <a name="deploy-azure-function-as-an-iot-edge-module---preview"></a>Azure 関数を IoT Edge モジュールとして展開する - プレビュー
Azure Functions を使用して、ビジネス ロジックを実装するコードを IoT Edge デバイスに直接展開できます。 このチュートリアルでは、[Windows][lnk-tutorial1-win] または [Linux][lnk-tutorial1-lin] のシミュレートされたデバイスに Azure IoT Edge を展開するチュートリアルで作成した、シミュレートされた IoT Edge デバイスで、センサー データをフィルター処理する Azure 関数を作成および展開します。 このチュートリアルで学習する内容は次のとおりです。     

> [!div class="checklist"]
> * Visual Studio Code を使用して、Azure 関数を作成する
> * VS Code と Docker を使用して Docker イメージを作成し、レジストリに発行する 
> * モジュールを IoT Edge デバイスに展開する
> * 生成されたデータを表示する


このチュートリアルで作成した Azure 関数は、デバイスによって生成される温度データをフィルター処理し、指定されたしきい値を温度が上回っているときにのみ、上流の Azure IoT Hub にメッセージを送信します。 

## <a name="prerequisites"></a>前提条件

* クイック スタートまたは前のチュートリアルで作成した Azure IoT Edge デバイス。
* [Visual Studio Code](https://code.visualstudio.com/)。 
* [Visual Studio Code 用の C# (OmniSharp を使用) 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)  (拡張機能は Visual Studio Code の拡張機能パネルからインストールできます)。
* [Visual Studio Code 用の Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)  (拡張機能は Visual Studio Code の拡張機能パネルからインストールできます)。
* [Docker](https://docs.docker.com/engine/installation/)。 このチュートリアルでは、プラットフォームの Community Edition (CE) で十分です。 
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)。 

## <a name="set-up-a-docker-registry"></a>Docker レジストリを設定する
このチュートリアルでは、VS Code 用の Azure IoT Edge 拡張機能を使用して、Azure 関数を作成し、それを使用して [Docker イメージ](https://docs.docker.com/glossary/?term=image)をビルドします。 次に、この Docker イメージを、[Docker レジストリ](https://docs.docker.com/glossary/?term=registry)によってホストされている [Docker リポジトリ](https://docs.docker.com/glossary/?term=repository)にプッシュします。 最後に、[Docker コンテナー](https://docs.docker.com/glossary/?term=container)としてパッケージ化された Docker イメージを、レジストリから IoT Edge デバイスに展開します。  

このチュートリアルでは、Docker と互換性のある任意のレジストリをご利用いただけます。 クラウドで使用できる 2 つの一般的な Docker レジストリ サービスは、**Azure Container Registry** と **Docker Hub** です。

- [Azure Container Registry](https://docs.microsoft.com/en-us/azure/container-registry/) は、[有料サブスクリプション](https://azure.microsoft.com/en-us/pricing/details/container-registry/)で使用できます。 このチュートリアルには**基本**サブスクリプションで十分です。 

- [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) では、(無料) Docker ID にサインアップしたときに、無料のプライベート リポジトリが提供されます。 
    1. Docker ID にサインアップするには、Docker サイトで「[Register for a Docker ID (Docker ID の登録)](https://docs.docker.com/docker-id/#register-for-a-docker-id)」の手順に従います。 

    2. プライベート Docker リポジトリを作成するには、「[Creating a new repository on Docker Hub (Docker Hub での新しいリポジトリの作成)](https://docs.docker.com/docker-hub/repos/#creating-a-new-repository-on-docker-hub)」の手順に従います。

このチュートリアルでは、必要に応じて、Azure Container Registry と Docker Hub の両方のコマンドが提供されます。

## <a name="create-a-function-project"></a>関数プロジェクトを作成する
次の手順では、Visual Studio Code と Azure IoT Edge 拡張機能を使用して、IoT Edge 関数を作成する方法を説明します。
1. VS Code を開きます。
2. **[表示]、[統合ターミナル]** メニュー コマンドを使用して、VS Code 統合ターミナルを開きます。
3. 統合ターミナルで、次のコマンドを入力して、dotnet で **AzureIoTEdgeFunction** テンプレートをンストール (または更新) します。

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. 統合ターミナルで、次のコマンドを入力して、新しいモジュールのプロジェクトを作成します。

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction
    ```

    >[!NOTE]
    > このコマンドにより、現在の作業フォルダーにプロジェクト フォルダー **FilterFunction** が作成されます。 別の場所に作成する場合は、コマンドを実行する前にディレクトリを変更します。

3. **[ファイル]、[フォルダーを開く]** メニュー コマンドを使用して、**FilterFunction** フォルダーに移動し、**[フォルダーの選択]** をクリックして、VS Code でプロジェクトを開きます。
4. VS Code エクスプローラーで、**EdgeHubTrigger-Csharp** フォルダー、**run.csx** ファイルの順にクリックし、そのファイルを開きます。
5. `#r "Microsoft.Azure.Devices.Client"` ステートメントの後に、次のステートメントを追加します。

    ```csharp
    #r "Newtonsoft.Json"
    ```

5. 既存の `using` ステートメントの後に、次の using ステートメントを追加します。

    ```csharp
    using Newtonsoft.Json;
    ```

1. 次のクラスを追加します。 このクラスは、受信メッセージの本文に対して予期されるスキーマを定義します。

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

1. **Run** メソッドの本文を次のコードに置き換えます。 メッセージの本文に含まれる温度の値と、温度のしきい値に基づいてメッセージをフィルター処理します。

    ```csharp
    const int temperatureThreshold = 25;

    byte[] messageBytes = messageReceived.GetBytes();
    var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

    if (!string.IsNullOrEmpty(messageString))
    {
        // Get the body of the message and deserialize it
        var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);
        
        if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
        {
            // We will send the message to the output as the temperature value is greater than the threashold
            var filteredMessage = new Message(messageBytes);
            // We need to copy the properties of the original message into the new Message object
            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
            {
                filteredMessage.Properties.Add(prop.Key, prop.Value);
            }
            // We are adding a new property to the message to indicate it is an alert
            filteredMessage.Properties.Add("MessageType", "Alert");
            // Send the message        
            await output.AddAsync(filteredMessage);
            log.Info("Received and transferred a message with temperature above the threshold");
        }
    }
    ```

11. ファイルを保存します。

## <a name="publish-a-docker-image"></a>Docker イメージを発行する

1. Docker イメージをビルドします。
    1. VS Code エクスプローラーで **[Docker]** フォルダーをクリックして開きます。 コンテナー プラットフォームのフォルダーとして、**linux-x64** または **windows-nano** を選択します。 
    2. **[Dockerfile]** ファイルを右クリックし、**[Build IoT Edge module Docker image] (IoT Edge モジュール Docker イメージのビルド)** をクリックします。 
    3. **[フォルダーの選択]** ボックスでプロジェクト フォルダー **FilterFunction** に移動し、**[Select Folder as EXE_DIR]\(EXE_DIR としてフォルダーを選択\)** をクリックします。 
    4. VS Code ウィンドウの上部にあるポップアップ テキスト ボックスで、イメージの名前を入力します。 たとえば、`<docker registry address>/filterfunction:latest` を入力します。ここで、*docker registry address* は Docker ID (Docker Hub を使用している場合) または `<your registry name>.azurecr.io` のような名前 (Azure Container Registry を使用している場合) です。
 
4. Docker にサインインします。 統合ターミナルで、次のコマンドを入力します。 

    - Docker Hub (メッセージが表示されたら、資格情報を入力):
        
        ```csh/sh
        docker login
        ```

    - Azure Container Registry:
        
        ```csh/sh
        docker login -u <username> -p <password> <Login server>
        ```
        
        このコマンドで使用するユーザー名、パスワード、ログイン サーバーを見つけるには、Azure Portal (https://portal.azure.com) に移動します。 **[すべてのリソース]** から、Azure Container Registry のタイルをクリックして、プロパティを開き、**[アクセス キー]** をクリックします。 **ユーザー名**、**パスワード**、**ログイン サーバー**の各フィールドの値をコピーします。 ログイン サーバーの形式は `<your registry name>.azurecr.io` です。

3. イメージを Docker リポジトリにプッシュします。 **[表示]、[コマンド パレット]、[Edge: Push IoT Edge module Docker image] (Edge: IoT Edge モジュール Docker イメージをプッシュ)** メニュー コマンドを使用して、VS Code ウィンドウの上部にあるポップアップ テキスト ボックスにイメージ名を入力します。 手順 1.c. で使用したイメージ名と同じものを使用してください。

## <a name="add-registry-credentials-to-your-edge-device"></a>レジストリ資格情報を Edge デバイスに追加する
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

1. **Azure Portal** で、IoT ハブに移動します。
2. **IoT Edge (プレビュー)** に移動し、IoT Edge デバイスを選択します。
1. **[Set Modules] (モジュールの設定)** を選択します。 
2. **tempSensor** モジュールを追加します。 この手順は、**tempSensor** モジュールが IoT Edge デバイスに展開されていない場合にのみ必要です。
    1. **[Add IoT Edge Module] (IoT Edge モジュールの追加)** を選択します。
    2. **[名前]** フィールドに「`tempSensor`」と入力します。
    3. **[イメージの URI]** フィールドに「`microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`」と入力します。
    4. 他の設定はそのままにして、**[保存]** をクリックします。
1. **filterfunction** モジュールを追加します。
    1. もう一度 **[Add IoT Edge Module]\(IoT Edge モジュールの追加\)** を選択します。
    2. **[名前]** フィールドに「`filterfunction`」と入力します。
    3. **[イメージ]** フィールドに、イメージ アドレスを入力します (`<docker registry address>/filterfunction:latest` など)。
    74. **[Save]** をクリックします。
2. **[次へ]** をクリックします。
3. **[Specify Routes] (ルートの指定)** の手順で、下記の JSON をテキスト ボックスにコピーします。 モジュールによって、すべてのメッセージが Edge ランタイムに発行されます。 こうしたメッセージがフローする場所は、ランタイムの宣言型ルールによって定義されます。 このチュートリアルでは、2 つのルートが必要です。 1 つ目のルートは、**FilterMessages** ハンドラーで構成された "input1" エンドポイント経由で、温度センサーからフィルター モジュールにメッセージを転送します。 2 つ目のルートは、フィルター モジュールから IoT ハブにメッセージを転送します。 このルートでは、`upstream` は特別な転送先で、Edge Hub に対して、メッセージを IoT ハブに送信するように指示します。 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
    }
    ```

4. **[次へ]** をクリックします。
5. **[Review Template] (テンプレートのレビュー)** の手順で、**[送信]** をクリックします。 
6. IoT Edge デバイスの詳細ページに戻り、**[更新]** をクリックします。 新しい **filterfunction** モジュールが、**tempSensor** モジュールおよび **IoT Edge ランタイム**と一緒に実行されています。 

## <a name="view-generated-data"></a>生成されたデータを表示する

IoT Edge デバイスから IoT ハブに送信されたデバイスからクラウドへのメッセージを監視するには:
1. Azure IoT ツールキット拡張機能を IoT ハブの接続文字列で構成します。 
    1. **[表示]、[エクスプローラー]** メニュー コマンドを使用して、VS Code エクスプローラーを開きます。 
    3. エクスプローラーで、**[IOT HUB DEVICES]\(IoT ハブ デバイス\)**、**[...]** の順にクリックします。**[Set IoT Hub Connection String]\(IoT ハブ接続文字列の設定\)** をクリックし、ポップアップ ウィンドウに、IoT Edge デバイスの接続先 IoT ハブの接続文字列を入力します。 

        接続文字列を見つけるには、Azure Portal で IoT ハブのタイルをクリックし、**[共有アクセス ポリシー]** をクリックします。 **[共有アクセス ポリシー]** で、**[iothubowner]** ポリシーをクリックし、**[iothubowner]** ウィンドウで IoT ハブ接続文字列をコピーします。   

1. IoT ハブに届くデータを監視するには、**[表示]、[コマンド パレット]、[IoT: Start monitoring D2C message]\(IoT: D2C メッセージの監視を開始\)** メニュー コマンドを使用します。 
2. データの監視を停止するには、**[表示]、[コマンド パレット]、[IoT: Stop monitoring D2C message] (IoT: D2C メッセージの監視を停止)** メニュー コマンドを使用します。 

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、IoT Edge デバイスで生成された生データをフィルター処理するコードを含む、Azure 関数を作成しました。 Azure IoT Edge を探索し続けるには、Azure IoT Edge デバイスをゲートウェイとして使用する方法を確認します。 

> [!div class="nextstepaction"]
> [IoT Edge ゲートウェイ デバイスを作成する](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
