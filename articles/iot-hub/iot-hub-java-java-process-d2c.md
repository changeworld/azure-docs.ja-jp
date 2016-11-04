---
title: IoT Hub のデバイスからクラウドへのメッセージの処理 (Java) | Microsoft Docs
description: この Java のチュートリアルでは、IoT Hub のデバイスからクラウドへのメッセージの処理に便利なパターンを学習します。
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/01/2016
ms.author: dobett

---
# <a name="tutorial:-how-to-process-iot-hub-device-to-cloud-messages-using-java"></a>チュートリアル: Java を使用して IoT Hub のデバイスからクラウドへのメッセージを処理する方法
[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## <a name="introduction"></a>はじめに
Azure IoT Hub は、何百万もの IoT デバイスとアプリケーション バックエンドの間に信頼性のある保護された双方向通信を確立できる、完全に管理されたサービスです。 他のチュートリアル ([IoT Hub の使用]と [IoT Hub を使用したクラウドからデバイスへのメッセージの送信]に関するチュートリアル[lnk-c2d]) では、IoT Hub のデバイスからクラウドとクラウドからデバイスのメッセージングについて、基本的な機能の使用方法を説明しています。

このチュートリアルは、 [IoT Hub の概要] に関するチュートリアルに示されているコードを基に作成されており、デバイスからクラウドへのメッセージを処理するために使用できる 2 つのスケーラブルなパターンについて説明しています。

* [Azure BLOB Storage] のデバイスからクラウドへのメッセージの信頼性の高いストレージ。 一般的なシナリオとしては*コールド パス*分析があります。このシナリオでは、分析プロセスへの入力として使用するテレメトリ データを BLOB に格納します。 これらのプロセスは、[Azure Data Factory] や [HDInsight (Hadoop)] スタックなどのツールによって実行できます。
* *対話型* のデバイスからクラウドへのメッセージの信頼性の高い処理。 デバイスからクラウドへのメッセージは、アプリケーション バックエンドでの一連のアクションの即時トリガーである場合に対話型となります。 たとえば、デバイスは、CRM システムへのチケットの挿入をトリガーするアラーム メッセージを送信する場合があります。 これに対して、 *データポイント* メッセージは、分析エンジンにフィードされるだけです。 たとえば、後の分析用に保存されるデバイスの温度テレメトリはデータポイント メッセージです。

IoT Hub はデバイスからクラウドへのメッセージを受信するために [Event Hubs][lnk-event-hubs] と互換性のあるエンドポイントを公開することから、このチュートリアルでは [EventProcessorHost] インスタンスを使用します。 このインスタンスは次のような性質があります。

* Azure Blob Storage に *データポイント* メッセージを確実に保存する。
* *対話型* のデバイスからクラウドへのメッセージを Azure [Service Bus キュー] に転送して直ちに処理する。

Service Bus は、メッセージごとのチェックポイントと期間ベースの重複除去機能を提供するため、対話型メッセージの信頼性の高い処理を保証できます。

> [!NOTE]
> **EventProcessorHost** インスタンスは、対話型のメッセージを処理する方法の 1 つにすぎません。 他にも、[Azure Service Fabric][lnk-service-fabric] や [Azure Stream Analytics][lnk-stream-analytics] を使用できます。
> 
> 

チュートリアルの終わりとして、次の 3 つの Java コンソール アプリケーションを実行します。

* **simulated-device**: [IoT Hub の概要]に関するチュートリアルで作成したアプリを変更したものです。デバイスからクラウドへのデータポイント メッセージを 1 秒ごとに送信し、デバイスからクラウドへの対話型メッセージを 10 秒ごとに送信します。 このアプリでは、IoT Hub との通信に AMQPS プロトコルを使用します。
* **process-d2c-messages** : [EventProcessorHost] クラスを使用して、Event Hubs と互換性のあるエンドポイントからメッセージを取得します。 その後、データポイント メッセージを Azure Blob Storage に確実に格納し、対話型メッセージを Service Bus キューに転送します。
* **process-interactive-messages** : Service Bus キューから対話型メッセージをデキューします。

> [!NOTE]
> IoT Hub には、多数のデバイス プラットフォームや言語 (C、Java、JavaScript など) に対する SDK サポートがあります。 このチュートリアルのシミュレート対象デバイスを物理デバイスに置き換える方法と、IoT Hub にデバイスを接続する方法の手順については、 [Azure IoT デベロッパー センター]を参照してください。
> 
> 

このチュートリアルは、 [HDInsight (Hadoop)] プロジェクトなど、Event Hubs と互換性のあるメッセージを使用する他の方法に直接適用できます。 詳細については、「Azure IoT Hub 開発者ガイド」の「 [D2C (デバイスからクラウド)]」を参照してください。

このチュートリアルを完了するには、以下が必要です。

* [IoT Hub の概要] に関するチュートリアルで完成させたアプリ。
* Java SE 8。 <br/> 「[Prepare your development environment (開発環境を準備する)][lnk-dev-setup]」には、このチュートリアルのために Java をインストールする方法が記載されています。Windows と Linux の両方が対象となっています。
* Maven 3。  <br/> 「[Prepare your development environment (開発環境を準備する)][lnk-dev-setup]」には、このチュートリアルのために Maven をインストールする方法が記載されています。Windows と Linux の両方が対象となっています。
* アクティブな Azure アカウント。 <br/>Azure サブスクリプションがない場合は、 [無料アカウント](https://azure.microsoft.com/free/) を数分で作成できます。

[Azure Storage] と [Azure Service Bus] について、ある程度の基礎知識が必要です。

## <a name="send-interactive-messages-from-a-simulated-device"></a>シミュレーション済みデバイスからの対話型メッセージの送信
このセクションでは、 [IoT Hub の概要] に関するチュートリアルで作成したシュミレーション済みデバイス アプリケーションを変更して、デバイスからクラウドへの対話型メッセージが IoT Hub に送信されるようにします。

1. テキスト エディターを使用し、simulated-device\src\main\java\com\mycompany\app\App.java ファイルを開きます。 このファイルには、 **IoT Hub の概要** のチュートリアルで作成した [simulated-device] アプリのコードが含まれています。
2. 次の入れ子になったクラスを **App** クラスに追加します。
   
    ```
    private static class InteractiveMessageSender implements Runnable {
      public void run() {
        try {
          while (true) {
            String msgStr = "Alert message!";
            Message msg = new Message(msgStr);
            msg.setMessageId(java.util.UUID.randomUUID().toString());
            msg.setProperty("messageType", "interactive");
            System.out.println("Sending interactive message: " + msgStr);
   
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
   
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(10000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished sending interactive messages.");
        }
      }
    }
    ```
   
    このクラスは、**simulated-device** プロジェクトの **MessageSender** クラスに似ています。 唯一の違いは、**MessageId** システム プロパティと **messageType** という名前のカスタム プロパティを設定することです。
    コードでは、汎用一意識別子 (UUID) が **MessageId** プロパティに割り当てられます。 Service Bus はこの識別子を使用して、受信したメッセージの重複除去を行うことができます。 このサンプルでは **messageType** プロパティを利用し、対話型メッセージとデータポイント メッセージを区別します。 この情報は、メッセージ本文ではなく、メッセージ プロパティで渡されるため、イベント プロセッサでメッセージをルーティングするためにメッセージを逆シリアル化する必要はありません。
   
   > [!NOTE]
   > 対話型メッセージの重複除去に使用する **MessageId** はデバイス コードで作成することが重要です。 ネットワーク通信の切断や、その他の障害により、デバイスから同じメッセージが繰り返し再送信されることがあります。 UUID の代わりに、関連メッセージ データ フィールドのハッシュなど、セマンティック メッセージ ID を利用することもできます。
   > 
   > 
3. 次のコード スニペットで示すように、 **main** メソッドを、対話型メッセージとデータポイント メッセージの両方を送信するように変更します。
   
    ````
    MessageSender sender = new MessageSender();
    InteractiveMessageSender interactiveSender = new InteractiveMessageSender();
   
    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(sender);
    executor.execute(interactiveSender);
    ````
4. simulated-device\src\main\java\com\mycompany\app\App.java を保存して閉じます。
   
   > [!NOTE]
   > わかりやすくするために、このチュートリアルでは再試行ポリシーは実装しません。 運用環境のコードでは、MSDN の記事「 [Transient Fault Handling (一時的な障害の処理)]」で推奨されているように、再試行ポリシー (指数関数的バックオフなど) を実装することをお勧めします。
   > 
   > 
5. Maven を使用して **simulated-device** アプリケーションをビルドするには、コマンド プロンプトで simulated-device フォルダーに移動し、次のコマンドを実行します。
   
    ```
    mvn clean package -DskipTests
    ```

## <a name="process-device-to-cloud-messages"></a>デバイスからクラウドへのメッセージの処理
このセクションでは、IoT Hub のデバイスからクラウドへのメッセージを処理する Java コンソール アプリケーションを作成します。 IoT Hub は、アプリケーションでデバイスからクラウドへのメッセージを読み取ることができるように、 [Event Hubs]と互換性のあるエンドポイントを公開します。 このチュートリアルでは [EventProcessorHost] クラスを使用して、コンソール アプリでこれらのメッセージを処理します。 Event Hubs からのメッセージを処理する方法の詳細については、「 [Event Hubs の使用] 」のチュートリアルを参照してください。

データポイント メッセージの信頼性の高いストレージを実装する場合や、対話型メッセージを転送する場合の主な課題は、イベント処理がその進行状況のチェックポイントを提供するメッセージ コンシューマーに依存することです。 また、高いスループットを達成するためには、Event Hubs から読み取るときに大きなバッチでチェックポイントを提供する必要があります。 このアプローチでは、エラーが発生して前のチェックポイントに戻すときに、大量のメッセージが重複処理される可能性が生まれます。 このチュートリアルでは、 **EventProcessorHost** のチェックポイントを使用して、Azure Storage の書き込みと Service Bus の重複除去期間を同期する方法を示します。

メッセージを Azure Storage に確実に書き込むために、サンプルでは、[ブロック BLOB][Azure ブロック BLOB] の個別ブロック コミット機能を使用します。 イベント プロセッサでは、チェックポイントの実行時間になるまでメッセージがメモリに蓄積されます。 たとえば、蓄積されたメッセージ バッファーが最大ブロック サイズの 4 MB に到達したときや、Service Bus の重複除去期間が経過したときです。 その後、チェックポイントの前に、コードにより新しいブロックが BLOB にコミットされます。

イベント プロセッサでは、ブロック ID として Event Hubs のメッセージ オフセットを使用します。 これにより、イベント プロセッサは新しいブロックをストレージにコミットする前に重複除去を実行して、ブロックのコミットとチェックポイント間でクラッシュが発生する可能性を抑えることができます。

> [!NOTE]
> このチュートリアルでは 1 つのストレージ アカウントを使用して、IoT Hub から取得したすべてのメッセージを書き込みます。 ソリューションで複数の Azure Storage アカウントを使用する必要があるかどうかを判断するには、 [Azure Storage のスケーラビリティのガイドライン]をご覧ください。
> 
> 

このアプリケーションでは、対話型メッセージの処理時に、Service Bus 重複除去機能を利用して重複を回避します。 シミュレートされたデバイスでは、各対話型メッセージに一意の **MessageId**を付与します。 この ID により、Service Bus では、指定された重複除去期間に同じ **MessageId** を持つメッセージが受信側に配信されないようにすることができます。 この重複除去を、Service Bus キューによって提供されるメッセージごとの完了セマンティクスと共に使用することで、対話型メッセージを信頼性の高い方法で簡単に処理できます。

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

Service Bus キューで対話型メッセージの信頼性の高い処理を有効にする必要もあります。  [Service Bus キューの使用方法][Service Bus キュー] を使用できます。 あるいは、[Azure クラシック ポータル][lnk-classic-portal]を使用し、以下の手順に従って作成することもできます。

1. 右下隅にある **[新規]** をクリックします。 次に、**[App Services]** > **[Service Bus]** > **[キュー]** > **[カスタム作成]** の順にクリックします。 **d2ctutorial**という名前を入力し、リージョンを選択し、既存の名前空間を使用するか名前空間を新規に作成します。 名前空間名をメモしておきます。このチュートリアルの後半で必要になります。 次のページで、**[重複データ検出の有効化]** を選択し、**[重複データ検出の履歴時間枠]** を 1 時間に設定します。 右下隅にあるチェック マークをクリックして、キューの構成を保存します。
   
    ![Azure ポータルでキューを作成する][30]
2. Service Bus キューの一覧で、**[d2ctutorial]** をクリックし、**[構成]** をクリックします。 2 つの共有アクセス ポリシー (1 つは **Send** アクセス許可を持つ **send**、もう 1 つは **Listen** アクセス許可を持つ **isten**) を作成します。 両方のポリシーの**プライマリ キー**の値をメモしておきます。このチュートリアルの後半で必要になります。 完了したら、下部にある **[保存]** をクリックします。
   
    ![Azure ポータルでキューを構成する][31]

### <a name="create-the-event-processor"></a>イベント プロセッサの作成
このセクションでは、Event Hubs と互換性のあるエンドポイントからのメッセージを処理する Java アプリケーションを作成します。

最初のタスクは、**process-d2c-messages** という名前の Maven プロジェクトを追加することです。これはデバイスからクラウドへのメッセージを Event Hubs と互換性のある IoT Hub エンドポイントから受信し、他のバックエンド サービスにそのメッセージをルーティングします。

1. コマンド プロンプトで次のコマンドを実行して、 [IoT Hub の使用] に関するチュートリアルで作成した iot-java-get-started フォルダーに **process-d2c-messages** という名前の Maven プロジェクトを作成します。 これは、1 つの長いコマンドであることに注意してください。
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. コマンド プロンプトで、新しい process-d2c-messages フォルダーに移動します。
3. テキスト エディターを使用して、process-d2c-messages フォルダー内の pom.xml ファイルを開き、次の依存関係を **dependencies** ノードに追加します。 これらの依存関係により、アプリケーションの azure-eventhubs パッケージ、azure-eventhubs-eph パッケージ、azure-servicebus パッケージを使用して IoT Hub と Service Bus キューを操作できるようになります。
   
    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs-eph</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```
4. pom.xml ファイルを保存して閉じます。

次に、 **ErrorNotificationHandler** クラスをプロジェクトに追加します。

1. テキスト エディターを使用して、process-d2c-messages\src\main\java\com\mycompany\app\ErrorNotificationHandler.java ファイルを作成します。 **EventProcesssorHost** インスタンスからのエラー メッセージを表示する次のコードをこのファイルに追加します。
   
    ```
    package com.mycompany.app;
   
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   
    public class ErrorNotificationHandler implements
        Consumer<ExceptionReceivedEventArgs> {
      @Override
      public void accept(ExceptionReceivedEventArgs t) {
        System.out.println("EventProcessorHost: Host " + t.getHostname()
            + " received general error notification during " + t.getAction() + ": "
            + t.getException().toString());
      }
    }
    ```
2. ErrorNotificationHandler.java ファイルを保存して閉じます。

これで、 **IEventProcessor** インターフェイスを実装するクラスを追加できるようになりました。 **EventProcessorHost** クラスはこのクラスを呼び出し、IoT Hub から受信したデバイスからクラウドへのメッセージを処理します。 このクラスのコードは、信頼できる方法で BLOB コンテナーにメッセージを保存し、対話メッセージを Service Bus キューに転送するためのロジックを実装します。

**onEvents** メソッドは、このイベント プロセッサで読み込まれる最新のメッセージのオフセットやシーケンス番号を追跡する **latestEventData** 変数を設定します。 プロセッサごとに 1 つのパーティションを担当することに注意してください。 **onEvents** メソッドは IoT Hub からメッセージをバッチで受信すると、対話型メッセージを Service Bus キューに送信し、データポイント メッセージを **toAppend** メモリ バッファーに追加します。 メモリ バッファーが 4 MB のブロック制限に達したか、重複除去期間 (このチュートリアルでは 前回のチェックポイントから1 時間) が経過した場合に、このメソッドによりチェックポイントがトリガーされます。

**ppendAndCheckpoint** メソッドは、まず BLOB に追加するブロックの **blockId** を生成します。 Azure Storage ではすべてのブロック ID が同じ長さである必要があるため、オフセットには先行ゼロが埋め込まれます。 次に、この ID を持つブロックが既に BLOB にある場合は、メソッドは現在のバッファーの内容で上書きします。

> [!NOTE]
> コードを簡略化するために、このチュートリアルでは、メッセージを格納するパーティションごとに 1 つの BLOB ファイルを使用します。 実際のソリューションではファイル ローリングを実装します。その場合、一定時間後、または特定のサイズに達したときに、追加ファイルを作成します。 Azure ブロック BLOB が格納できるデータは最大 195 GB であることに注意してください。
> 
> 

続いて、 **IEventProcessor** インターフェイスを実装します。

1. テキスト エディターを使用し、process-d2c-messages\src\main\java\com\mycompany\app\EventProcessor.java ファイルを作成します。
2. 次の imports とクラス定義を EventProcessor.java ファイルに追加します。 **EventProcessor** クラスは、Event Hubs クライアントの動作を定義する **IEventProcessor** インターフェイスを実装します。
   
    ```
    package com.mycompany.app;
   
    import java.io.ByteArrayInputStream;
    import java.io.ByteArrayOutputStream;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.nio.charset.StandardCharsets;
    import java.time.Duration;
    import java.time.Instant;
    import java.util.ArrayList;
    import java.util.Base64;
    import java.util.concurrent.ExecutionException;
   
    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.BrokeredMessage;
   
    public class EventProcessor implements IEventProcessor {
   
    }
    ```
3. **IEventProcessor** インターフェイスを実装する次のメソッドを、**EventProcessor** クラスに追加します。
   
    ```
    @Override
    public void onOpen(PartitionContext context) throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is opening");
    }
   
    @Override
    public void onClose(PartitionContext context, CloseReason reason)
        throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is closing for reason "
          + reason.toString());
    }
   
    @Override
    public void onError(PartitionContext context, Throwable error) {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " onError: " + error.toString());
    }
   
    @Override
    public void onEvents(PartitionContext context, Iterable<EventData> messages)
        throws Exception {
    }
    ```
4. 次のクラスレベル変数を **EventProcessor** クラスに追加します。
   
    ```
    public static CloudBlobContainer blobContainer;
    public static ServiceBusContract serviceBusContract;
   
    // Use a smaller MAX_BLOCK_SIZE value to test.
    final private int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
    final private Duration MAX_CHECKPOINT_TIME = Duration.ofHours(1);
   
    private ByteArrayOutputStream toAppend = new ByteArrayOutputStream(
        MAX_BLOCK_SIZE);
    private Instant start = Instant.now();
    private EventData latestEventData;
    ```
5. **AppendAndCheckPoint** メソッドと次のシグネチャを **EventProcessor** クラスに追加します。
   
    ```
    private void AppendAndCheckPoint(PartitionContext context)
      throws URISyntaxException, StorageException, IOException,
      IllegalArgumentException, InterruptedException, ExecutionException {
    }
    ```
6. パーティションの現在のメッセージ オフセットやシーケンス番号を取得する次のコードを、 **AppendAndCheckPoint** メソッドに追加します。
   
    ```
    String currentOffset = latestEventData.getSystemProperties().getOffset();
    Long currentSequence = latestEventData.getSystemProperties().getSequenceNumber();
    System.out
        .printf(
            "\nAppendAndCheckPoint using partition: %s, offset: %s, sequence: %s\n",
            context.getPartitionId(), currentOffset, currentSequence);
    ```
7. **AppendAndCheckPoint** メソッドで、現在のオフセット値を使用して、BLOB に保存する次のブロックの **BlockEntry** インスタンスを作成します。
   
    ```
    Long blockId = Long.parseLong(currentOffset);
    String blockIdString = String.format("startSeq:%1$025d", blockId);
    String encodedBlockId = Base64.getEncoder().encodeToString(
        blockIdString.getBytes(StandardCharsets.US_ASCII));
    BlockEntry block = new BlockEntry(encodedBlockId);
    ```
8. **AppendAndCheckPoint** メソッドで、最新のメッセージのセットをブロック BLOB にアップロードし、現在のブロックの一覧を取得します。
   
    ```
    String blobName = String.format("iothubd2c_%s", context.getPartitionId());
    CloudBlockBlob currentBlob = blobContainer.getBlockBlobReference(blobName);
   
    currentBlob.uploadBlock(block.getId(),
        new ByteArrayInputStream(toAppend.toByteArray()), toAppend.size());
    ArrayList<BlockEntry> blockList = currentBlob.downloadBlockList();
    ```
9. **AppendAndCheckPoint** メソッドで、新しい BLOB に最初のブロックを作成するか、または既存の BLOB にブロックを追加します。
   
    ```
    if (currentBlob.exists()) {
      // Check if we should append new block or overwrite existing block
      BlockEntry last = blockList.get(blockList.size() - 1);
      if (blockList.size() > 0 && !last.getId().equals(block.getId())) {
        System.out.printf("Appending block %s to blob %s\n", blockId, blobName);
        blockList.add(block);
      } else {
        System.out.printf("Overwriting block %s in blob %s\n", blockId,
            blobName);
      }
    } else {
      System.out.printf("Creating initial block %s in new blob: %s\n", blockId,
          blobName);
      blockList.add(block);
    }
    currentBlob.commitBlockList(blockList);
    ```
10. 最後に、 **AppendAndCheckPoint** メソッドでパーティションにチェックポイントを作成し、次のメッセージのブロックを保存する準備をします。
    
    ```
    context.checkpoint(latestEventData);
    
    // Reset everything after the checkpoint.
    toAppend.reset();
    start = Instant.now();
    System.out.printf("Checkpointed on partition id: %s\n",
        context.getPartitionId());
    ```
11. **onEvents** メソッドで、IoT Hub エンドポイントからメッセージを受信し、Service Bus キューに対話型のメッセージを転送する次のコードを追加します。 また、このコードでは、ブロックがいっぱいになるかタイムアウトになったときに、 **AppendAndCheckPoint** メソッドを呼び出します。
    
    ```
    if (messages != null) {
      for (EventData eventData : messages) {
        latestEventData = eventData;
        byte[] data = eventData.getBody();
        if (eventData.getProperties().containsKey("messageType")
            && eventData.getProperties().get("messageType")
                .equals("interactive")) {
          String messageId = (String) eventData.getSystemProperties().get(
              "message-id");
          BrokeredMessage message = new BrokeredMessage(data);
          message.setMessageId(messageId);
          serviceBusContract.sendQueueMessage("d2ctutorial", message);
          continue;
        }
        if (toAppend.size() + data.length > MAX_BLOCK_SIZE
            || Duration.between(start, Instant.now()).compareTo(
                MAX_CHECKPOINT_TIME) > 0) {
          AppendAndCheckPoint(context);
        }
        toAppend.write(data);
      }
    }
    ```
12. 最後に、IoT Hub からのメッセージがないときにタイムアウトになった場合に **AppendAndCheckPoint** を呼び出す 'else if' 句を、**onEvents** メソッドに追加します。
    
    ```
    else if ((toAppend.size() > 0)
        && Duration.between(start, Instant.now())
            .compareTo(MAX_CHECKPOINT_TIME) > 0) {
      AppendAndCheckPoint(context);
    }
    ```
13. EventProcessor.java ファイルへの変更を保存します。

**process-d2c-messages** プロジェクトの最後のタスクは、**main** メソッドに、**EventProcessorHost** インスタンスをインスタンス化するコードを追加することです。

1. テキスト エディターを使用し、process-d2c-messages\src\main\java\com\mycompany\app\App.java ファイルを開きます。
2. ファイルに次の **import** ステートメントを追加します。
   
    ```
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.storage.CloudStorageAccount;
    import com.microsoft.azure.storage.StorageException;
    import com.microsoft.azure.storage.blob.CloudBlobClient;
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusConfiguration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusService;
   
    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.concurrent.*;
    ```
3. 次のクラスレベル変数を **App** クラスに追加します。 **{yourstorageaccountconnectionstring}** は、「 [Azure ストレージ アカウントと Service Bus キューのプロビジョニング](#provision-an-azure-storage-account-and-a-service-bus-queue) 」セクションでメモした Azure ストレージ アカウント接続文字列に置き換えてください。
   
    ```
    private final static String storageConnectionString = "{yourstorageaccountconnectionstring}";
    ```
4. 次のクラスレベルの変数を **App** クラスに追加します。**{yourservicebusnamespace}** は実際の Service Bus 名前空間に、**{yourservicebussendkey}** は実際のキューの **send** キーに置き換えます。 名前空間と **listen** キーの値は、「[Azure Storage アカウントと Service Bus キューのプロビジョニング](#provision-an-azure-storage-account-and-a-service-bus-queue)」セクションでメモしています。
   
    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "send";
    private final static String serviceBusSASKey = "{yourservicebussendkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    ```
