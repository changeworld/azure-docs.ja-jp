---
title: Azure Web PubSub サービス サーバーレス クイックスタート
description: Azure Web PubSub サービスと Azure Functions を使用したサーバーレス アプリケーションのためのクイックスタート。
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: overview
ms.date: 03/11/2021
ms.openlocfilehash: 41d60c8e565655793dd33393f41876438c7eab66
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166956"
---
# <a name="quickstart-create-a-serverless-simple-chat-application-with-azure-functions-and-azure-web-pubsub-service"></a>クイックスタート: Azure Functions と Azure Web PubSub サービスを使用してシンプルなサーバーレス チャット アプリケーションを作成する 

Azure Web PubSub サービスは、WebSocket とパブリッシュ-サブスクライブ パターンを使用して、リアルタイム メッセージング Web アプリケーションを作成するのに役立ちます。 Azure Functions は、インフラストラクチャを管理することなくコードを実行できるサーバーレス プラットフォームです。 このクイックスタートでは、Azure Web PubSub サービスと Azure Functions を使用して、リアルタイム メッセージングとパブリッシュ-サブスクライブ パターンによるサーバーレス アプリケーションを作成する方法について説明します。

## <a name="prerequisites"></a>必須コンポーネント

# <a name="javascript"></a>[JavaScript](#tab/javascript)

[Visual Studio Code](https://code.visualstudio.com/) などのコード エディターをインストールし、さらに [Node.js](https://nodejs.org/en/download/) バージョン 10.x ライブラリをインストールします。

   > [!NOTE]
   > サポートされているバージョンの Node.js の詳細については、[Azure Functions ランタイム バージョンのドキュメント](../azure-functions/functions-versions.md#languages)を参照してください。

Azure Function アプリをローカルで実行するために、[Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (バージョン 2.7.1505 以降) をインストールします。

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

- 関数の構成を更新する。

  クローンしたリポジトリの */samples/functions/js/simplechat* フォルダーを開きます。 *local.settings.json* を編集して、サービスの接続文字列を追加します。
  これらの変更を *local.settings.json* に加えたら、ファイルを保存します。
    - プレース ホルダー *<connection-string>* は、**Azure portal** の **`WebPubSubConnectionString`** 設定からコピーした実際の文字列に置き換えます。 
    - **`AzureWebJobsStorage`** の設定には、これが必須となります。[Azure Functions には Azure ストレージ アカウントが必要](https://docs.microsoft.com/azure/azure-functions/storage-considerations)であるためです。
        - Azure ストレージ エミュレーターがローカルで実行されている場合、"UseDevelopmentStorage=true" という元の設定を維持します。
        - Azure ストレージの接続文字列がある場合は、その値に置き換えます。
 
- フォルダーに JavaScript 関数が編成されます。 各フォルダーには、関数で使用されるバインディングを定義する `function.json` と、関数の本体である `index.js` という 2 つのファイルがあります。 この関数アプリには、トリガーによって作動する関数がいくつかあります。

    - **login** - これは HTTP によってトリガーされる関数です。 *webPubSubConnection* 入力バインディングを使用して、有効なサービス接続情報を生成して返します。
    - **messages** - これは `WebPubSubTrigger` によってトリガーされる関数です。 要求本文でチャット メッセージを受信し、`WebPubSub` 出力バインディングを使用して、接続されているすべてのクライアント アプリケーションにメッセージをブロードキャストします。
    - **connect** および **connected** - これらは `WebPubSubTrigger` によってトリガーされる関数です。 connect と connected のイベントを処理します。

- ターミナルで、現在のフォルダーが */samples/functions/js/simplechat* であることを確認します。 拡張機能をインストールし、関数アプリを実行します。

    ```bash
    func extensions install

    func start
    ```

- ローカル関数では、`local.settings.json` ファイルに定義されているポートが使用されます。 それを公衆ネットワークから利用できるようにするためには、[ngrok](https://ngrok.com) を使用して、このエンドポイントを公開する必要があります。 次のコマンドを実行すると、転送エンドポイント (例: http://{ngrok-id}.ngrok.io -> http://localhost:7071 ) が返されます。

    ```bash
    ngrok http 7071
    ```    

- Azure Web PubSub サービスに `Event Handler` を設定します。 **Azure portal** に移動して、自分の Web PubSub リソースを見つけ、 **[設定]** に移動します。 次のように、新しいハブ設定と使用中の 1 つの関数とのマッピングを追加します。 {ngrok-id} は、実際の文字列に置き換えてください。

   - ハブ名: simplechat
   - URL テンプレート: **http://{ngrok-id}.ngrok.io/runtime/webhooks/webpubsub**
   - ユーザー イベント パターン: *
   - システム イベント: connect、connected、disconnected

---

## <a name="run-the-web-application"></a>Web アプリケーションの実行

1. クライアントのテストを簡素化するには、ブラウザーでサンプルの[シングル ページ Web アプリケーション](http://jialinxin.github.io/webpubsub/)を開きます。 

1. 関数アプリのベース URL をローカルとして入力します (`http://localhost:7071`)。

1. ユーザー名を入力します。

1. Web アプリケーションは関数アプリで *login* 関数を呼び出して、Azure Web PubSub サービスに接続するための接続情報を取得します。 `Client websocket opened.` と表示されれば、接続が確立されていることになります。 

1. メッセージを入力して Enter キーを押します。 アプリケーションは Azure Function アプリで *messages* 関数にメッセージを送信します。次に、Web PubSub 出力バインディングを使用して、接続されているすべてのクライアントにメッセージをブロードキャストします。 すべてが正しく動作している場合、アプリケーションでメッセージが表示されます。

1. Web アプリケーションの別のインスタンスを、別のブラウザー ウィンドウで開きます。 送信したメッセージがアプリケーションのすべてのインスタンスで表示されることを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリの使用を続けない場合は、次の手順に従って、このドキュメントで作成したすべてのリソースを削除して、課金が発生しないようにします。

1. Azure Portal の左端で **[リソース グループ]** を選択し、作成したリソース グループを選択します。 検索ボックスを使用して名前でリソース グループを検索することもできます。

1. 表示されたウィンドウでリソース グループを選択し、 **[リソース グループの削除]** を選択します。

1. 新しいウィンドウで、削除するリソース グループの名前を入力し、 **[削除]** を選択します。
