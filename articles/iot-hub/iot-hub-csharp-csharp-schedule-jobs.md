---
title: Azure IoT Hub を使用してジョブのスケジュールを設定する (.NET/.NET) | Microsoft Docs
description: 複数のデバイスでダイレクト メソッドを呼び出すように Azure IoT Hub ジョブのスケジュールを設定する方法。 Azure IoT device SDK for .NET を使用して、シミュレートされたデバイス アプリと、ジョブを実行するサービス アプリを実装します。
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/06/2018
ms.author: dobett
ms.openlocfilehash: 0ac74a5b1a65dc171c6addd30152010965888808
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185528"
---
# <a name="schedule-and-broadcast-jobs-netnet"></a>ジョブのスケジュールとブロードキャスト (.NET/.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub を使用して、数百万のデバイスを更新するジョブのスケジュールと追跡を行います。 ジョブを使用して、次の操作を行います。

* 必要なプロパティを更新する
* タグを更新する
* ダイレクト メソッドを呼び出す

ジョブはこれらのアクションの 1 つをラップし、デバイス ツイン クエリで定義されるデバイスのセットに対して実行状況を追跡します。 たとえば、バックエンド アプリは、ジョブを使用して 10,000 台のデバイスでダイレクト メソッドを呼び出し、デバイスを再起動できます。 デバイス ツイン クエリでデバイスのセットを指定し、ジョブが将来実行されるようにスケジュールを設定します。 各デバイスが再起動のダイレクト メソッドを受信し、実行するたびに、ジョブは進行状況を追跡します。

これらの各機能の詳細については、次の記事をご覧ください。

* デバイス ツインとプロパティ: [デバイス ツインの概要][lnk-get-started-twin]に関する記事と[デバイス ツインのプロパティの使用方法に関するチュートリアル][lnk-twin-props]
* ダイレクト メソッド: [ダイレクト メソッドに関する IoT Hub 開発者ガイド][lnk-dev-methods]と[ダイレクト メソッドの使用に関するチュートリアル][lnk-c2d-methods]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

このチュートリアルでは、次の操作方法について説明します。

* バックエンド アプリから呼び出し可能な **LockDoor** と呼ばれるダイレクト メソッドを実装するデバイス アプリを作成します。
* 複数のデバイスで **LockDoor** ダイレクト メソッドを呼び出すジョブを作成するバックエンド アプリを作成します。 もう 1 つのジョブは、必要なプロパティの更新を複数のデバイスに送信します。

このチュートリアルの最後には、次の 2 つの .NET (C#) コンソール アプリが完成します。

**SimulateDeviceMethods** は IoT ハブに接続し、**LockDoor** ダイレクト メソッドを実装します。

**ScheduleJob** はジョブを使用して **LockDoor** ダイレクト メソッドを呼び出し、複数のデバイスでデバイス ツインの必要なプロパティを更新します。

このチュートリアルを完了するには、以下が必要です。

* Visual Studio 2015 または Visual Studio 2017。
* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成することができます。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]


## <a name="create-a-simulated-device-app"></a>シミュレート対象デバイス アプリの作成
このセクションでは、ソリューション バックエンドによって呼び出されたダイレクト メソッドに応答する .NET コンソール アプリを作成します。

