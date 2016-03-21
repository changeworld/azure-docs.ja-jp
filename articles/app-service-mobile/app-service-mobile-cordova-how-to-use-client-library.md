<properties
	pageTitle="Azure Mobile Apps 向け Apache Cordova プラグインの使用方法"
	description="Azure Mobile Apps 向け Apache Cordova プラグインの使用方法"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="adrianha"/>

# Azure Mobile Apps 向け Apache Cordova クライアント ライブラリの使用方法

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

このガイドでは、最新の [Azure Mobile Apps 向け Apache Cordova プラグイン]を使用して一般的なシナリオを実行する方法について説明します。Azure Mobile Apps を初めて使用する場合は、まず、「[Apache Cordova アプリの作成]」を参照して、バックエンドの作成、テーブルの作成、構築済みの Apache Cordova プロジェクトのダウンロードを行ってください。このガイドでは、クライアント側の Apache Cordova プラグインに重点を置いています。

##<a name="Setup"></a>セットアップと前提条件

このガイドでは、バックエンドとテーブルを作成済みであることを前提としています。このガイドでは、テーブルのスキーマが、これらのチュートリアルのテーブルの場合と同じであることを前提とします。さらに、Apache Cordova プラグインがコードに追加済みであることを前提とします。まだそれを行っていない場合は、コマンドラインで Apache Cordova プラグインをプロジェクトに追加することができます。

```
cordova plugin add ms-azure-mobile-apps
```

Apache Cordova アプリを初めて作成する場合は、[こちら]のドキュメントを参照してください。

##<a name="create-client"></a>方法: クライアントを作成する

`WindowsAzure.MobileServicesClient` オブジェクトを作成して、クライアント接続を作成します。`appUrl` を Mobile App の URL に置き換えます。

```
var client = WindowsAzure.MobileServicesClient(appUrl);
```

##<a name="table-reference"></a>方法: テーブル参照を作成する

データへのアクセスやデータの更新を行うには、バックエンド テーブルへの参照を作成します。`tableName` を実際のテーブルの名前に置き換えます。

```
var table = client.getTable(tableName);
```

##<a name="querying"></a>方法: テーブル参照をクエリする

テーブル参照を作成したら、それを使用してサーバー上のデータのクエリを実行できます。クエリは "LINQ のような" 言語で作成します。テーブルからすべてのデータを返すには、次の手順に従います。

```
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

success 関数は results を指定して呼び出します。success 関数で `for (var i in results)` は使用しないでください。これを指定すると、その他のクエリ関数 (`.includeTotalCount()` など) を使用した場合に、results に含まれる情報が反復処理されるからです。

クエリ構文の詳細については、「[query オブジェクト]」を参照してください。

### サーバー上のデータのフィルター処理

テーブル参照に対して `where` 句を使用できます。

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

オブジェクトをフィルター処理する関数を使用することもできます。この場合は、フィルター処理対象の現在のオブジェクトに `this` 変数を割り当てます。以下は、機能的に前の例と等価です。

```
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

### データのページング

take() メソッドと skip() メソッドを利用します。たとえば、テーブルを 100 行のレコードに分割する場合:

```
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

`.includeTotalCount()` メソッドを使用して、results オブジェクトに totalCount フィールドを追加します。totalCount フィールドには、ページングを使用しない場合に返されるレコード数の合計が入力されます。

pages 変数と一部の UI ボタンを使用すると、ページ リストを指定できます。loadPage() を使用すると、ページごとに新しいレコードを読み込むことができます。既に読み込まれているレコードへのアクセス時間を短縮するには、何種類かのキャッシュを実装する必要があります。


###<a name="sorting-data"></a>方法: 並べ替えられたデータを返す

.orderBy() または .orderByDescending() クエリ メソッドを次のように使用します。

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

query オブジェクトの詳細については、「[query オブジェクト]」を参照してください。

##<a name="inserting"></a>方法: データを挿入する

適切な日付を指定して JavaScript オブジェクトを作成し、table.insert() を非同期に呼び出します。

```
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

挿入に成功すると、挿入された項目が、同期操作で必要な追加のフィールドで返されます。以降の更新に対しては、この情報を保持する独自のキャッシュを更新する必要があります。

Azure Mobile Apps Node.js サーバー SDK では、開発用に動的なスキーマをサポートしています。動的スキーマの場合、テーブルのスキーマは即座に更新されます。このため、挿入操作または更新操作で列を指定するだけで、テーブルに列を追加できます。実稼働環境にアプリケーションを移行する前に、動的スキーマを無効にしておくことをお勧めします。

##<a name="modifying"></a>方法: データを変更する

.insert() メソッドの場合と同様に、update オブジェクトを作成して .update() を呼び出す必要があります。update オブジェクトには更新するレコードの ID を含める必要があります。この ID は、レコードの読み取り時または .insert() の呼び出し時に取得されます。

```
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

##<a name="deleting"></a>方法: データを削除する

レコードを削除するには .del() メソッドを呼び出します。オブジェクト参照に ID を渡します。

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```

