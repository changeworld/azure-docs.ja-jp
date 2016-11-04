---
title: IoT Hub のデバイスからクラウドへのメッセージの処理 (.Net) | Microsoft Docs
description: このチュートリアルに従って、IoT Hub のデバイスからクラウドへのメッセージの処理に便利なパターンを学習します。
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2016
ms.author: dobett

---
# <a name="tutorial:-how-to-process-iot-hub-device-to-cloud-messages-using-.net"></a>チュートリアル: .Net を使用してIoT Hub のデバイスからクラウドへのメッセージを処理する方法
[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## <a name="introduction"></a>はじめに
Azure IoT Hub は、何百万もの IoT デバイスとアプリケーション バックエンドの間に信頼性のある保護された双方向通信を確立できる、完全に管理されたサービスです。 他のチュートリアル ([IoT Hub の使用]と [IoT Hub を使用した cloud-to-device メッセージの送信]に関するチュートリアル[lnk-c2d]) では、IoT Hub のデバイスからクラウドとクラウドからデバイスのメッセージングについて、基本的な機能の使用方法を説明しています。

このチュートリアルは、 [IoT Hub の概要] に関するチュートリアルに示されているコードを基に作成されており、デバイスからクラウドへのメッセージを処理するために使用できる 2 つのスケーラブルなパターンについて説明しています。

* [Azure BLOB Storage] のデバイスからクラウドへのメッセージの信頼性の高いストレージ。 一般的なシナリオとしては*コールド パス*分析があります。このシナリオでは、分析プロセスへの入力として使用するテレメトリ データを BLOB に格納します。 これらのプロセスは、[Azure Data Factory] や [HDInsight (Hadoop)] スタックなどのツールによって実行できます。
* *対話型* のデバイスからクラウドへのメッセージの信頼性の高い処理。 デバイスからクラウドへのメッセージは、アプリケーション バックエンドでの一連のアクションの即時トリガーである場合に対話型となります。 たとえば、デバイスは、CRM システムへのチケットの挿入をトリガーするアラーム メッセージを送信する場合があります。 これに対して、 *データ ポイント* メッセージは、分析エンジンにフィードされるだけです。 たとえば、後で分析するために保存される、デバイスの温度テレメトリはデータ ポイント メッセージです。

IoT Hub はデバイスからクラウドへのメッセージを受信するために [Event Hubs][lnk-event-hubs] と互換性のあるエンドポイントを公開することから、このチュートリアルでは [EventProcessorHost] インスタンスを使用します。 このインスタンスは次のような性質があります。

* Azure Blob Storage に *データ ポイント* メッセージを確実に保存する。
* *対話型* のデバイスからクラウドへのメッセージを Azure [Service Bus キュー] に転送して直ちに処理する。

Service Bus は、メッセージごとのチェックポイントと期間ベースの重複除去機能を提供するため、対話型メッセージの信頼性の高い処理を保証できます。

> [!NOTE]
> **EventProcessorHost** インスタンスは、対話型のメッセージを処理する方法の 1 つにすぎません。 他にも、[Azure Service Fabric][lnk-service-fabric] や [Azure Stream Analytics][lnk-stream-analytics] を使用できます。
> 
> 

このチュートリアルの最後に、次の 3 つの Windows コンソール アプリケーションを実行します。

* **SimulatedDevice**: [IoT Hub の概要]に関するチュートリアルで作成したアプリを変更したものです。デバイスからクラウドへのデータ ポイント メッセージを 1 秒ごとに送信し、デバイスからクラウドへの対話型メッセージを 10 秒ごとに送信します。 このアプリでは、IoT Hub との通信に AMQPS プロトコルを使用します。
* **ProcessDeviceToCloudMessages** : [EventProcessorHost] クラスを使用して、Event Hubs と互換性のあるエンドポイントからメッセージを取得します。 その後、確実にデータ ポイント メッセージを Azure Blob Storage に格納し、対話型メッセージを Service Bus キューに転送します。
* **ProcessD2CInteractiveMessages** : Service Bus キューから対話型メッセージをデキューします。

> [!NOTE]
> IoT Hub には、多数のデバイス プラットフォームや言語 (C、Java、JavaScript など) に対する SDK サポートがあります。 このチュートリアルのシミュレート対象デバイスを物理デバイスに置き換える方法と、IoT Hub にデバイスを接続する方法については、「[Azure IoT デベロッパー センター]」をご覧ください。
> 
> 

このチュートリアルは、 [HDInsight (Hadoop)] プロジェクトなど、Event Hubs と互換性のあるメッセージを使用する他の方法に直接適用できます。 詳細については、「Azure IoT Hub 開発者ガイド」の「 [D2C (デバイスからクラウド)]」を参照してください。

このチュートリアルを完了するには、以下が必要です。

* Microsoft Visual Studio 2015
* アクティブな Azure アカウント。 <br/>Azure サブスクリプションがない場合は、 [無料アカウント](https://azure.microsoft.com/free/) を数分で作成できます。

[Azure Storage] と [Azure Service Bus] について、ある程度の基礎知識が必要です。

## <a name="send-interactive-messages-from-a-simulated-device"></a>シミュレーション済みデバイスからの対話型メッセージの送信
このセクションでは、 [IoT Hub の概要] に関するチュートリアルで作成したシュミレーション済みデバイス アプリケーションを変更して、デバイスからクラウドへの対話型メッセージが IoT Hub に送信されるようにします。

1. Visual Studio の **SimulatedDevice** プロジェクトで、次のメソッドを **Program** クラスに追加します。
   
    ```
    private static async void SendDeviceToCloudInteractiveMessagesAsync()
    {
      while (true)
      {
        var interactiveMessageString = "Alert message!";
        var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
        interactiveMessage.Properties["messageType"] = "interactive";
        interactiveMessage.MessageId = Guid.NewGuid().ToString();
   
        await deviceClient.SendEventAsync(interactiveMessage);
        Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);
   
        Task.Delay(10000).Wait();
      }
    }
    ```
   
    このメソッドは **SimulatedDevice** プロジェクトの **SendDeviceToCloudMessagesAsync** メソッドに似ています。 唯一の違いは、**MessageId** システム プロパティと **messageType** という名前のユーザー プロパティを設定することです。
    コードにより、グローバル一意識別子 (GUID) が **MessageId** プロパティに割り当てられます。 Service Bus はこの識別子を使用して、受信したメッセージの重複除去を行うことができます。 このサンプルでは **messageType** プロパティを利用し、データ ポイント メッセージから対話式メッセージを識別します。 この情報は、メッセージ本文ではなく、メッセージ プロパティで渡されるため、イベント プロセッサでメッセージをルーティングするためにメッセージを逆シリアル化する必要はありません。
   
   > [!NOTE]
   > 対話型メッセージの重複除去に使用する **MessageId** はデバイス コードで作成することが重要です。 ネットワーク通信の切断や、その他の障害により、デバイスから同じメッセージが繰り返し再送信されることがあります。 GUID の代わりに、関連メッセージ データ フィールドのハッシュなど、セマンティック メッセージ ID を利用することもできます。
   > 
   > 
2. **Main** メソッドの `Console.ReadLine()` 行の直前に次のメソッドを追加します。
   
    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````
   
   > [!NOTE]
   > わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、MSDN の記事「 [Transient Fault Handling (一時的な障害の処理)]」で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
   > 
   > 

## <a name="process-device-to-cloud-messages"></a>デバイスからクラウドへのメッセージの処理
このセクションでは、IoT Hub からのデバイスからクラウドへのメッセージを処理する Windows コンソール アプリを作成します。 IoT Hub は、アプリケーションでデバイスからクラウドへのメッセージを読み取ることができるように、 [Event Hubs]と互換性のあるエンドポイントを公開します。 このチュートリアルでは [EventProcessorHost] クラスを使用して、コンソール アプリでこれらのメッセージを処理します。 Event Hubs からのメッセージを処理する方法の詳細については、「 [Event Hubs の使用] 」のチュートリアルを参照してください。

データ ポイント メッセージの信頼性の高いストレージを実装する場合や、対話型メッセージを転送する場合の課題は、イベント処理がその進行状況のチェックポイントを提供するメッセージ コンシューマーに依存することです。 また、高いスループットを達成するためには、Event Hubs から読み取るときに大きなバッチでチェックポイントを提供する必要があります。 このアプローチにより、エラーが発生し、前のチェックポイントに戻すとき、大量のメッセージが重複処理される可能性が生まれます。 このチュートリアルでは、 **EventProcessorHost** のチェックポイントを使用して、Azure Storage の書き込みと Service Bus の重複除去期間を同期する方法を示します。

メッセージを Azure Storage に確実に書き込むために、サンプルでは、[ブロック BLOB][Azure ブロック BLOB] の個別ブロック コミット機能を使用します。 イベント プロセッサでは、チェックポイントの実行時間になるまでメッセージがメモリに蓄積されます。 たとえば、蓄積されたメッセージ バッファーが最大ブロック サイズの 4 MB に到達したときや、Service Bus の重複除去期間が経過したときです。 その後、チェックポイントの前に、コードにより新しいブロックが BLOB にコミットされます。

イベント プロセッサでは、ブロック ID として Event Hubs のメッセージ オフセットを使用します。 これにより、イベント プロセッサは新しいブロックをストレージにコミットする前に重複除去を実行して、ブロックのコミットとチェックポイント間でクラッシュが発生する可能性を抑えることができます。

> [!NOTE]
> このチュートリアルでは 1 つのストレージ アカウントを使用して、IoT Hub から取得したすべてのメッセージを書き込みます。 ソリューションで複数の Azure Storage アカウントを使用する必要があるかどうかを判断するには、 [Azure Storage のスケーラビリティのガイドライン]をご覧ください。
> 
> 

このアプリケーションでは、対話型メッセージの処理時に、Service Bus 重複除去機能を利用して重複を回避します。 シミュレートされたデバイスでは、各対話型メッセージに一意の **MessageId**を付与します。 これらの ID により、Service Bus では、指定された重複除去期間に、同じ **MessageId** を持つメッセージが受信側に配信されないようにすることができます。 この重複除去を、Service Bus キューによって提供されるメッセージごとの完了セマンティクスと共に使用することで、対話型メッセージを信頼性の高い方法で簡単に処理できます。

重複除去期間外にメッセージが再送信されないように、このコードは **EventProcessorHost** のチェックポイント メカニズムを Service Bus キューの重複除去期間に同期します。 この同期は、重複除去期間 (このチュートリアルでは 1 時間) が経過するたびにチェックポイントを少なくとも 1 回強制実行することで実現されます。

> [!NOTE]
> このチュートリアルでは 1 つのパーティション分割された Service Bus キューを使用して、IoT Hub から取得した対話型メッセージをすべて処理します。 ソリューションの拡張要件に合わせて Service Bus キューを使用する方法の詳細については、 [Azure Service Bus] のドキュメントをご覧ください。
> 
> 

### <a name="provision-an-azure-storage-account-and-a-service-bus-queue"></a>Azure ストレージ アカウントと Service Bus キューのプロビジョニング
[EventProcessorHost] クラスを使用するには、Azure ストレージ アカウントを用意し、 **EventProcessorHost** でチェックポイント情報を記録できるようにする必要があります。 既存のストレージ アカウントを使用することも、 [Azure Storage について] のページに記載された手順に従って新しいアカウントを作成することもできます。 ストレージ アカウント接続文字列はメモしておいてください。

> [!NOTE]
> ストレージ アカウント接続文字列をコピーして貼り付けた場合、空白が含まれていないことを確認してください。
> 
> 

Service Bus キューで対話型メッセージの信頼性の高い処理を有効にする必要もあります。  [Service Bus キューの使用方法][Service Bus キュー]を使用できます。 あるいは、[Azure クラシック ポータル][lnk-classic-portal]を使用し、以下の手順に従って作成することもできます。

1. 右下隅にある **[新規]** をクリックします。 次に、**[App Services]** > **[Service Bus]** > **[キュー]** > **[カスタム作成]** の順にクリックします。 **d2ctutorial**という名前を入力し、リージョンを選択し、既存の名前空間を使用するか名前空間を新規に作成します。 次のページで、**[重複データ検出の有効化]** を選択し、**[重複データ検出の履歴時間枠]** を 1 時間に設定します。 右下隅にあるチェック マークをクリックして、キューの構成を保存します。
   
    ![Azure ポータルでキューを作成する][30]
2. Service Bus キューの一覧で、**[d2ctutorial]** をクリックし、**[構成]** をクリックします。 2 つの共有アクセス ポリシー (1 つは **Send** アクセス許可を持つ **send**、もう 1 つは **Listen** アクセス許可を持つ **isten**) を作成します。 完了したら、下部にある **[保存]** をクリックします。
   
    ![Azure ポータルでキューを構成する][31]
3. 上部にある **[ダッシュボード]** をクリックし、下部にある **[接続情報]** をクリックします。 2 つの接続文字列をメモします。
   
    ![Azure ポータルのキューのダッシュ ボード][32]

### <a name="create-the-event-processor"></a>イベント プロセッサの作成
1. 現在の Visual Studio ソリューションで、**コンソール アプリケーション** プロジェクト テンプレートを使用して Visual C# Windows プロジェクトを作成するには、**[ファイル]** > **[追加]** > **[新しいプロジェクト]** の順にクリックします。 .NET Framework のバージョンが 4.5.1 以降であることを確認します。 プロジェクトに **ProcessDeviceToCloudMessages** という名前を付けて、**[OK]** をクリックします。
   
    ![Visual Studio での新しいプロジェクト][10]
2. ソリューション エクスプローラーで、**ProcessDeviceToCloudMessages** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。 **[NuGet パッケージ マネージャー]** ダイアログ ボックスが表示されます。
3. **WindowsAzure.ServiceBus** を検索し、**[インストール]** をクリックして、使用条件に同意します。 この操作によって、[Azure Service Bus NuGet パッケージ](https://www.nuget.org/packages/WindowsAzure.ServiceBus)への参照がすべての依存関係と共にダウンロード、インストール、追加されます。
4. **Microsoft.Azure.ServiceBus.EventProcessorHost** を検索し、**[インストール]** をクリックして、使用条件に同意します。 この操作よって、[Azure Service Bus Event Hub - EventProcessorHost NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost)への参照がすべての依存関係と共にダウンロード、インストール、追加されます。
5. **[ProcessDeviceToCloudMessages]** プロジェクトを右クリックし、**[追加]** をクリックしてから **[クラス]** をクリックします。 新しいクラスに **StoreEventProcessor** という名前を付けてから、**[OK]** をクリックしてクラスを作成します。
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
        queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString);
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
   
    **EventProcessorHost** クラスは、このクラスを呼び出し、IoT Hub から受信したデバイスとクラウド間のメッセージを処理します。 このクラスのコードは、信頼できる方法で BLOB コンテナーにメッセージを保存し、対話メッセージを Service Bus キューに転送するためのロジックを実装します。
   
    **OpenAsync** メソッドは、このイベント プロセッサで読み込まれる最初のメッセージの現在のオフセットを追跡する **currentBlockInitOffset** 変数を初期化します。 プロセッサごとに 1 つのパーティションを担当することに注意してください。
   
    **ProcessEventsAsync** メソッドは IoT Hub からメッセージをバッチで受信すると、対話型メッセージを Service Bus キューに送信し、データ ポイント メッセージを **toAppend** という名前のメモリ バッファーに追加します。 メモリ バッファーが 4 MB の制限に達したか、重複除去期間 (このチュートリアルではチェックポイント後 1 時間) が経過した場合に、アプリケーションによりチェックポイントがトリガーされます。
   
    **AppendAndCheckpoint** メソッドは、まず追加するブロックの blockId を生成します。 Azure Storage ではすべてのブロック ID が同じ長さである必要があるため、オフセットには先行ゼロが埋め込まれます ( `currentBlockInitOffset.ToString("0000000000000000000000000")`)。 次に、この ID を持つブロックが既に BLOB にある場合は、メソッドはバッファーの現在のコンテンツで上書きします。
   
   > [!NOTE]
   > コードを簡略化するために、このチュートリアルでは、メッセージを格納するパーティションごとに 1 つの BLOB ファイルを使用します。 実際のソリューションではファイル ローリングを実装します。その場合、一定時間後、または特定のサイズに達したときに、追加ファイルを作成します。 Azure ブロック BLOB が格納できるデータは最大 195 GB であることに注意してください。
   > 
   > 
8. **Program** クラスには、先頭に次の **using** ステートメントを追加します。
   
    ```
    using Microsoft.ServiceBus.Messaging;
    ```
9. **Program** クラスの **Main** メソッドを次のように変更します。 **{iot hub connection string}** は、「[IoT Hub の概要]」の **iothubowner** 接続文字列に置き換えます。 "storage connection string" は、このセクションの冒頭でメモした接続文字列に置き換えます。 Service Bus 接続文字列は、このセクションの冒頭でメモした **d2ctutorial** という名前のキューの **Send** アクセス許可に置き換えます。
   
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
   
   > [!NOTE]
   > わかりやすくするため、このチュートリアルでは [EventProcessorHost] クラスの単一のインスタンスを使用します。 詳細については、「 [Event Hubs のプログラミング ガイド]」を参照してください。
   > 
   > 

## <a name="receive-interactive-messages"></a>対話型メッセージの受信
このセクションでは、Service Bus キューから対話型メッセージを受信する Windows コンソール アプリケーションを作成します。 Service Bus を使用してソリューションを構築する方法の詳細については、 [Service Bus を使用する多層アプリケーションの構築][]に関する記事を参照してください。

1. 現在の Visual Studio ソリューションで、 **コンソール アプリケーション** プロジェクト テンプレートを使用して、Visual C# Windows プロジェクトを作成します。 プロジェクトに **ProcessD2CInteractiveMessages**という名前を付けます。
2. ソリューション エクスプローラーで、**ProcessD2CInteractiveMessages** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。 **[NuGet パッケージ マネージャー]** ウィンドウが表示されます。
3. **WindowsAzure.ServiceBus** を検索し、**[インストール]** をクリックして、使用条件に同意します。 この操作で、 [Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus)への参照がすべての依存関係と共にダウンロード、インストール、追加されます。
4. **Program.cs** ファイルの先頭に、次の **using** ステートメントを追加します。
   
    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```
5. 最後に、 **Main** メソッドに次の行を追加します。 接続文字列を **d2ctutorial** という名前のキューの **Listen** アクセス許可に置き換えます。
   
    ```
    Console.WriteLine("Process D2C Interactive Messages app\n");
   
    string connectionString = "{service bus listen connection string}";
    QueueClient Client = QueueClient.CreateFromConnectionString(connectionString);
   
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

## <a name="run-the-applications"></a>アプリケーションの実行
これで、アプリケーションを実行する準備が整いました。

1. Visual Studio のソリューション エクスプローラーでソリューションを右クリックし、 **[スタートアップ プロジェクトの設定]**を選択します。 **[マルチ スタートアップ プロジェクト]** を選択し、**ProcessDeviceToCloudMessages**、**SimulatedDevice**、**ProcessD2CInteractiveMessages** の各プロジェクトのアクションとして **[開始]** を選択します。
2. **F5** キーを押して 3 つのコンソール アプリケーションを起動します。 **ProcessD2CInteractiveMessages** アプリケーションは、**SimulatedDevice** アプリケーションから送信されたすべての対話型メッセージを処理します。
   
   ![3 つのコンソール アプリケーション][50]

> [!NOTE]
> BLOB ファイルの更新内容を表示するために、**StoreEventProcessor** クラスの **MAX_BLOCK_SIZE** 定数を、**1024** などのより小さい値に設定することが必要になる場合があります。 シミュレーション対象デバイスによって送信されたデータのブロック サイズの制限に達するまでに時間がかかるため、この変更が役立ちます。 ブロック サイズを小さくすることで、作成および更新される BLOB を表示するために長時間待機する必要がなくなります。 ただし、より大きなブロック サイズを使用すると、アプリケーションの拡張性が向上します。
> 
> 

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、 [EventProcessorHost] クラスを使用して、データ ポイント メッセージと、デバイスからクラウドへの対話型メッセージを確実に処理する方法について学習しました。

[IoT Hub でクラウドからデバイスへのメッセージを送信する方法][lnk-c2d]では、バックエンドからデバイスにメッセージを送信する方法を説明しています。

IoT Hub を使用する完全なエンド ツー エンド ソリューションの例については、[Azure IoT Suite][lnk-suite] に関するドキュメントをご覧ください。

IoT Hub を使用したソリューションの開発に関する詳細については、 [IoT Hub 開発者ガイド]をご覧ください。

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[10]: ./media/iot-hub-csharp-csharp-process-d2c/create-identity-csharp1.png

[30]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue2.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue3.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue4.png

<!-- Links -->

[Azure BLOB Storage]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus キュー]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[Azure IoT Hub 開発者ガイド - デバイスからクラウド]: iot-hub-devguide-messaging.md

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT Hub 開発者ガイド]: iot-hub-devguide.md
[IoT Hub の概要]: iot-hub-csharp-csharp-getstarted.md
[Azure IoT デベロッパー センター]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Transient Fault Handling (一時的な障害の処理) (一時的な障害の処理)]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Azure Storage について]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Event Hubs の使用]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[Azure Storage のスケーラビリティのガイドライン]: ../storage/storage-scalability-targets.md
[Azure ブロック BLOB]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Event Hubs のプログラミング ガイド]: ../event-hubs/event-hubs-programming-guide.md
[Transient Fault Handling (一時的な障害の処理) (一時的な障害の処理)]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Service Bus を使用する多層アプリケーションのビルド]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-csharp-csharp-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/



<!--HONumber=Oct16_HO2-->


