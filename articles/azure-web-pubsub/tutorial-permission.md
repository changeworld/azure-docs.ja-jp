---
title: チュートリアル - Azure Web PubSub サービスを使用するときにアプリケーションに認証とアクセス許可を追加する
description: Azure Web PubSub サービスを使用するときにアプリケーションに認証とアクセス許可を追加する方法を説明するチュートリアル
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 08/26/2021
ms.openlocfilehash: cca4951aac8844c13f36ebb58f8c326cec7b6104
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117822"
---
# <a name="tutorial-add-authentication-and-permissions-to-your-application-when-using-azure-web-pubsub-service"></a>チュートリアル: Azure Web PubSub サービスを使用するときにアプリケーションに認証とアクセス許可を追加する

[チャット アプリの構築チュートリアル](./tutorial-build-chat.md)では、WebSocket API を使用して Azure Web PubSub でデータを送受信する方法について学習しました。 わかりやすくするために認証が要求されていないことにお気づきかもしれません。 Azure Web PubSub を接続するにはアクセス トークンが必要ですが、アクセス トークンを生成するためにチュートリアルで使用した `negotiate` API には認証が必要ないため、誰でもこの API を呼び出してアクセス トークンを取得できます。

実際のアプリケーションでは、アプリケーションの不正使用を防ぐために、ユーザーがアプリケーションを使用する前に、まずログインを求めるのが一般的です。 このチュートリアルでは、Azure Web PubSub をアプリケーションの認証または認可システムと統合してセキュリティを向上させる方法について説明します。

このチュートリアルの完全なコード サンプルは、[こちら][code]にあります。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * GitHub 認証を有効にする
> * アプリケーションに認証ミドルウェアを追加する
> * クライアントにアクセス許可を追加する

## <a name="add-authentication-to-the-chat-room-app"></a>チャット ルーム アプリに認証を追加する

このチュートリアルでは、[チャット アプリの構築チュートリアル](./tutorial-build-chat.md)で作成したチャット アプリケーションを再利用します。 また、[こちら][chat-js]から、チャット アプリの完全なコード サンプルを複製することもできます。 

このチュートリアルでは、チャット アプリケーションに認証を追加し、それを Azure Web PubSub サービスと統合します。

まず、ユーザーが GitHub アカウントを使用してログインできるように、チャット ルームに GitHub 認証を追加しましょう。

1.  依存関係のインストール

    ```bash
    npm install --save cookie-parser
    npm install --save express-session
    npm install --save passport
    npm install --save passport-github2
    ```

