---
title: Node.js を使用して Azure Event Hubs との間でイベントを送受信する (最新)
description: この記事では、最新の azure/event-hubs バージョン 5 パッケージを使用して、Azure Event Hubs との間でイベントを送受信する Node.js アプリケーションを作成する方法について説明します。
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: b523e4a7b463564cbfeb407c91b7bb05317f8166
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906375"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-nodejs--azureevent-hubs-version-5"></a>Node.js を使用して Event Hubs との間でイベントを送受信する (azure/event-hubs バージョン 5)

Azure Event Hubs はビッグ データ ストリーミング プラットフォームであり、毎秒数百万のイベントを受け取って処理できるイベント インジェスト サービスです。 Event Hubs では、分散されたソフトウェアやデバイスから生成されるイベント、データ、またはテレメトリを処理および格納できます。 イベント ハブに送信されたデータは、任意のリアルタイム分析プロバイダーやバッチ処理アダプター、ストレージ アダプターを使用して、変換および保存できます。 詳細については、[Event Hubs の概要](event-hubs-about.md)と [Event Hubs の機能](event-hubs-features.md)に関するページを参照してください。

このクイックスタートでは、イベント ハブとの間でイベントを送受信できる Node.js アプリケーションの作成方法について説明します。

> [!IMPORTANT]
> このクイックスタートでは、Azure Event Hubs JavaScript SDK のバージョン 5 を使用します。 JavaScript SDK のバージョン 2 を使用するクイック スタートについては、[こちらの記事](event-hubs-node-get-started-send.md)を参照してください。 

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、次の前提条件を用意しておく必要があります。

- Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)してください。  
- Node.js バージョン 8.x 以降。 最新の[長期サポート (LTS) バージョン](https://nodejs.org)をダウンロードしてください。  
- Visual Studio Code (推奨) または他の任意の統合開発環境 (IDE)。  
- アクティブな Event Hubs 名前空間とイベント ハブ。 これらを作成するには、次の手順を実行します。 

   1. [Azure portal](https://portal.azure.com) で *Event Hubs* 型の名前空間を作成し、アプリケーションがイベント ハブと通信するために必要な管理資格情報を取得します。 
   1. 名前空間とイベント ハブを作成するために、[Azure portal を使用したイベント ハブの作成に関するクイックスタート](event-hubs-create.md)の手順に従います。
   1. 続けてこのクイックスタートの手順に従います。 
   1. イベント ハブの名前空間の接続文字列を取得するために、[接続文字列の取得](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)に関するセクションの手順に従います。 接続文字列は記録しておいてください。後でこのクイックスタートの中で使用します。

### <a name="install-the-npm-package"></a>npm パッケージのインストール
[Event Hubs 用の Node Package Manager (npm) パッケージ](https://www.npmjs.com/package/@azure/event-hubs)をインストールするには、パスに *npm* を設定してコマンド プロンプトを開き、サンプルを格納するフォルダーにディレクトリを変更してから、このコマンドを実行します。

```shell
npm install @azure/event-hubs
```

受信側では、追加で 2 つのパッケージをインストールする必要があります。 このクイックスタートでは、既に読み取ったイベントをプログラムが読み取らないよう、Azure Blob Storage を使用してチェックポイントを永続化します。 BLOB 内で定期的に受信したメッセージのメタデータ チェックポイントが実行されます。 この手法によって、後で前回終了したところからメッセージを継続して受信しやすくなります。

次のコマンドを実行します。

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>送信イベント

このセクションでは、イベント ハブにイベントを送信する Node.js アプリケーションを作成します。

1. [Visual Studio Code](https://code.visualstudio.com) など、お好みのエディターを開きます。
1. *send.js* というファイルを作成し、そこに次のコードを貼り付けます。

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // Create a producer client to send messages to the event hub.
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // Prepare a batch of three events.
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // Send the batch to the event hub.
      await producer.sendBatch(batch);

      // Close the producer client.
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
1. このコード内の次の値は、実際の値に置き換えてください。
    * `EVENT HUBS NAMESPACE CONNECTION STRING` 
    * `EVENT HUB NAME`
1. `node send.js` を実行して、このファイルを実行します。 このコマンドにより、3 つのイベントのバッチがイベント ハブに送信されます
1. Azure portal で、イベント ハブがメッセージを受信したことを確認します。 **[メトリック]** セクションで **[メッセージ]** ビューに切り替えます。 ページを最新の情報に更新して、グラフを更新します。 メッセージが受信されたことが示されるまでに数秒かかることがあります。

    [![イベント ハブがメッセージを受信したことを確認する](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > 情報提供を目的とした補足コメントを含む完全なソース コードについては、[GitHub の sendEvents.js ページ](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js)にアクセスしてください。

お疲れさまでした。 これで、イベントをイベント ハブに送信できました。


## <a name="receive-events"></a>受信イベント
このセクションでは、Node.js アプリケーション内で Azure Blob Storage チェックポイント ストアを使用して、イベント ハブからイベントを受信します。 これにより、Azure Storage Blob 内で定期的に受信したメッセージのメタデータ チェックポイントが実行されます。 この手法によって、後で前回終了したところからメッセージを継続して受信しやすくなります。

### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Azure Storage アカウントと BLOB コンテナーを作成する
Azure Storage アカウントを作成して、そこに BLOB コンテナーを作成するには、次の作業を行います。

1. [Azure Storage アカウントを作成する](../storage/common/storage-account-create.md?tabs=azure-portal)  
2. [ストレージ アカウントに BLOB コンテナーを作成する](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
3. [ストレージ アカウントへの接続文字列を取得する](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

接続文字列とコンテナー名を記録しておいてください。後から受信コードで使用します。

### <a name="write-code-to-receive-events"></a>イベントを受信するコードを記述する

1. [Visual Studio Code](https://code.visualstudio.com) など、お好みのエディターを開きます。
1. *receive.js* というファイルを作成し、そこに次のコードを貼り付けます。

    ```javascript
    const { EventHubConsumerClient } = require("@azure/event-hubs");
    const { ContainerClient } = require("@azure/storage-blob");    
    const { BlobCheckpointStore } = require("@azure/eventhubs-checkpointstore-blob");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";    
    const eventHubName = "EVENT HUB NAME";
    const consumerGroup = "$Default"; // name of the default consumer group
    const storageConnectionString = "AZURE STORAGE CONNECTION STRING";
    const containerName = "BLOB CONTAINER NAME";

    async function main() {
      // Create a blob container client and a blob checkpoint store using the client.
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // Create a consumer client for the event hub by specifying the checkpoint store.
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // Subscribe to the events, and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // Update the checkpoint.
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // After 30 seconds, stop processing.
      await new Promise((resolve) => {
        setTimeout(async () => {
          await subscription.close();
          await consumerClient.close();
          resolve();
        }, 30000);
      });
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });    
    ```
1. このコード内の次の値は、実際の値に置き換えてください。
    - `EVENT HUBS NAMESPACE CONNECTION STRING`
    - `EVENT HUB NAME`
    - `AZURE STORAGE CONNECTION STRING`
    - `BLOB CONTAINER NAME`
1. コマンド プロンプトから `node receive.js` を実行して、このファイルを実行します。 受信したイベントに関するメッセージがウィンドウに表示されます。

    > [!NOTE]
    > 情報提供を目的とした補足コメントを含む完全なソース コードについては、[GitHub の receiveEventsUsingCheckpointStore.js ページ](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js)にアクセスしてください。

お疲れさまでした。 これで、イベント ハブからイベントを受信できました。 受信側プログラムは、イベント ハブの既定のコンシューマー グループのすべてのパーティションからイベントを受信します。

## <a name="next-steps"></a>次のステップ
GitHub で次のサンプルを確認します。

- [JavaScript のサンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [TypeScript のサンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
