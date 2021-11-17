---
title: チュートリアル - Azure Web PubSub を使用するときにアプリケーションに認証とアクセス許可を追加する
description: Azure Web PubSub を使用するときにアプリケーションに認証とアクセス許可を追加する方法を説明します。
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 11/01/2021
ms.openlocfilehash: 027cd7197167a3667748c2c470e17b83aa3bf03d
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578727"
---
# <a name="tutorial-add-authentication-and-permissions-to-your-application-when-using-azure-web-pubsub"></a>チュートリアル: Azure Web PubSub を使用するときにアプリケーションに認証とアクセス許可を追加する

「[チャット アプリを構築する](./tutorial-build-chat.md)」では、WebSocket API を使用して Azure Web PubSub でデータを送受信する方法について学習しました。 わかりやすくするために認証が要求されていないことにお気づきかもしれません。 Azure Web PubSub では接続のためにアクセス トークンが必要ですが、アクセス トークンを生成するためにチュートリアルで使用した `negotiate` API には認証が必要ありません。 誰でもこの API を呼び出してアクセス トークンを取得できます。

実際のアプリケーションでは、ユーザーがアプリケーションを使用する前に、通常は先にサインインしてもらいます。 このチュートリアルでは、Web PubSub をアプリケーションの認証および認可のシステムと統合してセキュリティを向上させる方法について説明します。

このチュートリアルの完全なコード サンプルは、[GitHub][code] にあります。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * GitHub 認証を有効にする
> * アプリケーションに認証ミドルウェアを追加する
> * クライアントにアクセス許可を追加する

## <a name="add-authentication-to-the-chat-room-app"></a>チャット ルーム アプリに認証を追加する

このチュートリアルでは、[チャット アプリの構築](./tutorial-build-chat.md)で作成したチャット アプリケーションを再利用します。 また、[GitHub][chat-js] から、チャット アプリの完全なコード サンプルを複製することもできます。 

このチュートリアルでは、チャット アプリケーションに認証を追加し、それを Web PubSub と統合します。

まず、ユーザーが GitHub アカウントを使用してサイン インできるように、チャット ルームに GitHub 認証を追加します。

1.  依存関係をインストールします。

    ```bash
    npm install --save cookie-parser
    npm install --save express-session
    npm install --save passport
    npm install --save passport-github2
    ```

