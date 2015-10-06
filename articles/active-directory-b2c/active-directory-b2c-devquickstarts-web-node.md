<properties
	pageTitle="Azure B2C プレビュー用に nodeJS Web アプリへのサインインを追加する | Microsoft Azure"
	description="B2C テナントでユーザーをサインインさせる Node.js Web アプリを作成する方法。"
	services="active-directory-b2c"
	documentationCenter=""
	authors="brandwe"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
  ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="brandwe"/>

# B2C プレビュー: NodeJS Web アプリへのサインインを追加する


[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]この記事では、Azure AD B2C でサインイン、サインアップ、プロファイルの管理を実装する方法については説明しません。ユーザーが既に認証された後での Web API の呼び出しに焦点を合わせています。まだ行っていない場合は、先に [.NET Web アプリ入門チュートリアル](active-directory-b2c-devquickstarts-web-dotnet.md)で Azure AD B2C の基本を学習してください。

**Passport** は Node.js 用の認証ミドルウェアです。Passport は、非常に柔軟で高度なモジュール構造をしており、任意の Express ベースまたは Resitify Web アプリケーションに、支障をきたすことなくドロップされます。包括的な認証手法セットにより、ユーザー名とパスワードを使用する認証、Facebook、Twitter などをサポートします。Microsoft Azure Active Directory 用の戦略が開発されています。ここでは、このモジュールをインストールした後、Microsoft Azure Active Directory `passport-azure-ad` プラグインを追加します。

これを行うには、次の手順を実行する必要があります。

1. アプリケーションを Azure AD に登録する
2. Passport の azure-ad-passport プラグインを使用するようにアプリをセットアップする
3. Passport を使用して、サインイン要求とサインアウト要求を Azure AD に発行する
4. ユーザーに関するデータを印刷する

