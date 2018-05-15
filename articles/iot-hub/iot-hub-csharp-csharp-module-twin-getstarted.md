---
title: Azure IoT Hub モジュール ID とモジュール ツイン (.NET) の概要 | Microsoft Docs
description: モジュール ID を作成し、IoT SDK を使用して .NET のモジュール ツインを更新する方法を説明します。
services: iot-hub
documentationcenter: .net
author: chrissie926
manager: timlt
editor: ''
ms.assetid: f40604ff-8fd6-4969-9e99-8574fbcf036c
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: menchi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ea22106a41e7e440cb3e76f02814b76a64627279
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/03/2018
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-net-backup-and-net-device"></a>.NET バックアップおよび .NET デバイスを使用した Azure IoT Hub モジュール ID とモジュール ツインの概要

> [!NOTE]
> [モジュール ID とモジュール ツイン](iot-hub-devguide-module-twins.md)は Azure IoT Hub のデバイス ID およびデバイス ツインに類似していますが、より細かい粒度で設定できます。 Azure IoT Hub のデバイス ID とデバイス ツインを使用すると、バックエンド アプリケーションからデバイスを構成し、デバイスの状態を可視化できるのに対し、モジュール ID およびモジュール ツインは、デバイスの各コンポーネントごとにこれらの機能を提供します。 オペレーティング システム ベースのデバイスやファームウェア デバイスなどの複数のコンポーネントで構成され、この機能をサポートするデバイスの場合、各コンポーネントの状態が可視化され、個別に構成できます。

このチュートリアルの最後には、次の 2 つの .NET コンソール アプリが完成します。

* **CreateIdentities** は、デバイスの ID、モジュール ID と、関連づけられたセキュリティ キーを作成してデバイスおよびモジュール クライアントと接続します。
* **UpdateModuleTwinReportedProperties** は、更新されたモジュール ツインによって報告されたプロパティを IoT Hub に送信します。

> [!NOTE]
> デバイス上で動作するアプリケーションの作成とソリューションのバックエンドで動作するアプリケーションの開発に利用できる各種 Azure IoT SDK については、「[Azure IoT SDK][lnk-hub-sdks]」を参照してください。

このチュートリアルを完了するには、以下が必要です。

* Visual Studio 2015 または Visual Studio 2017。
* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

IoT Hub の作成は以上です。以降の作業に必要なホスト名と IoT Hub 接続文字列が得られました。

<a id="DeviceIdentity_csharp"></a>
[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]


<a id="D2C_csharp"></a>
## <a name="update-the-module-twin-using-net-device-sdk"></a>.NET device SDK を使用してモジュールを更新する

このセクションでは、モジュール ツインによって報告されるプロパティを更新する、シミュレートされたデバイス上に.NET を作成するコンソール アプリを作成します。

1. **Visual Studio プロジェクトを作成する** - Visual Studio で、**コンソール アプリケーション (.NET Framework)** プロジェクト テンプレートを使用して、既存のソリューションに Visual C# Windows Classic Desktop プロジェクトを追加します。 .NET Framework のバージョンが 4.6.1 以降であることを確認してください。 プロジェクトの名前を **UpdateModuleTwinReportedProperties** に設定します。

    ![Visual Studio プロジェクトを作成する][13]

2. **Azure IoT Hub .NET device SDK V1.16.0-preview-005 をインストールする** - モジュール ID およびモジュール ツインはパブリック プレビュー段階です。 これは、IoT Hub プレリリース デバイス版の SDK でのみ使用可能です。 Visual Studio で、[ツール] > [Nuget パッケージ マネージャー] > [ソリューションの Nuget パッケージの管理] の順に選択します。 Microsoft.Azure.Devices.Client を検索します。 [プレリリースを含める] チェック ボックスをオンにしてください。 V1.16.0-preview-005 バージョンを選択し、インストールします。 これで、モジュールのすべての機能を使用できるようになりました。 

    ![Azure IoT Hub .NET service SDK V1.16.0-preview-005 をインストールする][14]

3. **モジュールの接続文字列を取得する** -- [Azure Portal][lnk-portal] にログインします。 IoT Hub に移動し、[IoT デバイス] をクリックします。 myFirstDevice を検索して開くと、myFirstModule が作成されていることを確認できます。 モジュールの接続文字列をコピーします。 これは、次の手順で必要になります。

    ![Azure Portal モジュールの詳細][15]

4. **UpdateModuleTwinReportedProperties コンソール アプリを作成する** - **Program.cs** ファイルの最上部に、次の `using` ステートメントを追加します。

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

    **Program** クラスに次のフィールドを追加します。 プレース ホルダーの値をモジュールの接続文字列に置き換えます。

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>";
    private static DeviceClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", status, reason);
    }
    ```

    次の **OnDesiredPropertyChanged** メソッドを **Program** クラスに追加します。

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

    最後に、**Main** メソッドに次の行を追加します。

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = DeviceClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin));

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    このコード サンプルでは、モジュール ツインを取得し、AMQP プロトコルを使って報告されるプロパティを 更新する方法を示します。 パブリック プレビューでは、AMQP 用のモジュール ツイン操作のみがサポートされています。

5. 上記の **Main** メソッドの他に、下記のコード ブロックを追加して、モジュールから IoT Hub にイベントを送信できます。
    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>アプリの実行

これで、アプリを実行する準備が整いました。 Visual Studio のソリューション エクスプローラーでソリューションを右クリックし、 **[スタートアップ プロジェクトの設定]** をクリックします。 コンソール アプリの動作として、**[マルチ スタートアップ プロジェクト]** を選択し、**[起動する]** を選択します。 F5 キーを押してアプリを起動します。 

## <a name="next-steps"></a>次の手順

引き続き IoT Hub の使用方法を確認すると共に、他の IoT のシナリオについて調べるには、次のページを参照してください。

* [デバイス管理の概要][lnk-device-management]
* [IoT Edge の概要][lnk-iot-edge]


<!-- Images. -->
[13]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.JPG
[14]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png
[15]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
