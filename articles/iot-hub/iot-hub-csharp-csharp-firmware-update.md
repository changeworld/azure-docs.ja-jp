---
title: Azure IoT Hub を使用したデバイス ファームウェアの更新 (.NET/.NET) | Microsoft Docs
description: Azure IoT Hub でデバイス管理を使用してデバイス ファームウェアの更新を開始する方法。 Azure IoT device SDK for .NET を使用して、シミュレートされたデバイス アプリを実装し、Azure IoT service SDK for .NET を使用して、ファームウェアの更新をトリガーするサービス アプリを実装します。
services: iot-hub
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: v-jamebr
ms.openlocfilehash: 984fd7516b5c05973c45891f4eda31c41a5a2be5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnet"></a>デバイス管理を使用してデバイス ファームウェアの更新を開始する (.NET/.NET)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>はじめに
「[デバイス管理の開始][lnk-dm-getstarted]」チュートリアルでは、[デバイス ツイン][lnk-devtwin]および[ダイレクト メソッド][lnk-c2dmethod] プリミティブを使用してリモートでデバイスを再起動する方法を説明しました。 このチュートリアルでは、同じ IoT Hub プリミティブを使用して、エンド ツー エンドでシミュレートされたファームウェア更新を実行する方法を示します。  このパターンは、[Raspberry Pi デバイスの実装サンプル][lnk-rpi-implementation]のファームウェア更新の実装で使用されます。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

このチュートリアルでは、次の操作方法について説明します。

* シミュレート対象デバイス アプリで **firmwareUpdate** ダイレクト メソッドを IoT Hub 経由で呼び出す .NET コンソール アプリを作成します。
* **firmwareUpdate** ダイレクト メソッドを実装するシミュレート対象デバイス アプリを作成します。 このメソッドは、ファームウェア イメージをダウンロードするまで待機し、ファームウェア イメージをダウンロードし、最後にファームウェア イメージを適用する、多段階のプロセスを開始します。 更新の各段階中、デバイスは、報告されるプロパティを使用して進捗状況を報告します。