##<a name="auth"></a>方法: ユーザーを認証する

Azure App Service は、Facebook、Google、Microsoft アカウント、Twitter などのさまざまな外部 ID プロバイダーを使用したアプリケーション ユーザーの認証と承認をサポートします。テーブルのアクセス許可を設定することにより、特定の操作へのアクセスを認証されたユーザーのみに制限できます。さらに、認証されたユーザーの ID を使用することにより、サーバー スクリプトで承認ルールを実装することもできます。詳細については、チュートリアル「[モバイル サービスでの認証の使用]」を参照してください。

Apache Cordova アプリで認証を使用する場合は、次の Cordova プラグインが使用できる状態になければなりません。

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

サーバー フローとクライアント フローの 2 つの認証フローがサポートされます。サーバー フローには、プロバイダーの Web 認証のインターフェイスを利用する、最も簡単な認証方法が用意されています。クライアント フローでは、プロバイダー固有およびデバイス固有の SDK を利用することから、シングル サインオンなどのデバイス固有の機能との統合がさらに進みます。

##<a name="server-auth"></a>方法: プロバイダーでの認証 (サーバー フロー)

Mobile Services によって Windows ストア アプリまたは HTML5 アプリの認証プロセスが管理されるようにするには、アプリケーションを ID プロバイダーに登録する必要があります。その後、Azure App Service 内で、プロバイダーから提供されたアプリケーション ID とシークレットを構成する必要があります。詳細については、チュートリアル「[アプリへの認証の追加]」を参照してください。

ID プロバイダーを登録したら、プロバイダーの値を指定して .login() メソッドを呼び出します。たとえば、Facebook にログインするには、次のコードを使用します。

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Facebook 以外の ID プロバイダーを使用している場合は、上の login メソッドに渡す値を `microsoftaccount`、`facebook`、`twitter`、`google`、または `aad` のいずれかに変更します。

この場合、Azure App Service は、選択されたプロバイダーのログイン ページを表示し、ID プロバイダーでのログインが成功した後で App Service 認証トークンを生成することで、OAuth 2.0 認証フローを管理します。login 関数は、完了すると、userId フィールドのユーザー ID と authenticationToken フィールドの App Service 認証トークンの両方を公開する JSON オブジェクト (user) を返します。このトークンはキャッシュして、期限が切れるまで再利用することができます。

##<a name="client-auth"></a>方法: プロバイダーでの認証 (クライアント フロー)

アプリケーションは個別に ID プロバイダーにアクセスして、返されたトークンを認証のために App Service に提供することもできます。このクライアント フローでは、ユーザーにシングル サインイン エクスペリエンスを提供したり、ID プロバイダーから追加のユーザー データを取得したりすることができます。

### ソーシャル認証の基本的な例

この例では、認証用の Facebook クライアント SDK を使用します。

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```
この例では、それぞれのプロバイダー SDK で提供されるトークンが変数 token に格納されるとします。

### Microsoft アカウントの例

次の例では、Windows ストア アプリケーションのシングル サインオンを Microsoft アカウントによってサポートしている Live SDK を使用します。

```
WL.login({ scope: "wl.basic"}).then(function (result) {
      client.login(
            "microsoftaccount",
            {"authenticationToken": result.session.authentication_token})
      .done(function(results){
            alert("You are now logged in as: " + results.userId);
      },
      function(error){
            alert("Error: " + err);
      });
});
```

この例では、トークンを Live Connect から取得します。このトークンは、login 関数を呼び出すことによって、App Service に渡されます。

##<a name="auth-getinfo"></a>方法: 認証されたユーザーに関する情報の取得

現在のユーザーの認証情報は、AJAX メソッドを使用して `/.auth/me` エンドポイントから取得できます。たとえば、次のフェッチ API を使用します。

```
var url = client.applicationUrl + '/.auth/me';
fetch(url)
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

jQuery または別の AJAX API を使用して情報を取得することも可能です。データは JSON オブジェクトとして取得されます。

##<a name="register-for-push"></a>方法: プッシュ通知に登録する

プッシュ通知を処理するには、[phonegap-plugin-push] をインストールします。これは、コマンド ラインで `cordova plugin add` コマンドを使用するか、または Visual Studio の Git プラグイン インストーラーを使用することで、簡単に追加できます。Apache Cordova アプリの次のコードによって、デバイスがプッシュ通知に登録されます。

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

プッシュ通知は、Notification Hubs SDK を使用してサーバーから送信します。プッシュ通知をクライアントから直接送信しないでください。この場合、Notification Hubs または PNS に対してサービス拒否攻撃をトリガーすることに利用されるおそれがあるからです。

<!-- URLs. -->
[Apache Cordova アプリの作成]: app-service-mobile-cordova-get-started.md
[モバイル サービスでの認証の使用]: app-service-mobile-cordova-get-started-users.md
[アプリへの認証の追加]: app-service-mobile-cordova-get-started-users.md

[Azure Mobile Apps 向け Apache Cordova プラグイン]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[こちら]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[query オブジェクト]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0224_2016-->