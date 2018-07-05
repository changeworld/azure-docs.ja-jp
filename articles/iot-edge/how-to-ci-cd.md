---
title: Azure IoT Edge の継続的インテグレーションと継続的配置 | Microsoft Docs
description: Azure IoT Edge の継続的インテグレーションと継続的配置の概要
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 62d8d770f6b4c3a62a2395eb8c1505dbc3835c28
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37047457"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Azure IoT Edge に対する継続的インテグレーションと継続的配置

この記事では、Visual Studio Team Services (VSTS) と Microsoft Team Foundation Server (TFS) の継続的インテグレーションと継続的配置の機能を使用して、アプリケーションを迅速かつ効率的に Azure IoT Edge にビルド、テスト、および配置する方法について説明します。 

この記事では、次のことについて説明します。
* 単体テストを含む IoT Edge ソリューションのサンプルを作成してチェックインします。
* お使いの VSTS 用の Azure IoT Edge 拡張機能をインストールします。
* 継続的インテグレーション (CI) を構成して、ソリューションをビルドし、単体テストを実行します。
* 継続的配置 (CD) を構成して、ソリューションを配置し、応答を表示します。

この記事の手順を完了するには、30 分かかります。

![CI および CD](./media/how-to-ci-cd/cd.png)

## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Visual Studio Code を使用して Azure IoT Edge ソリューションのサンプルを作成する

このセクションでは、ビルド プロセスの一部として実行できる単体テストを含む IoT Edge ソリューションのサンプルを作成します。 このセクションのガイダンスに従う前に、「[Visual Studio Code で複数のモジュールを含む IoT Edge ソリューションを開発する](tutorial-multiple-modules-in-vscode.md)」の手順を完了してください。

1. VS Code コマンド パレットで、**Edge: New IoT Edge solution** コマンドを入力して実行します。 次に、ご自身のワークスペース フォルダーを選択し、ソリューション名 (既定の名前は **EdgeSolution**) を指定して、C# モジュール (**FilterModule**) をこのソリューションの最初のユーザー モジュールとして作成します。 また、この最初のモジュールの Docker イメージ リポジトリを指定する必要もあります。 既定のイメージ リポジトリは、ローカル Docker レジストリに基づきます (`localhost:5000/filtermodule`)。 継続的インテグレーションを強化するには、Azure Container Registry (`<your container registry address>/filtermodule`) または Docker Hub に変更する必要があります。

    ![ACR のセットアップ](./media/how-to-ci-cd/acr.png)

2. VS Code ウィンドウによって、ご自身の IoT Edge ソリューション ワークスペースが読み込まれます。 必要に応じて、**Edge: Add IoT Edge module** を入力して実行し、さらにモジュールを追加できます。 ルート フォルダーには、`modules` フォルダー、`.vscode` フォルダー、および配置マニフェスト テンプレート ファイルがあります。 ユーザー モジュール コードはすべて、`modules` フォルダーのサブフォルダーになります。 `deployment.template.json` は、配置マニフェスト テンプレートです。 このファイルのパラメーターの中には、すべてのモジュール フォルダーにある `module.json` から解析されるものがあります。