2.  次のコードを `server.js` に追加して、GitHub 認証を有効にします。

    ```javascript
    const app = express();

    const users = {};
    passport.use(
      new GitHubStrategy({
        clientID: process.argv[3],
        clientSecret: process.argv[4]
      },
      (accessToken, refreshToken, profile, done) => {
        users[profile.id] = profile;
        return done(null, profile);
      }
    ));

    passport.serializeUser((user, done) => {
      done(null, user.id);
    });

    passport.deserializeUser((id, done) => {
      if (users[id]) return done(null, users[id]);
      return done(`invalid user id: ${id}`);
    });

    app.use(cookieParser());
    app.use(session({
      resave: false,
      saveUninitialized: true,
      secret: 'keyboard cat'
    }));
    app.use(passport.initialize());
    app.use(passport.session());
    app.get('/auth/github', passport.authenticate('github', { scope: ['user:email'] }));
    app.get('/auth/github/callback', passport.authenticate('github', { successRedirect: '/' }));
    ```

    上記のコードでは、GitHub 認証を有効にするために [Passport.js](http://www.passportjs.org/) が使用されています。 その動作を簡単に示すと次のようになります。

    1. `/auth/github` がログインのために github.com にリダイレクトされます
    2. ログインが完了すると、GitHub によって `/auth/github/callback` にリダイレクトされ、アプリケーションで認証を完了するためのコードが表示されます (GitHub から返されたプロファイルがどのように検証されてサーバーに保持されるかを確認するには、`passport.use()` の検証コールバックを参照してください)。
    3. 認証が完了すると、サイトのホームページ (`/`) にリダイレクトされます。
 
    GitHub OAuth と Passport.js の詳細については、次の記事を参照してください。

    - [OAuth アプリの承認](https://docs.github.com/en/developers/apps/authorizing-oauth-apps)
    - [Passport.js のドキュメント](http://www.passportjs.org/docs/)

    これをテストするには、最初に GitHub OAuth を作成する必要があります。

    1. https://www.github.com にアクセスし、プロファイルを開き、[Settings] -> [Developer settings] の順に選択します。
    2. [OAuth Apps] に移動し、[New OAuth App] をクリックします。
    3. アプリケーション名、ホームページ URL (任意のもの) を入力し、認可コールバック URL を `http://localhost:8080/auth/github/callback` (サーバーで公開したコールバック API と一致するもの) に設定します。
    4. アプリケーションが登録されたら、クライアント ID をコピーし、[Generate a new client secret] をクリックして新しいクライアント シークレットを生成します

    次に、`node server <connection-string> <client-id> <client-secret>` を実行し、`http://localhost:8080/auth/github` を開くと、ログインするために GitHub にリダイレクトされます。 ログインが成功すると、チャット アプリケーションにリダイレクトされます。

3.  次に、ユーザー名を求めるダイアログをポップアップ表示するのではなく、GitHub から取得した ID を利用するようにチャット ルームを更新しましょう。

    ユーザー ID を渡さずに `/negotiate` を直接呼び出すように `public/index.html` を更新します。

    ```javascript
    let messages = document.querySelector('#messages');
    let res = await fetch(`/negotiate`);
    if (res.status === 401) {
      let m = document.createElement('p');
      m.innerHTML = 'Not authorized, click <a href="/auth/github">here</a> to login';
      messages.append(m);
      return;
    }
    let data = await res.json();
    let ws = new WebSocket(data.url);
    ```

    ユーザーがログインすると、要求でユーザーの ID は Cookie を介して自動的に渡されます。 したがって、必要なのは、ユーザーが `req` オブジェクトに存在するかどうかを確認し、ユーザー名を Web PubSub アクセス トークンに追加することだけです。

    ```javascript
    app.get('/negotiate', async (req, res) => {
      if (!req.user || !req.user.username) {
        res.status(401).send('missing user id');
        return;
      }
      let options = {
        userId: req.user.username
      };
      let token = await serviceClient.getAuthenticationToken(options);
      res.json({
        url: token.url
      });
    });
    ```

    ここでサーバーを再実行すると、チャット ルームを初めて開いたときに "承認されていない" というメッセージが表示されます。 ログイン リンクをクリックしてログインすると、以前と同じように機能するのを確認できます。

## <a name="working-with-permissions"></a>アクセス許可を操作する

前のチュートリアルでは、`WebSocket.send()` を使用して、サブプロトコルを使って他のクライアントにメッセージを直接発行する方法を学習しました。 実際のアプリケーションでは、アクセス許可制御なしに、クライアントが任意のグループとの間で発行やサブスクライブを行えるようにするのはお勧めできません。 このセクションでは、Azure Web PubSub のアクセス許可システムを使用してクライアントを制御する方法について説明します。

Azure Web PubSub には、クライアントがサブプロトコルを使用して実行できる 3 種類の操作があります。

- サーバーにイベントを送信する
- グループにメッセージを発行する
- グループに参加 (サブスクライブ) する

サーバーへのイベントの送信は、プロトコルが使用されなくてもクライアントの既定の操作なので、常に許可されます。 グループへの発行とサブスクライブを実行するには、クライアントでアクセス許可を取得する必要があります。 サーバーでクライアントにアクセス許可を付与するには、次の 2 つの方法があります。

- クライアントが接続するときのロールを指定する (ロールは、クライアントが接続するときの最初のアクセス許可を表す概念です)
- 接続後に API を使用してクライアントにアクセス許可を付与する

グループへの参加アクセス許可の場合、クライアントはアクセス許可を取得した後も引き続き、グループへの参加メッセージを使用してグループに参加する必要があります。 または、参加アクセス許可がなくても、サーバーで API を使用してクライアントをグループに追加できます。

次に、このアクセス許可システムを使用して、チャット ルームに新しい機能を追加しましょう。 管理者という新しい種類のユーザーをチャット ルームに追加します。管理者には、システム メッセージ ("[SYSTEM]" で始まるメッセージ) をクライアントから直接送信することを許可します。

まず、アクセス許可を別々に制御できるように、システムとユーザーのメッセージを 2 つの異なるグループに分ける必要があります。

異なるグループに異なるメッセージを送信するように `server.js` を変更します。

```javascript
let handler = new WebPubSubEventHandler(hubName, ['*'], {
  path: '/eventhandler',
  handleConnect: (req, res) => {
    res.success({
      groups: ['system', 'message'],
    });
  },
  onConnected: req => {
    console.log(`${req.context.userId} connected`);
    serviceClient.group('system').sendToAll(`${req.context.userId} joined`, { contentType: 'text/plain' });
  },
  handleUserEvent: (req, res) => {
    if (req.context.eventName === 'message') {
      serviceClient.group('message').sendToAll({
        user: req.context.userId,
        message: req.data
      });
    }
    res.success();
  }
});
```

上記のコードでは `WebPubSubServiceClient.group().sendToAll()` を使用して、ハブではなくグループにメッセージが送信されているのを確認できます。

メッセージはグループに送信されるようになったので、クライアントがメッセージを引き続き受信できるように、グループに追加する必要があります。 これは、`handleConnect` ハンドラーで行われます。

> [!Note]
> `handleConnect` は、クライアントが Azure Web PubSub に接続しようとするとトリガーされます。 このハンドラーでグループとロールを返せるため、接続が確立されるとすぐに、サービスではグループに接続を追加したり、ロールを付与したりできます。 また、`res.fail()` で接続を拒否することもできます。
>

`handleConnect` がトリガーされるようにするには、Azure portal のイベント ハンドラー設定に移動し、システム イベントで `connect` をチェックします。

また、サーバーによってプレーン テキストではなく JSON メッセージが送信されるようになったので、クライアント HTML も更新する必要があります。

```javascript
let ws = new WebSocket(data.url, 'json.webpubsub.azure.v1');
ws.onopen = () => console.log('connected');

ws.onmessage = event => {
  let m = document.createElement('p');
  let message = JSON.parse(event.data);
  switch (message.type) {
    case 'message':
      if (message.group === 'system') m.innerText = `[SYSTEM] ${message.data}`;
      else if (message.group === 'message') m.innerText = `[${message.data.user}] ${message.data.message}`;
      break;
  }
  messages.appendChild(m);
};

let message = document.querySelector('#message');
message.addEventListener('keypress', e => {
  if (e.charCode !== 13) return;
  ws.send(JSON.stringify({
    type: 'event',
    event: 'message',
    dataType: 'text',
    data: message.value
  }));
  message.value = '';
});
```

次に、ユーザーが [システム メッセージ] をクリックしたときにシステム グループに送信されるようにクライアント コードを変更します。

```html
<button id="system">system message</button>
...
<script>
  (async function() {
    ...
    let system = document.querySelector('#system');
    system.addEventListener('click', e => {
      ws.send(JSON.stringify({
        type: 'sendToGroup',
        group: 'system',
        dataType: 'text',
        data: message.value
      }));
      message.value = '';
    });
  })();
</script>
```

既定では、クライアントには任意のグループに送信するアクセス許可はありません。管理者ユーザーのアクセス許可を付与するようにサーバー コードを更新します (わかりやすくするために、管理者の ID はコマンド ライン引数として提供されます)。

```javascript
app.get('/negotiate', async (req, res) => {
  ...
  if (req.user.username === process.argv[5]) options.claims = { role: ['webpubsub.sendToGroup.system'] };
  let token = await serviceClient.getAuthenticationToken(options);
});
```

次に `node server <connection-string> <client-id> <client-secret> <admin-id>` を実行します。`<admin-id>` としてログインすると、すべてのクライアントにシステム メッセージを送信できるのを確認できます。

ただし、別のユーザーとしてログインした場合は、"システム メッセージ" をクリックしても何も起こりません。 その操作が許可されていないことを知らせるエラーがサービスによって出されます。 これは、メッセージを発行するときに `ackId` を設定することによって実行できます。 `ackId` が指定されている場合は、Azure Web PubSub によって、操作が成功したかどうかを示す、一致する `ackId` を持つ ACK メッセージが返されます。

システム メッセージを送信するコードを次のコードに変更します。

```javascript
let ackId = 0;
system.addEventListener('click', e => {
  ws.send(JSON.stringify({
    type: 'sendToGroup',
    group: 'system',
    ackId: ++ackId,
    dataType: 'text',
    data: message.value
    }));
  message.value = '';
});
```

また、メッセージを処理するコードを、ACK メッセージを処理するように変更します。

```javascript
ws.onmessage = event => {
  ...
  switch (message.type) {
    case 'ack':
      if (!message.success && message.error.name === 'Forbidden') m.innerText = 'No permission to send system message';
      break;
  }
};
```

サーバーを再実行し、別のユーザーとしてログインすると、システム メッセージを送信しようとしたときにエラー メッセージが表示されます。

このチュートリアルの完全なコード サンプルは、[こちら][code]にあります。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Web PubSub サービスに接続する方法と、サブプロトコルを使用して、接続されているクライアントにメッセージを発行する方法に関する基本的な考え方を説明しています。

サービスの使用方法の詳細については、他のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Azure Web PubSub のサンプルをさらに確認する](https://aka.ms/awps/samples)

[code]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/githubchat/
[chat-js]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/chatapp
