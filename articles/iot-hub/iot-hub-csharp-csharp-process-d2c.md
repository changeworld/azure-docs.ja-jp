---
title: "Azure IoT Hub (.NET) を使用したメッセージのルーティング | Microsoft Docs"
description: "他のバックエンド サービスにメッセージをディスパッチするルーティング規則とカスタム エンドポイントを使用して、Azure IoT Hub デバイスからクラウドへのメッセージを処理する方法について説明します。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 5177bac9-722f-47ef-8a14-b201142ba4bc
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.openlocfilehash: 4794f81787fd575c34fa8a2ef66431d85dcf4a5d
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2018
---
# <a name="routing-messages-with-iot-hub-net"></a>IoT Hub (.NET) でのメッセージのルーティング

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

このチュートリアルは、チュートリアル「[IoT Hub の概要]」に基づいています。 このチュートリアルでは以下を行います。

* ルーティング規則を使用して、簡単な構成ベースの方法でデバイスからクラウドにメッセージをディスパッチする方法について説明します。
* さらに処理するために早急な対応を必要とする対話型メッセージを、ソリューションのバックエンドから分離する方法を示します。 たとえば、デバイスは、CRM システムへのチケットの挿入をトリガーするアラーム メッセージを送信する場合があります。 これに対して、温度テレメトリなどのデータポイント メッセージは、分析エンジンにフィードされます。

このチュートリアルの最後に、次の 3 つの .NET コンソール アプリを実行します。

* **SimulatedDevice** は [IoT Hub の概要]に関するチュートリアルで作成したアプリを変更したもので、デバイスからクラウドへのデータ ポイント メッセージを 1 秒ごとに送信し、デバイスからクラウドへの対話型メッセージを 10 秒ごとに送信します。
* **ReadDeviceToCloudMessages**: デバイス アプリから送信された重大ではないテレメトリを表示します。
* **ReadCriticalQueue** は Service Bus キューからデバイス アプリによって送信された重大なメッセージをデキューします。 このキューは、IoT hub に接続されています。

> [!NOTE]
> IoT Hub には、多数のデバイス プラットフォームや言語 (C、Java、JavaScript など) に対する SDK サポートがあります。 このチュートリアルのシミュレート対象デバイスを物理デバイスに置き換える方法については、「[Azure IoT デベロッパー センター]」をご覧ください。

このチュートリアルを完了するには、以下が必要です。

