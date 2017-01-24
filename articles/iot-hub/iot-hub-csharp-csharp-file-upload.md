---
title: "Azure IoT Hub を使用したデバイスからのファイルのアップロード | Microsoft Docs"
description: ".NET 用 Azure IoT device SDK を使用して、デバイスからクラウドにファイルをアップロードする方法。 アップロードしたファイルは Azure Storage Blob コンテナーに格納されます。"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: a243e4f64b6cd0bf7b0776e938150a352d424ad1
ms.openlocfilehash: ddc181bde6a154cb3fb35254da6b76a91450ba6b


---
# <a name="upload-files-from-devices-to-the-cloud-with-iot-hub"></a>IoT Hub でデバイスからクラウドにファイルをアップロードする
## <a name="introduction"></a>はじめに
Azure IoT Hub は、何百万ものデバイスとソリューション バック エンド間で、セキュリティで保護された信頼性のある双方向通信を実現する、完全に管理されたサービスです。 前のチュートリアル (「[Azure IoT Hub for .NET の使用]」と「[IoT Hub と .Net でクラウドからデバイスへのメッセージを送信する方法]」) では、IoT Hub の基本的なデバイスとクラウド間のメッセージング機能を説明し、「[チュートリアル: .Net を使用して IoT Hub のデバイスからクラウドへのメッセージを処理する方法]」では、デバイスからクラウドへのメッセージを Azure BLOB ストレージに確実に格納する方法を示しています。 ただし、一部のシナリオでは、デバイスから送信されるデータを、IoT Hub が受け取る、クラウドからデバイスへの比較的小さなメッセージにマッピングすることは簡単ではありません。 イメージ、ビデオ、高周波振動データのサンプルが含まれる大きなファイルや、何らかの形式の前処理済みデータが含まれる大きなファイルがその例です。 これらのファイルは通常、[Azure Data Factory] や [Hadoop] スタックなどのツールを使用してクラウドでバッチ処理されます。 イベントの送信よりもデバイスからのファイルのアップロードの方が好ましい場合は、IoT Hub セキュリティと信頼性機能を引き続き使用できます。

このチュートリアルは、 [IoT Hub と .Net でクラウドからデバイスへのメッセージを送信する方法] に関するページ内のコードに基づいて作成されており、IoT Hub のファイル アップロード機能を使用する方法を説明します。 次の方法について説明します。

- ファイルのアップロードで Azure BLOB URI を使用してデバイスをセキュリティで保護する。
- IoT Hub ファイル アップロード通知を使用して、アプリのバックエンドでのファイルの処理を開始する。

このチュートリアルの最後に、次の 2 つの .NET コンソール アプリを実行します。

* **SimulatedDevice**: 「[IoT Hub と .Net でクラウドからデバイスへのメッセージを送信する方法]」で作成したアプリの変更済みバージョンです。 このアプリは、IoT Hub によって提供された SAS URI を使用してストレージにファイルをアップロードします。
* **ReadFileUploadNotification**: IoT Hub からファイル アップロード通知を受信します。

> [!NOTE]
> IoT Hub は、Azure IoT device SDK を介して多数のデバイス プラットフォームや言語 (C、Java、Javascript など) をサポートしています。 Azure IoT Hub にデバイスを接続するための詳しい手順については、[Azure IoT デベロッパー センター]を参照してください。
> 
> 

このチュートリアルを完了するには、以下が必要です。

* Microsoft Visual Studio 2015
* アクティブな Azure アカウント。 (アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成できます)。

## <a name="associate-an-azure-storage-account-to-iot-hub"></a>IoT Hub への Azure Storage アカウントの関連付け
シミュレート対象デバイス アプリによって BLOB にファイルがアップロードされるため、IoT Hub に関連付けられている [Azure Storage] アカウントが必要です。 Azure Storage アカウントを IoT Hub に関連付けると、その IoT Hub で SAS URI を生成できます。この SAS URI をデバイスで使用することにより、ファイルを BLOB コンテナーに安全にアップロードできます。 SAS URI を生成し、デバイスでファイルのアップロードに使用できるようにするプロセスは、IoT Hub サービスとデバイス SDK によって調整されます。

「[Configure file uploads using the Azure portal (Azure Portal を使用したファイルのアップロードの構成)][lnk-configure-upload]」の指示に従って、Azure Storage アカウントを IoT Hub に関連付けます。

## <a name="upload-a-file-from-a-simulated-device-app"></a>シミュレート対象デバイス アプリからのファイルのアップロード
このセクションでは、[IoT Hub と .Net でクラウドからデバイスへのメッセージを送信する方法]に関するチュートリアルで作成したシミュレート対象デバイス アプリを変更して、クラウドからデバイスへのメッセージを IoT Hub から受信するようにします。

