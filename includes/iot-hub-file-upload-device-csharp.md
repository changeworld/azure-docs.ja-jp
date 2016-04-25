## シミュレーションされたデバイスからのファイルのアップロード

このセクションでは、「IoT Hub を使用したクラウドからデバイスへのメッセージの送信」で作成したシミュレーション済みデバイス アプリケーションを変更して、クラウドからデバイスへのメッセージを IoT Hub から受信します。

1. Visual Studio で **SimulatedDevice** プロジェクトを右クリックし、**[NuGet パッケージの管理...]** をクリックします。 

    [NuGet パッケージの管理] ウィンドウが表示されます。

2. `WindowsAzure.Storage` を検索し、**[インストール]** をクリックして、使用条件に同意します。

    これにより、[Microsoft Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage/) のダウンロード、インストール、およびリファレンスの追加が行われます。

3. **Program.cs** ファイルの先頭に、次のステートメントを追加します。

        using System.IO;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

4. **Program** クラスで、**ReceiveC2dAsync** メソッドを次の方法で変更します。
         
        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                if (receivedMessage.Properties.ContainsKey("command") && receivedMessage.Properties["command"] == "FileUpload")
                {
                    UploadFileToBlobAsync(receivedMessage);
                    continue;
                }

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();
            }
        }

    これにより、`command` プロパティが `FileUpload` に設定され、**ReceiveC2dAsync** によってメッセージが識別されるようになります。これは、**UploadFileToBlobAsync** メソッドによって処理されます。

    以下のメソッドを追加して、ファイル アップロード コマンドを処理します。
   
        private static async Task UploadFileToBlobAsync(Message fileUploadCommand)
        {
            var fileUri = fileUploadCommand.Properties["fileUri"];
            var blob = new CloudBlockBlob(new Uri(fileUri));

            byte[] data = new byte[10 * 1024 * 1024];
            Random rng = new Random();
            rng.NextBytes(data);

            MemoryStream msWrite = new MemoryStream(data);
            msWrite.Position = 0;
            using (msWrite)
            {
                await blob.UploadFromStreamAsync(msWrite);
            }
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Uploaded file to: {0}", fileUri);
            Console.ResetColor();

            await deviceClient.CompleteAsync(fileUploadCommand);
        }

    このメソッドでは、Azure Storage SDK を使用してランダムに生成された 10Mb の BLOB が、指定された URI にアップロードされます。BLOB のアップロード方法については、「[Azure Storage - How to use blobs (Azure ストレージ - BLOB の使用方法)]」をご覧ください。

> [AZURE.NOTE] シミュレーション済みデバイスのこの実装によって、BLOB がアップロードされた後にのみ、クラウドからデバイスへのメッセージが処理されるという方法に留意してください。この方法では、配信の受信確認によってアップロードされたファイルを処理できるかどうかが示されるため、バックエンドでのアップロード済みファイルの処理が簡略化されます。ただし、「[IoT Hub 開発者ガイド][IoT Hub Developer Guide - C2D]」で説明されているように、*表示タイムアウト* (通常は 1 分) までに完了しなかったメッセージはデバイス キューに戻され、**ReceiveAsync()** メソッドによってもう一度受信されます。ファイル アップロードに時間がかかるシナリオの場合、シミュレーション済みデバイスで現在のアップロード ジョブの永続的ストアを保持するほうが良い場合があります。このようにすると、シミュレーション済みデバイスは、ファイルのアップロードが完了する前にクラウドからデバイスへのメッセージを完了し、バックエンドに完了を通知するデバイスからクラウドへのメッセージを送信できるようになります。

<!-- Links -->
[IoT Hub Developer Guide - C2D]: ../articles/iot-hub/iot-hub-devguide.md#c2d
[Azure Storage - How to use blobs (Azure ストレージ - BLOB の使用方法)]: ../articles/storage/storage-dotnet-how-to-use-blobs.md#upload-a-blob-into-a-container

<!-- Images -->

<!---HONumber=AcomDC_0413_2016-->