1. Visual Studio で、 **[コンソール アプリケーション]** プロジェクト テンプレートを使用し、Visual C# Windows クラシック デスクトップ プロジェクトを現在のソリューションに追加します。 プロジェクトに **SimulateDeviceMethods** という名前を付けます。
   
    ![New Visual C# Windows Classic device app][img-createdeviceapp]
    
1. ソリューション エクスプローラーで **[SimulateDeviceMethods]** プロジェクトを右クリックし、**[NuGet パッケージの管理...]** をクリックします。

1. **[NuGet パッケージ マネージャー]** ウィンドウで **[参照]** を選択し、**microsoft.azure.devices.client** を検索します。 **[インストール]** を選択して、**Microsoft.Azure.Devices.Client** パッケージをインストールし、使用条件に同意します。 この手順により、パッケージのダウンロードとインストールが実行され、[Azure IoT device SDK][lnk-nuget-client-sdk] NuGet パッケージへの参照とその依存関係が追加されます。
   
    ![NuGet Package Manager window Client app][img-clientnuget]

1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;

    using Newtonsoft.Json;
    ```

1. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値は、前のセクションで記したデバイス接続文字列に置き換えてください。

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

1. デバイスにダイレクト メソッドを実装するために以下を追加します。

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);
            
        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```

1. デバイスにデバイス ツイン リスナーを実装するには、以下を追加します。

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. 最後に、IoT ハブへの接続を開いてメソッド リスナーを初期化する次のコードを **Main** メソッドに追加します。
   
    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
1. 作業内容を保存し、ソリューションを構築します。         

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、MSDN の記事「[Transient Fault Handling (一時的な障害の処理)][lnk-transient-faults]」で推奨されているように、再試行ポリシー (接続の再試行など) を実装することをお勧めします。
> 


## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>ダイレクト メソッドを呼び出し、デバイス ツインの更新を送信するジョブのスケジュール

このセクションでは、ジョブを使用して **LockDoor** ダイレクト メソッドを呼び出し、複数のデバイスに必要なプロパティの更新を送信する .NET コンソール アプリ (C# を使用) を作成します。

1. Visual Studio で、 **[コンソール アプリケーション]** プロジェクト テンプレートを使用し、Visual C# Windows クラシック デスクトップ プロジェクトを現在のソリューションに追加します。 プロジェクトの名前として「**ScheduleJob**」と入力します。

    ![New Visual C# Windows Classic Desktop project][img-createapp]

1. ソリューション エクスプローラーで **ScheduleJob** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

1. **[NuGet パッケージ マネージャー]** ウィンドウで **[参照]** を選択し、**microsoft.azure.devices** を検索します。**[インストール]** を選択して **Microsoft.Azure.Devices** パッケージをインストールし、使用条件に同意します。 この手順により、パッケージのダウンロードとインストールが実行され、[Azure IoT service SDK][lnk-nuget-service-sdk] NuGet パッケージへの参照とその依存関係が追加されます。

    ![NuGet Package Manager window][img-servicenuget]

1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. 既定のステートメント内に存在しない場合は、次の `using` ステートメントを追加します。

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. **Program** クラスに次のフィールドを追加します。 プレースホルダーは、前のセクションで作成したハブの IoT Hub 接続文字列とデバイスの名前に置き換えてください。

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

1. **Program** クラスに次のメソッドを追加します。

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && (result.Status != JobStatus.Failed));
    }
    ```

1. **Program** クラスに次のメソッドを追加します。

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), TimeSpan.FromSeconds(5));
       
        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. 別のメソッドを **Program** クラスに追加します。

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > クエリ構文について詳しくは、[IoT Hub クエリ言語][lnk-query]に関するページをご覧ください。
    > 

1. 最後に、**Main** メソッドに次の行を追加します。

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. 作業内容を保存し、ソリューションを構築します。 


## <a name="run-the-apps"></a>アプリの実行

これで、アプリを実行する準備が整いました。

1. Visual Studio のソリューション エクスプローラーでソリューションを右クリックし、**[ビルド]** をクリックします。 **[マルチ スタートアップ プロジェクト]** を選択します。 `SimulateDeviceMethods` が一覧の一番上にあり、その後に `ScheduleJob` があることを確認します。 両方のアクションを **[開始]** に設定し、**[OK]** をクリックします。

1. **[開始]** をクリックしてプロジェクトを実行するか、**[デバッグ]** メニューに移動し、**[デバッグの開始]** をクリックします。

1. デバイスとバックエンド アプリの両方からの出力が表示されます。

    ![アプリを実行して、ジョブをスケジュールします。][img-schedulejobs]


## <a name="next-steps"></a>次の手順

このチュートリアルでは、ジョブを使用して、デバイスへのダイレクト メソッドと、デバイス ツインのプロパティの更新をスケジュールしました。

IoT Hub およびリモートによるファームウェアのワイヤレス更新などの他のデバイス管理パターンを確認するには、「[チュートリアル: ファームウェアを更新する方法][lnk-fwupdate]」を参照してください。

Azure IoT Edge を使用したエッジ デバイスへの AI のデプロイについては、「[IoT Edge の使用][lnk-iot-edge]」に関する記事をご覧ください。

<!-- images -->
[img-createdeviceapp]: ./media/iot-hub-csharp-csharp-schedule-jobs/create-device-app.png
[img-clientnuget]: ./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png
[img-servicenuget]: media/iot-hub-csharp-csharp-schedule-jobs/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-schedule-jobs/createnetapp.png
[img-schedulejobs]: media/iot-hub-csharp-csharp-schedule-jobs/schedulejobs.png

[lnk-get-started-twin]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-twin-props]: tutorial-device-twins.md
[lnk-c2d-methods]: quickstart-control-device-dotnet.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: tutorial-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://docs.microsoft.com/azure/architecture/best-practices/transient-faults
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-query]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language