* Visual Studio 2015 または Visual Studio 2017。
* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 <br/>アカウントがない場合は、 [無料アカウント](https://azure.microsoft.com/free/) を数分で作成することができます。

[Azure Storage] と [Azure Service Bus] の記事も読むことをお勧めします。

## <a name="send-interactive-messages"></a>対話型メッセージの送信

[IoT Hub の概要]に関するチュートリアルで作成したデバイス アプリを変更して、対話型メッセージをランダムに送信するようにします。

Visual Studio で、**SimulatedDevice** プロジェクトの `SendDeviceToCloudMessagesAsync` メソッドを次のコードに置き換えます。

```csharp
private static async void SendDeviceToCloudMessagesAsync()
{
    double minTemperature = 20;
    double minHumidity = 60;
    Random rand = new Random();

    while (true)
    {
        double currentTemperature = minTemperature + rand.NextDouble() * 15;
        double currentHumidity = minHumidity + rand.NextDouble() * 20;

        var telemetryDataPoint = new
        {
            deviceId = "myFirstDevice",
            temperature = currentTemperature,
            humidity = currentHumidity
        };
        var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
        string levelValue;

        if (rand.NextDouble() > 0.7)
        {
            if (rand.NextDouble() > 0.5)
            {
                messageString = "This is a critical message";
                levelValue = "critical";
            }
            else 
            {
                messageString = "This is a storage message";
                levelValue = "storage";
            }
        }
        else
        {
            levelValue = "normal";
        }
        
        var message = new Message(Encoding.ASCII.GetBytes(messageString));
        message.Properties.Add("level", levelValue);
        
        await deviceClient.SendEventAsync(message);
        Console.WriteLine("{0} > Sent message: {1}", DateTime.Now, messageString);

        await Task.Delay(1000);
    }
}
```

このメソッドは、デバイスによって送信されたメッセージに `"level": "critical"` と `"level": "storage"` のプロパティをランダムに追加して、アプリケーションのバックエンドによる早急な対応を必要とするメッセージまたは永続的に保存する必要があるメッセージをシミュレートします。 アプリケーションは、メッセージ本文に基づいたメッセージのルーティングをサポートしています。

> [!NOTE]
> メッセージのプロパティを使用したメッセージのルーティングは、ここで示すホット パスの例に加え、コールド パス処理などのさまざまなシナリオで使用できます。

> [!NOTE]
> MSDN の記事「[Transient Fault Handling (一時的な障害の処理)]」で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することを強くお勧めします。

## <a name="route-messages-to-a-queue-in-your-iot-hub"></a>メッセージを IoT ハブのキューにルーティングする

このセクションでは、次の作業を行います。

* Service Bus キューを作成する。
* 作成したキューを IoT Hub に接続する。
* メッセージのプロパティの有無に基づいてメッセージをキューに送信するように IoT Hub を構成する。

Service Bus キューのメッセージを処理する方法の詳細については、「[Service Bus キューの使用][Service Bus queue]」を参照してください。

1. 「[Service Bus キューの使用][Service Bus queue]」の説明に従って、Service Bus キューを作成します。 キューは、IoT Hub と同じサブスクリプションとリージョン内にある必要があります。 名前空間とキューの名前をメモしておきます。

    > [!NOTE]
    > IoT Hub エンドポイントとして使用される Service Bus のキューとトピックでは、**セッション**または**重複データ検出**が有効になっていてはいけません。 これらのオプションのいずれかが有効になっている場合、エンドポイントは Azure Portal に**到達不能**として表示されます。

2. Azure Portal で、IoT Hub を開き、**[エンドポイント]** をクリックします。
    
    ![IoT Hub 内のエンドポイント][30]

3. **[エンドポイント]** ブレードで、上部にある **[追加]** をクリックして、IoT Hub にキューを追加します。 エンドポイントに「**CriticalQueue**」という名前を付け、ドロップダウン リストを使用して、**Service Bus キュー**、キューを配置する Service Bus 名前空間、およびキューの名前を選択します。 完了したら、下部にある **[保存]** をクリックします。
    
    ![エンドポイントの追加][31]
    
4. 次に、IoT Hub で **[ルート]** をクリックします。 ブレードの上部にある **[追加]** をクリックして、先ほど追加したキューにメッセージをルーティングするルーティング規則を作成します。 データのソースとして **[DeviceTelemetry]** を選択します。 条件として「`level="critical"`」を入力し、カスタム エンドポイントとして先ほど追加したキューをルーティング規則エンドポイントとして選択します。 完了したら、下部にある **[保存]** をクリックします。
    
    ![ルートの追加][32]
    
    フォールバック ルートが **[オン]** に設定されていることを確認します。 この値は IoT Hub の既定の構成です。
    
    ![フォールバック ルート][33]

## <a name="read-from-the-queue-endpoint"></a>キュー エンドポイントからの読み取り

このセクションでは、キュー エンドポイントからメッセージを読み取ります。

1. Visual Studio で、**[Console App (.NET Framework)]** プロジェクト テンプレートを使用し、Visual C# Windows クラシック デスクトップ プロジェクトを現在のソリューションに追加します。 プロジェクトに **ReadCriticalQueue** という名前を付けます。

2. ソリューション エクスプローラーで **ReadCriticalQueue** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。 **[NuGet パッケージ マネージャー]** ウィンドウが表示されます。

3. **WindowsAzure.ServiceBus** を検索し、**[インストール]** をクリックして、使用条件に同意します。 この操作によって、Azure Service Bus への参照がすべての依存関係と共にダウンロード、インストール、追加されます。

4. **Program.cs** ファイルの先頭に、次の **using** ステートメントを追加します。
   
    ```csharp
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. 最後に、 **Main** メソッドに次の行を追加します。 接続文字列を、キューの **Listen** アクセス許可に置き換えます。
   
    ```csharp
    Console.WriteLine("Receive critical messages. Ctrl-C to exit.\n");
    var connectionString = "{service bus listen string}";
    var queueName = "{queue name}";
    
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);

    client.OnMessage(message =>
        {
            Stream stream = message.GetBody<Stream>();
            StreamReader reader = new StreamReader(stream, Encoding.ASCII);
            string s = reader.ReadToEnd();
            Console.WriteLine(String.Format("Message body: {0}", s));
        });
        
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>アプリケーションの実行
これで、アプリケーションを実行する準備が整いました。

