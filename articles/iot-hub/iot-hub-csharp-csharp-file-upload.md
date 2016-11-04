---
title: IoT Hub を使用したデバイスからのファイルのアップロード | Microsoft Docs
description: このチュートリアルに従って、C# を使用して Azure IoT Hub でデバイスからファイルをアップロードする方法を学習します。
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2016
ms.author: elioda

---
# チュートリアル: IoT Hub でデバイスからクラウドにファイルをアップロードする方法
## はじめに
Azure IoT Hub は、何百万もの IoT デバイスとアプリケーション バックエンドの間に信頼性のある保護された双方向通信を確立できる、完全に管理されたサービスです。前のチュートリアル ([IoT Hub の使用]と [IoT Hub を使用したクラウドからデバイスへのメッセージの送信]) では、IoT Hub の基本的なデバイスとクラウド間のメッセージング機能を説明し、[デバイスからクラウドへのメッセージの処理]に関するチュートリアルでは、デバイスからクラウドへのメッセージを Azure BLOB ストレージに確実に格納する方法を示しています。ただし、一部のシナリオでは、デバイスから送信されるデータを、IoT Hub が受け取る、クラウドからデバイスへの比較的小さなメッセージにマッピングすることは簡単ではありません。イメージ、ビデオ、高周波振動データのサンプルが含まれる大きなファイルや、何らかの形式の前処理済みデータが含まれる大きなファイルがその例です。これらのファイルは通常、[Azure Data Factory] や [Hadoop] スタックなどのツールを使用してクラウドでバッチ処理されます。イベントの送信よりもデバイスからのファイルのアップロードの方が好ましい場合は、IoT Hub セキュリティと信頼性機能を引き続き使用できます。

このチュートリアルは、[IoT Hub を使用したクラウドからデバイスへのメッセージの送信]に関するページ内のコードに基づいて作成されており、IoT Hub のファイル アップロード機能を使用する方法を説明します。また、デバイスに Azure BLOB URI を安全に提供してファイルをアップロードする方法と、IoT Hub のファイル アップロード通知を使用してアプリケーションのバックエンドでファイル処理をトリガーする方法を紹介します。

このチュートリアルの最後には、次の 2 つの Windows コンソール アプリケーションを実行します。

* **SimulatedDevice**。[IoT Hub でクラウドからデバイスへのメッセージを送信する方法]に関するチュートリアルで作成したアプリケーションの変更済みバージョンです。このアプリケーションは、IoT Hub によって提供された SAS URI を使用してストレージにファイルをアップロードします。
* **ReadFileUploadNotification**。IoT Hub からファイル アップロード通知を受信します。

> [!NOTE]
> IoT Hub は、Azure IoT device SDK を介して多数のデバイス プラットフォームや言語 (C、Java、Javascript など) をサポートしています。このチュートリアルで例示しているコード (一般的には Azure IoT Hub) にデバイスを接続するための詳しい手順については、[Azure IoT デベロッパー センター]を参照してください。
> 
> 

このチュートリアルを完了するには、以下が必要になります。

* Microsoft Visual Studio 2015、
* アクティブな Azure アカウント。アカウントがない場合は、無料試用版アカウントを数分で作成することができます。詳細については、[Azure の無料試用版][lnk-free-trial]サイトを参照してください。

## IoT Hub への Azure Storage アカウントの関連付け
シミュレートされたデバイスによって Azure Storage BLOB にファイルがアップロードされるため、IoT Hub に関連付けられている [Azure Storage] アカウントが必要です。ストレージ アカウントを IoT Hub に関連付けると、その IoT Hub で SAS URI を生成できます。この SAS URI をデバイスで使用することにより、ファイルを BLOB コンテナーに安全にアップロードできます。SAS URI を生成し、デバイスでファイルのアップロードに使用できるようにするプロセスは、IoT Hub サービスとデバイス SDK によって調整されます。

Azure ストレージ アカウントを IoT Hub に関連付けるには、「[Azure ポータルを使用した IoT Hub の管理]」の手順に従ってください。

## シミュレーションされたデバイスからのファイルのアップロード
このセクションでは、[IoT Hub を使用したクラウドからデバイスへのメッセージの送信]についてのチュートリアルで作成したシミュレートされたデバイス アプリケーションを変更して、クラウドからデバイスへのメッセージを IoT Hub から受信するようにします。

