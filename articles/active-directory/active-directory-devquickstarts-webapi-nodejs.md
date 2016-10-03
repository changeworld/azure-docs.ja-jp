<properties
	pageTitle="Azure AD NodeJS の概要 | Microsoft Azure"
	description="認証のために Azure AD と連携する Node.js REST Web API を構築する方法"
	services="active-directory"
	documentationCenter="nodejs"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="brandwe"/>

# ノード用の Web API の概要

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

**Passport** は Node.js 用の認証ミドルウェアです。Passport は、非常に柔軟で高度なモジュール構造をしており、任意の Express ベースまたは Resitify Web アプリケーションに、支障をきたすことなくドロップされます。包括的な認証手法セットにより、ユーザー名とパスワードを使用する認証、Facebook、Twitter などをサポートします。Microsoft Azure Active Directory 用の戦略が開発されています。ここでは、このモジュールをインストールした後、Microsoft Azure Active Directory `passport-azure-ad` プラグインを追加します。

これを行うには、次の手順を実行する必要があります。

1. アプリケーションを Azure AD に登録する
2. Passport の azure-ad-passport プラグインを使用するようにアプリをセットアップする
3. To Do List Web API を呼び出すように、クライアント アプリケーションを構成する

このチュートリアルのコードは、[GitHub](https://github.com/Azure-Samples/active-directory-node-webapi) で管理されています。

> [AZURE.NOTE] この記事では、Azure AD B2C でサインイン、サインアップ、プロファイルの管理を実装する方法については説明しません。ユーザーが既に認証された後での Web API の呼び出しに焦点を合わせています。Azure Active Directory の基本についてまだ確認していない場合は、まず「[How to integrate with Azure Active Directory document (Azure Active Directory ドキュメントと統合する方法)](active-directory-how-to-integrate.md)」を確認してください。


この実稼働するサンプルに関連するすべてのソース コードが、MIT ライセンスの下で、GitHub で提供されています。自由にクローン (できれば、フォーク) 操作を行って、フィードバックおよびプル リクエストを提供してください。

## Node.js モジュールとは

このチュートリアルでは、Node.js を使用します。モジュールは、アプリケーションに対して特定の機能を提供する読み込み可能な JavaScript パッケージです。モジュールは、通常、Node.js NPM コマンド ライン ツールを使用して NPM インストール ディレクトリにインストールされますが、HTTP モジュールなどの一部のモジュールは、コア Node.js パッケージに含まれています。インストールされたモジュールは、Node.js インストール ディレクトリのルートにある node\_modules ディレクトリに保存されます。node\_modules ディレクトリの各モジュールは、それが依存関係を持つすべてのモジュールを含む独自の node\_modules ディレクトリを維持し、各必須モジュールは node\_modules ディレクトリを持ちます。この再帰的なディレクトリ構造は、依存関係チェーンを表します。

この依存関係チェーンにより、アプリケーションのフットプリントは大きくなりますが、すべての依存関係が満たされ、開発で使用されるモジュールのバージョンが運用で使用されることが保証されます。このため、運用アプリの動作がより期待どおりになり、ユーザーに悪影響を及ぼすバージョン管理の問題を防ぐことができます。

## 1\.Azure AD テナントを登録する

このサンプルを使用するには、Azure Active Directory テナントが必要です。テナントについて不明な場合、または取得方法が不明な場合は、「[How to get an Azure Active Directory tenant (Azure Active Directory テナントを取得する方法)](active-directory-howto-tenant.md)」を参照してください。

## 2\.アプリケーションを作成する

ここで、ディレクトリにアプリを作成する必要があります。このディレクトリによって、アプリと安全に通信するために必要ないくつかの情報が Azure AD に提供されます。ここでは、クライアント アプリと Web API の両方が単一の**アプリケーション ID** で表されます。これは、クライアント アプリと Web API が 1 つの論理アプリを構成するためです。アプリを作成するには、[こちらの手順](active-directory-how-applications-are-added.md)に従います。基幹業務アプリケーションを作成する場合、[この追加手順が役に立つ場合があります](active-directory-applications-guiding-developers-for-lob-applications.md)。

次を行ってください。

- Microsoft Azure の管理ポータルにサインインします。
- 左側のナビゲーションで **[Active Directory]** をクリックします。
- アプリケーションの登録先となるテナントを選択します。
- **[アプリケーション]** タブをクリックし、下部のドロアーで [追加] をクリックします。
- 画面の指示に従い、新しい **Web アプリケーションまたは WebAPI** を作成します。
    - アプリケーションの **[名前]** には、エンド ユーザーがアプリケーションの機能を把握できるような名前を設定します。
    - **[サインオン URL]** は、アプリのベース URL です。サンプル コードの既定値は `https://localhost:8080` です。
    - **[アプリケーション ID/URI]** は、アプリケーションの一意識別子です。形式は、`https://<tenant-domain>/<app-name>` (たとえば、`https://contoso.onmicrosoft.com/my-first-aad-app`) です。
- 登録が完了すると、AAD により、アプリケーションに一意のクライアント ID が割り当てられます。この値は次のセクションで必要になるので、[構成] タブからコピーします。

- リマインダー: アプリケーション用の**アプリケーション シークレット**を作成し、それをメモしておきます。このプロジェクトはすぐに必要になります。
- リマインダー: アプリケーションに割り当てられた**アプリケーション ID** をメモしておきます。こちらもすぐに必要になります。


## 手順 3.プラットフォーム用の Node.js をダウンロードする
このサンプルを正常に使用するには、Node.js の実稼働するインストール環境が必要になります。

Node.js を [http://nodejs.org](http://nodejs.org) からインストールします。

## 4\.プラットフォームに MongoDB をインストールする

このサンプルを正常に使用するには、MongoDB の実稼働するインストール環境が必要になります。MongoDB を使用して、REST API がサーバー インスタンス間で持続されるようにします。

MongoDB を [http://mongodb.org](http://www.mongodb.org) からインストールします。

> [AZURE.NOTE] このチュートリアルでは、MongoDB の既定のインストール環境およびサーバー エンドポイント (チュートリアルの記述時点では mongodb://localhost) が使用されることを想定しています。


## 5\.Web API に Restify モジュールをインストールする

Resitfy を使用して REST API を構築します。Resitfy は最小で柔軟性のある Node.js アプリケーション フレームワークで、Connect 上に REST API を構築するための一連の堅牢な機能を備えた Express から派生しています。

### Restify をインストールする

コマンド ラインで、azuread ディレクトリに移動します。**azuread** ディレクトリが存在しない場合は、作成します。

`cd azuread - or- mkdir azuread; cd azuread`

次のコマンドを入力します。

`npm install restify`

このコマンドにより、Restify がインストールされます。

#### エラーが発生した場合

一部のオペレーティング システムで npm を使用すると、エラー メッセージ「Error: EPERM, chmod '/usr/local/bin/..'」が表示され、管理者のアカウントを使用して再実行するように要求されることがあります。このような場合は、sudo コマンドを使用して、より高い権限レベルで npm を実行します。

#### Dtrace に関するエラーが表示された場合

Restify をインストールするときに、次のようなメッセージが表示されることがあります。

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```


Restify は、DTrace を使用して REST 呼び出しをトレースする強力なメカニズムを備えています。ただし、多くのオペレーティング システムで DTrace は使用できません。これらのエラーは無視してかまいません。


このコマンドの出力は次のように表示されます。


	restify@2.6.1 node_modules/restify
	├── assert-plus@0.1.4
	├── once@1.3.0
	├── deep-equal@0.0.0
	├── escape-regexp-component@1.0.2
	├── qs@0.6.5
	├── tunnel-agent@0.3.0
	├── keep-alive-agent@0.0.1
	├── lru-cache@2.3.1
	├── node-uuid@1.4.0
	├── negotiator@0.3.0
	├── mime@1.2.11
	├── semver@2.2.1
	├── spdy@1.14.12
	├── backoff@2.3.0
	├── formidable@1.0.14
	├── verror@1.3.6 (extsprintf@1.0.2)
	├── csv@0.3.6
	├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
	└── bunyan@0.22.0 (mv@0.0.5)


## 6\.Passport.js を Web API にインストールする

[Passport](http://passportjs.org/) は Node.js 用の認証ミドルウェアです。Passport は、非常に柔軟で高度なモジュール構造をしており、任意の Express ベースまたは Resitify Web アプリケーションに、支障をきたすことなくドロップされます。包括的な認証手法セットにより、ユーザー名とパスワードを使用する認証、Facebook、Twitter などをサポートします。Azure Active Directory 用の認証手法を開発しました。このモジュールをインストールし、Azure Active Directory 認証手法プラグインを追加します。

コマンド ラインで、azuread ディレクトリに移動します。

次のコマンドを入力して、Passport.js をインストールします。

`npm install passport`

コマンドの出力は次のように表示されます。

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## 7\.Passport-Azure-AD を Web API に追加する

次に、Azure Active Directory を Passport に追加する一連の戦略である passport-azure-ad を使用する OAuth 戦略を追加します。この Rest API の例では、ベアラー トークン用の戦略を使用します。

> [AZURE.NOTE] OAuth2 は、任意の既知のトークン タイプを発行できるフレームワークを提供しますが、一部のトークン タイプのみが広範に使用されています。エンドポイントを保護するために、ベアラー トークンが広く使用されるようになっています。ベアラー トークンは、OAuth2 の最も広く発行されるタイプのトークンで、多くの実装では、発行されるトークンのタイプとしてベアラー トークンのみを想定しています。

コマンド ラインで、azuread ディレクトリに移動します。

次のコマンドを入力して、Passport.js の passport-azure-ad モジュールをインストールします。

`npm install passport-azure-ad`

コマンドの出力は次のように表示されます。

	``
	passport-azure-ad@1.0.0 node_modules/passport-azure-ad
	├── xtend@4.0.0
	├── xmldom@0.1.19
	├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
	├── underscore@1.8.3
	├── async@1.3.0
	├── jsonwebtoken@5.0.2
	├── xml-crypto@0.5.27 (xpath.js@1.0.6)
	├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
	├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
	├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
	└── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)



## 8\.MongoDB モジュールを Web API に追加する

データ ストアとして MongoDB を使用します。そのため、どちらも広く使用されている、Mongoose と呼ばれるモデルおよびスキーマを管理するためのプラグインと、MongoDB という名前の MongoDB 用のデータベース ドライバーの両方をインストールする必要があります。


* `npm install mongoose`

## 9\.追加モジュールをインストールする

次に、その他の必須モジュールをインストールします。


コマンド ラインで、**azuread** フォルダーに移動します (現在のディレクトリがこのディレクトリではない場合)。

`cd azuread`


次のコマンドを入力して、次のモジュールを node\_modules ディレクトリにインストールします。

* `npm install assert-plus`
* `npm install bunyan`
* `npm update`


## 10\.依存関係を持つ server.js を作成する

server.js ファイルは、Web API サーバーの機能の多くを提供します。このため、ほとんどのコードをこのファイルに追加します。運用環境では、ルートとコントローラーを分割するなどして、機能をより小さなファイルに分散します。このデモでは、その目的に沿って、この機能用に server.js を使用します。

コマンド ラインで、**azuread** フォルダーに移動します (現在のディレクトリがこのディレクトリではない場合)。

`cd azuread`

お気に入りのエディターを使用して `server.js` ファイルを作成し、次の情報を追加します。

```Javascript
	'use strict';

	/**
 	* Module dependencies.
 	*/

	var fs = require('fs');
	var path = require('path');
	var util = require('util');
	var assert = require('assert-plus');
	var bunyan = require('bunyan');
	var getopt = require('posix-getopt');
	var mongoose = require('mongoose/');
	var restify = require('restify');
	var passport = require('passport');
  var BearerStrategy = require('passport-azure-ad').BearerStrategy;
```

ファイルを保存します。この後すぐに、このファイルを使用します。

## 11\.Azure AD の設定を保存する構成ファイルを作成する

このコード ファイルは、構成パラメーターを Azure Active Directory ポータルから Passport.js に渡します。これらの構成値は、チュートリアルの初期の手順で Web API をポータルに追加したときに作成されています。ここでは、コードをコピーした後に、これらのパラメーターにどのような値を設定するかについて説明します。


コマンド ラインで、**azuread** フォルダーに移動します (現在のディレクトリがこのディレクトリではない場合)。

`cd azuread`

お気に入りのエディターを使用して `config.js` ファイルを作成し、次の情報を追加します。

```Javascript
 exports.creds = {
     mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
     clientID: 'your client ID',
     audience: 'your application URL',
    // you cannot have users from multiple tenants sign in to your server unless you use the common endpoint
  // example: https://login.microsoftonline.com/common/.well-known/openid-configuration
     identityMetadata: 'https://login.microsoftonline.com/<your tenant id>/.well-known/openid-configuration', 
     validateIssuer: true, // if you have validation on, you cannot have users from multiple tenants sign in to your server
     passReqToCallback: false,
     loggingLevel: 'info' // valid are 'info', 'warn', 'error'. Error always goes to stderr in Unix.

 };


```
ファイルを保存します。

## 12\.構成を server.js ファイルに追加する

これらの値は、アプリケーション全体で、作成した構成ファイルから読み込む必要があります。これを行うには、.config ファイルを必須リソースとしてアプリケーションに追加し、グローバル変数を config.js ドキュメントに設定するだけです。

コマンド ラインで、**azuread** フォルダーに移動します (現在のディレクトリがこのディレクトリではない場合)。

`cd azuread`

お気に入りのエディターを使用して `server.js` ファイルを開き、次の情報を追加します。

```Javascript
var config = require('./config');
```
次に、`server.js` に新しいセクションを追加して、次のコードを記述します。

```Javascript
var options = {
    // The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback,
    loggingLevel: config.creds.loggingLevel

};

// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;

// Our logger
var log = bunyan.createLogger({
    name: 'Azure Active Directory Bearer Sample',
         streams: [
        {
            stream: process.stderr,
            level: "error",
            name: "error"
        }, 
        {
            stream: process.stdout,
            level: "warn",
            name: "console"
        }, ]
});

  // if logging level specified, switch to it.
  if (config.creds.loggingLevel) { log.levels("console", config.creds.loggingLevel); }

// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
```

ファイルを保存します。



## 13\.Moongoose を使用して MongoDB モデルとスキーマ情報を追加する

これまでの準備が報われるときが来ました。これら 3 つのファイルを一緒に REST API サービスに取り込みます。

このチュートリアルでは、***手順 4.*** で説明しているように、MongoDB を使用してタスクを格納します。

***手順 11.*** で作成した `config.js` ファイルは、mogoose\_auth\_local 接続 URL の末尾に配置されているので、このファイルから再呼び出しを行うと、データベース `tasklist` が呼び出されました。このデータベースを MongoDB で事前に作成する必要はありません。存在しない場合、サーバー アプリケーションの初回実行時に作成されます。

これで、使用する MongoDB データベースについて、事前にサーバーに通知したことになります。次に、サーバーのタスク用のモデルとスキーマを作成する追加コードを記述する必要があります。

#### モデルについて

使用するスキーマ モデルは非常に単純で、必要に応じて拡張できます。

NAME - タスクに割り当てられているモデルの名前。***String***

TASK - タスク自体。***String***

DATE - タスクの期限日。***DATETIME***

COMPLETED - タスクが完了したかどうかを示します。***BOOLEAN***

#### スキーマをコードで作成する


コマンド ラインで、**azuread** フォルダーに移動します (現在のディレクトリがこのディレクトリではない場合)。

`cd azuread`

お気に入りのエディターを使用して `server.js` ファイルを開き、次の情報を構成エントリの下に追加します。

```Javascript
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
    owner: String,
    task: String,
    completed: Boolean,
    date: Date
});

// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
コードからわかるように、Schema を作成し、次に、***ルート*** を定義する際に、データを格納するためにコード全体で使用するモデル オブジェクトを作成します。

## 14\.Task REST API サーバー用ルートを追加する

これで、操作対象のデータベース モデルが作成されたので、REST API サーバー用に使用するルートを追加します。

### Restify 内のルートについて

ルートは、Express スタックを使用する場合とまったく同じ方法で Restify 内で動作します。ルートは、クライアント アプリが呼び出すことが想定される URI を使用して定義されます。通常、ルートは個別のファイルで定義されますが、デモの目的に沿って、すべてのルートを server.js ファイルに格納します。運用環境では、それぞれ独自のファイルに格納することをお勧めします。

Restify ルートの典型的なパターンを次に示します。

```Javascript
function createObject(req, res, next) {

// do work on Object

 _object.name = req.params.object; // passed value is in req.params under object

 ///...

return next(); // keep the server going
}

....

server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.

```


これは、最も基本的なレベルのパターンです。Resitfy (および Express) では、アプリケーション タイプの定義、異なるエンドポイントにまたがる複雑なルーティングなどのより高度な機能が提供されますが、デモの目的に沿って、これらのルートを非常に単純に維持します。

### 1\.既定のルートをサーバーに追加する

Create、Retrieve、Update、および Delete の基本的な CRUD ルートを追加します。

コマンド ラインで、**azuread** フォルダーに移動します (現在のディレクトリがこのディレクトリではない場合)。

`cd azuread`

お気に入りのエディターを使用して `server.js` ファイルを開き、これまでに作成したデータベース エントリの下に次の情報を追加します。

```Javascript

/**
 *
 * APIs for our REST Task server
 */

// Create a task

function createTask(req, res, next) {

    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
    var _task = new Task();

    if (!req.params.task) {
        req.log.warn('createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.task = req.params.task;
    _task.date = new Date();

    _task.save(function(err) {
        if (err) {
            req.log.warn(err, 'createTask: unable to save');
            next(err);
        } else {
            res.send(201, _task);

        }
    });

    return next();

}


// Delete a task by name

function removeTask(req, res, next) {

    Task.remove({
        task: req.params.task,
        owner: owner
    }, function(err) {
        if (err) {
            req.log.warn(err,
                'removeTask: unable to delete %s',
                req.params.task);
            next(err);
        } else {
            log.info('Deleted task:', req.params.task);
            res.send(204);
            next();
        }
    });
}

// Delete all tasks

function removeAll(req, res, next) {
    Task.remove();
    res.send(204);
    return next();
}


// Get a specific task based on name

function getTask(req, res, next) {

    log.info('getTask was called for: ', owner);
    Task.find({
        owner: owner
    }, function(err, data) {
        if (err) {
            req.log.warn(err, 'get: unable to read %s', owner);
            next(err);
            return;
        }

        res.json(data);
    });

    return next();
}

/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err) {
            return next(err);
        }

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Did you initalize the database as stated in the README?");
        }

        if (!owner) {
            log.warn(err, "You did not pass an owner when listing tasks.");
        } else {

            res.json(data);

        }
    });

    return next();
}

```

### 2\.次に、いくつかのエラー処理を API に追加する

```

///--- Errors for communicating something interesting back to the client

function MissingTaskError() {
    restify.RestError.call(this, {
        statusCode: 409,
        restCode: 'MissingTask',
        message: '"task" is a required parameter',
        constructorOpt: MissingTaskError
    });

    this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);


function TaskExistsError(owner) {
    assert.string(owner, 'owner');

    restify.RestError.call(this, {
        statusCode: 409,
        restCode: 'TaskExists',
        message: owner + ' already exists',
        constructorOpt: TaskExistsError
    });

    this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);


function TaskNotFoundError(owner) {
    assert.string(owner, 'owner');

    restify.RestError.call(this, {
        statusCode: 404,
        restCode: 'TaskNotFound',
        message: owner + ' was not found',
        constructorOpt: TaskNotFoundError
    });

    this.name = 'TaskNotFoundError';
}

util.inherits(TaskNotFoundError, restify.RestError);
```


## 15\.サーバーを作成する

データベースの定義およびルートの配置が完了したので、最後に、呼び出しを管理するサーバー インスタンスを追加します。

Restify (および Express) では、REST API サーバーに対してより高度なカスタマイズを実行できますが、デモの目的に沿って、最も基本的なセットアップを使用します。

```Javascript
/**
 * Our Server
 */


var server = restify.createServer({
    name: "Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());

// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());

// Allow 5 requests/second by IP, and burst to 10
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allows for JSON mapping to REST
```

## 16\.サーバーにルートを追加する (まだ認証は行われません)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers are protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the pasport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiement removing API protection
/* by removing the passport.authenticate() method like so:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```

## 17\.OAuth サポートを追加する前にサーバーを実行する

認証を追加する前に、サーバーをテストします。

これを行うための最も簡単な方法は、コマンド ラインで curl を使用することです。これを行うには、出力を JSON として解析することを可能にする単純なユーティリティが必要です。このため、以降のすべての例で使用する json ツールをインストールします。

`$npm install -g jsontool`

これにより、JSON ツールがグローバルにインストールされます。ツールがインストールされたので、サーバーの操作を開始します。

まず、monogoDB インスタンスが動作していることを確認します。

`$sudo mongod`

次に、ディレクトリを変更し、curl コマンドを実行してコンテンツを取得します。

`$ cd azuread` `$ node server.js`

`$ curl -isS http://127.0.0.1:8080 | json`

```Shell
HTTP/1.1 200 OK
Connection: close
Content-Type: application/json
Content-Length: 171
Date: Tue, 14 Jul 2015 05:43:38 GMT
[
"GET /",
"POST /tasks/:owner/:task",
"POST /tasks (for JSON body)",
"GET /tasks",
"PUT /tasks/:owner",
"GET /tasks/:owner",
"DELETE /tasks/:owner/:task"
]
```

最後に、タスクを次のように追加します。

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

次のような応答が返ります。

```Shell
HTTP/1.1 201 Created
Connection: close
Access-Control-Allow-Origin: *
Access-Control-Allow-Headers: X-Requested-With
Content-Type: application/x-www-form-urlencoded
Content-Length: 5
Date: Tue, 04 Feb 2014 01:02:26 GMT
Hello
```
Brandon 用のタスクを次の方法でリストできます。

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

これらのすべてが正常に機能した場合、OAuth を REST API サーバーに追加できます。

**これで、MongoDB がある REST API サーバーが用意できました。**


## 18\.REST API サーバーに認証を追加する

実稼働する REST API を作成できたので、次に、Azure AD に対して使用できるようにします。

コマンド ラインで、**azuread** フォルダーに移動します (現在のディレクトリがこのディレクトリではない場合)。

`cd azuread`

### 1: passport-azure-ad に含まれている OIDCBearerStrategy を使用する

ここまで、認証がまったく行われない REST TODO サーバーを構築してきました。ここから、認証を配置する手順を開始します。

最初に、Passport を使用することを指定する必要があります。次のコードを、他のサーバー構成の直後に配置します。

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
API を記述するときは、ユーザーがなりすますことができないトークンの一意の情報に常にデータをリンクする必要があります。このサーバーは、TODO 項目を保存するときに、“owner” フィールドに配置される (token.oid を通して呼び出される) トークン内のユーザーのオブジェクト ID に基づいてそれらを保存します。これにより、そのユーザーだけが自分の TODO にアクセスでき、他のユーザーは入力された TODO にアクセスすることはできません。API 内で “owner” が公開されることはないため、外部ユーザーは、認証された場合でも、他のユーザーの TODO を要求することができます。

次に、passport-azure-ad に含まれるべアラー戦略を使用します。今はコードをざっと見てください。内容は後で説明します。このコードを、上述のコードの後ろに置きます。

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementor.
/* Here we just autoregister any user and implement a FindById().
/* You'll want to do something smarter.
**/

var findById = function(id, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
        var user = users[i];
        if (user.sub === id) {
            log.info('Found user: ', user);
            return fn(null, user);
        }
    }
    return fn(null, null);
};


var bearerStrategy = new BearerStrategy(options,
    function(token, done) {
        log.info('verifying the user');
        log.info(token, 'was the token retreived');
        findById(token.sub, function(err, user) {
            if (err) {
                return done(err);
            }
            if (!user) {
                // "Auto-registration"
                log.info('User was added automatically as they were new. Their sub is: ', token.sub);
                users.push(token);
                owner = token.sub;
                return done(null, token);
            }
            owner = token.sub;
            return done(null, user, token);
        });
    }
);

passport.use(bearerStrategy);
```

Passport は、すべての戦略ライターが従うすべての戦略 (Twitter や Facebook など) に対して類似するパターンを使用します。戦略を調べると、それは、パラメーターとして token と done を持つ function() が渡されることがわかります。戦略は、その処理をすべて終えると、必ず戻ってきます。戻ったら、再度要求しなくてもいいように、ユーザーを保存し、トークンを隠します。

> [AZURE.IMPORTANT]
上記のコードでは、サーバーに認証を求めたすべてのユーザーを受け入れています。これは、自動登録と呼ばれます。運用サーバーでは、指定された登録プロセスを先に実行していないユーザーにはアクセスを許可しないように設定できます。これは、Facebook への登録は許可するが、その後で追加情報の入力を求めるコンシューマー アプリで通常見られるパターンです。これがコマンド ライン プログラムでなければ、返されるトークン オブジェクトから電子メールを抽出した後、追加情報の入力を要求できます。これはテスト サーバーなので、単純にユーザーをメモリ内データベースに追加します。

### 2\.最後にいくつかのエンドポイントを保護する

エンドポイントを保護するには、使用するプロトコルをパラメーターとして、`passport.authenticate()` 呼び出しを指定します。

さらに興味深いことを行うためにサーバー コードのルートを編集します。

```Javascript
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## 19\.サーバー アプリケーションを再度実行して自分が拒否されることを確認する

再び `curl` を使用して、エンドポイントに対して OAuth2 保護が有効になっていることを確認します。この操作は、このエンドポイントに対して、クライアント SDK のいずれかを実行する前に行います。返されるヘッダーは、正しいパスに沿っていることを確認するのに十分である必要があります。

まず、monogoDB インスタンスが実行されていることを確認します。

  $sudo mongod

次に、ディレクトリを変更し、curl コマンドを実行してコンテンツを取得します。

  $ cd azuread $ node server.js

基本的な POST を試してください。

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

ここで期待される応答は、Passport レイヤーが認証エンドポイントへのリダイレクトを試みていることを示す 401 です。期待どおりの応答が返っています。

## お疲れさまでした。 OAuth2 を使用する REST API サービスが完成しました。

OAuth2 互換のクライアントを使用することなく、このサーバーを使用して最大限のことを実現できました。別のチュートリアルに進むことが必要になります。

Restify と OAuth2 を使用して REST API を実装する方法についての情報のみを探している場合は、サービスの開発を継続し、このサンプルで構築方法を習得するのに十分過ぎるコードを既に所有しています。

ADAL に関連するさらに高度な手順に興味がある場合は、学習を継続するために推奨される、サポートされる ADAL クライアントをいくつか次に示します。

開発マシンに複製するかダウンロードして、チュートリアルで説明されているように構成するだけで動作します。

[ADAL for iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[ADAL for Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)


[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->