5. 次のクラスレベル変数を **App** クラスに追加します。 **{youreventhubcompatibleendpoint}** は、イベント ハブと互換性のあるエンドポイントの名前で置き換えます。 エンドポイント名は **ihs....namespace** のようになるため、**sb://** プレフィックスと **.servicebus.windows.net/** サフィックスを削除する必要があります。 **{youreventhubcompatiblename}** は、イベント ハブと互換性のある名前で置き換えます。 **{youriothubkey}** は、**iothubowner*** キーで置き換えます。 これらの値は、「*Azure IoT Hub for Java の使用*」チュートリアルの「[IoT Hub の作成][lnk-create-an-iot-hub]」セクションでメモしています。
   
    ```
    private final static String consumerGroupName = "$Default";
    private final static String namespaceName = "{youreventhubcompatibleendpoint}";
    private final static String eventHubName = "{youreventhubcompatiblename}";
    private final static String sasKeyName = "iothubowner";
    private final static String sasKey = "{youriothubkey}";
    ```
6. **main** メソッドのシグネチャを以下のように変更します。
   
    ```
    public static void main(String args[]) throws InvalidKeyException,
      URISyntaxException, StorageException {
    }
    ```
7. メッセージの格納先の BLOB コンテナーへの参照を取得する次のコードを、 **main** メソッドに追加します。
   
    ```
    System.out.println("Process D2C messages using EventProcessorHost");
    CloudStorageAccount account = CloudStorageAccount
        .parse(storageConnectionString);
    CloudBlobClient client = account.createCloudBlobClient();
    EventProcessor.blobContainer = client
        .getContainerReference("d2cjavatutorial");
    EventProcessor.blobContainer.createIfNotExists();
    ```
