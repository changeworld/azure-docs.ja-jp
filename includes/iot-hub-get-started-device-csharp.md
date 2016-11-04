## シミュレーション対象デバイス アプリの作成
このセクションでは、デバイスからクラウドへのメッセージを IoT ハブに送信するデバイスをシミュレートする Windows コンソール アプリを作成します。

1. Visual Studio で、**[コンソール アプリケーション]** プロジェクト テンプレートを使用し、新しい Visual C# Windows クラシック デスクトップ プロジェクトを現在のソリューションに追加します。.NET Framework のバージョンが 4.5.1 以降であることを確認します。プロジェクトに **SimulatedDevice** という名前を付けます。
   
       ![][30]
2. ソリューション エクスプローラーで **SimulatedDevice** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。
3. **[NuGet パッケージ マネージャー]** ウィンドウで **[参照]** を選択し、**Microsoft.Azure.Devices.Client** を検索して **[インストール]** をクリックし、**Microsoft.Azure.Devices.Client** パッケージをインストールして使用条件に同意します。
   
    これによりパッケージのダウンロードとインストールが実行され、[Azure IoT - デバイス SDK NuGet パッケージ][lnk-device-nuget]への参照とその依存関係が追加されます。
4. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。
   
        using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;
5. **Program** クラスに次のフィールドを追加し、プレースホルダーの値を、「*IoT Hub の作成*」セクションで取得した IoT Hub のホスト名と「*デバイス ID の作成*」セクションで取得したデバイス キーにそれぞれ置き換えます。
   
        static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";
6. **Program** クラスに次のメソッドを追加します。
   
        private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();
   
            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;
   
                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));
   
                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);
   
                Task.Delay(1000).Wait();
            }
        }
   
    このメソッドは、デバイスからクラウドへの新しいメッセージを 1 秒おきに送信します。このメッセージには、JSON 形式でシリアル化されたオブジェクトが、デバイス ID、ランダムに生成された番号と共に含まれ、これによって風速センサーがシミュレートされます。
7. 最後に、**Main** メソッドに次の行を追加します。
   
        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));
   
        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();
   
   既定では、**Create** メソッドは、IoT Hub と通信するために AMQP プロトコルを使用する **DeviceClient** インスタンスを作成します。HTTPS プロトコルを使用するには、プロトコルを引数として受け取る、**Create** メソッドのオーバーライドを使用します。HTTPS プロトコルを使用するように選択する場合は、**Microsoft.AspNet.WebApi.Client** NuGet パッケージをプロジェクトに追加して、**System.Net.Http.Formatting** 名前空間を含める必要もあります。

このチュートリアルでは、IoT Hub デバイス クライアントを作成する手順について説明します。代わりに、Visual Studio 拡張機能の [Azure IoT Hub の接続済みサービス][lnk-connected-service]を使用して、デバイス クライアント アプリケーションに必要なコードを追加することもできます。

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。運用環境のコードでは、[一時的な障害処理][lnk-transient-faults]に関する MSDN の記事で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
> 
> 

<!-- Links -->

[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6

<!-- Images -->
[30]: ./media/iot-hub-getstarted-device-csharp/create-identity-csharp1.png

<!---HONumber=AcomDC_0413_2016-->