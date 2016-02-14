## デバイスからクラウドへのメッセージの処理

このセクションでは、IoT Hub からのデバイスからクラウドへのメッセージを処理する Windows コンソール アプリを作成します。IoT Hub は、アプリケーションがデバイスからクラウドへのメッセージを読み取るための、[Event Hubs] と互換性のあるエンドポイントを公開します。このチュートリアルでは [EventProcessorHost] クラスを使用して、コンソール アプリでこれらのメッセージを処理します。Event Hubs からのメッセージを処理する方法の詳細については、「[Event Hubs の使用]」チュートリアルを参照してください。

データ ポイント メッセージの信頼性の高いストレージを実装する場合や、対話型メッセージを転送する場合の主な課題は、Event Hubs のイベント処理がその進行状況をチェックポイントとするメッセージ コンシューマーに依存することです。また、高いスループットを達成するためには、Event Hubs から読み取るとき、大きなバッチでチェックポイントを作成する必要があります。それにより、エラーが発生し、前のチェックポイントに戻すとき、大量のメッセージが重複処理される可能性が生まれます。このチュートリアルでは、**EventProcessorHost** のチェックポイントを使用して、Azure Storage の書き込みと Service Bus の重複除去期間を同期する方法を示します。

メッセージを Azure Storage に確実に書き込むために、サンプルでは、[ブロック BLOB][Azure Block Blobs] の個別ブロック コミット機能を使用します。イベント プロセッサでは、チェックポイントの実行時間になるまで (つまり、蓄積されたメッセージ バッファーが最大ブロック サイズの 4 Mb に到達するか、Service Bus の重複除去期間が経過するまで)、メッセージがメモリに蓄積されます。その後、チェックポイント処理の前に、コードにより新しいブロックが BLOB にコミットされます。

イベント プロセッサでは、ブロック ID として Event Hubs のメッセージ オフセットを使用します。これにより、ブロックのコミットとチェックポイント間でクラッシュが発生する可能性がないかどうかを注意して、新しいブロックをストレージにコミットする前に、重複除去を確認できます。

> [AZURE.NOTE] このチュートリアルでは 1 つのストレージ アカウントを使用して、IoT Hub から取得したすべてのメッセージを書き込みます。ソリューションで複数の Azure ストレージ アカウントを使用する必要があるかどうかを判断する場合は、[Azure Storage のスケーラビリティのガイドライン]を参照してください。

このアプリケーションでは、対話メッセージの処理時に重複を回避するために、Service Bus 重複除去機能が利用されます。シミュレーションされたデバイスでは、各対話型メッセージに一意の **MessageId** がスタンプされます。Service Bus は、指定された重複除去期間で、同じ **MessageId** を持つ 2 つのメッセージが受信側に配信されないようにできます。この重複除去を、Service Bus キューによって提供されるメッセージごとの完了セマンティクスと共に使用することで、対話型メッセージを信頼性の高い方法で簡単に処理できます。

重複除去期間外にメッセージが再送信されないように、このコードは **EventProcessorHost** のチェックポイント処理メカニズムを Service Bus キューの重複除去期間に同期します。これは、チェックポイントを重複除去期間 (このチュートリアルでは 1 時間) が経過するたびに少なくとも 1 回強制実行することで実現されます。

> [AZURE.NOTE] このチュートリアルでは 1 つのパーティション分割された Service Bus キューを使用して、IoT Hub から取得した対話型メッセージをすべて処理します。ソリューションの拡張要件に合わせて Service Bus キューを使用する方法の詳細については、[Service Bus のドキュメント]を参照してください。

### Azure ストレージ アカウントと Service Bus キューのプロビジョニング
[EventProcessorHost] クラスを使用するには、Azure Storage アカウントを用意し、**EventProcessorHost** でチェックポイント情報を記録できるようにする必要があります。既存のストレージ アカウントを使用することも、「[Azure Storage について]」の手順に従って新しいものを作成することもできます。ストレージ アカウント接続文字列はメモしておいてください。

Service Bus キューで対話型メッセージの信頼性の高い処理を有効にする必要もあります。「[Service Bus キューの使用方法][Service Bus Queue]」で説明されているように、キューは、1 時間の重複除去期間でプログラムを使用して作成できます。また、[Azure ポータル]を使用し、以下の手順に従って作成することもできます。

