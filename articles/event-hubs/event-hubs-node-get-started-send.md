---
title: Node.js を使用して Azure Event Hubs にイベントを送信する | Microsoft Docs
description: Node.js を使用した Event Hubs へのイベントの送信について説明します
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 09/18/2018
ms.author: shvija
ms.openlocfilehash: 413f36a12dee135cc1a7dc99a34d8b7b2be6c46f
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801065"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Node.js を使用して Azure Event Hubs にイベントを送信する

Azure Event Hubs は、拡張性の高いイベント管理システムで、1 秒あたり何百万ものイベントを処理することができます。そのためアプリケーションは、接続されているデバイスや他のシステムによって生成された大量のデータを処理し、分析できます。 イベント ハブに収集されたイベントは、インプロセス ハンドラーを使用するか、他の分析システムに転送して、受信し処理することができます。

Event Hubs について詳しくは、[Event Hubs の概要](event-hubs-about.md)に関する記事をご覧ください。

このチュートリアルでは、Node.js で書かれたアプリケーションからイベント ハブにイベントを送信する方法について説明します。 Node.js イベント プロセッサ ホスト パッケージを使用してイベントを受信する方法については、[対応する受信の記事](event-hubs-node-get-started-receive.md)をご覧ください。

このクイック スタートのコードは、[GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client) で入手できます。 

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

- Node.js バージョン 8.x 以降。 [https://nodejs.org](https://nodejs.org) から最新の LTS バージョンをダウンロードします。
- アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント][] を作成してください。
- Visual Studio Code (推奨) または他の任意の IDE

## <a name="create-a-namespace-and-event-hub"></a>名前空間とイベント ハブを作成する
最初の手順として、Azure portal を使用して、Event Hubs 名前空間とイベント ハブを作成します。 既存のものがない場合は、[Azure portal を使用して Event Hubs 名前空間とイベント ハブを作成する方法](event-hubs-create.md)に関する記事の手順に従って、これらのエンティティを作成できます。

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
3. 自分のイベント ハブの接続文字列、イベント ハブ名、およびストレージ エンドポイントを構成します。 Azure portal 内のイベント ハブ ページの **RootManageSharedAccessKey** の下にある **[接続文字列 - プライマリ キー]** から自分のイベント ハブ用の接続文字列をコピーできます。 詳細な手順については、[接続文字列の取得](event-hubs-quickstart-portal.md#create-an-event-hubs-namespace)に関するページを参照してください。
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

Event Hubs の詳細については、次の記事を参照してください。

* [Node.js を使用したイベントの受信](event-hubs-node-get-started-receive.md)
* [GitHub のサンプル](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/)
* [Event Hubs の FAQ](event-hubs-faq.md)

<!-- Links -->
[無料アカウント]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
