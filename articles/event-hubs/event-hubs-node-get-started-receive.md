---
title: Node.js を使用してイベントを受信する - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Event Hubs からイベントを受信する Node.js アプリケーションを作成するためのチュートリアルを提供します。
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: d13f97ff7ec25fd638b92dc1c057afcaeecc2bf9
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53434071"
---
# <a name="receive-events-from-azure-event-hubs-using-nodejs"></a>Node.js を使用して Azure Event Hubs からイベントを受信する

Azure Event Hubs は、拡張性の高いイベント管理システムで、1 秒あたり何百万ものイベントを処理することができます。そのためアプリケーションは、接続されているデバイスや他のシステムによって生成された大量のデータを処理し、分析できます。 イベント ハブに収集されたイベントは、インプロセス ハンドラーを使用するか、他の分析システムに転送して、受信し処理することができます。 処理する前に、Azure Storage や Azure Data Lake Store でイベント データをキャプチャすることもできます。  

Event Hubs の詳細については、[Event Hubs の概要](event-hubs-about.md)に関する記事を参照してください。

このチュートリアルでは、Node.js アプリケーションで Azure [EventProcessorHost](event-hubs-event-processor-host.md) を使用して、イベント ハブからイベントを受信する方法を示します。 EventProcessorHost (EPH) を使用すると、イベント ハブの顧客グループのすべてのパーティションで受信者を作成することで、イベント ハブからイベントを効率的に受信できます。 これにより、Azure Storage Blob 内で定期的に受信したメッセージのメタデータがチェックポイントに設定されます。 この手法によって、後で前回終了したところからメッセージを継続して受信しやすくなります。

このクイックスタート用のコードは、[GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/processor) で入手できます。

> [!NOTE]
>  Node.js を使用して Event Hubs にイベントを送信するには、こちらの記事を参照してください:[Node.js を使用して Azure Event Hubs にイベントを送信する](event-hubs-node-get-started-send.md)。 

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

- Node.js バージョン 8.x 以降。 [https://nodejs.org](https://nodejs.org) から最新の LTS バージョンをダウンロードします。Node.js の古い LTS バージョンを使用しないでください。 
- アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント][] を作成してください。

## <a name="create-a-namespace-and-event-hub"></a>名前空間とイベント ハブを作成する
最初の手順として、Azure portal を使用して、Event Hubs 名前空間とイベント ハブを作成します。 既存のものがない場合は、[Azure portal を使用して Event Hubs 名前空間とイベント ハブを作成する方法](event-hubs-create.md)に関する記事の手順に従って、これらのエンティティを作成できます。

## <a name="create-a-storage-account-and-container"></a>ストレージ アカウントとコンテナーの作成
EventProcessorHost を使用するには、Azure Storage アカウントが必要です。 パーティションのリースやイベント ストリーム内のチェックポイントなどの状態情報は、Azure Storage コンテナーを使用して受信者間で共有されます。 Azure Storage アカウントは、[こちらの記事](../storage/common/storage-quickstart-create-account.md)の手順に従って作成できます。

## <a name="clone-the-git-repository"></a>Git リポジトリを複製する
GitHub から[サンプル](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/)をダウンロードまたは複製します。 

## <a name="install-the-eventprocessorhost"></a>EventProcessorHost をインストールする
Event Hubs モジュール用の EventProcessorHost をインストールします。 

```nodejs
npm install @azure/event-processor-host
```

## <a name="receive-events-using-eventprocessorhost"></a>EventProcessorHost を使用してイベントを受信する
複製した SDK には、Node.js を使用してイベント ハブからイベントを受信する方法を示す、複数のサンプルが含まれます。 このクイックスタートでは、**singleEPH.js** の例を使用します。 受信しているイベントを確認するには、別のターミナルを開き、[送信のサンプル](event-hubs-node-get-started-send.md)を使用してイベントを送信します。

1. Visual Studio Code でプロジェクトを開きます。 
2. **processor** フォルダーの下で **.env** という名前のファイルを作成します。 ルート フォルダーの **sample.env** からサンプルの環境変数をコピーして貼り付けます。
3. 自分のイベント ハブの接続文字列、イベント ハブ名、およびストレージ エンドポイントを構成します。 Azure portal 内のイベント ハブ ページの **RootManageSharedAccessKey** の下にある **[接続文字列 – プライマリ]** からご自分のイベント ハブ用の接続文字列をコピーできます。 詳細な手順については、[接続文字列の取得](event-hubs-create.md#create-an-event-hubs-namespace)に関するページを参照してください。
4. ご利用の Azure CLI で、**processor** フォルダー パスに移動します。 次のコマンドを実行して、ノード パッケージをインストールし、プロジェクトをビルドします。

    ```nodejs
    npm i
    npm run build
    ```
5. 次のコマンドを実行して、ご利用のイベント プロセッサ ホストでイベントを受信します。

    ```nodejs
    node dist/examples/singleEph.js
    ```

## <a name="review-the-sample-code"></a>サンプル コードを確認する 
Node.js を使用してイベント ハブからイベントを受信するサンプル コードを次に示します。 手動で sampleEph.js ファイルを作成して実行し、イベント ハブへのイベントを受信できます。 

  ```nodejs
  const { EventProcessorHost, delay } = require("@azure/event-processor-host");

  const path = process.env.EVENTHUB_NAME;
  const storageCS = process.env.STORAGE_CONNECTION_STRING;
  const ehCS = process.env.EVENTHUB_CONNECTION_STRING;
  const storageContainerName = "test-container";
  
  async function main() {
    // Create the Event Processo Host
    const eph = EventProcessorHost.createFromConnectionString(
      EventProcessorHost.createHostName("my-host"),
      storageCS,
      storageContainerName,
      ehCS,
      {
        eventHubPath: path
      },
      onEphError: (error) => {
        console.log("This handler will notify you of any internal errors that happen " +
        "during partition and lease management: %O", error);
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

```
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

その他のサンプルについては、[こちら](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples)を参照してください。


## <a name="next-steps"></a>次の手順

このクイック スタートでは、イベント ハブからメッセージを受信する Node.js アプリケーションを作成しました。 Node.js を使用してイベント ハブにイベントを送信する方法については、[ を使用してイベント ハブからイベントを送信すること](event-hubs-node-get-started-send.md)に関するページを参照してください。

<!-- Links -->
[無料アカウント]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