1. 左下隅で **[新規]** をクリックし、それから **[App Services]**、**[Service Bus]**、**[キュー]**、**[カスタム作成]** の順にクリックし、「**d2ctutorial**」という名前を入力し、地域を選択し、既存の名前空間を使用するか、新しい名前空間を作成します。それから、次のページで **[重複データ検出の有効化]** を選択し、**[重複データ検出の履歴時間枠]** を 1 時間に設定します。その後、チェック マークをクリックしてキュー構成を保存します。

    ![][30]

2. Service Bus キューの一覧で、[**d2ctutorial**] をクリックし、[**構成**] をクリックします。2 つの共有アクセス ポリシー (1 つは **Send **アクセス許可を持つ **send**、もう 1 つは **Listen ** アクセス許可を持つ **listen**) を作成します。完了したら、下部の **[保存]** をクリックします。

    ![][31]

3. 上部の **[ダッシュボード]** をクリックしてから、下部の **[接続情報]** をクリックし、2 つの接続文字列をメモします。

    ![][32]

### イベント プロセッサの作成

1. 現在の Visual Studio ソリューションで、**[ファイル]**、**[追加]**、**[新しいプロジェクト]** の順にクリックし、**コンソール アプリケーション** プロジェクト テンプレートを使用して新しい Visual C# Windows プロジェクトを作成します。プロジェクトに **ProcessDeviceToCloudMessages** という名前を付けます。

    ![][10]

2. ソリューション エクスプローラーで、**[ProcessDeviceToCloudMessages]** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。**[NuGet パッケージ マネージャー]** ダイアログ ボックスが表示されます。

