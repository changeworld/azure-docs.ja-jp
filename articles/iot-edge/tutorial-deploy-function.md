---
title: Azure IoT Edge で Azure 関数を展開する | Microsoft Docs
description: このチュートリアルでは、Azure 関数をモジュールとしてエッジ デバイスに展開します。
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: d37e08f58986a1318e6b379d2efeb71bc58d4583
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413742"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules-preview"></a>チュートリアル: Azure 関数を IoT Edge モジュールとして展開する (プレビュー)

Azure Functions を使用して、ビジネス ロジックを実装するコードを Azure IoT Edge デバイスに直接展開できます。 このチュートリアルでは、シミュレートされた IoT Edge デバイス上のセンサー データをフィルター処理する Azure 関数の作成と展開について段階的に説明します。 [Windows][lnk-tutorial1-win] または [Linux][lnk-tutorial1-lin] のシミュレートされたデバイスに Azure IoT Edge を展開するクイック スタートで作成した、シミュレートされた IoT Edge デバイスを使用します。 このチュートリアルで学習する内容は次のとおりです。     

> [!div class="checklist"]
> * Visual Studio Code を使用して、Azure 関数を作成する。
> * VS Code と Docker を使用して Docker イメージを作成し、コンテナー レジストリに発行する。
> * コンテナー レジストリから IoT Edge デバイスにモジュールを配置する。
> * フィルター処理されたデータを表示する。

>[!NOTE]
>Azure IoT Edge 上の Azure 関数モジュールは[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)段階にあります。 

このチュートリアルで作成する Azure 関数は、デバイスによって生成される温度データをフィルター処理します。 この関数は、指定されたしきい値を温度が上回っているときにのみ、上流の Azure IoT Hub にメッセージを送信します。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

Azure IoT Edge デバイス:

* [Linux デバイス](quickstart-linux.md) または [Windows デバイス](quickstart.md)のクイック スタートに記載された手順に従って開発マシンまたは仮想マシンをエッジ デバイスとして使用できます。

クラウド リソース:

* Azure の Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。 

開発リソース:

* [Visual Studio Code](https://code.visualstudio.com/)。 
* [Visual Studio Code 用の C# (OmniSharp を使用) 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* Visual Studio Code 用の [Azure IoT Edge 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
* [The .NET Core 2.1 SDK](https://www.microsoft.com/net/download)。
* [Docker CE](https://docs.docker.com/install/)。 

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成
このチュートリアルでは、VS Code 用の Azure IoT Edge 拡張機能を使用してモジュールをビルドし、ファイルから**コンテナー イメージ**を作成します。 その後、このイメージを**レジストリ**にプッシュし、格納および管理します。 最後に、レジストリからイメージを展開し、IoT Edge デバイスで実行します。  

このチュートリアルでは、Docker と互換性のある任意のレジストリをご利用いただけます。 クラウドで使用できる 2 つの一般的な Docker レジストリ サービスは、[Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) と [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) です。 このチュートリアルでは、Azure Container Registry を使用します。 

1. [Azure portal](https://portal.azure.com) で、**[リソースの作成]** > **[コンテナー]** > **[コンテナー レジストリ]** の順に選択します。

    ![コンテナー レジストリの作成](./media/tutorial-deploy-function/create-container-registry.png)

2. レジストリの名前を入力し、サブスクリプションを選択します。
3. リソース グループには、IoT Hub が含まれているのと同じリソース グループの名前を使用することをお勧めします。 すべてのリソースを同じグループにまとめておくことで、それらを一緒に管理できます。 たとえば、テストに使用されるリソース グループを削除すると、グループに含まれているすべてのテスト リソースが削除されます。 
4. SKU を **[Basic]** に設定し、**[管理者ユーザー]** を **[有効]** に切り替えます。 
5. **[作成]** を選択します。
6. コンテナー レジストリが作成されたら、その場所を参照し、**[アクセス キー]** を選択します。 
7. **ログイン サーバー**、**ユーザー名**、および**パスワード**の値をコピーします。 これらの値を、このチュートリアルで後ほど使用します。 

## <a name="create-a-function-project"></a>関数プロジェクトを作成する
以下の手順では、Visual Studio Code と Azure IoT Edge 拡張機能を使用して、IoT Edge 関数を作成します。

1. Visual Studio Code を開きます。
2. **[表示]** > **[統合ターミナル]** の順に選択して、VS Code 統合ターミナルを開きます。 
2. **[表示]** > **[コマンド パレット]** を選択して、VS Code コマンド パレットを開きます。
3. コマンド パレットで、**Azure: Sign in** コマンドを入力して実行します。 手順に従って Azure アカウントにサインインします。 既にサインインしている場合、この手順は省略できます。
3. コマンド パレットで、**Azure IoT Edge: New IoT Edge solution** コマンドを入力して実行します。 コマンド パレットで、次の情報を指定してソリューションを作成します。 

   1. ソリューションの作成先フォルダーを選択します。 
   2. ソリューションの名前を指定するか、既定の **EdgeSolution** をそのまま使用します。
   3. **Azure Functions - C#** をモジュール テンプレートとして選択します。 
   4. モジュールに **CSharpFunction** という名前を付けます。 
   5. 前のセクションで作成した Azure Container Registry を、最初のモジュールのイメージ リポジトリとして指定します。 **localhost:5000** を、コピーしたログイン サーバーの値に置き換えます。 文字列は最終的に、\<レジストリ名\>.azurecr.io/csharpfunction のようになります。

4. VS Code ウィンドウによって、ご自身の IoT Edge ソリューション ワークスペース (\.vscode フォルダー、modules フォルダー、配置マニフェスト テンプレート ファイル、 \.env ファイル) が読み込まれます。 VS Code エクスプローラーで、**[モジュール]** > **[CSharpFunction]** > **[EdgeHubTrigger-Csharp]** > **[run.csx]** の順に開きます。

5. このファイルの内容を次のコードに置き換えます。

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
            // Get the body of the message and deserialize it.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold.
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object.
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);                }
                // Add a new property to the message to indicate it is an alert.
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message.       
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
            }
        }
    }

    //Define the expected schema for the body of incoming messages.
    class MessageBody
    {
        public Machine machine {get; set;}
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

6. ファイルを保存します。

## <a name="build-your-iot-edge-solution"></a>IoT Edge ソリューションのビルド

前のセクションでは、IoT Edge ソリューションを作成し、**CSharpFunction** にコードを追加しました。これにより、報告されるマシンの温度が許容可能なしきい値を下回っている場合のメッセージがフィルターで除外されます。 次は、ソリューションをコンテナー イメージとしてビルドして、それをコンテナー レジストリにプッシュする必要があります。

1. Visual Studio Code 統合ターミナルで次のコマンドを入力して、Docker にサインインします。 これで、必要なモジュール イメージを Azure Container Registry にプッシュすることができます。 
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    前に Azure Container Registry からコピーしたユーザー名とログイン サーバーを使用します。 パスワードを入力するように求められます。 パスワードをプロンプトに貼り付けて、**Enter** キーを押します。

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. VS Code エクスプローラーで、IoT Edge ソリューション ワークスペースの deployment.template.json ファイルを開きます。 このファイルでは、IoT Edge ランタイムによってデバイスに配置されるモジュールが指定されます。 配置マニフェストの詳細については、[IoT Edge モジュールを使用、構成、再利用する方法の確認](module-composition.md)に関するページをご覧ください。

3. 配置マニフェストで **registryCredentials** セクションを探します。 **ユーザー名**、**パスワード**、**アドレス**を、コンテナー レジストリの資格情報で更新します。 このセクションによって、プライベート レジストリに格納されるコンテナー イメージをプルするためのアクセス許可が、デバイス上の IoT Edge ランタイムに付与されます。 実際のユーザー名とパスワードの組み合わせは、.env ファイル (Git では無視されます) に格納されます。

5. このファイルを保存します。

6. VS Code エクスプローラーで、deployment.template.json ファイルを右クリックし、**[Build IoT Edge solution]\(IoT Edge ソリューションのビルド\)** を選択します。 

ソリューションのビルドを指示すると、最初に Visual Studio Code によって配置テンプレートの情報が読み取られて、**config** という名前の新しいフォルダーに deployment.json ファイルが生成されます。次に、`docker build` と `docker push` の 2 つのコマンドが統合ターミナルで実行されます。 これらの 2 つのコマンドによって、コードがビルドされ、関数がコンテナー化されたうえで、ソリューションを初期化したときに指定したコンテナー レジストリにコードがプッシュされます。 

## <a name="view-your-container-image"></a>コンテナー イメージの表示

コンテナー イメージがコンテナー レジストリにプッシュされると、Visual Studio Code によって成功メッセージが出力されます。 操作の成功を自分で確認したい場合は、レジストリのイメージを表示できます。 

1. Azure portal で、Azure Container Registry に移動します。 
2. **[リポジトリ]** を選択します。
3. **csharpfunction** リポジトリが一覧に表示されます。 このリポジトリを選択すると、さらに詳しい情報が表示されます。
4. **[タグ]** セクションには、**0.0.1-amd64** タグが表示されます。 このタグには、作成したイメージのバージョンとプラットフォームが示されます。 これらの値は、CSharpFunction フォルダーの module.json ファイルで設定されます。 

## <a name="deploy-and-run-the-solution"></a>ソリューションの配置と実行

クイック スタートで行ったように、Azure portal を使用して関数モジュールを IoT Edge デバイスに展開できます。 また、Visual Studio Code 内からモジュールを配置して、監視することもできます。 以降のセクションでは、前提条件で示された VS Code 用の Azure IoT Edge 拡張機能が使用されます。 この拡張機能をまだインストールしていない場合は、ここでインストールしてください。 

1. **[表示]** > **[コマンド パレット]** を選択して、VS Code コマンド パレットを開きます。

2. **Azure: Sign in** コマンドを検索して、実行します。 手順に従って Azure アカウントにサインインします。 

3. コマンド パレットで、**Azure IoT Hub: Select IoT Hub** コマンドを検索して実行します。 

4. 自分の IoT Hub が含まれているサブスクリプションを選択し、アクセス先の IoT Hub を選択します。

5. VS Code エクスプローラーで、**[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開します。 

6. IoT Edge デバイスの名前を右クリックして、**[Create Deployment for IoT Edge device]\(IoT Edge デバイスの配置の作成\)** を選択します。 

7. **CSharpFunction** が含まれているソリューション フォルダーの場所を参照します。 config フォルダーを開き、deployment.json ファイルを選択して、**[Select Edge Deployment Manifest]\(Edge 配置マニフェストの選択\)** を選択します。

8. **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを更新します。 新しい **CSharpFunction** が、**TempSensor** モジュール、**$edgeAgent** および **$edgeHub** と一緒に実行されていることがわかります。 

   ![VS Code での配置されたモジュールの表示](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>生成されたデータを表示する

コマンド パレットで **Azure IoT Hub: Start Monitoring D2C Message** を実行することで、IoT ハブに届くすべてのメッセージを確認できます。

また、特定のデバイスから IoT Hub に届くすべてのメッセージが表示されるよう、ビューをフィルター処理することもできます。 **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションのデバイスを右クリックして、**Start Monitoring D2C Messages** を選択します。

メッセージの監視を停止するには、コマンド パレットで **Azure IoT Hub: Stop monitoring D2C message** コマンドを実行します。 


## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)]

IoT デバイスのプラットフォーム (Linux または Windows) に基づいて、IoT Edge サービス ランタイムを削除します。

#### <a name="windows"></a>Windows

IoT Edge ランタイムを削除します。

```Powershell
stop-service iotedge -NoWait
sleep 5
sc.exe delete iotedge
```

デバイス上に作成されたコンテナーを削除します。 

```Powershell
docker rm -f $(docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor" --filter "name=CSharpFunction")
```

#### <a name="linux"></a>Linux

IoT Edge ランタイムを削除します。

```bash
sudo apt-get remove --purge iotedge
```

デバイス上に作成されたコンテナーを削除します。 

```bash
sudo docker rm -f $(sudo docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor" --filter "name=CSharpFunction")
```

コンテナー ランタイムを削除します。

```bash
sudo apt-get remove --purge moby
```



## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Edge デバイスによって生成された生データをフィルター処理するコードを含む Azure 関数モジュールを作成しました。 独自のモジュールをビルドする準備ができたら、[Visual Studio Code 用の Azure IoT Edge で Azure 関数を開発する](how-to-develop-csharp-function.md)方法の詳細をご覧ください。 

引き続き次のチュートリアルを実行すると、Azure IoT Edge を利用して、エッジでデータをビジネス上の分析情報に変える他の方法について学習できます。

> [!div class="nextstepaction"]
> [Azure Stream Analytics でフローティング ウィンドウを使用して平均値を見つける](tutorial-deploy-stream-analytics.md)

<!--Links-->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