3. これで、IoT Edge ソリューションのサンプルが準備できました。 既定の C# モジュールは、パイプ メッセージ モジュールとして機能します。 `deployment.template.json` では、このソリューションに 2 つのモジュールが含まれていることがわかります。 メッセージは `tempSensor` モジュールから生成され、`FilterModule` を介して直接パイプ処理された後、使用している IoT ハブに送信されます。 **Program.cs** ファイル全体を下の内容と置き換えます。 このコード スニペットに関する詳細については、[IoT Edge C# モジュール プロジェクトの作成](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module#create-an-iot-edge-module-project)に関するセクションを参照してください。

    ```csharp
    namespace FilterModule
    {
        using System;
        using System.IO;
        using System.Runtime.InteropServices;
        using System.Runtime.Loader;
        using System.Security.Cryptography.X509Certificates;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using System.Collections.Generic;     // for KeyValuePair<>
        using Microsoft.Azure.Devices.Shared; // for TwinCollection
        using Newtonsoft.Json;                // for JsonConvert

        public class MessageBody
        {
            public Machine machine { get; set; }
            public Ambient ambient { get; set; }
            public string timeCreated { get; set; }
        }
        public class Machine
        {
            public double temperature { get; set; }
            public double pressure { get; set; }
        }
        public class Ambient
        {
            public double temperature { get; set; }
            public int humidity { get; set; }
        }

        public class Program
        {
            static int counter;
            static int temperatureThreshold { get; set; } = 25;

            static void Main(string[] args)
            {
                Init().Wait();

                // Wait until the app unloads or is cancelled
                var cts = new CancellationTokenSource();
                AssemblyLoadContext.Default.Unloading += (ctx) => cts.Cancel();
                Console.CancelKeyPress += (sender, cpe) => cts.Cancel();
                WhenCancelled(cts.Token).Wait();
            }

            /// <summary>
            /// Handles cleanup operations when app is cancelled or unloads
            /// </summary>
            public static Task WhenCancelled(CancellationToken cancellationToken)
            {
                var tcs = new TaskCompletionSource<bool>();
                cancellationToken.Register(s => ((TaskCompletionSource<bool>)s).SetResult(true), tcs);
                return tcs.Task;
            }

            /// <summary>
            /// Initializes the ModuleClient and sets up the callback to receive
            /// messages containing temperature information
            /// </summary>
            static async Task Init()
            {
                MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
                ITransportSettings[] settings = { mqttSetting };

                // Open a connection to the Edge runtime
                ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
                await ioTHubModuleClient.OpenAsync();
                Console.WriteLine("IoT Hub module client initialized.");

                // Register callback to be called when a message is received by the module
                await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessage, ioTHubModuleClient);
            }

            /// <summary>
            /// This method is called whenever the module is sent a message from the EdgeHub. 
            /// It just pipe the messages without any change.
            /// It prints all the incoming messages.
            /// </summary>
            static async Task<MessageResponse> FilterMessage(Message message, object userContext)
            {
                int counterValue = Interlocked.Increment(ref counter);

                var moduleClient = userContext as ModuleClient;
                if (moduleClient == null)
                {
                    throw new InvalidOperationException("UserContext doesn't contain " + "expected values");
                }

                byte[] messageBytes = message.GetBytes();
                string messageString = Encoding.UTF8.GetString(messageBytes);
                Console.WriteLine($"Received message: {counterValue}, Body: [{messageString}]");

                var filteredMessage = filter(message);

                if (filteredMessage != null && !string.IsNullOrEmpty(messageString))
                {
                    var pipeMessage = new Message(messageBytes);
                    foreach (var prop in message.Properties)
                    {
                        pipeMessage.Properties.Add(prop.Key, prop.Value);
                    }
                    await moduleClient.SendEventAsync("output1", pipeMessage);
                    Console.WriteLine("Received message sent");
                }
                return MessageResponse.Completed;
            }

            public static Message filter(Message message)
            {
                var counterValue = Interlocked.Increment(ref counter);

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
                    return filteredMessage;
                }
                return null;
            }
        }
    }
    ```