8. Service Bus サービスへの参照を取得する次のコードを、 **main** メソッドに追加します。
   
    ```
    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    EventProcessor.serviceBusContract = ServiceBusService.create(config);
    ```
9. **main** メソッドで、**EventProcessorHost** インスタンスを構成して作成します。 **setInvokeProcessorAfterReceiveTimeout** オプションにより、処理するメッセージがない場合でも、**EventProcessorHost** インスタンスで **IEventProcessor** インターフェイスの **onEvents** メソッドが呼び出されます。 そして **onEvents** メソッドが、タイムアウト時に常に **AppendAndCheckPoint** メソッドを呼び出します。
   
    ```
    ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(
        namespaceName, eventHubName, sasKeyName, sasKey);
    EventProcessorHost host = new EventProcessorHost(eventHubName,
        consumerGroupName, eventHubConnectionString.toString(),
        storageConnectionString);
    EventProcessorOptions options = new EventProcessorOptions();
    options.setExceptionNotification(new ErrorNotificationHandler());
    options.setInvokeProcessorAfterReceiveTimeout(true);
    ```
10. **main** メソッドで、**IEventProcessor** 実装を **EventProcessorHost** インスタンスに登録します。
    
    ```
    try {
      System.out.println("Registering host named " + host.getHostName());
      host.registerEventProcessor(EventProcessor.class, options).get();
    } catch (Exception e) {
      System.out.print("Failure while registering: ");
      if (e instanceof ExecutionException) {
        Throwable inner = e.getCause();
        System.out.println(inner.toString());
      } else {
        System.out.println(e.toString());
      }
      System.out.println(e.toString());
    }
    ```
