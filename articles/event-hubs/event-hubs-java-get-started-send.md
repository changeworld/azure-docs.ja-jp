---
title: Java を使用して Azure Event Hubs との間でイベントを送受信する (最新バージョン)
description: この記事では、最新の azure-messaging-eventhubs パッケージを使用して、Azure Event Hubs との間でイベントを送受信する Java アプリケーションを作成する方法について説明します。
ms.topic: quickstart
ms.date: 04/05/2021
ms.custom: devx-track-java
ms.openlocfilehash: bae0d4147fddf57398d494ca7f13c347f892e45e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448442"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Java を使用して Azure Event Hubs との間でイベントを送受信する (azure-messaging-eventhubs)
このクイックスタートでは、**azure-messaging-eventhubs** Java パッケージを使用して、イベント ハブとの間でイベントを送受信する方法について説明します。

> [!IMPORTANT]
> このクイックスタートでは、新しい **azure-messaging-eventhubs** ライブラリを使用します。 以前の **azure-eventhubs** および **azure-eventhubs-eph** パッケージを使用するクイックスタートについては、[azure-eventhubs と azure-eventhubs-eph を使用したイベントの送受信](event-hubs-java-get-started-send-legacy.md)に関するページを参照してください。 


## <a name="prerequisites"></a>前提条件
Azure Event Hubs を初めて使用する場合は、このクイックスタートを行う前に [Event Hubs の概要](event-hubs-about.md)を参照してください。 

このクイック スタートを完了するには、次の前提条件を用意しておく必要があります。

