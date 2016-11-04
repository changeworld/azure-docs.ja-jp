## クラウドからデバイスへのメッセージをアプリケーションのバックエンドから送信する
このセクションでは、デバイスからクラウドへのメッセージを、シミュレートする デバイス アプリに送信する Windows コンソール アプリを作成します。

1. 現在の Visual Studio ソリューションで、**コンソール アプリケーション** プロジェクト テンプレートを使用して、新しい Visual C# のデスクトップ アプリ プロジェクトを作成します。プロジェクトに **SendCloudToDevice** という名前を付けます。
   
       ![Visual Studio での新しいプロジェクト][20]
2. ソリューション エクスプローラーでソリューションを右クリックし、**[ソリューション用 NuGet パッケージの管理]** をクリックします。
   
    **[NuGet パッケージの管理]** ウィンドウが開きます。
3. `Microsoft Azure Devices` を検索し、**[インストール]** をクリックして、使用条件に同意します。
   
    これによりパッケージのダウンロードとインストールが実行され、[Azure IoT - サービス SDK NuGet パッケージ]への参照が追加されます。
4. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。
   
        using Microsoft.Azure.Devices;
5. **Program** クラスに次のフィールドを追加します。 [IoT Hub の概要] から、IoT ハブの接続文字列を値に代入します。
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. **Program** クラスに次のメソッドを追加します。
   
        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }
   
    このメソッドは、新しいクラウドからデバイスへのメッセージを ID `myFirstDevice` を持つデバイスに送信します。[[IoT Hub の概要]] で使用される内容から変更している場合は、それに応じてこのパラメーターを変更します。
7. 最後に、**Main** メソッドに次の行を追加します。
   
        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
   
        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();
8. Visual Studio 内でソリューションを右クリックし、**[スタートアップ プロジェクトの設定...]** を選択します。**[マルチ スタートアップ プロジェクト]** を選択してから、**ProcessDeviceToCloudMessages**、**SimulatedDevice**、および **SendCloudToDevice** の **[開始]** アクションを選択します。
9. **F5** キーを押します。3 つのすべてのアプリケーションが開始されます。**[SendCloudToDevice]** ウィンドウを選択して **Enter** キーを押します。シミュレーション対象アプリによってメッセージが受信されていることがわかります。
   
   ![アプリによるメッセージの受信][21]

## 配信フィードバックの受信
クラウドからデバイスへの各メッセージに対して IoT Hub からの配信 (または有効期限) 確認を要求できます。これにより、クラウド バックエンドで再試行または補正ロジックを簡単に通知できるようになります。クラウドからデバイスへのフィードバックの詳細については、「[Azure IoT Hub 開発者ガイド][IoT Hub Developer Guide - C2D]」をご覧ください。

このセクションでは、**SendCloudToDevice** アプリを、フィードバックを要求し、IoT Hub からそれらを受信するように変更します。

1. Visual Studio の **SendCloudToDevice** プロジェクトで、次のメソッドを **Program** クラスに追加します。
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();
   
            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();
   
                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }
   
    受信パターンは、クラウドからデバイスへのメッセージをデバイス アプリから受信するために使用するものと同じであることに注意してください。
2. `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` 行の直後の **Main** メソッドに次のメソッドを追加します。
   
        ReceiveFeedbackAsync();
3. クラウドからデバイスへのメッセージの配信のためのフィードバックを要求するには、**SendCloudToDeviceMessageAsync** メソッドにプロパティを指定する必要があります。`var commandMessage = new Message(...);` 行の直後に次の行を追加します。
   
        commandMessage.Ack = DeliveryAcknowledgement.Full;
4. **F5** キーを押してアプリを実行します。3 つすべてのアプリケーションが開始されていることが確認できます。**[SendCloudToDevice]** ウィンドウを選択して **Enter** キーを押します。シミュレーション対象アプリによってメッセージが受信され、数秒後に、**SendCloudToDevice** アプリケーションによりフィードバック メッセージが受信されていることがわかります。
   
   ![アプリによるメッセージの受信][22]

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。運用環境のコードでは、MSDN の記事「[Transient Fault Handling (一時的な障害処理)]」で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
> 
> 

<!-- Links -->

[IoT Hub Developer Guide - C2D]: ../articles/iot-hub/iot-hub-devguide.md#c2d
[Azure IoT - サービス SDK NuGet パッケージ]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling (一時的な障害処理)]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[IoT Hub の概要]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md

<!-- Images -->
[20]: ./media/iot-hub-c2d-cloud-csharp/create-identity-csharp1.png
[21]: ./media/iot-hub-c2d-cloud-csharp/sendc2d1.png
[22]: ./media/iot-hub-c2d-cloud-csharp/sendc2d2.png

<!---HONumber=AcomDC_0608_2016-->