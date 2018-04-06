---
title: Visual Studio Code を使用して Azure IoT Edge で Azure Functions を開発およびデプロイする | Microsoft Docs
description: コンテキストを切り替えずに、VS Code で Azure IoT Edge を使用して C# Azure Functions を開発およびデプロイします。
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 219474a4577a76f5ceb9a9efaa3c349d633de047
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2018
---
# <a name="use-visual-studio-code-to-develop-and-deploy-azure-functions-to-azure-iot-edge"></a>Visual Studio Code を使用して Azure IoT Edge で Azure Functions を開発およびデプロイする

この記事では、主要開発ツールとして [Visual Studio Code](https://code.visualstudio.com/) を使用して、IoT Edge で Azure Functions を開発およびデプロイする手順について詳しく説明します。 

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、Windows または Linux を実行しているコンピューターまたは仮想マシンを開発用マシンとして使用していることを前提としています。 別の物理デバイスを IoT Edge デバイスとして使用することも、開発用マシンで IoT Edge デバイスをシミュレートすることもできます。

このガイダンスを開始する前に、次のチュートリアルを完了していることを確認してください。
- [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) または [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux) のシミュレートされたデバイスに Azure IoT Edge をデプロイする
- [Azure Functions のデプロイ](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function)

次のチェックリストは、上記のチュートリアルの終了後に準備する必要があるものを示しています。

- [Visual Studio Code](https://code.visualstudio.com/)。 
- [Visual Studio Code 用の Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)  
- [Visual Studio Code 用の C# (OmniSharp を使用) 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)  
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)。 
- [Python 2.7](https://www.python.org/downloads/)
- [IoT Edge コントロール スクリプト](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- AzureIoTEdgeFunction テンプレート (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
- 少なくとも 1 つの IoT Edge デバイスを含むアクティブな IoT Hub

モジュール イメージとコンテナーを効率的に管理するために、[Docker Support for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) もインストールすることをお勧めします。

> [!NOTE]
> 現時点では、IoT Edge の Azure Functions は C# のみをサポートしています。

## <a name="deploy-azure-iot-functions-in-vs-code"></a>VS Code で Azure IoT Functions をデプロイする
[Azure Functions のデプロイ](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function)に関するチュートリアルでは、VS Code で関数モジュール イメージを更新、ビルドし、発行した後、Azure Portal にアクセスして Azure Functions をデプロイしました。 このセクションでは、VS Code を使用して Azure Functions をデプロイし、監視する方法を紹介します。

### <a name="start-a-local-docker-registry"></a>ローカルDocker レジストリを起動する
このチュートリアルでは、Docker と互換性のある任意のレジストリをご利用いただけます。 クラウドで使用できる 2 つの一般的な Docker レジストリ サービスは、[Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) と [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) です。 このセクションでは、初期開発段階でのテストに使いやすい[ローカル Docker レジストリ](https://docs.docker.com/registry/deploying/)を使用します。
VS Code **統合ターミナル** (Ctrl + ` キー) で、次のコマンドを実行してローカル レジストリを起動します。  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> 上記の例は、テストにのみ適したレジストリ構成を示しています。 稼働準備済みのレジストリは TLS で保護する必要があり、アクセス制御メカニズムを使用するのが理想的です。 稼働準備済みの IoT Edge モジュールをデプロイするときは、[Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) または [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) を使用することをお勧めします。

### <a name="create-a-function-project"></a>関数プロジェクトを作成する
以下の手順では、Visual Studio Code と Azure IoT Edge 拡張機能を使用して、.NET core 2.0 に基づく IoT Edge モジュールを作成する方法を示します。 前のチュートリアルでこのセクションを完了している場合は、このセクションをスキップしても問題ありません。

1. Visual Studio Code で、**[表示]** > **[統合ターミナル]** を選択し、VS Code 統合ターミナルを開きます。
2. dotnet で **AzureIoTEdgeFunction** テンプレートをインストール (または更新) するには、統合ターミナルで次のコマンドを実行します。

   ```cmd/sh
   dotnet new -i Microsoft.Azure.IoT.Edge.Function
   ```
3. 新しいモジュールのプロジェクトを作成します。 次のコマンドにより、現在の作業フォルダーに **FilterFunction** プロジェクト フォルダーが作成されます。

   ```cmd/sh
   dotnet new aziotedgefunction -n FilterFunction
   ```
 
4. **[ファイル] > [フォルダーを開く]** を選択し、**FilterFunction** フォルダーを参照して、VS Code でプロジェクトを開きます。
5. **FilterFunction** フォルダーに移動し、**[フォルダーの選択]** をクリックして、VS Code でプロジェクトを開きます。
6. VS Code エクスプローラーで、**EdgeHubTrigger-Csharp** フォルダーを展開し、**run.csx** ファイルを開きます。
7. このファイルの内容を次のコードに置き換えます。

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
        const int temperatureThreshold = 25;
        byte[] messageBytes = messageReceived.GetBytes();
        var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

        if (!string.IsNullOrEmpty(messageString))
        {
            // Get the body of the message and deserialize it
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }
                // Add a new property to the message to indicate it is an alert
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message        
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
            }
        }
    }

    //Define the expected schema for the body of incoming messages
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

8. ファイルを保存します。

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Docker イメージを作成してレジストリに発行する

1. VS Code エクスプローラーで、**Docker** フォルダーを展開します。 次に、コンテナー プラットフォームのフォルダー、**linux-x64** または **windows-nano** を展開します。
2. **[Dockerfile]** ファイルを右クリックし、**[Build IoT Edge module Docker image] \(IoT Edge モジュール Docker イメージのビルド)** をクリックします。 

    ![Docker イメージのビルド](./media/how-to-vscode-develop-csharp-function/build-docker-image.png)

3. **FilterFunction** プロジェクト フォルダーに移動し、**[Select Folder as EXE_DIR]\(EXE_DIR としてフォルダーを選択\)** をクリックします。 
4. VS Code ウィンドウの上部にあるポップアップ テキスト ボックスで、イメージの名前を入力します。 たとえば、「 `<your container registry address>/filterfunction:latest`」のように入力します。 ローカル レジストリにデプロイする場合は、`localhost:5000/filterfunction:latest` のようになります。
5. イメージを Docker リポジトリにプッシュします。 **[Edge: Push IoT Edge module Docker image]\(Edge: IoT Edge モジュール Docker イメージをプッシュ\)** コマンドを使用し、VS Code ウィンドウの上部にあるポップアップ テキスト ボックスにイメージの URL を入力します。 前の手順で使用したイメージの URL を使用してください。
    ![Docker イメージのプッシュ](./media/how-to-vscode-develop-csharp-function/push-image.png)

### <a name="deploy-your-function-to-iot-edge"></a>関数を IoT Edge にデプロイする

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
   "filterfunction": {
      "version": "1.0",
      "type": "docker",
      "status": "running",
      "restartPolicy": "always",
      "settings": {
         "image": "localhost:5000/filterfunction:latest",
         "createOptions": ""
      }
   }
   ```

2. **routes** セクションを次の内容に置き換えます。
   ```json
       "routes":{
           "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
           "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
   ```
   > [!NOTE]
   > それらのメッセージが流れる場所は、ランタイム内の宣言型ルールによって定義されます。 このチュートリアルでは、2 つのルートが必要です。 1 つ目のルートは、 "input1" エンドポイント経由で、温度センサーからフィルター関数にメッセージを転送します。このエンドポイントは、FilterMessages ハンドラーで構成したものです。 2 つ目のルートは、フィルター関数から IoT Hub にメッセージを転送します。 このルートでは、上流は、IoT Hub にメッセージを送信するよう Edge Hub に指示する特別な転送先です。

3. このファイルを保存します。
4. コマンド パレットで、**[Edge: Create deployment for Edge device]\(Edge: Edge デバイスのデプロイの作成\)** を選択します。 次に、IoT Edge デバイス ID を選択してデプロイを作成します。 または、デバイスの一覧でデバイス ID を右クリックし、**[Create deployment for Edge device]\(Edge デバイスのデプロイの作成\)** を選択します。

    ![Create deployment](./media/how-to-vscode-develop-csharp-function/create-deployment.png)

5. 更新した `deployment.json` を選択します。 出力ウィンドウに、デプロイの対応する出力が表示されます。
6. コマンド パレットで Edge ランタイムを開始します  (**[Edge: Start Edge]\(Edge: Edge の開始\)**)。
7. シミュレートされたセンサーとフィルター関数で IoT Edge ランタイムの実行が開始されたことを Docker エクスプローラーで確認できます。

    ![実行中のソリューション](./media/how-to-vscode-develop-csharp-function/solution-running.png)

8. Edge デバイス ID を右クリックし、VS Code で D2C メッセージを監視できます。

    ![メッセージの監視](./media/how-to-vscode-develop-csharp-function/monitor-d2c-messages.png)


## <a name="next-steps"></a>次の手順

このチュートリアルでは、VS Code で Azure 関数を作成し、IoT Edge デバイスにデプロイしました。 次のチュートリアルに進み、VS Code で Azure IoT Edge を開発するときの他のシナリオを確認できます。

> [!div class="nextstepaction"]
> [VS Code で Azure Functions をデバッグする](how-to-vscode-debug-azure-function.md)