1. Visual Studio で **SimulatedDevice** プロジェクトを右クリックして **[追加]** をクリックし、**[既存の項目]** をクリックします。 イメージ ファイルに移動し、プロジェクトに含めます。 このチュートリアルでは、イメージ名が `image.jpg`という前提で説明します。
2. イメージを右クリックし、 **[プロパティ]**をクリックします。 **[出力ディレクトリにコピー]** が **[常にコピーする]** に設定されていることを確認します。
   
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
   
    `UploadToBlobAsync` メソッドは、アップロードするファイルのファイル名とストリーム ソースを取り込み、ストレージへのアップロードを処理します。 コンソール アプリには、ファイルのアップロードにかかる時間が表示されます。
5. **Main** メソッドの `Console.ReadLine()` 行の直前に次のメソッドを追加します。
   
        SendToBlobAsync();

> [!NOTE]
> わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、MSDN の記事「 [Transient Fault Handling (一時的な障害の処理)]」で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
> 
> 

## <a name="receive-a-file-upload-notification"></a>ファイル アップロードの通知の受信
このセクションでは、IoT Hub からファイル アップロードの通知メッセージを受信する .NET コンソール アプリケーションを作成します。

1. 現在の Visual Studio ソリューションで、 **コンソール アプリケーション** プロジェクト テンプレートを使用して、Visual C# Windows プロジェクトを作成します。 プロジェクトに **ReadFileUploadNotification**という名前を付けます。
   
    ![Visual Studio での新しいプロジェクト][2]
2. ソリューション エクスプローラーで **ReadFileUploadNotification** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。
   
    この操作によって、[NuGet パッケージの管理] ウィンドウが表示されます。
3. `Microsoft.Azure.Devices`を検索し、 **[インストール]**をクリックして、使用条件に同意します。 
   
    この操作によって、パッケージのダウンロードとインストールが実行され、[Azure IoT サービス SDK NuGet パッケージ]への参照が **ReadFileUploadNotification** プロジェクトに追加されます。

4. **Program.cs** ファイルの先頭に、次のステートメントを追加します。
   
        using Microsoft.Azure.Devices;
5. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値を「[Azure IoT Hub for .NET の使用]」で取得した IoT hub の接続文字列に置き換えます。
   
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
7. 最後に、 **Main** メソッドに次の行を追加します。
   
        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

## <a name="run-the-applications"></a>アプリケーションの実行
これで、アプリケーションを実行する準備が整いました。

1. Visual Studio でソリューションを右クリックし、 **[スタートアップ プロジェクトの設定]**を選択します。 **[マルチ スタートアップ プロジェクト]** を選択してから、**ReadFileUploadNotification** と **SimulatedDevice** に対して **[開始]** アクションを選択します。
2. **F5**キーを押します。 両方のアプリケーションが開始されます。 1 つのコンソール アプリケーションでアップロードの完了が表示され、もう 1 つのコンソール アプリケーションでアップロード通知メッセージが受信されます。 [Azure Portal] または Visual Studio サーバー エクスプローラーを使用して、Azure Storage アカウントにアップロードされたファイルがあるかどうかを確認できます。
   
   ![][50]

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、IoT Hub のファイル アップロード機能を活用して、デバイスからのファイルのアップロードを簡素化する方法を学習しました。 次の記事で IoT Hub の機能やシナリオをさらに詳しく調べることができます。

* [プログラムによる IoT Hub の作成][lnk-create-hub]
* [C SDK の概要][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Gateway SDK を使用したデバイスのシミュレーション][lnk-gateway]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/create-identity-csharp1.png

<!-- Links -->

[Azure Portal]: https://portal.azure.com/

[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[IoT Hub と .Net でクラウドからデバイスへのメッセージを送信する方法]: iot-hub-csharp-csharp-c2d.md
[チュートリアル: .Net を使用して IoT Hub のデバイスからクラウドへのメッセージを処理する方法]: iot-hub-csharp-csharp-process-d2c.md
[Azure IoT Hub for .NET の使用]: iot-hub-csharp-csharp-getstarted.md
[Azure IoT デベロッパー センター]: http://www.azure.com/develop/iot

[Transient Fault Handling (一時的な障害の処理)]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure Storage]: ../storage/storage-create-storage-account.md#create-a-storage-account
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[Azure IoT サービス SDK NuGet パッケージ]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md





<!--HONumber=Dec16_HO1-->


