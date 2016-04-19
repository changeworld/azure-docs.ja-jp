## デバイス ID の作成

このセクションでは、IoT ハブの ID レジストリに新しいデバイス ID を作成する Windows コンソール アプリケーションを作成します。IoT Hub に接続するデバイスは、あらかじめデバイス ID レジストリに登録されている必要があります。詳細については、「[IoT Hub 開発者ガイド][lnk-devguide-identity]」の「**デバイス ID レジストリ**」を参照してください。このコンソール アプリケーションを実行すると、デバイスからクラウドへのメッセージを IoT Hub に送信するときにそのデバイスを識別する一意の ID とキーが生成されます。

1. Visual Studio で、**[コンソール アプリケーション]** プロジェクト テンプレートを使用し、新しい Visual C# Windows クラシック デスクトップ プロジェクトを現在のソリューションに追加します。.NET Framework のバージョンが 4.5.1 以降であることを確認します。プロジェクトに **CreateDeviceIdentity** という名前を付けます。

	![][10]

2. ソリューション エクスプローラーで **CreateDeviceIdentity** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

3. **[NuGet パッケージ マネージャー]** ウィンドウで **[参照]** を選択し、**microsoft.azure.devices** を検索して **[インストール]** をクリックし、**Microsoft.Azure.Devices** パッケージをインストールして使用条件に同意します。

	![][11]

4. これにより、パッケージのダウンロードとインストールが実行され、[Microsoft Azure IoT Service SDK][lnk-nuget-service-sdk] NuGet パッケージへの参照とその依存関係が追加されます。

4. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

		using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. **Program** クラスに次のフィールドを追加し、プレースホルダーの値を、前のセクションで作成した IoT Hub の接続文字列に置き換えます。

		static RegistryManager registryManager;
        static string connectionString = "{iothub connection string}";

6. **Program** クラスに次のメソッドを追加します。

		private static async Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }

	このメソッドは、**myFirstDevice** という ID で新しいデバイス ID を作成します (そのデバイス ID が既にレジストリに存在する場合は、単にその既存のデバイス情報を取得します)。続けてその ID のプライマリ キーが表示されます。シミュレーション対象デバイスでこのキーを使用して IoT Hub に接続することになります。

7. 最後に、**Main** メソッドに次の行を追加します。

		registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. このアプリケーションを実行し、デバイスのキーを書き留めます。

    ![][12]

> [AZURE.NOTE] IoT Hub の ID レジストリには、ハブに対するアクセスの安全性を確保するためのデバイス ID だけが格納されます。セキュリティ資格情報として使用するキーとデバイス ID、そして個々のデバイスについてアクセスを無効にすることのできる有効/無効フラグが格納されます。その他デバイス固有のメタデータをアプリケーションで保存する必要がある場合は、アプリケーション固有のストアを使用する必要があります。詳細については、「[IoT Hub 開発者ガイド][lnk-devguide-identity]」を参照してください。

## デバイスからクラウドへのメッセージの受信

このセクションでは、IoT Hub からのデバイスからクラウドへのメッセージを読み込む Windows コンソール アプリケーションを作成します。Iot Hub は、デバイスからクラウドへのメッセージを読み取るための、[Event Hubs][lnk-event-hubs-overview] と互換性のあるエンドポイントを公開します。わかりやすくするために、このチュートリアルで作成するリーダーは基本的なものであり、高スループットのデプロイメントには適していません。「[デバイスからクラウドへのメッセージの処理][lnk-processd2c-tutorial]」チュートリアルでは、デバイスからクラウドへの大規模なメッセージを処理する方法を紹介しています。「[Event Hubs の使用][lnk-eventhubs-tutorial]」チュートリアルでは、Event Hubs からのメッセージを処理する方法について詳しく説明しています。これは IoT Hub のイベント ハブと互換性のあるエンドポイントに適用されます。

> [AZURE.NOTE] 常に、Event Hubs と互換性のあるエンドポイントは、デバイスからクラウドへのメッセージを読み取るために AMQPS プロトコルを使用します。

1. Visual Studio で、**[コンソール アプリケーション]** プロジェクト テンプレートを使用し、新しい Visual C# Windows クラシック デスクトップ プロジェクトを現在のソリューションに追加します。.NET Framework のバージョンが 4.5.1 以降であることを確認します。プロジェクトに **ReadDeviceToCloudMessages** という名前を付けます。

    ![][10]

2. ソリューション エクスプローラーで、**[ReadDeviceToCloudMessages]** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。

3. **[NuGet パッケージ マネージャー]** ウィンドウで **WindowsAzure.ServiceBus** を検索し、**[インストール]** をクリックして、使用条件に同意します。

    これによって、[Azure Service Bus][lnk-servicebus-nuget] への参照がすべての依存関係と共にダウンロード、インストール、追加されます。このパッケージは、アプリケーションが IoT Hub でイベント ハブと互換性のあるエンドポイントに接続できるようにします。

4. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

        using Microsoft.ServiceBus.Messaging;
        using System.Threading;

5. **Program** クラスに次のフィールドを追加し、プレースホルダーの値を、「*IoT Hub の作成*」セクションで作成した IoT Hub の接続文字列に置き換えます。

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. **Program** クラスに次のメソッドを追加します。

        private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
            while (true)
            {
                if (ct.IsCancellationRequested) break;
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }

    このメソッドは、**EventHubReceiver** インスタンスを使用して、IoT Hub のすべてのデバイスからクラウドへの受信パーティションからメッセージを受け取ります。**EventHubReceiver** オブジェクトを作成するときの `DateTime.Now` パラメーターの渡し方に注目してください。これによって、起動後に送信されたメッセージのみを受信するようにしています。現在の一連のメッセージを確認できるので、テスト環境ではこれは便利ですが、運用環境の場合、コードですべてのメッセージが処理されることを確認する必要があります。詳細については、「[IoT Hub のデバイスからクラウドへのメッセージを処理する方法][lnk-processd2c-tutorial]」チュートリアルを参照してください。

7. 最後に、**Main** メソッドに次の行を追加します。

        Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        CancellationTokenSource cts = new CancellationTokenSource();

        System.Console.CancelKeyPress += (s, e) =>
        {
          e.Cancel = true;
          cts.Cancel();
          Console.WriteLine("Exiting...");
        };

        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
           tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }  
        Task.WaitAll(tasks.ToArray());


<!-- Links -->

[lnk-eventhubs-tutorial]: ../articles/event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide.md#identityregistry
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../articles/event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-processd2c-tutorial]: ../articles/iot-hub/iot-hub-csharp-csharp-process-d2c.md

<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

<!---HONumber=AcomDC_0413_2016-->