1.  次のコードを `server.js` に追加して、GitHub 認証を有効にします。

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

    前述のコードでは、GitHub 認証を有効にするために [Passport.js](http://www.passportjs.org/) が使用されています。 その動作を簡単に示すと次のようになります。

    1. `/auth/github` でサインインのために github.com にリダイレクトします。
    1. サインインすると、GitHub でアプリケーションのコードを使用して `/auth/github/callback` にリダイレクトされ、認証が完了します。 (GitHub から返されたプロファイルが検証され、サーバーで永続化される方法を確認するには、`passport.use()` で確認コールバックを参照してください)。
    1. 認証が完了すると、サイトのホームページ (`/`) にリダイレクトされます。
 
    GitHub OAuth と Passport.js の詳細については、次の記事を参照してください。

    - [OAuth アプリの承認](https://docs.github.com/en/developers/apps/authorizing-oauth-apps)
    - [Passport.js のドキュメント](http://www.passportjs.org/docs/)

    これをテストするには、最初に GitHub OAuth を作成する必要があります。

    1. https://www.github.com にアクセスして自分のプロファイルを開き、 **[設定]**  >  **[開発者向け設定]** の順に選択します。
    1. OAuth Apps に移動し、 **[新規 OAuth アプリ]** を選択します。
    1. アプリケーション名とホームページの URL (任意の URL を指定できます) を入力し、 **[認証コールバックの URL]** を `http://localhost:8080/auth/github/callback` に設定します。 この URL は、サーバーに公開したコールバック API と一致します。
    1. アプリケーションが登録されたら、クライアント ID をコピーし、 **[新しいクライアント シークレットを生成する]** を選択します。

    次のコマンドを実行して設定をテストします。`<connection-string>`、`<client-id>`、`<client-secret>` は必ず実際の値に置き換えます。

    ```bash
    export WebPubSubConnectionString="<connection-string>"
    export GitHubClientId="<client-id>"
    export GitHubClientSecret="<client-secret>"
    node server
    ```
    
    ここで、`http://localhost:8080/auth/github` を開きます。 サインインするために GitHub にリダイレクトされます。 サインインすると、チャット アプリケーションにリダイレクトされます。

1.  ユーザーにユーザー名の入力を求めるのではなく、GitHub から取得した ID を利用するようにチャット ルームを更新します。

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

    ユーザーがサインインすると、要求でユーザーの ID は Cookie を介して自動的に渡されます。 したがって、必要なのは、ユーザーが `req` オブジェクトに存在するかどうかを確認し、ユーザー名を Web PubSub アクセス トークンに追加することだけです。

    ```javascript
    app.get('/negotiate', async (req, res) => {
      if (!req.user || !req.user.username) {
        res.status(401).send('missing user id');
        return;
      }
      let options = {
        userId: req.user.username
      };
      let token = await serviceClient.getClientAccessToken(options);
      res.json({
        url: token.url
      });
    });
    ```

    ここでサーバーを再実行すると、チャット ルームを初めて開いたときに "承認されていない" というメッセージが表示されます。 サインイン リンクを選択してサインインすると、以前と同じように機能するのを確認できます。

## <a name="work-with-permissions"></a>アクセス許可の操作

前のチュートリアルでは、`WebSocket.send()` を使用して、サブプロトコルを使って他のクライアントにメッセージを直接公開する方法を学習しました。 実際のアプリケーションでは、アクセス許可制御なしに、クライアントが任意のグループとの間で公開やサブスクライブを行えるようにするのはお勧めできません。 このセクションでは、Web PubSub のアクセス許可システムを使用してクライアントを制御する方法について説明します。

Web PubSub では、クライアントは次の種類の操作をサブプロトコルで実行できます。

- サーバーにイベントを送信する。
- グループにメッセージを公開する。
- グループに参加 (サブスクライブ) する。

サーバーにイベントを送信するのは、クライアントの既定の操作です。 プロトコルは使用されないため、常に許可されます。 グループへの公開とサブスクライブを実行するために、クライアントはアクセス許可を取得する必要があります。 サーバーがクライアントにアクセス許可を付与するには、次の 2 つの方法があります。

- クライアントが接続するときのロールを指定する ("*ロール*"は、クライアントが接続するときの最初のアクセス許可を表す概念です)。
- 接続後に API を使用してクライアントにアクセス許可を付与する。

グループに参加するアクセス許可の場合、クライアントはアクセス許可を取得した後も引き続き、"グループへの参加" メッセージを使用することによりグループに参加する必要があります。 あるいは、サーバーは、クライアントに参加アクセス許可がなくても、API を使用してグループに追加できます。

次に、このアクセス許可システムを使用して、チャット ルームに新しい機能を追加しましょう。 "*管理者*" という新しい種類のユーザーをチャットルームに追加します。 管理者がシステムメッセージ ("[SYSTEM]" で始まるメッセージ) をクライアントから直接送信できるようにします。

最初に、システムとユーザーのメッセージを 2 つの異なるグループに分けて、それぞれのアクセス許可を別々に制御できるようにする必要があります。

異なるグループに異なるメッセージを送信するように `server.js` を変更します。

```javascript
let handler = new WebPubSubEventHandler(hubName, {
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

前のコードでは、`WebPubSubServiceClient.group().sendToAll()` を使用して、ハブではなくグループにメッセージを送信します。

メッセージはグループに送信されるようになったので、クライアントをグループに追加して、引き続きメッセージを受信できるようにする必要があります。 クライアントをグループに追加するには、`handleConnect` ハンドラーを使用します。

> [!Note]
> `handleConnect` は、クライアントが Web PubSub に接続しようとするとトリガーされます。 このハンドラーで、グループとロールを返せるため、接続が確立されるとすぐに、サービスではグループに接続を追加したり、ロールを付与したりできます。 サービスは `res.fail()` を使用して接続を拒否することもできます。
>

`handleConnect` をトリガーするには、Azure portal のイベント ハンドラー設定に移動し、システム イベントで **connect** を選択します。

クライアント HTML も更新する必要があります。これは、サーバーによってプレーンテキストではなく JSON メッセージが送信されるようになったためです。

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

次にクライアント コードを変更して、ユーザーが **システム メッセージ** を選択したときシステム グループに送信されるようにします。

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

既定では、クライアントには、どのグループにも送信するアクセス許可がありません。 管理者ユーザーのアクセス許可を付与するようにサーバー コードを更新します (わかりやすくするために、管理者の ID はコマンド ライン引数として提供されます)。

```javascript
app.get('/negotiate', async (req, res) => {
  ...
  if (req.user.username === process.argv[2]) options.claims = { role: ['webpubsub.sendToGroup.system'] };
  let token = await serviceClient.getClientAccessToken(options);
});
```

次に `node server <admin-id>` を実行します。 `<admin-id>` としてサインインすると、すべてのクライアントにシステム メッセージを送信できるのを確認できます。

ただし、別のユーザーとしてサインインした場合は、**システム メッセージ** を選択しても何も起こりません。 その操作が許可されていないことを知らせるエラーをサービスによって出すことができます。 このフィードバックを提供するには、メッセージを公開するときに `ackId` を設定します。 `ackId` が指定されている場合は、Web PubSub によって、操作が成功したかどうかを示す、一致する `ackId` を持つメッセージが返されます。

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

また、メッセージを処理するコードを、`ack` メッセージを処理するように変更します。

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

次に、サーバーを再実行し、別のユーザーとしてサイン インします。 システム メッセージを送信しようとすると、エラー メッセージが表示されます。

このチュートリアルの完全なコード サンプルは、[GitHub][code] にあります。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Web PubSub サービスに接続する方法と、サブプロトコルを使用して、接続されているクライアントにメッセージを公開する方法に関する基本的な考え方を説明しています。

Web PubSub サービスの使用に関する詳細については、ドキュメントに記載されている他のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Azure Web PubSub のサンプルをさらに確認する](https://aka.ms/awps/samples)

[code]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/githubchat/
[chat-js]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/chatapp
