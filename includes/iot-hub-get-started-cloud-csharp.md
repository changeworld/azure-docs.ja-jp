## デバイス ID の作成

このセクションでは、IoT ハブで新しいデバイス ID を作成する Windows コンソール アプリを記述します。詳細については、「[IoT Hub 開発者ガイド][IoT Hub Developer Guide - Identity Registry]」の「**デバイス ID レジストリ**」を参照してください。このコンソール アプリケーションを実行した後、ID、キーとして使用して、デバイスからクラウドへのメッセージを IoT Hub に送信するためにデバイス ID として使用する ID とキーが提供されます。

1. Visual Studio で、**コンソール アプリケーション** プロジェクト テンプレートを使用して、新しい Visual C# のデスクトップ アプリ プロジェクトを作成します。プロジェクトに **CreateDeviceIdentity** という名前を付けます。

	![][10]

2. ソリューション エクスプローラーでソリューションを右クリックし、**[ソリューション用 NuGet パッケージの管理]** をクリックします。

	**[NuGet パッケージ マネージャー]** ウィンドウが表示されます。

3. **[プレリリースを含む]** チェック ボックスがオンになっていることを確認します。`Microsoft Azure Devices` を検索し、**[インストール]** をクリックして、使用条件に同意します。

	![][11]

4. これによりパッケージのダウンロードとインストールが実行され、[Microsoft Azure Devices SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet パッケージへの参照が追加されます。

4. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

		using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. **Program** クラスに次のフィールドを追加し、前のセクションで作成した IoT ハブの名前と接続文字列を値に代入します。

		static RegistryManager registryManager;
        static string connectionString = "{iothub connection string}";

6. **Program** クラスに次のメソッドを追加します。

		private async static Task AddDeviceAsync()
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

	このメソッドは、ID **myFirstDevice** を持つ新しいデバイス ID を作成します (同じ ID を持つ ID が既に存在する場合はそれを単に取得します)。次に、アプリにその ID のプライマリ キーが表示されます。このキーは、IoT Hub に接続するために、シミュレーション対象デバイスによって使用されます。

7. 最後に、**Main** メソッドに次の行を追加します。

		registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. このアプリケーションを実行し、デバイスのキーを書き留めます。

    ![][12]

> [AZURE.NOTE]IoT ハブの ID レジストリは、アクセスをセキュリティ保護する目的で、デバイス ID を格納するためだけに使用する (つまり、セキュリティ資格情報の格納や、個々のデバイスのアクセスの有効化/無効化のため) 点に注意してください。デバイス アプリケーションのメタデータは、アプリケーション固有のストアに保存する必要があります。詳細については、「[IoT Hub 開発者ガイド][IoT Hub Developer Guide - Identity Registry]」を参照してください。

## デバイスからクラウドへのメッセージの受信

このセクションでは、IoT Hub からのデバイスからクラウドへのメッセージを読み込む Windows コンソール アプリを作成します。Iot Hub は、デバイスからクラウドへのメッセージを読み取るために [Event Hubs][Event Hubs Overview] と互換性のあるエンドポイントを公開します。わかりやすくするために、このチュートリアルでは読者を単純化しているため、高スループットのデプロイメントには適しません。IoT Hub のデバイスからクラウドへのメッセージを処理する方法の詳細については、「[Process Device-to-Cloud messages (デバイスからクラウドへのメッセージの処理)]」チュートリアルを参照してください。Event Hubs からのメッセージを処理する方法の詳細については、「[Event Hubs の使用]」チュートリアルを参照してください。

1. 現在の Visual Studio ソリューションで、**[ファイル]、[追加]、[プロジェクト]** の順にクリックし、**コンソール アプリケーション** プロジェクト テンプレートを使用して、新しい Visual C# のデスクトップ アプリ プロジェクトを作成します。プロジェクトに **ReadDeviceToCloudMessages** という名前を付けます。

    ![][10]

2. ソリューション エクスプローラーでソリューションを右クリックし、**[NuGet パッケージの管理]** をクリックします。

    **[NuGet パッケージの管理]** ダイアログ ボックスが表示されます。

3. `WindowsAzure.ServiceBus` を検索し、**[インストール]** をクリックして、使用条件に同意します。

    これによって、[Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) への参照がすべての依存関係と共にダウンロード、インストール、追加されます。

4. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。

        using Microsoft.ServiceBus.Messaging;

5. **Program** クラスに次のフィールドを追加し、前のセクションで作成した IoT ハブの名前と接続文字列を値に代入します。

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. **Program** クラスに次のメソッドを追加します。

        private async static Task ReceiveMessagesFromDeviceAsync(string partition)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.Now);
            while (true)
            {
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine(string.Format("Message received. Partition: {0} Data: '{1}'", partition, data));
            }
        }

    このメソッドでは、EventHub クライアントを使用して、すべてのデバイスからクラウドへのメッセージから、IoT ハブのパーティションを受信します。いつ、どのように EventHubReceiver が作成され、`DateTime.Now` パラメーターが渡されるかに注意してください。これにより、開始後に送信されるメッセージのみを受信する受信者が作成されます。

7. 最後に、**Main** メソッドに次の行を追加します。

        Console.WriteLine("Receive messages\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        foreach (string partition in d2cPartitions)
        {
           ReceiveMessagesFromDeviceAsync(partition);
        }  
        Console.ReadLine();


<!-- Links -->

[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[Event Hubs の使用]: event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: iot-hub-devguide.md#identityregistry

[Event Hubs Overview]: event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[Azure Storage account]: storage-create-storage-account.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Azure preview portal]: https://portal.azure.com/


<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

<!----HONumber=Nov15_HO3-->