4. .Net Core 単体テスト プロジェクトを作成します。 VS Code ファイル エクスプローラーで、ご自身のワークスペースに新しいフォルダー **tests\FilterModuleTest** を作成します。 次に、VS Code 統合ターミナル (**Ctrl + '**) 上で、次のコマンドを実行して、xunit テスト プロジェクトを作成し、**FilterModule** プロジェクトに参照を追加します。

    ```cmd
    cd tests\FilterModuleTest
    dotnet new xunit
    dotnet add reference ../../modules/FilterModule/FilterModule.csproj
    ```

    ![フォルダー構造](./media/how-to-ci-cd/add-test-project.png)

5. **FilterModuleTest** フォルダーで、**UnitTest1.cs** のファイル名を **FilterModuleTest.cs** に更新します。 **FilterModuleTest.cs** を選択して開き、コード全体を下記のコード スニペットに置き換えます。このコード スニペットには、FilterModule プロジェクトに対する単体テストが含まれています。

    ```csharp
    using Xunit;
    using FilterModule;
    using Newtonsoft.Json;
    using System;
    using System.IO;
    using System.Runtime.InteropServices;
    using System.Runtime.Loader;
    using System.Security.Cryptography.X509Certificates;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Client.Transport.Mqtt;

    namespace FilterModuleTest
    {
        public class FilterModuleTest
        {
            [Fact]
            public void filterLessThanThresholdTest()
            {
                var source = createMessage(25 - 1);
                var result = Program.filter(source);
                Assert.True(result == null);
            }

            [Fact]
            public void filterMoreThanThresholdAlertPropertyTest()
            {
                var source = createMessage(25 + 1);
                var result = Program.filter(source);
                Assert.True(result.Properties["MessageType"] == "Alert");
            }

            [Fact]
            public void filterMoreThanThresholdCopyPropertyTest()
            {
                var source = createMessage(25 + 1);
                source.Properties.Add("customTestKey", "customTestValue");
                var result = Program.filter(source);
                Assert.True(result.Properties["customTestKey"] == "customTestValue");
            }

            private Message createMessage(int temperature)
            {
                var messageBody = createMessageBody(temperature);
                var messageString = JsonConvert.SerializeObject(messageBody);
                var messageBytes = Encoding.UTF8.GetBytes(messageString);
                return new Message(messageBytes);
            }

            private MessageBody createMessageBody(int temperature)
            {
                var messageBody = new MessageBody
                {
                    machine = new Machine
                    {
                        temperature = temperature,
                        pressure = 0
                    },
                    ambient = new Ambient
                    {
                        temperature = 0,
                        humidity = 0
                    },
                    timeCreated = string.Format("{0:O}", DateTime.Now)
                };

                return messageBody;
            }
        }
    }
    ```

6. 統合ターミナルでは、次のコマンドを入力して、単体テストをローカルで実行することができます。 
    ```cmd
    dotnet test
    ```

    ![単体テスト](./media/how-to-ci-cd/unit-test.png)

7. これらのプロジェクトを保存し、VSTS または TFS リポジトリにチェックインします。
    

> [!NOTE]
> VSTS コード リポジトリの使用の詳細については、「[Share your code with Visual Studio and VSTS Git (コードと Visual Studio および VSTS Git を共有する)](https://docs.microsoft.com/vsts/git/share-your-code-in-git-vs?view=vsts)」を参照してください。


## <a name="configure-continuous-integration"></a>継続的インテグレーションを構成する
このセクションでは、IoT Edge ソリューションのサンプルへの変更をチェックインするときに自動的に実行するように構成されたビルド定義を作成します。それによって、そのサンプルに含まれている単体テストは自動的に実行されます。

1. ご自身の VSTS アカウント (**https://**_your-account_**.visualstudio.com**) にサインインして、サンプル アプリにチェックインしたプロジェクトを開きます。

    ![チェックイン コード](./media/how-to-ci-cd/init-project.png)

1. VSTS Marketplace 上の [Azure IoT Edge For VSTS](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy) にアクセスします。 **[無料で入手]** をクリックして、ウィザードに従ってこの拡張機能をご自身の VSTS アカウントにインストールするか、TFS にダウンロードします。

    ![拡張機能のインストール](./media/how-to-ci-cd/install-extension.png)

1. VSTS で、**[ビルドとリリース]** ハブを開き、**[ビルド]** タブで、**[+ 新しい定義]** を選択します。 または、すでにビルド定義がある場合は、**[+ 新規]** ボタンを選択します。 

    ![新しいビルド](./media/how-to-ci-cd/add-new-build.png)

1. メッセージが表示されたら、**[VSTS Git]** ソース タイプを選択し、コードが配置されているプロジェクト、リポジトリ、ブランチを選択します。 **[続行]** を選択します。

    ![VSTS git の選択](./media/how-to-ci-cd/select-vsts-git.png)

1. **[テンプレートの選択]** ウィンドウで、**[start with an Empty process]\(空のプロセスを開始する\)** を選択します。

    ![空の開始](./media/how-to-ci-cd/start-with-empty.png)

1. **[Phase 1]\(フェーズ 1\)** の右側にある **[+]** をクリックして、フェーズにタスクを追加します。 **[.Net Core]** を検索して選択し、**[追加]** をクリックしてフェーズにこのタスクを追加します。

    ![dotnet テスト](./media/how-to-ci-cd/add-dot-net-core.png)

1. **[表示名]** を **[dotnet test]\(dotnet テスト\)** に更新し、**[コマンド]** ドロップダウン リストで、**[test]** を選択します。 **[プロジェクトへのパス]** に以下のパスを追加します。

    ```
    tests/FilterModuleTest/*.csproj
    ```

    ![dotnet テストの構成](./media/how-to-ci-cd/dotnet-test.png)

1. **[Phase 1]\(フェーズ 1\)** の右側にある **[+]** をクリックして、フェーズにタスクを追加します。 **[Azure IoT Edge]** を検索して選択し、**[追加]** ボタンを **2 回**クリックしてフェーズにこれらのタスクを追加します。

    ![IoT Edge](./media/how-to-ci-cd/add-azure-iot-edge.png)

1. Azure IoT Edge の最初のタスクで、**[表示名]** を **[Module Build and Push]\(モジュールのビルドとプッシュ\)** に更新し、**[アクション]** ドロップダウン リストで、**[Build and Push]\(ビルドとプッシュ\)** を選択します。 **[Module.json File]\(Module.json ファイル\)** テキストボックスで、以下のパスを追加します。 その後、**[コンテナー レジストリの種類]** を選択して、コード内に同じレジストリを構成して選択します。 このタスクでは、ソリューション内のすべてのモジュールをビルドおよびプッシュし、指定したコンテナー レジストリにパブリッシュします。 複数のモジュールを異なるレジストリにプッシュする場合は、**モジュールのビルドとプッシュ**のタスクを複数保持できます。

    ```
    **/module.json
    ```

    ![モジュールのビルドとプッシュ](./media/how-to-ci-cd/module-build-push.png)

1. Azure IoT Edge の2 番目の タスクでは、**[表示名]** を **[Deploy to IoT Edge device]\(IoT Edge デバイスに配置する\)** に更新し、**[アクション]** ドロップダウン リストで、**[Deploy to IoT Edge device]\(IoT Edge デバイスに配置する\)** を選択します。 Azure サブスクリプションを選択し、使用している IoT ハブ名を入力します。 IoT Edge 配置 ID と配置の優先順位を指定できます。 1 つまたは複数のデバイスへの配置を選択することもできます。 複数のデバイスに配置する場合は、デバイスのターゲット条件を指定する必要があります。 たとえば、デバイス タグを条件として使用する場合は、配置する前に、対応するデバイス タグを更新する必要があります。 

    ![Edge への配置](./media/how-to-ci-cd/deploy-to-edge.png)

1. **[プロセス]** をクリックして、**[エージェント キュー]** が **[Hosted Linux Preview]\(ホストされている Linux (プレビュー)\)** であることを確認します。

    ![構成](./media/how-to-ci-cd/configure-env.png)

1. **[トリガ]** タブを開き、**[継続的インテグレーション]** トリガーを有効にします。 ご自身のコードを含むブランチを含まれていることを確認します。

    ![トリガー](./media/how-to-ci-cd/configure-trigger.png)

1. 新しいビルド定義を保存し、新しいビルドをキューに登録します。 **[保存してキューに登録]** ボタンをクリックします。

1. 表示されるメッセージ バーで、ビルドへのリンクを選択します。 または、ビルド定義に移動し、キューに登録した最新のビルド ジョブを表示します。

    ![構築](./media/how-to-ci-cd/build-def.png)

1. ビルドが完了すると、各タスクとその結果の概要がライブのログ ファイルに表示されます。 
    
    ![完了](./media/how-to-ci-cd/complete.png)

1. VS Code に戻り、IoT Hub デバイス エクスプローラーを確認できます。 モジュールがある Edge デバイスが実行を開始しているはずです (Edge ランタイムにレジストリの資格情報を追加されていることを確認してください)。

    ![Edge の実行](./media/how-to-ci-cd/edge-running.png)

## <a name="continuous-deployment-to-iot-edge-devices"></a>IoT Edge デバイスへの継続的配置

継続的配置を有効にするには、基本的に、適切な IoT Edge デバイスで CI ジョブを設定して、ご自身のプロジェクト内のブランチに対して **[トリガ]** を有効にする必要があります。 従来の DevOps 実習では、プロジェクトに 2 つの主要なブランチが含まれています。 マスター ブランチは安定したバージョンのコードになっており、開発ブランチには最新のコードの変更が含まれています。 チーム内のすべての開発者は、コードの更新を開始するときに自身の機能ブランチに開発ブランチをフォークする必要があります。つまり、すべてのコミットは開発ブランチから分岐して機能ブランチで行われます。 プッシュされたすべてのコミットは CI システムを介してテストされます。 ローカルでコードを完全にテストした後、機能ブランチはプル要求を介して開発ブランチにマージされます。 開発者のブランチ上のコードを CI システムを介してテストする場合は、プル要求を介してマスター ブランチにマージできます。

そのため、IoT Edge デバイスに配置する場合、3 つの主要な環境があります。
- 機能ブランチでは、開発用コンピューター上でシミュレートされた IoT Edge デバイスを使用したり、物理 IoT Edge デバイスに配置したりできます。
- 開発ブランチでは、物理 IoT Edge デバイスに配置する必要があります。
- マスター ブランチでは、ターゲット IoT Edge デバイスは、運用環境デバイスである必要があります。

## <a name="next-steps"></a>次の手順

* 「[1 台のデバイスまたは一群のデバイスを対象とした IoT Edge デプロイについて](module-deployment-monitoring.md)」で IoT Edge デプロイについて理解します。
* 「[Deploy and monitor IoT Edge modules at scale (IoT Edge モジュールを大規模にデプロイして監視する)](how-to-deploy-monitor.md)」で、デプロイを作成、更新、または削除するための手順を学習してください。
