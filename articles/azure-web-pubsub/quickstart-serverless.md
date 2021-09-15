---
title: チュートリアル - Azure Web PubSub サービスと Azure Functions を使用してサーバーレス チャットを作成する
description: Azure Web PubSub サービスと Azure Functions を使用して、サーバーレス アプリケーションを作成する方法を説明するチュートリアルです。
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 03/11/2021
ms.openlocfilehash: 466da06b470774c67fb93eca5cc027edb16bcbc7
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446168"
---
# <a name="tutorial-create-a-serverless-chat-with-azure-functions-and-azure-web-pubsub-service"></a>チュートリアル: Azure Functions と Azure Web PubSub サービスを使用してサーバーレス チャットを作成する

Azure Web PubSub サービスは、WebSocket とパブリッシュ-サブスクライブ パターンを使用して、リアルタイム メッセージング Web アプリケーションを作成するのに役立ちます。 Azure Functions は、インフラストラクチャを管理することなくコードを実行できるサーバーレス プラットフォームです。 このチュートリアルでは、Azure Web PubSub サービスと Azure Functions を使用して、リアルタイム メッセージングとパブリッシュ-サブスクライブ パターンによるサーバーレス アプリケーションを作成する方法について説明します。

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> * Web PubSub サービス インスタンスを作成する
> * サンプル関数をローカルで実行する
> * イベントとメッセージをアプリケーションにルーティングするように Web PubSub イベント ハンドラーを構成する

