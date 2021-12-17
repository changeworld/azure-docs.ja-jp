---
title: .NET を使用してデバイスから Azure IoT Hub にファイルをアップロードする | Microsoft Docs
description: .NET 用 Azure IoT device SDK を使用して、デバイスからクラウドにファイルをアップロードする方法。 アップロードしたファイルは Azure Storage Blob コンテナーに格納されます。
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/24/2021
ms.author: lizross
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: c797c2668a1af750f0cb1ae4a743f1bc4b9f3aac
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556099"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>IoT Hub を使用してデバイスからクラウドにファイルをアップロードする (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

このチュートリアルでは、IoT Hub のファイル アップロード機能を Azure IoT .NET デバイスおよびサービス SDK で使用する方法を紹介します。

[デバイスから IoT ハブにテレメトリを送信する方法](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp)のクイックスタートと [IoT Hub で cloud-to-device メッセージを送信する方法](iot-hub-csharp-csharp-c2d.md)のチュートリアルには、IoT Hub のデバイスからクラウドへのメッセージングと cloud-to-device メッセージの基本的な機能が示されています。 [IoT Hub を使用したメッセージ ルーティングの構成](tutorial-routing.md)に関するチュートリアルでは、デバイスからクラウドへのメッセージを Microsoft Azure BLOB ストレージに確実に格納する方法について説明しています。 ただし、一部のシナリオでは、デバイスが送信するデータを、IoT Hub が受け付けるデバイスからクラウドへの比較的小さなメッセージには簡単にマップできません。 次に例を示します。

* イメージを含む大きなファイル

* ビデオ

* 高頻度でサンプリングされる振動データ

* 何らかの形式の前処理済みデータ

これらのファイルは通常、[Azure Data Factory](../data-factory/introduction.md) や [Hadoop](../hdinsight/index.yml) スタックなどのツールを使用してクラウドでバッチ処理されます。 ただし、デバイスからファイルをアップロードする必要がある場合は、IoT Hub のセキュリティと信頼性を利用できます。 このチュートリアルでは、その方法を説明します。

このチュートリアルの最後に、次の 2 つの .NET コンソール アプリを実行します。

* **FileUploadSample**。 このデバイス アプリは、IoT Hub によって提供されている SAS URI を使用してストレージにファイルをアップロードします。 このアプリは、前提条件でダウンロードした Azure IoT C# サンプル リポジトリから実行します。

* **ReadFileUploadNotification**。 このサービス アプリは、IoT ハブからのファイル アップロード通知を受信します。 このアプリは、ユーザーが作成します。

> [!NOTE]
> IoT Hub は、Azure IoT device SDK を通して、多数のデバイス プラットフォームと言語 (C、Java、Python、JavaScript を含む) をサポートしています。 Azure IoT Hub にデバイスを接続するための詳しい手順については、[Azure IoT デベロッパー センター](https://azure.microsoft.com/develop/iot)を参照してください。

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>前提条件

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 アカウントがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成することができます。

* この記事で実行するサンプル アプリケーションは、C# を使って記述されています。 Azure IoT の C# サンプルの場合、.NET Core SDK 3.1 以上が搭載された開発マシンを用意することをお勧めします。

    複数のプラットフォームに対応する .NET Core SDK を [.NET](https://dotnet.microsoft.com/download) からダウンロードできます。

    開発コンピューターに現在インストールされている .NET Core SDK のバージョンは、次のコマンドを使って確認できます。

    ```cmd/sh
    dotnet --version
    ```

* [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/main.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/main.zip) から Azure IoT C# サンプルをダウンロードし、ZIP アーカイブを展開します。

* ポート 8883 がファイアウォールで開放されていることを確認してください。 この記事のサンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT ハブに新しいデバイスを登録する

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-include-associate-storage.md)]

## <a name="upload-file-from-a-device-app"></a>デバイス アプリからのファイルのアップロード

この記事では、事前にダウンロードした Azure IoT C# サンプル リポジトリのサンプルをデバイス アプリとして使用します。 以下のファイルは、Visual Studio、Visual Studio Code、またはテキスト エディターを使用して開くことができます。  

このサンプルは、Azure IoT C# サンプルを解凍したフォルダーの下にある、**azure-iot-samples-csharp-main\iot-hub\Samples\device\FileUploadSample** にあります。

