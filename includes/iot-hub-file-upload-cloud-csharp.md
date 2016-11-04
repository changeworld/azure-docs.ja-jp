## ファイル アップロードの通知の受信
このセクションでは、IoT Hub からファイル アップロードの通知メッセージを受信する Windows コンソール アプリケーションを作成します。

1. 現在の Visual Studio ソリューションで、**コンソール アプリケーション** プロジェクト テンプレートを使用して、新しい Visual C# Windows プロジェクトを作成します。プロジェクトに **ReadFileUploadNotification** という名前を付けます。
   
    ![Visual Studio での新しいプロジェクト][2]
2. ソリューション エクスプローラーで、**[ReadFileUploadNotification]** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。
   
    [NuGet パッケージの管理] ウィンドウが表示されます。
3. `Microsoft.Azure.Devices` を検索し、**[インストール]** をクリックして、使用条件に同意します。
   
    これによりパッケージのダウンロードとインストールが実行され、 **ReadFileUploadNotification** プロジェクトで [Azure IoT - サービス SDK NuGet パッケージ]への参照が追加されます。
4. **Program.cs** ファイルの先頭に、次のステートメントを追加します。
   
        using Microsoft.Azure.Devices;
5. **Program** クラスに次のフィールドを追加します。[[IoT Hub の概要]]から、IoT ハブの接続文字列を値に代入します。
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. **Program** クラスに次のメソッドを追加します。
   
        private async static Task ReceiveFileUploadNotificationAsync()
        {
            var notificationReceiver = serviceClient.GetFileNotificationReceiver();
   
            Console.WriteLine("\nReceiving file upload notification from service");
            while (true)
            {
                var fileUploadNotification = await notificationReceiver.ReceiveAsync();
                if (fileUploadNotification == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
                Console.ResetColor();
   
                await notificationReceiver.CompleteAsync(fileUploadNotification);
            }
        }
   
    受信パターンは、クラウドからデバイスへのメッセージをデバイス アプリから受信するために使用するものと同じであることに注意してください。
7. 最後に、**Main** メソッドに次の行を追加します。
   
        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

<!-- Links -->

[IoT Hub Developer Guide - C2D]: ../articles/iot-hub/iot-hub-devguide.md#c2d
[Azure IoT - サービス SDK NuGet パッケージ]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[[IoT Hub の概要]]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md

<!-- Images -->
[2]: ./media/iot-hub-c2d-cloud-csharp/create-identity-csharp1.png

<!---HONumber=AcomDC_0622_2016-->