11. 最後に、**EventProcessorHost** インスタンスをシャット ダウンするロジックを、**main** メソッドに追加します。
    
    ```
    System.out.println("Press enter to stop");
    try {
      System.in.read();
      host.unregisterEventProcessor();
    
      System.out.println("Calling forceExecutorShutdown");
      EventProcessorHost.forceExecutorShutdown(120);
    } catch (Exception e) {
      System.out.println(e.toString());
      e.printStackTrace();
    }
    
    System.out.println("End of sample");
    ```
12. process-d2c-messages\src\main\java\com\mycompany\app\App.java ファイルを保存して閉じます。
13. Maven を使用して **process-d2c-messages** アプリケーションをビルドするために、コマンド プロンプトで process-d2c-messages フォルダーに移動して次のコマンドを実行します。
    
    ```
    mvn clean package -DskipTests
    ```

## <a name="receive-interactive-messages"></a>対話型メッセージの受信
このセクションでは、Service Bus キューから対話型メッセージを受信する Java コンソール アプリケーションを作成します。

最初のタスクは、**EventProcessor** インスタンスから Service Bus キューに送信されたメッセージを受信する **process-interactive-messages** という名前の Maven プロジェクトを追加することです。

1. コマンド プロンプトで次のコマンドを実行して、 [IoT Hub の使用] に関するチュートリアルで作成した iot-java-get-started フォルダーに **process-interactive-messages** という名前の Maven プロジェクトを作成します。 これは、1 つの長いコマンドであることに注意してください。
   
    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-interactive-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```
2. コマンド プロンプトで、新しい process-interactive-messages フォルダーに移動します。
3. テキスト エディターを使用して、process-interactive-messages フォルダー内の pom.xml ファイルを開き、次の依存関係を **dependencies** ノードに追加します。 この依存関係により、アプリケーションの azure-servicebus パッケージを使用して Service Bus キューを操作できるようになります。
   
    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```