**FileUpLoadSample.cs** のコードを確認してください。 このファイルには、メインのサンプル ロジックが含まれています。 IoT Hub デバイス クライアントを作成した後、デバイスからファイルをアップロードするための標準的な 3 つの手順が実行されます。

1. このコードは、デバイス クライアントの **GetFileUploadSasUriAsync** メソッドを呼び出し、IoT ハブから SAS URI を取得します。

    ```csharp
    var fileUploadSasUriRequest = new FileUploadSasUriRequest
    {
        BlobName = fileName
    };

    // Lines removed for clarity

    FileUploadSasUriResponse sasUri = await _deviceClient.GetFileUploadSasUriAsync(fileUploadSasUriRequest);
    Uri uploadUri = sasUri.GetBlobUri();
    ```

1. このコードでは、SAS URI を使用してファイルを Azure Storage にアップロードします。 このサンプルでは、SAS URI を使用して Azure Storage ブロック BLOB クライアントを作成し、ファイルをアップロードします。

    ```csharp
    var blockBlobClient = new BlockBlobClient(uploadUri);
    await blockBlobClient.UploadAsync(fileStreamSource, new BlobUploadOptions());
    ```

1. このコードは、アップロードが完了したことを IoT ハブに通知します。 これにより、アップロードに関連するリソース (SAS URI) を解放してもよいことが、IoT ハブに通知されます。 ファイルのアップロード通知が有効になっている場合、IoT ハブからバックエンド サービスに通知メッセージが送信されます。

    ```csharp
    var successfulFileUploadCompletionNotification = new FileUploadCompletionNotification
    {
        // Mandatory. Must be the same value as the correlation id returned in the sas uri response
        CorrelationId = sasUri.CorrelationId,
    
        // Mandatory. Will be present when service client receives this file upload notification
        IsSuccess = true,
    
        // Optional, user defined status code. Will be present when service client receives this file upload notification
        StatusCode = 200,
    
        // Optional, user-defined status description. Will be present when service client receives this file upload notification
        StatusDescription = "Success"
    };
    
    await _deviceClient.CompleteFileUploadAsync(successfulFileUploadCompletionNotification);
    ```

**parameter.cs** ファイルを調べると、以下のことがわかります:

- このサンプルでは、デバイスの接続文字列を取るパラメーター *p* を渡す必要があります。 

- 既定では、デバイス サンプルは MQTT プロトコルを使用して、IoT Hub と通信します。 このトランスポート プロトコルは、パラメーター *t* を使用することで変更できます。 この選択に関係なく、Azure BLOB クライアントでは、ファイルを Azure ストレージにアップロードするプロトコルとして、常に HTTPS が使用されることに注意してください。

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

この記事では、IoT ハブからファイル アップロード通知メッセージを受信するバックエンド サービスを作成しました。 ファイル アップロード通知メッセージを受信するサービスには、**サービス接続** のアクセス許可が必要となります。 既定では、どの IoT Hub も、このアクセス許可を付与する **service** という名前の共有アクセス ポリシーがある状態で作成されます。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>ファイル アップロードの通知の受信

このセクションでは、IoT ハブからファイル アップロードの通知メッセージを受信する C# コンソール アプリを作成します。

1. コマンド ウィンドウを開いて、プロジェクトを作成するフォルダーに移動します。 **ReadFileUploadNotifications** という名前のフォルダーを作成し、そのフォルダーへのディレクトリを変更します。

    ```cmd/sh
    mkdir ReadFileUploadNotification
    cd ReadFileUploadNotification
    ```

1. 次のコマンドを実行して、C# コンソール プロジェクトを作成します。 コマンドを実行すると、**Program.cs** ファイルと **ReadFileUploadNotification.csproj** ファイルがフォルダーに格納されます。

    ```cmd/sh
    dotnet new console --language c#
    ```

1. 次のコマンドを実行して、**Microsoft.Azure.Devices** パッケージをプロジェクト ファイルに追加します。 このパッケージは、.Azure IoT .NET service SDK です。

    ```cmd/sh
    dotnet add package Microsoft.Azure.Devices
    ```

1. **Program.cs** ファイルを開き、ファイルの先頭に次のステートメントを追加します。

    ```csharp
    using Microsoft.Azure.Devices;
    ```
