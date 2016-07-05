## IoT Hub への Azure Storage アカウントの関連付け

シミュレーションされたデバイスが Azure Storage BLOB にファイルをアップロードするため、IoT Hub に関連付けられている Azure Storage アカウントが必要です。既存のストレージ アカウントを使用することも、[Azure ストレージ アカウントについて]に関するページの手順に従って新しいアカウントを作成することもできます。「[Azure ポータルを使用した IoT Hub の管理]」の手順に従って、Azure Storage アカウントを IoT Hub に関連付けることができます。

## シミュレーションされたデバイスからのファイルのアップロード

このセクションでは、「IoT Hub でクラウドからデバイスへのメッセージの送信」で作成したシミュレーション済みデバイス アプリケーションを変更して、クラウドからデバイスへのメッセージを IoT Hub から受信します。

1. Visual Studio で **SimulatedDevice** プロジェクトを右クリックして **[追加]** をクリックし、**[既存の項目]** をクリックします。イメージ ファイルに移動し、プロジェクトに含めます。このチュートリアルでは、イメージ名が `image.jpg` という前提で説明します。

2. イメージを右クリックし、**[プロパティ]** をクリックします。**[出力ディレクトリにコピー]** が **[常にコピーする]** に設定されていることを確認します。

    ![][1]

3. **Program.cs** ファイルの先頭に、次のステートメントを追加します。

        using System.IO;

4. **Program** クラスに次のメソッドを追加します。
         
        private static async void SendToBlobAsync()
        {
            string fileName = "image.jpg";
            Console.WriteLine("Uploading file: {0}", fileName);
            var watch = System.Diagnostics.Stopwatch.StartNew();

            using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
            {
                await deviceClient.UploadToBlobAsync(fileName, sourceData);
            }

            watch.Stop();
            Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
        }

    `UploadToBlobAsync` メソッドは、アップロードするファイルのファイル名とストリーム ソースを取り込み、ストレージへのアップロードを処理します。コンソール アプリケーションには、ファイルのアップロードにかかる時間が表示されます。

5. **Main** メソッドの `Console.ReadLine()` 行の直前に次のメソッドを追加します。

        SendToBlobAsync();

> [AZURE.NOTE] わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。運用コードでは、MSDN の記事「[Transient Fault Handling (一時的な障害の処理)]」で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Transient Fault Handling (一時的な障害の処理)]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure ストレージ アカウントについて]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Azure ポータルを使用した IoT Hub の管理]: ../articles/iot-hub-manage-through-portal/#file-upload

<!-- Images -->
[1]: ../articles/iot-hub/media/iot-hub-csharp-csharp-file-upload/image-properties.png

<!---HONumber=AcomDC_0622_2016-->