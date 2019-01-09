---
title: Node.js を使用してイベントを送信する - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Event Hubs からイベントを送信する Node.js アプリケーションを作成するためのチュートリアルを提供します。
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: c75d4404cb0892c3d90261af2fb4982ac84041c4
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53163788"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Node.js を使用して Azure Event Hubs にイベントを送信する

Azure Event Hubs はビッグ データ ストリーミング プラットフォームであり、毎秒数百万のイベントを受け取って処理できるイベント インジェスト サービスです。 Event Hubs では、分散されたソフトウェアやデバイスから生成されるイベント、データ、またはテレメトリを処理および格納できます。 イベント ハブに送信されたデータは、任意のリアルタイム分析プロバイダーやバッチ処理/ストレージ アダプターを使用して、変換および保存できます。 Event Hubs の詳しい概要については、[Event Hubs の概要](event-hubs-about.md)と [Event Hubs の機能](event-hubs-features.md)に関するページをご覧ください。

このチュートリアルでは、Node.js で書かれたアプリケーションからイベント ハブにイベントを送信する方法について説明します。

> [!NOTE]
> このクイック スタートをサンプルとして [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client) からダウンロードし、`EventHubConnectionString` と `EventHubName` の文字列を対象のイベント ハブの値に置き換え、実行します。 または、このチュートリアルの手順に従って独自のものを作成します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

- Node.js バージョン 8.x 以降。 [https://nodejs.org](https://nodejs.org) から最新の LTS バージョンをダウンロードします。
- Visual Studio Code (推奨) または他の任意の IDE

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs 名前空間とイベント ハブを作成する
最初の手順では、[Azure Portal](https://portal.azure.com) を使用して Event Hubs 型の名前空間を作成し、アプリケーションがイベント ハブと通信するために必要な管理資格情報を取得します。 名前空間とイベント ハブを作成するには、[こちらの記事](event-hubs-create.md)の手順を実行した後、このチュートリアルに示されている手順に進みます。

次の記事の手順に従って、イベント ハブ名前空間用の接続文字列を取得します。[接続文字列を取得する](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 このチュートリアルの中で、その接続文字列が後で必要になります。

## <a name="clone-the-sample-git-repository"></a>サンプル Git リポジトリを複製する
サンプルの Git リポジトリを [GitHub](https://github.com/Azure/azure-event-hubs-node) からお使いのコンピューターに複製します。 

## <a name="install-nodejs-package"></a>Node.js パッケージをインストールする
Azure Event Hubs 用の Node.js パッケージをお使いのコンピューターにインストールします。 

```nodejs
npm install @azure/event-hubs
```

## <a name="clone-the-git-repository"></a>Git リポジトリを複製する
GitHub から[サンプル](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples)をダウンロードまたは複製します。 

## <a name="send-events"></a>送信イベント
複製した SDK には、Node.js を使用してイベント ハブにイベントを送信する方法を示す、複数のサンプルが含まれます。 このクイック スタートでは、**simpleSender.js** の例を使用します。 受信しているイベントを確認するには、別のターミナルを開き、[受信のサンプル](event-hubs-node-get-started-receive.md)を使用してイベントを受信します。

1. Visual Studio Code でプロジェクトを開きます。 
2. **client** フォルダーに **.env** という名前のファイルを作成します。 ルート フォルダーの **sample.env** からサンプルの環境変数をコピーして貼り付けます。
3. 自分のイベント ハブの接続文字列、イベント ハブ名、およびストレージ エンドポイントを構成します。 Azure portal 内のイベント ハブ ページの **RootManageSharedAccessKey** の下にある **[接続文字列 – プライマリ]** からご自分のイベント ハブ用の接続文字列をコピーできます。 詳細な手順については、[接続文字列の取得](event-hubs-create.md#create-an-event-hubs-namespace)に関するページを参照してください。
4. Azure CLI で、**client** フォルダー パスに移動します。 次のコマンドを実行して、ノード パッケージをインストールし、プロジェクトをビルドします。

    ```nodejs
    npm i
    npm run build
    ```
5. 次のコマンドを実行して、イベントの送信を開始します。 

    ```nodejs
    node dist/examples/simpleSender.js
    ```


## <a name="review-the-sample-code"></a>サンプル コードを確認する 
Node.js を使用してイベント ハブにイベントを送信するサンプル コードを次に示します。 手動で sampleSender.js ファイルを作成して実行し、イベント ハブにイベントを送信できます。 


```nodejs
const { EventHubClient, EventPosition } = require('@azure/event-hubs');

const client = EventHubClient.createFromConnectionString(process.env["EVENTHUB_CONNECTION_STRING"], process.env["EVENTHUB_NAME"]);

async function main() {
    // NOTE: For receiving events from Azure Stream Analytics, please send Events to an EventHub where the body is a JSON object/array.
    // const eventData = { body: { "message": "Hello World" } };
    const data = { body: "Hello World 1" };
    const delivery = await client.send(data);
    console.log("message sent successfully.");
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

## <a name="next-steps"></a>次の手順
このクイック スタートでは、Node.js を使用してメッセージをイベント ハブに送信しました。 Node.js を使用してイベント ハブからイベントを受信する方法については、[Node.js を使用してイベント ハブからイベントを受信する方法](event-hubs-node-get-started-receive.md)に関するページをご覧ください。

Event Hubs に関する他の Node.js のサンプルを [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/) で確認してください。