1. **Program** クラスに次のフィールドを追加します。 プレースホルダー `{iot hub connection string}` の値を、先ほど「[IoT ハブ接続文字列を取得する](#get-the-iot-hub-connection-string)」でコピーしておいた IoT ハブ接続文字列に置き換えます。

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. **Program** クラスに次のメソッドを追加します。

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();
        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload notification: {0}", 
              string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();
            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    この受信パターンは、クラウドからデバイスへのメッセージをデバイス アプリから受信するために使用するものと同じであることに注意してください。

1. 最後に、**Main** メソッドの行を以下に置き換えます。

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```
    
## <a name="run-the-applications"></a>アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1. まず、IoT ハブからのファイル アップロード通知を受け取るために、サービス アプリを実行します。 **ReadFileUploadNotification** フォルダー内のコマンド プロンプトで、以下のコマンドを実行します。 

    ```cmd/sh
    dotnet restore
    dotnet run
    ```
    
    アプリが起動し、IoT ハブからのファイル アップロード通知を待機します。

    ```cmd/sh
    Receive file upload notifications


    Receiving file upload notification from service
    Press Enter to exit
    ```



1. 次に、デバイス アプリを実行して、ファイルを Azure Storage にアップロードします。 新しいコマンド プロンプトを開き、フォルダーを Azure IoT C# サンプルを解凍した下にある **azure-iot-samples-csharp-main\iot-hub\Samples\device\FileUploadSample** フォルダーに変更します。 次のコマンドを実行します。 2 つ目のコマンドにある `{Your device connection string}` プレースホルダーの値を、先ほど「[IoT ハブに新しいデバイスを登録する](#register-a-new-device-in-the-iot-hub)」でコピーしたデバイス接続文字列に置き換えます。

    ```cmd/sh
    dotnet restore
    dotnet run --p "{Your device connection string}"
    ```
    
    アップロードが完了した後のデバイス アプリからの出力は次の通りです。
    
    ```cmd/sh
      Uploading file TestPayload.txt
      Getting SAS URI from IoT Hub to use when uploading the file...
      Successfully got SAS URI (https://contosostorage.blob.core.windows.net/contosocontainer/MyDevice%2FTestPayload.txt?sv=2018-03-28&sr=b&sig=x0G1Baf%2BAjR%2BTg3nW34zDNKs07p6dLzkxvZ3ZSmjIhw%3D&se=2021-05-04T16%3A40%3A52Z&sp=rw) from IoT Hub
      Uploading file TestPayload.txt using the Azure Storage SDK and the retrieved SAS URI for authentication
      Successfully uploaded the file to Azure Storage
      Notified IoT Hub that the file upload succeeded and that the SAS URI can be freed.
      Time to upload file: 00:00:01.5077954.
      Done.
    ```
    
1. サービス アプリに、ファイル アップロードの通知を受信したと表示されていることに注意してください。

    ```cmd/sh
    Receive file upload notifications
    
    
    Receiving file upload notification from service
    Press Enter to exit
    
    Received file upload notification: myDeviceId/TestPayload.txt
    ```

## <a name="verify-the-file-upload"></a>ファイルのアップロードを確認する

ポータルを使用して、構成したストレージ コンテナーにアップロードされたファイルを表示できます。

1. Azure portal のストレージ アカウントに移動します。
1. ストレージ アカウントの左ペインで、 **[コンテナー]** を選択します。
1. ファイルをアップロードしたコンテナーを選択します。
1. デバイスにちなんだ名前のフォルダーを選択します。
1. ファイルをアップロードした BLOB を選択します。 この記事では、**TestPayload.txt** という名前の BLOB です。  

    :::image type="content" source="./media/iot-hub-csharp-csharp-file-upload/view-uploaded-file.png" alt-text="アップロードされたファイルを Azure portal で選択しているスクリーンショット。":::

1. 開いたページで BLOB のプロパティを表示します。 **[ダウンロード]** を選択 してファイルをダウンロードし、その内容をローカルで表示できます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、IoT Hub のファイル アップロード機能を使用して、デバイスからのファイルのアップロードを簡素化する方法を学習しました。 この機能をさらに詳しく知るには、以下の記事を参考にしてください。

* [IoT Hub を使用したファイル アップロードの概要](iot-hub-devguide-file-upload.md)

* [IoT Hub ファイルのアップロードを構成する](iot-hub-configure-file-upload.md)

* [Azure Blob Storage のドキュメント](../storage/blobs/storage-blobs-introduction.md)

* [Azure Blob Storage API リファレンス](../storage/blobs/reference.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)