4. pom.xml ファイルを保存して閉じます。

次に、Service Bus キューからメッセージを取得するためのコードを追加します。

1. テキスト エディターを使用して、process-interactive-messages\src\main\java\com\mycompany\app\App.java ファイルを開きます。
2. 次の `import` ステートメントをファイルに追加します。
   
    ```
    import java.io.IOException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
   
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.exception.ServiceException;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.*;
    ```
3. 次のクラスレベル変数を **App** クラスに追加します。**{yourservicebusnamespace}** は実際の Service Bus 名前空間に、**{yourservicebuslistenkey}** は実際のキューの **listen** キーに置き換えます。 名前空間と **listen** キーの値は、「[Azure Storage アカウントと Service Bus キューのプロビジョニング](#provision-an-azure-storage-account-and-a-service-bus-queue)」セクションでメモしています。
   
    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "listen";
    private final static String serviceBusSASKey = "{yourservicebuslistenkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    private final static String queueName = "d2ctutorial";
    private static ServiceBusContract service = null;
    ```
4. キューからメッセージを受信するための次の入れ子になったクラスを、 **App** クラスに追加します。
   
    ```
    private static class MessageReceiver implements Runnable {
      public void run() {
        ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
        try {
          while (true) {
            ReceiveQueueMessageResult resultQM = service.receiveQueueMessage(
                queueName, opts);
            BrokeredMessage message = resultQM.getValue();
            if (message != null && message.getMessageId() != null) {
              System.out.println("MessageID: " + message.getMessageId());
              System.out.print("From queue: ");
              byte[] b = new byte[200];
              String s = null;
              int numRead = message.getBody().read(b);
              while (-1 != numRead) {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
              }
              System.out.println();
            } else {
              Thread.sleep(1000);
            }
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        } catch (ServiceException e) {
          System.out.println("ServiceException: " + e.getMessage());
        } catch (IOException e) {
          System.out.println("IOException: " + e.getMessage());
        }
      }
    }
    ```
5. **main** メソッドのシグネチャを以下のように変更します。
   
    ```
    public static void main(String args[]) throws ServiceException, IOException {
    }
    ```
6. 新しいメッセージのリッスンを開始する次のコードを、 **main** メソッドに追加します。
   
    ```
    System.out.println("Process interactive messages");
   
    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    service = ServiceBusService.create(config);
   
    MessageReceiver receiver = new MessageReceiver();
   
    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(receiver);
   
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    ```
7. process-interactive-messages\src\main\java\com\mycompany\app\App.java ファイルを保存して閉じます。
8. Maven を使用して **process-interactive-messages** アプリケーションをビルドするために、コマンド プロンプトで process-interactive-messages フォルダーに移動して次のコマンドを実行します。
   
    ```
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>アプリケーションの実行
これで、3 つのアプリケーションを実行する準備が整いました。

1. コマンド プロンプトまたはシェルで process-interactive-messages フォルダーに移動して次のコマンドを実行し、 **process-interactive-messages** アプリケーションを実行します。
   
   ```
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![process-interactive-messages の実行][processinteractive]
2. コマンド プロンプトまたはシェルで process-d2c-messages フォルダーに移動して次のコマンドを実行し、 **process-d2c-messages** アプリケーションを実行します。
   
   ```
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![process-d2c-messages の実行][processd2c]
3. コマンド プロンプトまたはシェルで simulated-device フォルダーに移動して次のコマンドを実行し、 **simulated-device** アプリケーションを実行します。
   
   ```
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![simulated-device の実行][simulateddevice]

> [!NOTE]
> BLOB ファイルの更新内容を表示するために、**StoreEventProcessor** クラスの **MAX_BLOCK_SIZE** 定数を、**1024** などのより小さい値に設定することが必要になる場合があります。 シミュレーション対象デバイスによって送信されたデータのブロック サイズの制限に達するまでに時間がかかるため、この変更が役立ちます。 ブロック サイズを小さくすることで、作成および更新される BLOB を表示するために長時間待機する必要がなくなります。 ただし、より大きなブロック サイズを使用すると、アプリケーションの拡張性が向上します。
> 
> 

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、 [EventProcessorHost] クラスを使用して、データポイント メッセージとデバイスからクラウドへの対話型メッセージを確実に処理する方法について学習しました。

[IoT Hub でクラウドからデバイスへのメッセージを送信する方法][lnk-c2d]では、バックエンドからデバイスにメッセージを送信する方法を説明しています。

IoT Hub を使用する完全なエンド ツー エンド ソリューションの例については、[Azure IoT Suite][lnk-suite] に関するドキュメントをご覧ください。

IoT Hub を使用したソリューションの開発に関する詳細については、 [IoT Hub 開発者ガイド]をご覧ください。

<!-- Images. -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[processinteractive]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[processd2c]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/createqueue2.png
[31]: ./media/iot-hub-java-java-process-d2c/createqueue3.png

<!-- Links -->

[Azure BLOB Storage]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus キュー]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[Azure IoT Hub 開発者ガイド - デバイスからクラウド]: iot-hub-devguide-messaging.md

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT Hub 開発者ガイド]: iot-hub-devguide.md
[IoT Hub の概要]: iot-hub-java-java-getstarted.md
[Azure IoT デベロッパー センター]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Transient Fault Handling (一時的な障害の処理) (一時的な障害の処理)]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Azure Storage について]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Event Hubs の使用]: ../event-hubs/event-hubs-java-ephjava-getstarted.md
[Azure Storage のスケーラビリティのガイドライン]: ../storage/storage-scalability-targets.md
[Azure ブロック BLOB]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: https://github.com/Azure/azure-event-hubs/tree/master/java/azure-eventhubs-eph
[Transient Fault Handling (一時的な障害の処理) (一時的な障害の処理)]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-java-java-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-create-an-iot-hub]: iot-hub-java-java-getstarted.md#create-an-iot-hub


<!--HONumber=Oct16_HO2-->