このチュートリアルのコードは、[GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS) で管理されています。追加の参考資料として、[アプリのスケルトン (.zip) をダウンロード](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip)したり、スケルトンを複製したりすることができます:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git```

完成したアプリケーションは、このチュートリアルの終わりにも示しています。

> [AZURE.WARNING] 	B2C プレビューでは、Web-API タスク サーバーとそのサーバーに接続するクライアントの両方に同じクライアント ID またはアプリケーション ID およびポリシーを使用する必要があります。これは、iOS と Android のチュートリアルに当てはまります。これらのクイック スタートのいずれかでアプリケーションを作成したことがある場合は、以下で新しい値を作成するのではなく、それらの値を使用してください。

## 1.Azure AD B2C ディレクトリの取得

Azure AD B2C を使用するには、ディレクトリ (つまり、テナント) を作成しておく必要があります。ディレクトリは、ユーザー、アプリ、グループなどをすべて格納するためのコンテナーです。  まだディレクトリを作成していない場合は、
先に進む前に [B2C ディレクトリの作成](active-directory-b2c-get-started.md)に関するページを参照してください。

## 2.アプリケーションの作成

次に、B2C ディレクトリ内にアプリを作成する必要があります。これによって、 アプリと安全に通信するために必要ないくつかの情報が Azure AD に提供されます。ここでは、クライアント アプリと Web API の両方が単一の**アプリケーション ID** で表されます。これは、クライアント アプリと Web API が 1 つのロジック アプリを構成するためです。
アプリを作成するには、[こちらの手順](active-directory-b2c-app-registration.md)に従います。このとき、

- アプリケーションに **Web アプリまたは Web API** を含めます。
- `http://localhost/TodoListService` を**応答 URL** として入力します。これはこのサンプル コードで使用する既定の URL です。
- アプリケーション用の**アプリケーション シークレット**を作成し、それをメモしておきます。このプロジェクトはすぐに必要になります。
- アプリに割り当てられた**アプリケーション ID** をメモしておきます。こちらもすぐに必要になります。

    > [AZURE.IMPORTANT]
    [Azure ポータル](https://manage.windowsazure.com/)の **[アプリケーション]** タブで登録されているアプリケーションは、ここでは使用できません。

## 3.ポリシーの作成

Azure AD B2C では、すべてのユーザー エクスペリエンスが[**ポリシー**](active-directory-b2c-reference-policies.md)  によって定義されます。このアプリには、3
つの ID エクスペリエンス (サインアップ、サインイン、および Facebook でのサインイン) が含まれます。
[ポリシーについてのリファレンス記事](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)で説明されているように、種類ごとに 1 つのポリシーを作成する必要があります。3 つのポリシーを作成するときは、以下の点に注意してください。

- サインアップ ポリシーで、**[表示名]** と他のいくつかのサインアップ属性を選択します。
- すべてのポリシーで、**[表示名]** と **[オブジェクト ID]** のアプリケーション クレームを選択します。その他のクレームも選択できます。
- ポリシーの作成後、各ポリシーの **[名前]** をメモしておきます。名前には、プレフィックス `b2c_1_` が付加されます。これらのポリシー名はすぐに必要になります。 

3 つのポリシーの作成が正常に完了したら、いつでもアプリをビルドできます。

この記事では、作成したポリシーの使用方法については説明しません。ポリシーが Azure AD B2C 
でどのように機能するかを学習する場合は、[.NET Web アプリの入門チュートリアル](active-directory-b2c-devquickstarts-web-dotnet.md)から始めてください。



## 4.ディレクトリに前提条件を追加する

コマンド ラインから、ディレクトリをルート フォルダーに移動し (まだルート フォルダーでない場合)、次のコマンドを実行します。

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`
- `npm install webfinger`
- `npm install body-parser`
- `npm install express-session`
- `npm install cookie-parser`

- さらに、クイック スタートのスケルトンで、プレビュー用に `passport-azure-ad` を使用しています。

- `npm install passport-azure-ad`


これにより、passport-azure-ad が依存するライブラリがインストールされます。

## 5.passport-node-js 戦略を使用するようにアプリを設定する
ここでは、OpenID Connect 認証プロトコルを使用するように、Express ミドルウェアを構成します。Passport は、サインイン要求とサインアウト要求の発行、ユーザー セッションの管理、ユーザーに関する情報の取得などを行うために使用されます。

-	最初に、プロジェクトのルートにある `config.js` ファイルを開いて、アプリの構成値を `exports.creds` セクションに入力します。
    -	`clientID:` は、登録ポータル内のアプリに割り当てられた**アプリケーション ID** です。
    -	`returnURL` は、ポータルで入力した**リダイレクト URI** です。
    - `tenantName:` は、アプリの**テナント名** (たとえば、contoso.onmicrosoft.com) です。


- 次に、プロジェクトのルートにある `app.js` ファイルを開き、次の呼び出しを追加して、`passport-azure-ad` に付属する `OIDCStrategy` 戦略を呼び出します。


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

- その後、今参照した戦略を使用してログイン要求を処理します。

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2) 
// 
//   Strategies in passport require a `validate` function, which accept
//   credentials (in this case, an OpenID identifier), and invoke a callback
//   with a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    tenantName: config.creds.tenantName
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('Example: Email address we received was: ', profile.email);
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(profile.email, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(profile);
          return done(null, profile);
        }
        return done(null, user);
      });
    });
  }
));
```
Passport は、すべての戦略ライターが従うすべての戦略 (Twitter や Facebook など) に対して類似するパターンを使用します。戦略を調べると、それは、パラメーターとして token と done を持つ function() が渡されることがわかります。戦略は、その処理をすべて終えると、必ず戻ってきます。戻ったら、再度要求しなくてもいいように、ユーザーを保存し、トークンを隠します。

> [AZURE.IMPORTANT]上記のコードでは、サーバーに認証を求めたすべてのユーザーを受け入れています。これは、自動登録と呼ばれます。運用サーバーでは、指定された登録プロセスを先に実行していないユーザーにはアクセスを許可しないように設定できます。これは、Facebook への登録は許可するが、その後で追加情報の入力を求めるコンシューマー アプリで通常見られるパターンです。これがサンプル アプリケーションでなければ、返されるトークン オブジェクトから電子メールを抽出した後、追加情報の入力を要求できます。これはテスト サーバーなので、単純にユーザーをメモリ内データベースに追加します。

- 次に、Passport で必要な、ログオンしているユーザーの追跡を可能にするメソッドを追加します。これには、ユーザーの情報のシリアル化と逆シリアル化が含まれます。

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// array to hold logged in users
var users = [];

var findByEmail = function(email, fn) {
  for (var i = 0, len = users.length; i < len; i++) {
    var user = users[i];
    log.info('we are using user: ', user);
    if (user.email === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};

```

- 次に、express エンジンを読み込むコードを追加します。ここでは、Express が提供する既定の /views と /routes のパターンを使用します。

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware, to support
  // persistent login sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

- 最後に、実際のログイン要求を `passport-azure-ad` エンジンに渡す POST ルートを追加します。

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authenitcation was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    
    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    
    res.redirect('/');
  });
```

## 4\.Passport を使用してサインイン要求とサインアウト要求を Azure AD に発行する

これまでに、アプリは、OpenID Connect 認証プロトコルを使用して v2.0 エンドポイントと通信するように適切に構成されました。`passport-azure-ad` は、認証メッセージの作成、Azure AD からのトークンの検証、ユーザー セッションの維持を行うためのすべての煩わしい処理を実行します。残っているのは、サインインとサインアウトを行う方法をユーザーに提示することと、ログインしているユーザーに関する追加情報を収集することです。

- まず、既定のメソッド、login メソッド、account メソッド、および logout メソッドを `app.js` ファイルに追加します。

```JavaScript

//Routes (Section 4)

app.get('/', function(req, res){
  res.render('index', { user: req.user });
});

app.get('/account', ensureAuthenticated, function(req, res){
  res.render('account', { user: req.user });
});

app.get('/login',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Login was called in the Sample');
    res.redirect('/');
});

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});

```

-	これらについて詳しく説明しましょう。
    -	`/` ルートは、index.ejs ビューにリダイレクトして、要求内のユーザーを渡します (存在する場合)。
    - `/account` ルートは、***ユーザーが認証されていることを確認した後*** (次で実装します)、ユーザーに関する追加情報を取得できるように、要求内のユーザーを渡します。
    - `/login` ルートは、`passport-azuread` から azuread-openidconnect authenticator を呼び出し、これが成功しなかった場合は、ユーザーを /login にリダイレクトして戻します。
    - `/logout` は、クッキーをクリアする logout.ejs (およびルート) を呼び出した後、ユーザーを index.ejs に返します。


- `app.js` の最後の部分に、上記の `/account` で使用される EnsureAuthenticated メソッドを追加します。

```JavaScript

// Simple route middleware to ensure user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

- 最後に、`app.js` でサーバーそのものを実際に作成します。

```JavaScript

app.listen(3000);

```


## 5\.ポリシーを呼び出すビューおよびルートを express に作成する

`app.js` が完成しました。次に実行する必要があるのは、サインインおよびサインアップ ポリシーを呼び出すことができるルートとビューを追加し、作成した `/logout` ルートと `/login` ルートを処理することだけです。

- ルート ディレクトリの下に `/routes/index.js` ルートを作成します。

```JavaScript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- ルート ディレクトリの下に `/routes/user.js` ルートを作成します。

```JavaScript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

これらは、要求 (存在する場合はユーザーも) をビューに渡すだけの単純なルートです。

- ルート ディレクトリの下に `/views/index.ejs` ビューを作成します。これはサインインおよびサインアウト用のポリシーを呼び出して、アカウント情報を取得できるようにする単純なページです。条件付きの `if (!user)` を使用できることに注目してください。要求でユーザーが渡されることは、ログインしているユーザーが存在していることの証拠です。

```JavaScript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login/?p=your facebook policy">Sign In with Facebook</a>
	<a href="/login/?p=your email sign-in policy">Sign In With Email</a>
	<a href="/login/?p=your email sign-up policy">Sign Up With Email</a>
<% } else { %>
	<h2>Hello, <%= user.displayName %>.</h2>
	<a href="/account">Account Info</a></br>
	<a href="/logout">Log Out</a>
<% } %>
```

- ルート ディレクトリの下に `/views/account.ejs` ビューを作成し、`passport-azuread` がユーザー要求の中に配置された追加情報を表示できるようにします。

```Javascript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login">Log In</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claimes</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

最後に、アプリを構築して実行します。

`node app.js` を実行し、`http://localhost:3000` に移動します。


電子メールまたは Facebook でアプリケーションにサインアップまたはサインインします。サインアウトし、別のユーザーとしてサインインします。



##次のステップ

参照用の完全なサンプル (構成値を除く) が、[.zip としてこちらで提供されています](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip)。または、GitHub から複製できます:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git```

これ以降は、さらに高度なトピックに進むことができます。次のチュートリアルを試してみてください。

[Node.js の B2C モデルで Web App をセキュリティ保護する >>](active-directory-b2c-devquickstarts-webapi-nodejs.md)

<!--

For additional resources, check out:
You can now move onto more advanced B2C topics.  You may want to try:

[Calling a node.js Web API from a node.js Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=Sept15_HO4-->