このチュートリアルが終わると、次の .NET (C#) コンソール デバイス アプリと .NET (C#) コンソール バックエンド アプリが完成します。

* **SimulatedDeviceFwUpdate**: IoT Hub と既に作成したデバイス ID をつなげ、**firmwareUpdate** ダイレクト メソッドを受信し、イメージのダウンロードの待機、新しいイメージのダウンロード、イメージの適用というマルチステートメント プロセスを経由してファームウェアの更新をシミュレーションします。

* **TriggerFWUpdate**: サービス SDK を使用して、シミュレート対象デバイスで **firmwareUpdate** ダイレクト メソッドをリモートで呼び出し、応答を表示して、定期的 (500 ミリ秒ごと) に更新済みの報告されたプロパティを表示します。

このチュートリアルを完了するには、以下が必要です。

* Visual Studio 2015 または Visual Studio 2017。
* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

「[デバイス管理の開始](iot-hub-csharp-csharp-device-management-get-started.md)」の記事を参照して、IoT Hub を作成し、IoT Hub 接続文字列を取得します。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>ダイレクト メソッドを使用して、デバイス上でリモート ファームウェア更新を開始する
このセクションでは、デバイスでファームウェアのリモート更新を開始する .NET コンソール アプリケーションを作成します (C# を使用します)。 アプリケーションは、ダイレクト メソッドを使用して更新を開始し、デバイス ツイン クエリを使用してアクティブなファームウェア更新の状態を定期的に取得します。

1. Visual Studio で、 **[コンソール アプリケーション]** プロジェクト テンプレートを使用し、Visual C# Windows クラシック デスクトップ プロジェクトを現在のソリューションに追加します。 プロジェクトに **TriggerFWUpdate** という名前を付けます。

    ![New Visual C# Windows Classic Desktop project][img-createserviceapp]

2. ソリューション エクスプローラーで **TriggerFWUpdate** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。
3. **[NuGet パッケージ マネージャー]** ウィンドウで **[参照]** を選択し、**microsoft.azure.devices** を検索します。**[インストール]** を選択して **Microsoft.Azure.Devices** パッケージをインストールし、使用条件に同意します。 この手順により、パッケージのダウンロードとインストールが実行され、[Azure IoT service SDK][lnk-nuget-service-sdk] NuGet パッケージへの参照とその依存関係が追加されます。

    ![NuGet Package Manager window][img-servicenuget]
4. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp   
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. **Program** クラスに次のフィールドを追加します。 複数のプレースホルダーの値は、前のセクションで作成したハブの IoT Hub 接続文字列とデバイスの ID に置き換えます。
   
    ```csharp   
    static RegistryManager registryManager;
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static string targetDevice = "{deviceIdForTargetDevice}";
    ```
        
6. **Program** クラスに次のメソッドを追加します。 このメソッドは、500 ミリ秒ごとにデバイス ツインに、更新された状態をポーリングします。 状態が実際に変更された場合のみ、コンソールに書き込みます。 このサンプルでは、サブスクリプションで余分な IoT Hub メッセージを消費することを避けるため、デバイスが **applyComplete** またはエラーの状態を報告したときにポーリングが停止します。  
   
    ```csharp   
    public static async Task QueryTwinFWUpdateReported(DateTime startTime)
    {
        DateTime lastUpdated = startTime;

        while (true)
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);

            if (twin.Properties.Reported.GetLastUpdated().ToUniversalTime() > lastUpdated.ToUniversalTime())
            {
                lastUpdated = twin.Properties.Reported.GetLastUpdated().ToUniversalTime();
                Console.WriteLine("\n" + twin.Properties.Reported["iothubDM"].ToJson());

                var status = twin.Properties.Reported["iothubDM"]["firmwareUpdate"]["status"].Value;
                if ((status == "downloadFailed") || (status == "applyFailed") || (status == "applyComplete"))
                {
                    Console.WriteLine("\nStop polling.");
                    return;
                }
            }
            await Task.Delay(500);
        }
    }
    ```
        
7. **Program** クラスに次のメソッドを追加します。

    ```csharp   
    public static async Task StartFirmwareUpdate()
    {
        client = ServiceClient.CreateFromConnectionString(connString);
        CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
        method.ResponseTimeout = TimeSpan.FromSeconds(30);
        method.SetPayloadJson(
            @"{
               fwPackageUri : 'https://someurl'
            }");

        CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

        Console.WriteLine("Invoked firmware update on device.");
    }
    ```

8. 最後に、 **Main** メソッドに次の行を追加します。 これにより、デバイス ツインを読み取り、ワーカー スレッドへのポーリング タスクを開始して、ファームウェアの更新をトリガーするレジストリ マネージャーを作成します。
   
    ```csharp   
    registryManager = RegistryManager.CreateFromConnectionString(connString);

    Task queryTask = Task.Run(() => (QueryTwinFWUpdateReported(DateTime.Now)));

    StartFirmwareUpdate().Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```
        
9. ソリューションをビルドします。

## <a name="create-a-simulated-device-app"></a>シミュレート対象デバイス アプリの作成
このセクションでは、次の作業を行います。

* クラウドによって呼び出されたダイレクト メソッドに応答する .NET コンソール アプリを作成します。
* ダイレクト メソッド経由で、バックエンド サービスによってトリガーされるファームウェアの更新プログラムをシミュレートします。
* 報告されるプロパティを使用して、デバイス ツイン クエリで、デバイスと最後にファームウェアの更新が完了した日時を識別できるようにする

1. Visual Studio で、 **[コンソール アプリケーション]** プロジェクト テンプレートを使用し、Visual C# Windows クラシック デスクトップ プロジェクトを現在のソリューションに追加します。 プロジェクトに **SimulateDeviceFWUpdate** という名前を付けます。
   
    ![New Visual C# Windows Classic device app][img-createdeviceapp]
    
2. ソリューション エクスプローラーで **SimulateDeviceFWUpdate** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。
3. **[NuGet パッケージ マネージャー]** ウィンドウで **[参照]** を選択し、**microsoft.azure.devices.client** を検索します。 **[インストール]** を選択して、**Microsoft.Azure.Devices.Client** パッケージをインストールし、使用条件に同意します。 この手順により、パッケージのダウンロードとインストールが実行され、[Azure IoT device SDK][lnk-nuget-client-sdk] NuGet パッケージへの参照とその依存関係が追加されます。
   
    ![NuGet Package Manager window Client app][img-clientnuget]
4. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。
   
    ```csharp   
    using Newtonsoft.Json.Linq;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値を、**デバイス ID の作成**セクションでメモしたデバイス接続文字列に置き換えます。
   
    ```csharp   
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. 次のメソッドを追加して、デバイス ツイン経由でクラウドに状態を報告させます。 

    ```csharp   
    static async Task reportFwUpdateThroughTwin(Twin twin, TwinCollection fwUpdateValue)
    {
        try
        {
            TwinCollection patch = new TwinCollection();
            TwinCollection iothubDM = new TwinCollection();

            iothubDM["firmwareUpdate"] = fwUpdateValue;
            patch["iothubDM"] = iothubDM;

            await Client.UpdateReportedPropertiesAsync(patch);
            Console.WriteLine("Twin state reported: {0}", fwUpdateValue["status"]);
        }
        catch 
        {
            Console.WriteLine("Error updating device twin");
            throw;
        }
    }
    ```

7. 次のメソッドを追加して、ファームウェア イメージのダウンロードをシミュレートします。
        
    ```csharp   
    static async Task<byte[]> simulateDownloadImage(string imageUrl)
    {
        var image = "[fake image data]";

        Console.WriteLine("Downloading image from " + imageUrl);

        await Task.Delay(4000);

        return Encoding.ASCII.GetBytes(image);
            
    }
    ```

8. 次のメソッドを追加して、デバイスへのファームウェア イメージの適用をシミュレートします。
        
    ```csharp   
    static async Task simulateApplyImage(byte[] imageData)
    {
        if (imageData == null)
        {
            throw new ArgumentNullException();
        }

        await Task.Delay(4000);

    }
    ```
 
9.  次のメソッドを追加して、ファームウェア イメージのダウンロードの待機をシミュレートします。 状態を**待機中**に更新し、ツイン上のその他のファームウェア更新プロパティをクリアします。 これらのプロパティがクリアされ、以前のファームウェア更新による既存のすべての値が削除されます。 これは、報告されたプロパティが PATCH 操作 (差分) として送信され、PUT 操作 (以前のすべての値を置き換える完全なプロパティのセット) ではないために必要です。 通常、利用可能な更新プログラムがあるとデバイスに通知され、管理者が定義したポリシーによって更新のダウンロードと適用が開始されます。 この関数で、そのポリシーを実行するためのロジックが実行されます。 
        
    ```csharp   
    static async Task waitToDownload(Twin twin, string fwUpdateUri)
    {
        var now = DateTime.Now;
        TwinCollection status = new TwinCollection();
        status["fwPackageUri"] = fwUpdateUri;
        status["status"] = "waiting";
        status["error"] = null;
        status["startedWaitingTime"] = DateTime.Now;
        status["downloadCompleteTime"] = null;
        status["startedApplyingImage"] = null;
        status["lastFirmwareUpdate"] = null;

        await reportFwUpdateThroughTwin(twin, status);

        await Task.Delay(2000);
    }
    ```

10. 次のメソッドを追加して、ダウンロードを実行します。 これは、デバイス ツイン経由で状態を**ダウンロード中**に更新し、ダウンロードのシミュレート メソッドを呼び出して、ダウンロード操作の結果に応じてツイン経由で **downloadComplete** または **downloadFailed** の状態を報告します。 
        
    ```csharp   
    static async Task<byte[]> downloadImage(Twin twin, string fwUpdateUri)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloading";
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            byte[] imageData = await simulateDownloadImage(fwUpdateUri);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadComplete";
            statusUpdate["downloadCompleteTime"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            return imageData;
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

11. 次のメソッドを追加して、イメージを適用します。 これは、デバイス ツイン経由で状態を**適用中**に更新し、イメージの適用のシミュレート メソッドを呼び出して、適用操作の結果に応じてツイン経由で、状態を **applyComplete** または **applyFailed** に更新します。 
        
    ```csharp   
    static async Task applyImage(Twin twin, byte[] imageData)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applying";
            statusUpdate["startedApplyingImage"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            await simulateApplyImage(imageData);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyComplete";
            statusUpdate["lastFirmwareUpdate"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

12. 次のメソッドを追加して、イメージのダウンロードの待機から、デバイスへのイメージの適用までのファームウェア更新操作をシーケンス処理します。
        
    ```csharp   
    static async Task doUpdate(string fwUpdateUrl)
    {
        try
        {
            Twin twin = await Client.GetTwinAsync();
            await waitToDownload(twin, fwUpdateUrl);
            byte[] imageData = await downloadImage(twin, fwUpdateUrl);
            await applyImage(twin, imageData);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error during update: {0}", ex.Message);
        }
    }
    ```

13. 次のメソッドを追加して、クラウドから **updateFirmware** ダイレクト メソッドを処理します。 これはメッセージ ペイロードからファームウェア更新の URL を抽出し、それを **doUpdate** タスクに渡し、別のスレッドプール スレッドでタスクを開始します。 これはすぐに、メソッドの応答をクラウドに返します。
        
    ```csharp   
    static Task<MethodResponse> onFirmwareUpdate(MethodRequest methodRequest, object userContext)
    {
        string fwUpdateUrl = (string)JObject.Parse(methodRequest.DataAsJson)["fwPackageUri"];
        Console.WriteLine("\nMethod: {0} triggered by service, URI is: {1}", methodRequest.Name, fwUpdateUrl);

        Task updateTask = Task.Run(() => (doUpdate(fwUpdateUrl)));

        string result = "'FirmwareUpdate started.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```
> [!NOTE]
> このメソッドは、シミュレートされた更新を**タスク**として実行し、メソッド呼び出しにただちに応答して、ファームウェアの更新が開始されたことをサービスに通知します。 更新の状態と完了が、デバイス ツインの報告されたプロパティ経由で、サービスに送信されます。 次の理由で、更新の完了後ではなく、開始時に、メソッド呼び出しに応答します。
> * 実際の更新プロセスは、メソッド呼び出しのタイムアウトより長くかかる可能性がきわめて高くなります。
> * 実際の更新プロセスは、再起動を必要とする可能性がきわめて高く、このアプリケーションが再起動されることによって、**MetodRequest** オブジェクトを使用できなくなることがあります。 (ただし、再起動後でも、報告されたプロパティの更新は可能です。) 

14. 最後に、IoT ハブへの接続を開いてメソッド リスナーを初期化する次のコードを **Main** メソッドに追加します。
   
    ```csharp   
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
        
        // setup callback for "firmware update" method
        Client.SetMethodHandlerAsync("firmwareUpdate", onFirmwareUpdate, null).Wait();
        Console.WriteLine("Waiting for firmware update direct method call\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        // as a good practice, remove the firmware update handler
        Client.SetMethodHandlerAsync("firmwareUpdate", null, null).Wait();
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
15. ソリューションをビルドします。       

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、[一時的な障害処理][lnk-transient-faults]に関する MSDN の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。


## <a name="run-the-apps"></a>アプリの実行
これで、アプリを実行する準備が整いました。
1. .NET デバイス アプリ **SimulatedDeviceFWUpdate** を実行します。  **SimulatedDeviceFWUpdate** プロジェクトを右クリックし、**[デバッグ]**、**[新しいインスタンスを開始]** の順に選択します。 次のように、IoT ハブからのメソッド呼び出しのリッスンが開始するはずです。 

2. デバイスが接続され、メソッドの呼び出しを待機したら、.NET **TriggerFWUpdate** アプリを実行して、シミュレート対象デバイス アプリでファームウェア更新メソッドを呼び出します。 **TriggerFWUpdate** プロジェクトを右クリックし、**[デバッグ]**、**[新しいインスタンスを開始]** を選択します。 **SimulatedDeviceFWUpdate** コンソールに書き込まれた更新シーケンスと、**TriggerFWUpdate** コンソールに、デバイスの報告されたプロパティ経由で報告されたシーケンスが表示されるはずです。 プロセスの完了には数秒かかることに注意してください。 
   
    ![サービスとデバイス アプリの実行][img-combinedrun]


## <a name="next-steps"></a>次の手順
このチュートリアルでは、ダイレクト メソッドを使用してデバイス上でリモートのファームウェア更新を開始し、報告されるプロパティを使用してファームウェア更新の進捗状況を確認しました。

IoT ソリューションの拡張と複数のデバイスでのメソッドの呼び出しをスケジュールする方法については、「[ジョブのスケジュールとブロードキャスト][lnk-tutorial-jobs]」チュートリアルを参照してください。

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-firmware-update/servicesdknuget.png
[img-clientnuget]: media/iot-hub-csharp-csharp-firmware-update/clientsdknuget.png
[img-createserviceapp]: media/iot-hub-csharp-csharp-firmware-update/createserviceapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-firmware-update/createdeviceapp.png
[img-combinedrun]: media/iot-hub-csharp-csharp-firmware-update/combinedrun.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-csharp-csharp-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-csharp-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/