1. Visual Studio のソリューション エクスプローラーでソリューションを右クリックし、 **[スタートアップ プロジェクトの設定]**を選択します。 **[マルチ スタートアップ プロジェクト]** を選択し、**ReadDeviceToCloudMessages** プロジェクト、**SimulatedDevice** プロジェクト、および **ReadCriticalQueue** プロジェクトの [アクション] として **[開始]** を選択します。
2. **F5** キーを押して 3 つのコンソール アプリを起動します。 **ReadDeviceToCloudMessages** アプリには、**SimulatedDevice** アプリケーションから送信された重大でないメッセージのみが、**ReadCriticalQueue** アプリには重大なメッセージのみが含まれます。
   
   ![3 つのコンソール アプリ][50]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(省略可能) IoT ハブにストレージ コンテナーを追加してメッセージをルーティングする

このセクションでは、ストレージ アカウントを作成して IoT Hub に接続し、メッセージのプロパティの有無に基づいてこのアカウントにメッセージを送信するように IoT Hub を構成します。 ストレージを管理する方法の詳細については、[Azure Storage の概要][Azure Storage]に関する記事をご覧ください。

 > [!NOTE]
   > **エンドポイント**が 1 つに制限されていない場合は、**StorageContainer** と **CriticalQueue** を設定して両方を同時に実行します。

1. [Azure Storage のドキュメント][lnk-storage] の説明に従ってストレージ アカウントを作成します。 アカウント名をメモします。

2. Azure Portal で、IoT Hub を開き、**[エンドポイント]** をクリックします。

3. **[エンドポイント]** ブレードで **CriticalQueue** エンドポイントを選択し、**[削除]** をクリックします。 **[はい]** をクリックし、**[追加]** をクリックします。 エンドポイントに「**StorageContainer**」と名前を付け、ドロップダウンを使用して **[Azure Storage コンテナー]** を選択し、**[ストレージ アカウント]** と **[ストレージ コンテナー]** を作成します。  名前をメモします。  完了したら、下部にある **[OK]** をクリックします。 

 > [!NOTE]
   > **エンドポイント**が 1 つに制限されていない場合は、**CriticalQueue** を削除する必要はありません。

4. IoT Hub で **[ルート]** をクリックします。 ブレードの上部にある **[追加]** をクリックして、先ほど追加したキューにメッセージをルーティングするルーティング規則を作成します。 データのソースとして **[デバイス メッセージ]** を選択します。 条件として「`level="storage"`」を入力し、ルーティング規則エンドポイントとしてカスタム エンドポイントの **StorageContainer** を選択します。 下部にある **[保存]** をクリックします。  

    フォールバック ルートが **[オン]** に設定されていることを確認します。 この設定は IoT Hub の既定の構成です。

1. 以前のアプリケーションがまだ実行されていることを確認します。 

1. Azure Portal でストレージ アカウントに移動し、**[Blob service]** の **[BLOB の参照]** をクリックします。お使いのコンテナーを選択して移動し、JSON ファイルをクリックします。次に **[ダウンロード]** をクリックしてデータを表示します。

## <a name="next-steps"></a>次の手順
このチュートリアルでは、IoT Hub のメッセージ ルーティング機能を使用して、デバイスからクラウドへのメッセージを確実にディスパッチする方法について説明しました。

[IoT Hub でクラウドからデバイスへのメッセージを送信する方法][lnk-c2d]に関するページでは、ソリューション バックエンドからデバイスにメッセージを送信する方法を説明しています。

IoT Hub を使用する完全なエンド ツー エンド ソリューションの例については、[Azure IoT Suite][lnk-suite] に関するドキュメントを参照してください。

IoT Hub を使用したソリューションの開発に関する詳細については、[IoT Hub 開発者ガイド]をご覧ください。

IoT Hub でのメッセージのルーティングの詳細については、[IoT Hub でのメッセージの送受信][lnk-devguide-messaging]に関するページを参照してください。

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[30]: ./media/iot-hub-csharp-csharp-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/route-creation.png
[33]: ./media/iot-hub-csharp-csharp-process-d2c/fallback-route.png

<!-- Links -->
[Service Bus queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/
[IoT Hub 開発者ガイド]: iot-hub-devguide.md
[IoT Hub の概要]: iot-hub-csharp-csharp-getstarted.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Azure IoT デベロッパー センター]: https://azure.microsoft.com/develop/iot
[Transient Fault Handling (一時的な障害の処理)]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-c2d]: iot-hub-csharp-csharp-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