3. **WindowsAzure.ServiceBus** を検索し、**[インストール]** をクリックし、使用条件に同意します。これによって、[Azure Service Bus NuGet パッケージ](https://www.nuget.org/packages/WindowsAzure.ServiceBus)への参照がすべての依存関係と共にダウンロード、インストール、追加されます。

4. **Microsoft Azure Service Bus Event Hub - EventProcessorHost** を検索し、**[インストール]** をクリックして、使用条件に同意します。これによって、[Azure Service Bus Event Hub - EventProcessorHost NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost)への参照がすべての依存関係と共にダウンロード、インストール、追加されます。

5. **[ProcessDeviceToCloudMessages]** プロジェクトを右クリックし、**[追加]** をクリックしてから **[クラス]** をクリックします。新しいクラスに **SimpleEventProcessor** という名前を付けてから、**[OK]** をクリックしてクラスを作成します。

6. StoreEventProcessor.cs ファイルの先頭に次のステートメントを追加します。

    ```
    using System.IO;
    using System.Diagnostics;
    using System.Security.Cryptography;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

7. 次に、クラスの本文に次のコードを置き換えます。

    ```
    class StoreEventProcessor : IEventProcessor
    {
      private const int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
      public static string StorageConnectionString;
      public static string ServiceBusConnectionString;

      private CloudBlobClient blobClient;
      private CloudBlobContainer blobContainer;
      private QueueClient queueClient;

      private long currentBlockInitOffset;
      private MemoryStream toAppend = new MemoryStream(MAX_BLOCK_SIZE);

      private Stopwatch stopwatch;
      private TimeSpan MAX_CHECKPOINT_TIME = TimeSpan.FromHours(1);

      public StoreEventProcessor()
      {
        var storageAccount = CloudStorageAccount.Parse(StorageConnectionString);
        blobClient = storageAccount.CreateCloudBlobClient();
        blobContainer = blobClient.GetContainerReference("d2ctutorial");
        blobContainer.CreateIfNotExists();
        queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString, "d2ctutorial");
      }

      Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
      {
        Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
        return Task.FromResult<object>(null);
      }

      Task IEventProcessor.OpenAsync(PartitionContext context)
      {
        Console.WriteLine("StoreEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);

        if (!long.TryParse(context.Lease.Offset, out currentBlockInitOffset))
        {
          currentBlockInitOffset = 0;
        }
        stopwatch = new Stopwatch();
        stopwatch.Start();

        return Task.FromResult<object>(null);
      }

      async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
      {
        foreach (EventData eventData in messages)
        {
          byte[] data = eventData.GetBytes();

          if (eventData.Properties.ContainsKey("messageType") && (string) eventData.Properties["messageType"] == "interactive")
          {
            var messageId = (string) eventData.SystemProperties["message-id"];

            var queueMessage = new BrokeredMessage(new MemoryStream(data));
            queueMessage.MessageId = messageId;
            queueMessage.Properties["messageType"] = "interactive";
            await queueClient.SendAsync(queueMessage);

            WriteHighlightedMessage(string.Format("Received interactive message: {0}", messageId));
            continue;
          }

          if (toAppend.Length + data.Length > MAX_BLOCK_SIZE || stopwatch.Elapsed > MAX_CHECKPOINT_TIME)
          {
            await AppendAndCheckpoint(context);
          }
          await toAppend.WriteAsync(data, 0, data.Length);

          Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
            context.Lease.PartitionId, Encoding.UTF8.GetString(data)));
        }
      }

      private async Task AppendAndCheckpoint(PartitionContext context)
      {
        var blockIdString = String.Format("startSeq:{0}", currentBlockInitOffset.ToString("0000000000000000000000000"));
        var blockId = Convert.ToBase64String(ASCIIEncoding.ASCII.GetBytes(blockIdString));
        toAppend.Seek(0, SeekOrigin.Begin);
        byte[] md5 = MD5.Create().ComputeHash(toAppend);
        toAppend.Seek(0, SeekOrigin.Begin);

        var blobName = String.Format("iothubd2c_{0}", context.Lease.PartitionId);
        var currentBlob = blobContainer.GetBlockBlobReference(blobName);

        if (await currentBlob.ExistsAsync())
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var blockList = await currentBlob.DownloadBlockListAsync();
          var newBlockList = new List<string>(blockList.Select(b => b.Name));

          if (newBlockList.Count() > 0 && newBlockList.Last() != blockId)
          {
            newBlockList.Add(blockId);
            WriteHighlightedMessage(String.Format("Appending block id: {0} to blob: {1}", blockIdString, currentBlob.Name));
          }
          else
          {
            WriteHighlightedMessage(String.Format("Overwriting block id: {0}", blockIdString));
          }
          await currentBlob.PutBlockListAsync(newBlockList);
        }
        else
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var newBlockList = new List<string>();
          newBlockList.Add(blockId);
          await currentBlob.PutBlockListAsync(newBlockList);

          WriteHighlightedMessage(String.Format("Created new blob", currentBlob.Name));
        }

        toAppend.Dispose();
        toAppend = new MemoryStream(MAX_BLOCK_SIZE);

        // checkpoint.
        await context.CheckpointAsync();
        WriteHighlightedMessage(String.Format("Checkpointed partition: {0}", context.Lease.PartitionId));

        currentBlockInitOffset = long.Parse(context.Lease.Offset);
        stopwatch.Restart();
      }

      private void WriteHighlightedMessage(string message)
      {
        Console.ForegroundColor = ConsoleColor.Yellow;
        Console.WriteLine(message);
        Console.ResetColor();
      }
    }
    ```

    **EventProcessorHost** クラスは、このクラスを呼び出し、IoT Hub から受信したデバイスとクラウド間のメッセージを処理します。このクラスのコードは、信頼できる方法で BLOB コンテナーにメッセージを保存し、対話メッセージを Service Bus キューに転送するためのロジックを実装します。**OpenAsync** メソッドは、このイベント プロセッサで読み込まれる最初のメッセージの現在のオフセットを追跡する **currentBlockInitOffset** 変数を初期化します。プロセッサごとに 1 つのパーティションを担当することに注意してください。
    
    **ProcessEventsAsync** メソッドは IoT Hub からメッセージのメッセージを一括受信し、次のように処理します。対話メッセージを Service Bus キューに送信し、「**toAppend**」という名前のメモリ バッファーにデータ ポイント メッセージを追加します。メモリ バッファーが 4Mb のブロック制限に達したか、前回のチェックポイント以降に Service Bus の重複除去期間 (このチュートリアルでは 1 時間) が経過した場合に、チェックポイントがトリガーされます。

    メソッド **AppendAndCheckpoint** は最初に、追加されるブロックの blockId を生成します。Azure Storage ではすべてのブロック ID が同じ長さである必要があるため、オフセットには先行ゼロが埋め込まれます (`currentBlockInitOffset.ToString("0000000000000000000000000")`)。次に、この ID を持つブロックが既に BLOB にある場合は、メソッドはバッファーの現在のコンテンツで上書きします。

    > [AZURE.NOTE] コードを簡略化するために、このチュートリアルでは、メッセージを格納するパーティションごとに 1 つの BLOB ファイルを使用します。実際のソリューションではファイル ローリングを実装します。その場合、特定のサイズ (Azure ブロック BLOB は最大 195 Gb にすることができます) に達したときか、一定時間の後に、追加ファイルを作成します。

8. **Program** クラスには、上部に次の **using** ステートメントを追加します。

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

9. 以下のように **Main** メソッドを **Program** クラスに変更します。その場合、IoT Hub の **iothubowner** 接続文字列 (「[IoT Hub の概要]」チュートリアルを参照)、ストレージ接続文字列、および Service Bus の接続文字列を **d2ctutorial** という名前のキューの **Send** アクセス許可に置き換えます。

    ```
    static void Main(string[] args)
    {
      string iotHubConnectionString = "{iot hub connection string}";
      string iotHubD2cEndpoint = "messages/events";
      StoreEventProcessor.StorageConnectionString = "{storage connection string}";
      StoreEventProcessor.ServiceBusConnectionString = "{service bus send connection string}";

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, iotHubD2cEndpoint, EventHubConsumerGroup.DefaultGroupName, iotHubConnectionString, StoreEventProcessor.StorageConnectionString, "messages-events");
      Console.WriteLine("Registering EventProcessor...");
      eventProcessorHost.RegisterEventProcessorAsync<StoreEventProcessor>().Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
    ```
    
    > [AZURE.NOTE] わかりやすくするため、このチュートリアルでは [EventProcessorHost] クラスの単一のインスタンスを使用します。詳細については、[Event Hubs プログラミング ガイド]を参照してください。

## 対話型メッセージの受信
このセクションでは、Service Bus キューから対話型メッセージを受信する Windows コンソール アプリケーションを作成します。Service Bus を使用してソリューションを構築する方法の詳細については、[Service Bus を使用する多層アプリケーションのビルド][]に関する記述を参照してください。

1. 現在の Visual Studio ソリューションで、**コンソール アプリケーション** プロジェクト テンプレートを使用して、新しい Visual C# Windows プロジェクトを作成します。プロジェクトに **ProcessD2CInteractiveMessages** という名前を付けます。

2. ソリューション エクスプローラーで、**[ProcessD2CInteractiveMessages]** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。**[NuGet パッケージ マネージャー]** ウィンドウが表示されます。

3. **WindowsAzure.Service Bus** を検索し、**[インストール]** をクリックし、使用条件に同意します。これによって、[Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) への参照がすべての依存関係と共にダウンロード、インストール、追加されます。

4. **Program.cs** ファイルの先頭に次の **using** ステートメントを追加します。

    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. 最後に、次の行を **Main** メソッドに追加します。その場合、接続文字列を **d2ctutorial** という名前のキューの **Listen** アクセス許可に置き換えます。

    ```
    Console.WriteLine("Process D2C Interactive Messages app\n");

    string connectionString = "{service bus listen connection string}";
    QueueClient Client = QueueClient.CreateFromConnectionString(connectionString, "d2ctutorial");

    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    Client.OnMessage((message) =>
    {
      try
      {
        var bodyStream = message.GetBody<Stream>();
        bodyStream.Position = 0;
        var bodyAsString = new StreamReader(bodyStream, Encoding.ASCII).ReadToEnd();

        Console.WriteLine("Received message: {0} messageId: {1}", bodyAsString, message.MessageId);

        message.Complete();
      }
      catch (Exception)
      {
        message.Abandon();
      }
    }, options);

    Console.WriteLine("Receiving interactive messages from SB queue...");
    Console.WriteLine("Press any key to exit.");
    Console.ReadLine();
    ```

<!-- Links -->
[Azure Storage について]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Event Hubs の使用]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: iot-hub-devguide.md#identityregistry
[Azure Storage のスケーラビリティのガイドライン]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Event Hubs プログラミング ガイド]: ../event-hubs/event-hubs-programming-guide.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure ポータル]: https://manage.windowsazure.com/
[Service Bus Queue]: ../service-bus/service-bus-dotnet-how-to-use-queues.md
[Service Bus を使用する多層アプリケーションのビルド]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
[IoT Hub の概要]: iot-hub-csharp-csharp-getstarted.md
[Service Bus のドキュメント]: https://azure.microsoft.com/documentation/services/service-bus/

<!-- Images -->
[10]: ./media/iot-hub-process-d2c-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-process-d2c-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage3.png

[30]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue2.png
[31]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue3.png
[32]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue4.png

<!---HONumber=AcomDC_0204_2016-->