---
title: Node.js を使用してイベントを送受信する - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Event Hubs からイベントを送信する Node.js アプリケーションを作成するためのチュートリアルを提供します。
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: spelluru
ms.openlocfilehash: f03bfde8f7ea37989756ad47678369e94b831438
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59677903"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Node.js を使用して Azure Event Hubs との間でイベントを送受信する | Microsoft Docs

Azure Event Hubs はビッグ データ ストリーミング プラットフォームであり、毎秒数百万のイベントを受け取って処理できるイベント インジェスト サービスです。 Event Hubs では、分散されたソフトウェアやデバイスから生成されるイベント、データ、またはテレメトリを処理および格納できます。 イベント ハブに送信されたデータは、任意のリアルタイム分析プロバイダーやバッチ処理/ストレージ アダプターを使用して、変換および保存できます。 Event Hubs の詳しい概要については、[Event Hubs の概要](event-hubs-about.md)と [Event Hubs の機能](event-hubs-features.md)に関するページをご覧ください。

このチュートリアルでは、Node.js アプリケーションを作成し、イベント ハブとの間でイベントを送受信する方法について説明します。

> [!NOTE]
> このクイック スタートをサンプルとして [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client) からダウンロードし、`EventHubConnectionString` と `EventHubName` の文字列を対象のイベント ハブの値に置き換え、実行します。 または、このチュートリアルの手順に従って独自のものを作成します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

- アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。
- Node.js バージョン 8.x 以降。 [https://nodejs.org](https://nodejs.org) から最新の LTS バージョンをダウンロードします。
- Visual Studio Code (推奨) または他の任意の IDE
- **Event Hubs 名前空間とイベント ハブを作成する**。 最初の手順では、[Azure Portal](https://portal.azure.com) を使用して Event Hubs 型の名前空間を作成し、アプリケーションがイベント ハブと通信するために必要な管理資格情報を取得します。 名前空間とイベント ハブを作成するには、[こちらの記事](event-hubs-create.md)の手順を実行した後、このチュートリアルに示されている手順を続けます。 その後、次の記事の手順に従って、イベント ハブ名前空間用の接続文字列を取得します。[接続文字列を取得する](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 このチュートリアルの中で、その接続文字列が後で必要になります。
- マシン上で[サンプルの GitHub リポジトリ](https://github.com/Azure/azure-event-hubs-node)を複製する。 


## <a name="send-events"></a>送信イベント
このセクションでは、イベント ハブにイベントを送信する Node.js アプリケーションの作成方法を説明します。 

### <a name="install-nodejs-package"></a>Node.js パッケージをインストールする
Azure Event Hubs 用の Node.js パッケージをお使いのコンピューターにインストールします。 

```shell
npm install @azure/event-hubs
```

前提条件の説明に従って Git リポジトリを複製していない場合は、GitHub から[サンプル](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples)をダウンロードしてください。 

複製した SDK には、Node.js を使用してイベント ハブにイベントを送信する方法を示す、複数のサンプルが含まれます。 このクイック スタートでは、**simpleSender.js** の例を使用します。 受信しているイベントを確認するには、別のターミナルを開き、[受信のサンプル](event-hubs-node-get-started-receive.md)を使用してイベントを受信します。

1. Visual Studio Code でプロジェクトを開きます。 
2. **client** フォルダーに **.env** という名前のファイルを作成します。 ルート フォルダーの **sample.env** からサンプルの環境変数をコピーして貼り付けます。
3. 自分のイベント ハブの接続文字列、イベント ハブ名、およびストレージ エンドポイントを構成します。 イベント ハブの接続文字列を取得する方法については、「[接続文字列を取得する](event-hubs-create.md#create-an-event-hubs-namespace)」をご覧ください。
4. Azure CLI で、**client** フォルダー パスに移動します。 次のコマンドを実行して、ノード パッケージをインストールし、プロジェクトをビルドします。

    ```shell
    npm i
    npm run build
    ```
5. 次のコマンドを実行して、イベントの送信を開始します。 

    ```shell
    node dist/examples/simpleSender.js
    ```


### <a name="review-the-sample-code"></a>サンプル コードを確認する 
イベントをイベント ハブに送信するサンプル コードを simpleSender.js ファイルで確認します。

```javascript
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
const lib_1 = require("../lib");
const dotenv = require("dotenv");
dotenv.config();
const connectionString = "EVENTHUB_CONNECTION_STRING";
const entityPath = "EVENTHUB_NAME";
const str = process.env[connectionString] || "";
const path = process.env[entityPath] || "";

async function main() {
    const client = lib_1.EventHubClient.createFromConnectionString(str, path);
    const data = {
        body: "Hello World!!"
    };
    const delivery = await client.send(data);
    console.log(">>> Sent the message successfully: ", delivery.tag.toString());
    console.log(delivery);
    console.log("Calling rhea-promise sender close directly. This should result in sender getting reconnected.");
    await Object.values(client._context.senders)[0]._sender.close();
    // await client.close();
}

main().catch((err) => {
    console.log("error: ", err);
});

```

スクリプトを実行する前に、環境変数を設定することを忘れないでください。 次の例に示すように、コマンド ラインでこれを構成するか、[dotenv パッケージ](https://www.npmjs.com/package/dotenv#dotenv)を使用することができます。 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

## <a name="receive-events"></a>受信イベント
このチュートリアルでは、Node.js アプリケーションで Azure [EventProcessorHost](event-hubs-event-processor-host.md) を使用して、イベント ハブからイベントを受信する方法を示します。 EventProcessorHost (EPH) を使用すると、イベント ハブの顧客グループのすべてのパーティションで受信者を作成することで、イベント ハブからイベントを効率的に受信できます。 これにより、Azure Storage Blob 内で定期的に受信したメッセージのメタデータがチェックポイントに設定されます。 この手法によって、後で前回終了したところからメッセージを継続して受信しやすくなります。

このクイックスタート用のコードは、[GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/processor) で入手できます。

### <a name="clone-the-git-repository"></a>Git リポジトリを複製する
GitHub から[サンプル](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/)をダウンロードまたは複製します。 

### <a name="install-the-eventprocessorhost"></a>EventProcessorHost をインストールする
Event Hubs モジュール用の EventProcessorHost をインストールします。 

```shell
npm install @azure/event-processor-host
```

### <a name="receive-events-using-eventprocessorhost"></a>EventProcessorHost を使用してイベントを受信する
複製した SDK には、Node.js を使用してイベント ハブからイベントを受信する方法を示す、複数のサンプルが含まれます。 このクイックスタートでは、**singleEPH.js** の例を使用します。 受信しているイベントを確認するには、別のターミナルを開き、[送信のサンプル](event-hubs-node-get-started-send.md)を使用してイベントを送信します。

1. Visual Studio Code でプロジェクトを開きます。 
2. **processor** フォルダーの下で **.env** という名前のファイルを作成します。 ルート フォルダーの **sample.env** からサンプルの環境変数をコピーして貼り付けます。
3. 自分のイベント ハブの接続文字列、イベント ハブ名、およびストレージ エンドポイントを構成します。 Azure portal 内のイベント ハブ ページの **RootManageSharedAccessKey** の下にある **[接続文字列 – プライマリ]** からご自分のイベント ハブ用の接続文字列をコピーできます。 詳細な手順については、[接続文字列の取得](event-hubs-create.md#create-an-event-hubs-namespace)に関するページを参照してください。
4. ご利用の Azure CLI で、**processor** フォルダー パスに移動します。 次のコマンドを実行して、ノード パッケージをインストールし、プロジェクトをビルドします。

    ```shell
    npm i
    npm run build
    ```
5. 次のコマンドを実行して、ご利用のイベント プロセッサ ホストでイベントを受信します。

    ```shell
    node dist/examples/singleEph.js
    ```

### <a name="review-the-sample-code"></a>サンプル コードを確認する 
Node.js を使用してイベント ハブからイベントを受信するサンプル コードを次に示します。 手動で sampleEph.js ファイルを作成して実行し、イベント ハブへのイベントを受信できます。 

  ```javascript
  const { EventProcessorHost, delay } = require("@azure/event-processor-host");

  const path = process.env.EVENTHUB_NAME;
  const storageCS = process.env.STORAGE_CONNECTION_STRING;
  const ehCS = process.env.EVENTHUB_CONNECTION_STRING;
  const storageContainerName = "test-container";
  
  async function main() {
    // Create the Event Processor Host
    const eph = EventProcessorHost.createFromConnectionString(
      EventProcessorHost.createHostName("my-host"),
      storageCS,
      storageContainerName,
      ehCS,
      {
        eventHubPath: path,
        onEphError: (error) => {
          console.log("This handler will notify you of any internal errors that happen " +
          "during partition and lease management: %O", error);
        }
      }
    );
    let count = 0;
    // Message event handler
    const onMessage = async (context/*PartitionContext*/, data /*EventData*/) => {
      console.log(">>>>> Rx message from '%s': '%s'", context.partitionId, data.body);
      count++;
      // let us checkpoint every 100th message that is received across all the partitions.
      if (count % 100 === 0) {
        return await context.checkpoint();
      }
    };
    // Error event handler
    const onError = (error) => {
      console.log(">>>>> Received Error: %O", error);
    };
    // start the EPH
    await eph.start(onMessage, onError);
    // After some time let' say 2 minutes
    await delay(120000);
    // This will stop the EPH.
    await eph.stop();
  }
  
  main().catch((err) => {
    console.log(err);
  });
      
  ```

スクリプトを実行する前に、環境変数を設定することを忘れないでください。 次の例に示すように、コマンド ラインでこれを構成するか、[dotenv パッケージ](https://www.npmjs.com/package/dotenv#dotenv)を使用することができます。 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

その他のサンプルについては、[こちら](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples)を参照してください。


## <a name="next-steps"></a>次の手順
次の記事を参照してください。

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure Event Hubs の機能と用語](event-hubs-features.md)
- [Event Hubs の FAQ](event-hubs-faq.md)
- Event Hubs に関する他の Node.js のサンプルを [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/) で確認してください。
