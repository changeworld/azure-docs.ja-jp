## Azure ストレージ アカウントのプロビジョニング
シミュレーションされたデバイスが Azure Storage BLOB にファイルをアップロードするため、Azure ストレージ アカウントが必要です。既存のものを使用することも、「[Azure ストレージについて]」の手順に従って新しいものを作成することもできます。ストレージ アカウント接続文字列はメモしておいてください。

## シミュレート済みデバイスへの Azure BLOB URI の送信

このセクションでは、「IoT Hub を使用したクラウドからデバイスへのメッセージの送信」で作成した **SendCloudtoDevice** コンソール アプリケーションを変更して、共有アクセス署名と共に Azure BLOB URI が含まれるようにします。これにより、クラウド バックエンドは、クラウドからデバイスへのメッセージの受信者のみに BLOB へのアクセス権を付与できます。

1. Visual Studio で **SendCloudtoDevice** プロジェクトを右クリックし、**[NuGet パッケージの管理...]** をクリックします。 

    [NuGet パッケージの管理] ウィンドウが表示されます。

2. `WindowsAzure.Storage` を検索し、**[インストール]** をクリックして、使用条件に同意します。

    これにより、[Microsoft Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage/) のダウンロード、インストール、およびリファレンスの追加が行われます。

3. **Program.cs** ファイルの先頭に、次のステートメントを追加します。

        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

4. **Program** クラスで、次のクラス フィールドを追加し、ストレージ アカウントの接続文字列を置き換えます。

        static string storageConnectionString = "{storage connection string}";

    次に、次のメソッドを追加します (任意の BLOB コンテナー名に置き換えることができますが、このチュートリアルでは **iothubfileuploadtutorial** を使用します)。
   
        private static async Task<string> GenerateBlobUriAsync()
        {
            var storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            var blobClient = storageAccount.CreateCloudBlobClient();
            var blobContainer = blobClient.GetContainerReference("iothubfileuploadtutorial");
            await blobContainer.CreateIfNotExistsAsync();

            var blobName = String.Format("deviceUpload_{0}", Guid.NewGuid().ToString());
            CloudBlockBlob blob = blobContainer.GetBlockBlobReference(blobName);

            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
            sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
            sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
            sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            return blob.Uri + sasBlobToken;
        }

    このメソッドにより、新しい BLOB リファレンスが作成され、「[BLOB サービスによる SAS の作成および使用](https://azure.microsoft.com/ja-jp/documentation/articles/storage-dotnet-shared-access-signature-part-2/)」で説明されているように共有アクセス署名 URI が生成されます。上記のメソッドで生成される署名 URI の有効期限は 24 時間です。ターゲット デバイスでファイルをアップロードするのにこれ以上の時間を要する場合は (接続頻度が低い場合や、容量の大きいファイルをアップロードするには接続の信頼性が低い場合など)、署名の有効期限の延長を検討してください。

5. 次の方法で **SendCloudToDeviceMessageAsync** を変更します。

        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message();
            commandMessage.Properties["command"] = "FileUpload";
            commandMessage.Properties["fileUri"] = await GenerateBlobUriAsync();
            commandMessage.Ack = DeliveryAcknowledgement.Full;

            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

    このメソッドでは、ファイルをアップロードするコマンドとしてこのメッセージを識別するプロパティと、BLOB URI を保持するプロパティという 2 つのアプリケーション プロパティを含む、クラウドからデバイスへのメッセージが送信されます。また、完全な配信の受信確認も要求されます。2 つのアプリケーション プロパティの情報はメッセージ本文にシリアル化することができますが、情報のシリアル化と逆シリアル化を行うための追加処理が必要になります。

<!-- Links -->

[Azure ストレージについて]: https://azure.microsoft.com/ja-jp/documentation/articles/storage-create-storage-account/#create-a-storage-account

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling]: https://msdn.microsoft.com/ja-jp/library/hh680901(v=pandp.50).aspx
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md

<!-- Images -->

<!---HONumber=Oct15_HO3-->