## シミュレーション対象デバイス アプリの作成

このセクションでは、デバイスからクラウドへのメッセージを IoT ハブに送信するデバイスをシミュレートする Windows コンソール アプリを作成します。

1. 現在の Visual Studio ソリューションで、**[ファイル]、[追加]、[プロジェクト]** の順にクリックし、**コンソール アプリケーション** プロジェクト テンプレートを使用して、新しい Visual C# のデスクトップ アプリ プロジェクトを作成します。プロジェクトに **SimulatedDevice** という名前を付けます。

   	![][30]

2. ソリューション エクスプローラーでソリューションを右クリックし、**[ソリューション用 NuGet パッケージの管理]** をクリックします。

	[NuGet パッケージの管理] ウィンドウが表示されます。

3. `Microsoft Azure Devices Client` を検索し、**[インストール]** をクリックして、使用条件に同意します。

	これによりパッケージのダウンロードとインストールが実行され、[Azure IoT - デバイス SDK NuGet パッケージ]への参照が追加されます。

4. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

		using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;
        using System.Threading;

5. **Program** クラスに次のフィールドを追加し、プレースホルダーの値を IoT ハブ URI、**[IoT Hub の作成]** と **[デバイス ID の作成]** セクションで取得したデバイス キーにそれぞれ置き換えます。

		static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub URI}";
        static string deviceKey = "{deviceKey}";

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

                Thread.Sleep(1000);
            }
        }

	このメソッドは、新しいデバイスからクラウドへのメッセージを毎秒送信します。メッセージには、JSON シリアル化されたオブジェクトが、デバイス ID、ランダムに生成された番号と共に含まれ、シミュレートされた風速センサーを示します。

7. 最後に、**Main** メソッドに次の行を追加します。

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

> [AZURE.NOTE]わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。実稼働のコードでは、MSDN 記事 ([一時的な障害処理]) で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。

<!-- Links -->

[Azure IoT - デバイス SDK NuGet パッケージ]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[一時的な障害処理]: https://msdn.microsoft.com/ja-jp/library/hh680901(v=pandp.50).aspx

<!-- Images -->
[30]: ./media/iot-hub-getstarted-device-csharp/create-identity-csharp1.png

<!---HONumber=Oct15_HO3-->