- **Microsoft Azure サブスクリプション**。 Azure Event Hubs を含む Azure サービスを使用するには、サブスクリプションが必要です。  既存の Microsoft Azure アカウントをお持ちでない場合は、[アカウントを作成する](https://azure.microsoft.com)際に、[無料試用版](https://azure.microsoft.com/free/)にサインアップするか、MSDN サブスクライバー特典を利用できます。
- Java 開発環境。 このクイックスタートでは [Eclipse](https://www.eclipse.org/) を使用します。 Java Development Kit (JDK) バージョン 8 以上が必要です。 
- **Event Hubs 名前空間とイベント ハブを作成する**。 最初の手順では、[Azure Portal](https://portal.azure.com) を使用して Event Hubs 型の名前空間を作成し、アプリケーションがイベント ハブと通信するために必要な管理資格情報を取得します。 名前空間とイベント ハブを作成するには、[こちらの記事](event-hubs-create.md)の手順に従います。 その後、次の記事の手順に従って、**Event Hubs 名前空間用の接続文字列** を取得します: [接続文字列を取得する](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 この接続文字列は、このクイックスタートの後の手順で必要になります。

## <a name="send-events"></a>送信イベント 
このセクションでは、イベント ハブにイベントを送信する Java アプリケーションの作成方法を説明します。 

### <a name="add-reference-to-azure-event-hubs-library"></a>Azure Event Hubs ライブラリへの参照を追加する

Event Hubs 用の Java クライアント ライブラリは、[Maven Central Repository](https://search.maven.org/search?q=a:azure-messaging-eventhubs) にあります。 Maven プロジェクト ファイル内で次の依存関係宣言を使用して、このライブラリを参照できます。

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.6.0</version>
</dependency>
```

> [!NOTE]
> バージョンを、Maven リポジトリに発行された最新バージョンに更新します。 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>イベント ハブにメッセージを送信するコードの記述

次のサンプルでは、最初に、好みの Java 開発環境でコンソール/シェル アプリケーション用の新しい Maven プロジェクトを作成します。 `Sender` という名前のクラスを追加し、このクラスに次のコードを追加します。

> [!IMPORTANT]
> `<Event Hubs namespace connection string>` を、Event Hubs 名前空間への接続文字列で更新します。 `<Event hub name>` を、名前空間内でご利用のイベント ハブの名前で更新します。 

```java
import com.azure.messaging.eventhubs.*;
import java.util.Arrays;
import java.util.List;

public class Sender {
    private static final String connectionString = "<Event Hubs namespace connection string>";
    private static final String eventHubName = "<Event hub name>";

    public static void main(String[] args) {
        publishEvents();
    }
}
```
### <a name="add-code-to-publish-events-to-the-event-hub"></a>イベントをイベント ハブに発行するためのコードを追加する
次のように、`Sender` クラスに `publishEvents` という名前のメソッドを追加します。 

```java
    /**
     * Code sample for publishing events.
     * @throws IllegalArgumentException if the event data is bigger than max batch size.
     */
    public static void publishEvents() {
        // create a producer client
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

        // sample events in an array
        List<EventData> allEvents = Arrays.asList(new EventData("Foo"), new EventData("Bar"));
        
        // create a batch
        EventDataBatch eventDataBatch = producer.createBatch();

        for (EventData eventData : allEvents) {
            // try to add the event from the array to the batch
            if (!eventDataBatch.tryAdd(eventData)) {
                // if the batch is full, send it and then create a new batch
                producer.send(eventDataBatch);
                eventDataBatch = producer.createBatch();

                // Try to add that event that couldn't fit before.
                if (!eventDataBatch.tryAdd(eventData)) {
                    throw new IllegalArgumentException("Event is too large for an empty batch. Max size: "
                        + eventDataBatch.getMaxSizeInBytes());
                }
            }
        }
        // send the last batch of remaining events
        if (eventDataBatch.getCount() > 0) {
            producer.send(eventDataBatch);
        }
        producer.close();
    }
```

プログラムをビルドし、エラーがないことを確認します。 このプログラムは、受信側プログラムを実行した後に実行します。 

## <a name="receive-events"></a>受信イベント
このチュートリアルのコードは [GitHub の EventProcessorClient サンプル](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorBlobCheckpointStoreSample.java)に基づくものであり、完全に動作するアプリケーションを表示する場合に確認することができます。

> [!WARNING]
> このコードを Azure Stack Hub で実行すると、特定の Storage API バージョンを対象としている場合を除き、実行時エラーが発生します。 これは、Event Hubs SDK では、Azure で利用できる最新の Azure Storage API が使用されますが、Azure Stack Hub プラットフォームではこれを利用できない可能性があるためです。 Azure Stack Hub でサポートされる Storage Blob SDK のバージョンは、Azure で一般的に利用できるバージョンと異なる場合があります。 チェックポイント ストアとして Azure Blob Storage を使用している場合は、[Azure Stack Hub ビルドでサポートされている Azure Storage API バージョン](/azure-stack/user/azure-stack-acs-differences?#api-version)を確認し、コード内でそのバージョンを対象にします。 
>
> たとえば、Azure Stack Hub バージョン 2005 上で実行している場合、Storage サービスで利用できる最も高いバージョンは 2019-02-02 となります。 既定では、Event Hubs SDK クライアント ライブラリでは、Azure で利用できる最も高いバージョン (SDK のリリース時点では 2019-07-07) が使用されます。 この場合は、このセクションの手順に従うことに加え、Storage サービス API バージョン 2019-02-02 を対象とするコードを追加する必要もあります。 特定の Storage API バージョンを対象にする方法の例については、[GitHub のサンプル](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java)を参照してください。 


### <a name="create-an-azure-storage-and-a-blob-container"></a>Azure Storage と BLOB コンテナーを作成する
このクイックスタートでは、チェックポイント ストアとして Azure Storage (具体的には Blob Storage) を使用します。 チェックポイント処理とは、イベント プロセッサがパーティション内の最後に正常に処理されたイベントの位置をマークまたはコミットするために使用する処理です。 チェックポイントのマークは通常、イベントを処理する関数内で付けられます。 チェックポイント処理の詳細については、[イベント プロセッサ](event-processor-balance-partition-load.md)に関するページを参照してください。

Azure ストレージ アカウントを作成するには、次の手順に従います。 

1. [Azure ストレージ アカウントの作成](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [BLOB コンテナーを作成する](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [ストレージ アカウントへの接続文字列を取得する](../storage/common/storage-configure-connection-string.md)

    **接続文字列** と **コンテナー名** を書き留めておきます。 これらは、受信コードで使用します。 

### <a name="add-event-hubs-libraries-to-your-java-project"></a>Java プロジェクトに Event Hubs ライブラリを追加する
pom.xml ファイルに次の依存関係を追加します。 

- [azure-messaging-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs)
- [azure-messaging-eventhubs-checkpointstore-blob](https://search.maven.org/search?q=a:azure-messaging-eventhubs-checkpointstore-blob)

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.6.0</version>
    </dependency>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs-checkpointstore-blob</artifactId>
        <version>1.5.1</version>
    </dependency>
</dependencies>
```

1. 次の **import** ステートメントを Java ファイルの先頭に追加します。 

    ```java
    import com.azure.messaging.eventhubs.EventHubClientBuilder;
    import com.azure.messaging.eventhubs.EventProcessorClient;
    import com.azure.messaging.eventhubs.EventProcessorClientBuilder;
    import com.azure.messaging.eventhubs.checkpointstore.blob.BlobCheckpointStore;
    import com.azure.messaging.eventhubs.models.ErrorContext;
    import com.azure.messaging.eventhubs.models.EventContext;
    import com.azure.storage.blob.BlobContainerAsyncClient;
    import com.azure.storage.blob.BlobContainerClientBuilder;
    import java.util.function.Consumer;
    ```
2. `Receiver` という名前のクラスを作成し、そのクラスに次の文字列変数を追加します。 プレースホルダーを適切な値に置き換えます。 

    > [!IMPORTANT]
    > プレースホルダーを適切な値に置き換えます。
    > - `<Event Hubs namespace connection string>` を、Event Hubs 名前空間への接続文字列で置き換えます。 更新 
    > - `<Event hub name>` を、名前空間内でご利用のイベント ハブの名前で置き換えます。 
    > - `<Storage connection string>` を、Azure ストレージ アカウントへの接続文字列で置き換えます。 
    > - `<Storage container name>` を、Azure BLOB ストレージ内のコンテナーの名前で置き換えます。 

    ```java
    private static final String connectionString = "<Event Hubs namespace connection string>";
    private static final String eventHubName = "<Event hub name>";
    private static final String storageConnectionString = "<Storage connection string>";
    private static final String storageContainerName = "<Storage container name>";
    ```
1. クラスに次の `main` メソッドを追加します。 

    ```java
    public static void main(String[] args) throws Exception {
        // Create a blob container client that you use later to build an event processor client to receive and process events
        BlobContainerAsyncClient blobContainerAsyncClient = new BlobContainerClientBuilder()
            .connectionString(storageConnectionString) 
            .containerName(storageContainerName) 
            .buildAsyncClient();
    
        // Create a builder object that you will use later to build an event processor client to receive and process events and errors.
        EventProcessorClientBuilder eventProcessorClientBuilder = new EventProcessorClientBuilder()
            .connectionString(connectionString, eventHubName) 
            .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
            .processEvent(PARTITION_PROCESSOR) 
            .processError(ERROR_HANDLER) 
            .checkpointStore(new BlobCheckpointStore(blobContainerAsyncClient)); 

        // Use the builder object to create an event processor client 
        EventProcessorClient eventProcessorClient = eventProcessorClientBuilder.buildEventProcessorClient();

        System.out.println("Starting event processor");
        eventProcessorClient.start();

        System.out.println("Press enter to stop.");
        System.in.read();

        System.out.println("Stopping event processor");
        eventProcessorClient.stop();
        System.out.println("Event processor stopped.");

        System.out.println("Exiting process");
    }    
    ```
4. イベントとエラーを処理する 2 つのヘルパー メソッド (`PARTITION_PROCESSOR` と `ERROR_HANDLER`) を `Receiver` クラスに追加します。 

    ```java
    public static final Consumer<EventContext> PARTITION_PROCESSOR = eventContext -> {
        System.out.printf("Processing event from partition %s with sequence number %d with body: %s %n", 
                eventContext.getPartitionContext().getPartitionId(), eventContext.getEventData().getSequenceNumber(), eventContext.getEventData().getBodyAsString());

        if (eventContext.getEventData().getSequenceNumber() % 10 == 0) {
            eventContext.updateCheckpoint();
        }
    };
    
    public static final Consumer<ErrorContext> ERROR_HANDLER = errorContext -> {
        System.out.printf("Error occurred in partition processor for partition %s, %s.%n",
            errorContext.getPartitionContext().getPartitionId(),
            errorContext.getThrowable());
    };    
    ```
3. プログラムをビルドし、エラーがないことを確認します。 

## <a name="run-the-applications"></a>アプリケーションの実行
1. まず、**受信側** アプリケーションを実行します。
1. つぎに、**送信側** アプリケーションを実行します。 
1. **受信者** アプリケーション ウィンドウで、送信側アプリケーションによって発行されたイベントが表示されていることを確認します。

    ```cmd
    Starting event processor
    Press enter to stop.
    Processing event from partition 0 with sequence number 331 with body: Foo 
    Processing event from partition 0 with sequence number 332 with body: Bar 
    ```
1. アプリケーションを停止するには、受信側アプリケーション ウィンドウで **Enter** キーを押します。 

    ```cmd
    Starting event processor
    Press enter to stop.
    Processing event from partition 0 with sequence number 331 with body: Foo 
    Processing event from partition 0 with sequence number 332 with body: Bar 
    
    Stopping event processor
    Event processor stopped.
    Exiting process
    ```

## <a name="next-steps"></a>次のステップ
GitHub の次のサンプルを参照してください。

- [azure-messaging-eventhubs サンプル](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [azure-messaging-eventhubs-checkpointstore-blob サンプル](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob)。  