1. Visual Studio で **SimulatedDevice** プロジェクトを右クリックして **[追加]** をクリックし、**[既存の項目]** をクリックします。イメージ ファイルに移動し、プロジェクトに含めます。このチュートリアルでは、イメージ名を `image.jpg` と仮定して説明します。
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
   
    `UploadToBlobAsync` メソッドは、アップロードするファイルのファイル名とストリーム ソースを受け取り、ストレージへのアップロードを処理します。コンソール アプリケーションには、ファイルのアップロードにかかる時間が表示されます。
5. **Main** メソッドの `Console.ReadLine()` 行の直前に次のメソッドを追加します。
   
        SendToBlobAsync();

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。運用環境のコードでは、MSDN の記事「[Transient Fault Handling (一時的な障害の処理)]」で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
> 
> 

## ファイル アップロードの通知の受信
このセクションでは、IoT Hub からファイル アップロードの通知メッセージを受信する Windows コンソール アプリケーションを作成します。

1. 現在の Visual Studio ソリューションで、**コンソール アプリケーション** プロジェクト テンプレートを使用して、新しい Visual C# Windows プロジェクトを作成します。プロジェクトに **ReadFileUploadNotification** という名前を付けます。
   
    ![Visual Studio での新しいプロジェクト][2]
2. ソリューション エクスプローラーで **ReadFileUploadNotification** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。
   
    [NuGet パッケージの管理] ウィンドウが表示されます。
3. `Microsoft.Azure.Devices` を検索し、**[インストール]** をクリックして、使用条件に同意します。
   
    これによりパッケージのダウンロードとインストールが実行され、**ReadFileUploadNotification** プロジェクトに [Azure IoT Service SDK NuGet パッケージ]への参照が追加されます。
4. **Program.cs** ファイルの先頭に、次のステートメントを追加します。
   
        using Microsoft.Azure.Devices;
5. **Program** クラスに次のフィールドを追加します。プレースホルダーの値は、[IoT Hub の概要]に関するページで作成した IoT Hub の接続文字列に置き換えます。
   
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

## アプリケーションの実行
これで、アプリケーションを実行する準備が整いました。

1. Visual Studio でソリューションを右クリックし、**[スタートアップ プロジェクトの設定]** を選択します。**[マルチ スタートアップ プロジェクト]** を選択してから、**ReadFileUploadNotification** と **SimulatedDevice** に対して **[開始]** アクションを選択します。
2. **F5** キーを押します。両方のアプリケーションが開始されます。1 つのコンソール アプリケーションでアップロードの完了が表示され、もう 1 つのコンソール アプリケーションでアップロード通知メッセージが受信されます。[Azure ポータル]または Visual Studio サーバー エクスプローラーを使用して、ストレージ アカウントにアップロードされたファイルがあるかどうかを確認できます。
   
   ![][50]

## 次のステップ
このチュートリアルでは、IoT Hub のファイル アップロード機能を活用して、デバイスからのファイルのアップロードを簡素化する方法を学習しました。次の記事で IoT Hub の機能やシナリオをさらに詳しく調べることができます。

* [プログラムによる IoT Hub 作成][lnk-create-hub]
* [C SDK の概要][lnk-c-sdk]
* [IoT Hub SDK][lnk-sdks]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [ソリューションの設計][lnk-design]
* [サンプル UI を使用したデバイス管理の探求][lnk-dmui]
* [Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]
* [Azure ポータルを使用した IoT Hub の管理][lnk-portal]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/create-identity-csharp1.png

<!-- Links -->

[Azure ポータル]: https://portal.azure.com/

[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[IoT Hub でクラウドからデバイスへのメッセージを送信する方法]: iot-hub-csharp-csharp-c2d.md
[IoT Hub を使用したクラウドからデバイスへのメッセージの送信]: iot-hub-csharp-csharp-c2d.md
[デバイスからクラウドへのメッセージの処理]: iot-hub-csharp-csharp-process-d2c.md
[IoT Hub の使用]: iot-hub-csharp-csharp-getstarted.md
[IoT Hub の概要]: iot-hub-csharp-csharp-getstarted.md
[Azure IoT デベロッパー センター]: http://www.azure.com/develop/iot

[Transient Fault Handling (一時的な障害の処理)]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Storage]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Azure ポータルを使用した IoT Hub の管理]: iot-hub-manage-through-portal.md#file-upload
[Azure IoT Service SDK NuGet パッケージ]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-sdks-summary.md

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0914_2016-->