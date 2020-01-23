---
title: Node.js を使用してイベントを送受信する - Azure Event Hubs
description: この記事では、Azure Event Hubs からイベントを送信する Node.js アプリケーションを作成するためのチュートリアルを提供します。
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/09/2020
ms.author: spelluru
ms.openlocfilehash: d4810c325acc42d5aa665002654cb01154cdc6bb
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981610"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Node.js を使用して Azure Event Hubs との間でイベントを送受信する | Microsoft Docs

Azure Event Hubs はビッグ データ ストリーミング プラットフォームであり、毎秒数百万のイベントを受け取って処理できるイベント インジェスト サービスです。 Event Hubs では、分散されたソフトウェアやデバイスから生成されるイベント、データ、またはテレメトリを処理および格納できます。 イベント ハブに送信されたデータは、任意のリアルタイム分析プロバイダーやバッチ処理/ストレージ アダプターを使用して、変換および保存できます。 Event Hubs の詳しい概要については、[Event Hubs の概要](event-hubs-about.md)と [Event Hubs の機能](event-hubs-features.md)に関するページをご覧ください。

このチュートリアルでは、Node.js アプリケーションを作成し、イベント ハブとの間でイベントを送受信する方法について説明します。

> [!IMPORTANT]
> このクイックスタートでは、Azure Event Hubs JavaScript SDK のバージョン 5 を使用します。 JavaScript SDK の古いバージョン 2 を使用するクイック スタートについては、[こちらの記事](event-hubs-node-get-started-send.md)を参照してください。 SDK のバージョン 2 を使用している場合は、コードを最新バージョンに移行することをお勧めします。 詳細については、[移行ガイド](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

- アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。
- Node.js バージョン 8.x 以降。 [https://nodejs.org](https://nodejs.org) から最新の LTS バージョンをダウンロードします。
- Visual Studio Code (推奨) または他の任意の IDE
- **Event Hubs 名前空間とイベント ハブを作成する**。 最初の手順では、[Azure Portal](https://portal.azure.com) を使用して Event Hubs 型の名前空間を作成し、アプリケーションがイベント ハブと通信するために必要な管理資格情報を取得します。 名前空間とイベント ハブを作成するには、[こちらの記事](event-hubs-create.md)の手順を実行した後、このチュートリアルに示されている手順を続けます。 その後、次の記事の手順に従って、イベント ハブ名前空間用の接続文字列を取得します。[接続文字列を取得する](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 このチュートリアルの中で、その接続文字列が後で必要になります。


### <a name="install-npm-packages"></a>npm パッケージのインストール
[Event Hubs 用の npm パッケージ](https://www.npmjs.com/package/@azure/event-hubs)をインストールするには、パスに `npm` を設定してコマンド プロンプトを開き、サンプルを格納するフォルダーにディレクトリを変更してから、このコマンドを実行します。

```shell
npm install @azure/event-hubs
```

受信側では、追加で 2 つのパッケージをインストールする必要があります。 このクイックスタートでは、Azure Blob Storage を使用してチェックポイントを永続化し、プログラムが既に読み取ったイベントを読み取らないようにします。 これにより、BLOB 内で定期的に受信したメッセージのメタデータがチェックポイントに設定されます。 この手法によって、後で前回終了したところからメッセージを継続して受信しやすくなります。

```shell
npm install @azure/storage-blob
```

```shell
npm install @azure/eventhubs-checkpointstore-blob
```

## <a name="send-events"></a>送信イベント

このセクションでは、イベント ハブにイベントを送信する Node.js アプリケーションの作成方法を説明します。

1. [Visual Studio Code](https://code.visualstudio.com) など、お好みのエディターを開きます。
2. `send.js` というファイルを作成し、そこに次のコードを貼り付けます。

    ```javascript
    const { EventHubProducerClient } = require("@azure/event-hubs");

    const connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
    const eventHubName = "EVENT HUB NAME";

    async function main() {

      // create a producer client to send messages to the event hub
      const producer = new EventHubProducerClient(connectionString, eventHubName);

      // prepare a batch of three events
      const batch = await producer.createBatch();
      batch.tryAdd({ body: "First event" });
      batch.tryAdd({ body: "Second event" });
      batch.tryAdd({ body: "Third event" });    

      // send the batch to the event hub
      await producer.sendBatch(batch);

      // close the producer client
      await producer.close();

      console.log("A batch of three events have been sent to the event hub");
    }

    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. コード内の**接続文字列**と**イベント ハブ名**の値を必ず置き換えてください。
5. コマンド `node send.js` を実行して、このファイルを実行します。 これにより、3 つのイベントのバッチがイベント ハブに送信されます
6. Azure portal で、イベント ハブがメッセージを受信したことを確認できます。 **[メトリック]** セクションで **[メッセージ]** ビューに切り替えます。 ページを最新の情報に更新して、グラフを更新します。 メッセージが受信されたことが示されるまでに数秒かかることがあります。

    [![イベント ハブがメッセージを受信したことを確認する](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > より多くの情報を含むコメント付きの完全なソース コードについては、[GitHub 上のこちらのファイル](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/samples/javascript/sendEvents.js)を参照してください

お疲れさまでした。 これで、イベントをイベント ハブに送信できました。


## <a name="receive-events"></a>受信イベント
このセクションでは、Node.js アプリケーション内で Azure BLOB チェックポイント ストアを使用して、イベント ハブからイベントを受信する方法を示します。 これにより、Azure Storage Blob 内で定期的に受信したメッセージのメタデータがチェックポイントに設定されます。 この手法によって、後で前回終了したところからメッセージを継続して受信しやすくなります。

### <a name="create-an-azure-storage-and-a-blob-container"></a>Azure Storage と BLOB コンテナーを作成する
次の手順に従って、Azure ストレージ アカウントを作成し、その中に BLOB コンテナーを作成します。

1. [Azure ストレージ アカウントの作成](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [BLOB コンテナーを作成する](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [ストレージ アカウントへの接続文字列を取得する](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    接続文字列とコンテナー名を書き留めておきます。 これらは、受信コードで使用します。

### <a name="write-code-to-receive-events"></a>イベントを受信するコードを記述する

1. [Visual Studio Code](https://code.visualstudio.com) など、お好みのエディターを開きます。
2. `receive.js` というファイルを作成し、そこに次のコードを貼り付けます。 詳細については、コードのコメントを参照してください。
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
      // create a blob container client and a blob checkpoint store using the client
      const containerClient = new ContainerClient(storageConnectionString, containerName);
      const checkpointStore = new BlobCheckpointStore(containerClient);

      // create a consumer client for the event hub by specifying the checkpoint store
      const consumerClient = new EventHubConsumerClient(consumerGroup, connectionString, eventHubName, checkpointStore);

      // subscribe for the events and specify handlers for processing the events and errors.
      const subscription = consumerClient.subscribe({
          processEvents: async (events, context) => {
            for (const event of events) {
              console.log(`Received event: '${event.body}' from partition: '${context.partitionId}' and consumer group: '${context.consumerGroup}'`);
            }
            // update the checkpoint
            await context.updateCheckpoint(events[events.length - 1]);
          },

          processError: async (err, context) => {
            console.log(`Error : ${err}`);
          }
        }
      );

      // after 30 seconds, stop processing
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
3. コードで**次の値**を必ず指定してください。
    - Event Hubs 名前空間への接続文字列
    - イベント ハブの名前
    - Azure ストレージ アカウントへの接続文字列
    - BLOB コンテナーの名前
5. その後、コマンド プロンプトで `node receive.js` コマンドを実行して、このファイルを実行します。 受信したイベントに関するメッセージがウィンドウに表示されます。

    > [!NOTE]
    > より多くの情報を含むコメント付きの完全なソース コードについては、[GitHub 上のこちらのファイル](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsUsingCheckpointStore.js)を参照してください。

お疲れさまでした。 これで、イベント ハブからイベントを受信できました。 受信側プログラムは、お使いのイベント ハブの既定のコンシューマー グループのすべてのパーティションからイベントを受信します

## <a name="next-steps"></a>次のステップ
GitHub で次のサンプルを確認します。

- [JavaScript のサンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [TypeScript のサンプル](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