## <a name="prerequisites"></a>必須コンポーネント

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[Visual Studio Code](https://code.visualstudio.com/) などのコード エディターをインストールし、さらに [Node.js](https://nodejs.org/en/download/) バージョン 10.x ライブラリをインストールします。

   > [!NOTE]
   > サポートされているバージョンの Node.js の詳細については、[Azure Functions ランタイム バージョンのドキュメント](../azure-functions/functions-versions.md#languages)を参照してください。

Azure Function アプリをローカルで実行するために、[Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (バージョン 2.7.1505 以降) をインストールします。

# <a name="c"></a>[C#](#tab/csharp)

コード エディター ([Visual Studio Code](https://code.visualstudio.com/) など) をインストールします。

Azure Function アプリをローカルで実行するために、[Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (バージョン 3 以降) をインストールします。

---

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [create-instance-portal](includes/create-instance-portal.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

サービスのデプロイ中、コードでの作業に切り替えましょう。 最初の手順として、[GitHub からサンプル アプリ](https://github.com/Azure/azure-webpubsub/tree/main/samples/functions/js/simplechat)をクローンします。

1. git ターミナル ウィンドウを開きます。 サンプル プロジェクトのクローン先となるフォルダーに移動します。

1. 次のコマンドを実行して、サンプル レポジトリを複製します。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。

    ```bash
    git clone https://github.com/Azure/azure-webpubsub.git
    ```

## <a name="configure-and-run-the-azure-function-app"></a>Azure Function アプリを構成して実行する

- ブラウザーで **Azure portal** を開き、先ほどデプロイした Web PubSub サービス インスタンスが正常に作成されていることを確認します。 そのインスタンスに移動します。
- **[キー]** を選択し、接続文字列をコピーします。

:::image type="content" source="media/quickstart-serverless/copy-connection-string.png" alt-text="Web PubSub 接続文字列のコピーのスクリーンショット。":::

# <a name="javascript"></a>[JavaScript](#tab/javascript)

- 関数の構成を更新する。

  クローンしたリポジトリの */samples/functions/js/simplechat* フォルダーを開きます。 *local.settings.json* を編集して、サービスの接続文字列を追加します。
  これらの変更を *local.settings.json* に加えたら、ファイルを保存します。
    - プレース ホルダー `<connection-string>` は、**Azure portal** の **`WebPubSubConnectionString`** 設定からコピーした実際の文字列に置き換えます。 
    - [Azure Functions には Azure Storage アカウントが必須](../azure-functions/storage-considerations.md)であるため、 **`AzureWebJobsStorage`** の設定が必要になります。
        - [Azure ストレージ エミュレーター](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409)がローカルで実行されている場合、"UseDevelopmentStorage=true" という元の設定を維持します。
        - Azure ストレージの接続文字列がある場合は、その値に置き換えます。
 
- フォルダーに JavaScript 関数が編成されます。 各フォルダーには、関数で使用されるバインディングを定義する `function.json` と、関数の本体である `index.js` という 2 つのファイルがあります。 この関数アプリには、トリガーによって作動する関数がいくつかあります。

    - **login** - この関数は HTTP によってトリガーされる関数です。 *webPubSubConnection* 入力バインディングを使用して、有効なサービス接続情報を生成して返します。
    - **messages** - この関数は `WebPubSubTrigger` によってトリガーされる関数です。 要求本文でチャット メッセージを受信し、`WebPubSub` 出力バインディングを使用して、接続されているすべてのクライアント アプリケーションにメッセージをブロードキャストします。
    - **connect** および **connected** - これらの関数は `WebPubSubTrigger` によってトリガーされる関数です。 connect と connected のイベントを処理します。

- ターミナルで、現在のフォルダーが */samples/functions/js/simplechat* であることを確認します。 拡張機能をインストールし、関数アプリを実行します。

    ```bash
    func extensions install

    func start
    ```

# <a name="c"></a>[C#](#tab/csharp)

- 関数の構成を更新する。

  クローンしたリポジトリで */samples/functions/csharp/simplechat* フォルダーを開きます。 *local.settings.json* を編集して、サービスの接続文字列を追加します。
  これらの変更を *local.settings.json* に加えたら、ファイルを保存します。
    - プレース ホルダー `<connection-string>` は、**Azure portal** の **`WebPubSubConnectionString`** 設定からコピーした実際の文字列に置き換えます。 
    - [Azure Functions には Azure Storage アカウントが必須](../azure-functions/storage-considerations.md)であるため、 **`AzureWebJobsStorage`** の設定が必要になります。
        - [Azure ストレージ エミュレーター](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409)がローカルで実行されている場合、"UseDevelopmentStorage=true" という元の設定を維持します。
        - Azure ストレージの接続文字列がある場合は、その値に置き換えます。

- C# 関数はファイル Functions.cs で整理されます。 この関数アプリには、トリガーによって作動する関数がいくつかあります。

    - **login** - この関数は HTTP によってトリガーされる関数です。 *webPubSubConnection* 入力バインディングを使用して、有効なサービス接続情報を生成して返します。
    - **connected** - この関数は `WebPubSubTrigger` によってトリガーされる関数です。 要求本文でチャット メッセージを受信し、接続されているすべてのクライアント アプリケーションにメッセージを、複数のタスクでブロードキャストします。
    - **broadcast** - この関数は `WebPubSubTrigger` によってトリガーされる関数です。 要求本文でチャット メッセージを受信し、接続されているすべてのクライアント アプリケーションにメッセージを、1 つのタスクでブロードキャストします。
    - **connect** と **disconnect** - これらの関数は `WebPubSubTrigger` によってトリガーされる関数です。 接続を処理し、イベントを切断します。

- ターミナルで、現在のフォルダーが */samples/functions/csharp/simplechat* であることを確認します。 拡張機能をインストールし、関数アプリを実行します。

    ```bash
    func extensions install

    func start
    ```

---

- ローカル関数では、`local.settings.json` ファイルに定義されているポートが使用されます。 それを公衆ネットワークから利用できるようにするためには、[ngrok](https://ngrok.com) を使用して、このエンドポイントを公開する必要があります。 次のコマンドを実行すると、転送エンドポイント (例: http://{ngrok-id}.ngrok.io -> http://localhost:7071 ) が返されます。

    ```bash
    ngrok http 7071
    ``` 

- Azure Web PubSub サービスに `Event Handler` を設定します。 **Azure portal** に移動して、自分の Web PubSub リソースを見つけ、 **[設定]** に移動します。 次のように、新しいハブ設定と使用中の 1 つの関数とのマッピングを追加します。 {ngrok-id} は、実際の文字列に置き換えてください。

   - ハブ名: `simplechat`
   - URL テンプレート: **http://{ngrok-id}.ngrok.io/runtime/webhooks/webpubsub**
   - ユーザー イベント パターン: *
   - システム イベント: connect、connected、disconnected

:::image type="content" source="media/quickstart-serverless/set-event-handler.png" alt-text="イベント ハンドラーの設定のスクリーンショット。":::

## <a name="run-the-web-application"></a>Web アプリケーションの実行

1. クライアントのテストを簡素化するには、ブラウザーでサンプルの[シングル ページ Web アプリケーション](http://jialinxin.github.io/webpubsub/)を開きます。 

1. 関数アプリのベース URL をローカルとして入力します (`http://localhost:7071`)。

1. ユーザー名を入力します。

1. Web アプリケーションは関数アプリで *login* 関数を呼び出して、Azure Web PubSub サービスに接続するための接続情報を取得します。 `Client websocket opened.` と表示されれば、接続が確立されていることになります。 

1. メッセージを入力して Enter キーを押します。 アプリケーションは Azure Function アプリで *messages* 関数にメッセージを送信します。次に、Web PubSub 出力バインディングを使用して、接続されているすべてのクライアントにメッセージをブロードキャストします。 すべてが正しく動作している場合、アプリケーションでメッセージが表示されます。

1. Web アプリケーションの別のインスタンスを、別のブラウザー ウィンドウで開きます。 送信したメッセージが、アプリケーションのすべてのインスタンスで表示されることを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリの使用を続けない場合は、次の手順に従って、このドキュメントで作成したすべてのリソースを削除して、課金が発生しないようにします。

1. Azure Portal の左端で **[リソース グループ]** を選択し、作成したリソース グループを選択します。 検索ボックスを使用して名前でリソース グループを検索することもできます。

1. 表示されたウィンドウでリソース グループを選択し、 **[リソース グループの削除]** を選択します。

1. 新しいウィンドウで、削除するリソース グループの名前を入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、サーバーレス チャット アプリケーションを実行する方法について説明しました。 これで、独自のアプリケーションの作成を始められます。 

> [!div class="nextstepaction"]
> [クイックスタート: Azure Web PubSub で簡単なチャットルームを作成する](https://azure.github.io/azure-webpubsub/getting-started/create-a-chat-app/js-handle-events)

> [!div class="nextstepaction"]
> [Azure Functions の Azure Web PubSub バインディング](https://azure.github.io/azure-webpubsub/references/functions-bindings)

> [!div class="nextstepaction"]
> [Azure Web PubSub のサンプルをさらに確認する](https://github.com/Azure/azure-webpubsub/tree/main/samples)