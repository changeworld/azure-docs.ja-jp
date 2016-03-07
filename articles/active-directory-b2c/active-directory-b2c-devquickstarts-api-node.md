<properties
	pageTitle="B2C プレビュー: Node.js を使用して Web API をセキュリティで保護する | Microsoft Azure"
	description="B2C テナントからのトークンを受け付ける Node.JS Web API を作成する方法"
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
	ms.date="02/17/2016"
	ms.author="brandwe"/>

# B2C プレビュー: Node.js を使用して Web API をセキュリティで保護する

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]


> [AZURE.NOTE] この記事では、Azure AD B2C を使用してサインイン、サインアップ、プロファイルの管理を実装する方法については説明しません。ユーザーが認証された後の Web API の呼び出しに焦点を合わせています。まだであれば、先に「[Azure AD B2C プレビュー: .NET Web アプリをビルドする](active-directory-b2c-devquickstarts-web-dotnet.md)」で Azure Active Directory B2C の基本を学習してください。


> [AZURE.NOTE]	このサンプルは、[iOS B2C サンプル アプリケーション](active-directory-b2c-devquickstarts-ios.md)を使用して接続されるように作成されています。先にこのチュートリアルを行った後、そのサンプルに従ってください。

**Passport** は Node.js 用の認証ミドルウェアです。Passport は、非常に柔軟で高度なモジュール構造をしており、任意の Express ベースまたは Restify Web アプリケーションに、支障をきたすことなくインストールされます。包括的な認証手法セットにより、ユーザー名とパスワードを使用する認証、Facebook、Twitter などをサポートします。Azure Active Directory (Azure AD) 用の認証手法を開発しました。このモジュールをインストールし、Azure AD `passport-azure-ad` プラグインを追加します。

そのためには、次の手順を実行する必要があります。

1. アプリケーションを Azure AD に登録する。
2. Passport の `azure-ad-passport` プラグインを使用するようにアプリをセットアップする。
3. "to-do list" Web API を呼び出すように、クライアント アプリケーションを構成する。

このチュートリアルのコードは、[GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs) で管理されています。追加の参考資料として、[アプリのスケルトン (.zip ファイル) をダウンロード](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/skeleton.zip)したり、次のようにスケルトンを複製したりすることができます。

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```

完成したアプリケーションは、このチュートリアルの終わりに示しています。

> [AZURE.WARNING] 	B2C プレビューでは、Web API タスク サーバーとそのサーバーに接続するクライアントの両方に同じ**クライアント ID**/**アプリケーション ID** およびポリシーを使用する必要があります。これは、iOS と Android のチュートリアルにも当てはまります。これらのクイック スタートのいずれかでアプリケーションを作成したことがある場合は、新しい値を作成するのではなく、それらの値を使用してください。


## Azure AD B2C ディレクトリの取得

Azure AD B2C を使用するには、ディレクトリ (つまり、テナント) を作成しておく必要があります。ディレクトリは、ユーザー、アプリ、グループなどをすべて格納するためのコンテナーです。まだディレクトリを作成していない場合は、
先に進む前に [B2C ディレクトリを作成](active-directory-b2c-get-started.md)してください。

## アプリケーションの作成

次に、B2C ディレクトリにアプリを作成する必要があります。このディレクトリによって、アプリと安全に通信するために必要ないくつかの情報を Azure AD に提供します。ここでは、クライアント アプリと Web API の両方が単一の**アプリケーション ID** で表されます。これは、クライアント アプリと Web API が 1 つの論理アプリを構成するためです。アプリを作成するには、
[こちらの手順](active-directory-b2c-app-registration.md)に従います。次を行ってください。

- アプリケーションに **Web アプリまたは Web API** を含めます。
- **[応答 URL]** に「`http://localhost/TodoListService`」と入力します。これはこのサンプル コードで使用する既定の URL です。
- アプリケーション用の**アプリケーション シークレット**を作成し、それをメモしておきます。この情報は後で必要になります。このプロジェクトはすぐに必要になります。この値は、使用する前に [XML エスケープ](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape)する必要があることに注意してください。
- アプリに割り当てられた**アプリケーション ID** をコピーしておきます。この情報も後で必要になります。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## ポリシーの作成

Azure AD B2C では、すべてのユーザー エクスペリエンスが[ポリシー](active-directory-b2c-reference-policies.md)によって定義されます。このアプリには、3
つの ID エクスペリエンス (サインアップ、サインイン、および Facebook でのサインイン) が含まれます。
[ポリシーについてのリファレンス記事](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)で説明されているように、種類ごとに 1 つのポリシーを作成する必要があります。3 つのポリシーを作成するときは、以下の点に注意してください。

- サインアップ ポリシーで、**[表示名]** と他のサインアップ属性を選択します。
- すべてのポリシーで、アプリケーション要求として**表示名**と**オブジェクト ID** を選択します。その他のクレームも選択できます。
- ポリシーの作成後、各ポリシーの**名前**をメモしておきます。名前には、`b2c_1_` というプレフィックスが付加されています。これらのポリシー名は後で必要になります。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

3 つのポリシーを作成したら、アプリをビルドできます。

この記事では、作成したポリシーの使用方法については説明しません。ポリシーが Azure AD B2C でどのように機能するかを学習する場合は、「[Azure AD B2C プレビュー: .NET Web アプリを構築する](active-directory-b2c-devquickstarts-web-dotnet.md)」から始めてください。

## プラットフォーム用の Node.js のダウンロード

このサンプルを正常に使用するには、Node.js の実稼働するインストール環境が必要になります。

Node.js を [nodejs.org](http://nodejs.org) からインストールします。

## プラットフォーム用の MongoDB のインストール

このサンプルを正常に使用するには、MongoDB の実稼働するインストール環境も必要になります。MongoDB を使用して、REST API がサーバー インスタンス間で持続されるようにします。

MongoDB を [mongodb.org](http://www.mongodb.org) からインストールします。

> [AZURE.NOTE] このチュートリアルでは、MongoDB の既定のインストール環境およびサーバー エンドポイント (チュートリアルの記述時点では `mongodb://localhost`) が使用されることを想定しています。

## Web API への Restify モジュールのインストール

REST API のビルドには、Restify を使用します。Restify は、Express から派生した、最小限の柔軟な Node.js アプリケーション フレームワークです。Connect 上に REST API を構築するための堅牢な機能のセットが用意されています。

### Restify をインストールする

コマンド ラインで、ディレクトリを `azuread` に変更します。`azuread` ディレクトリがない場合は、作成します。

`cd azuread` または `mkdir azuread;`

次のコマンドを入力します。

`npm install restify`

このコマンドにより、Restify がインストールされます。

#### エラーが発生した場合

一部のオペレーティング システムでは、`npm` を使用すると、`Error: EPERM, chmod '/usr/local/bin/..'` というエラーが表示され、管理者としてアカウントを実行するように要求される場合があります。このような場合は、`sudo` コマンドを使用して、より高い権限レベルで `npm` を実行します。

#### DTrace エラーが発生した場合

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

コマンドの出力は次のように表示されます。

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

## Web API への Passport のインストール

[Passport](http://passportjs.org/) は Node.js 用の認証ミドルウェアです。Passport は、非常に柔軟で高度なモジュール構造をしており、任意の Express ベースまたは Restify Web アプリケーションに、支障をきたすことなくインストールされます。包括的な認証手法セットにより、ユーザー名とパスワードを使用する認証、Facebook、Twitter などをサポートします。Azure AD 用の認証手法を開発しました。このモジュールをインストールし、Azure AD 認証手法プラグインを追加します。

コマンド ラインで、ディレクトリを `azuread` に変更します (まだ変更していなかった場合)。

次のコマンドを入力して、Passport をインストールします。

`npm install passport`

コマンドの出力は次のようになります。

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## Web API への passport-azuread の追加

次に、Azure AD と Passport を接続する戦略のスイートである `passport-azuread` を使用して、OAuth 戦略を追加します。Rest API サンプルのベアラー トークン用に、この戦略を使用します。

> [AZURE.NOTE] OAuth2 は、任意の既知のトークン タイプを発行できるフレームワークを提供しますが、一部のトークン タイプのみが広範に使用されています。エンドポイントを保護するためのトークンが、ベアラー トークンです。これが、OAuth2 で最も広く発行されている種類のトークンです。多くの実装では、ベアラー トークンが、発行される唯一の種類のトークンであると想定されています。

コマンド ラインで、ディレクトリを `azuread` に変更します (まだ変更していなかった場合)。

次のコマンドを入力して、Passport `passport-azure-ad` モジュールをインストールします。

`npm install passport-azure-ad`

コマンドの出力は次のようになります。

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
``

## Web API への MongoDB モジュールの追加

MongoDB は、データ ストアとして使用します。そのため、モデルとスキーマを管理するために広く使用されているプラグインである Mongoose と、MongoDB のデータベース ドライバー (これも MongoDB と呼ばれます) の両方をインストールする必要があります。

* `npm install mongoose`
* `npm install mongodb`

## 追加モジュールをインストールする

次に、その他の必須モジュールをインストールします。

コマンド ラインで、ディレクトリを `azuread` に変更します (まだ変更していなかった場合)。

`cd azuread`

次のコマンドを入力して、モジュールを `node_modules` ディレクトリにインストールします。

* `npm install crypto`
* `npm install assert-plus`
* `npm install posix-getopt`
* `npm install util`
* `npm install path`
* `npm install connect`
* `npm install xml-crypto`
* `npm install xml2js`
* `npm install xmldom`
* `npm install async`
* `npm install request`
* `npm install underscore`
* `npm install grunt-contrib-jshint@0.1.1`
* `npm install grunt-contrib-nodeunit@0.1.2`
* `npm install grunt-contrib-watch@0.2.0`
* `npm install grunt@0.4.1`
* `npm install xtend@2.0.3`
* `npm install bunyan`
* `npm update`


## 依存関係を持つ server.js ファイルの作成

`server.js` ファイルは、Web API サーバー用のほとんどの機能を提供します。ほとんどのコードは、このファイルに追加します。運用環境では、ルートとコントローラーを分割するなどして、機能をより小さなファイルに分散する必要があります。このチュートリアルの目的では、この機能用に server.js を使用します。

コマンド ラインで、ディレクトリを `azuread` に変更します (まだ変更していなかった場合)。

`cd azuread`

エディターで `server.js` ファイルを作成します。以下の情報を追加します。

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

ファイルを保存します。後でこのファイルを使用します。

## Azure AD の設定を保存するための config.js ファイルの作成

このコード ファイルは、構成パラメーターを Azure AD ポータルから `Passport.js` ファイルに渡します。これらの構成値は、チュートリアルの最初の部分で Web API をポータルに追加したときに作成したものです。ここでは、コードをコピーした後に、これらのパラメーターにどのような値を設定するかについて説明します。

コマンド ラインで、ディレクトリを `azuread` に変更します (まだ変更していなかった場合)。

`cd azuread`

エディターで `config.js` ファイルを作成します。以下の情報を追加します。

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>',
identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration', // For using Microsoft you should never need to change this.
tenantName:'<tenant name>',
policyName:'b2c_1_<sign in policy name>',
};

```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### 必要な値

`IdentityMetadata`: これは、`passport-azure-ad` が ID プロバイダーの構成データを探す場所です。JSON Web トークンを検証するためのキーも、ここで探されます。Azure AD を使用する場合は、これを変更する必要はないでしょう。

`audience`: サービスを識別する、ポータルの Uniform Resource Identifier (URI) です。このサンプルでは `http://localhost/TodoListService` を使用します。

`tenantName`: テナントの名前 (例: **contoso.onmicrosoft.com**) です。

`policyName`: サーバーが受け取ったトークンを検証するポリシーです。クライアント アプリケーションでサインインに対して使用したのと同じポリシーを使用する必要があります。

> [AZURE.NOTE] この B2C プレビューでは、クライアントとサーバーの両方の設定に同じポリシーを使用します。既にチュートリアルを完了していて、これらのポリシーを作成してある場合は、再度作成する必要はありません。チュートリアルを完了していれば、サイトのクライアント チュートリアル用に新しいポリシーを設定する必要はないためです。

## 構成を server.js ファイルに追加する

これらの値は、アプリケーション全体で、作成した `config.js` ファイルから読み込む必要があります。これを行うには、`.config` ファイルを必須リソースとしてアプリケーションに追加し、グローバル変数を `config.js` ドキュメント内の変数に設定するだけです。

コマンド ラインで、ディレクトリを `azuread` に変更します (まだ変更していなかった場合)。

`cd azuread`

エディターで `server.js` ファイルを開きます。以下の情報を追加します。

```Javascript
var config = require('./config');
```
`server.js` に新しいセクションを追加して、次のコードを記述します。

```Javascript
// We pass these options in to the ODICBearerStrategy.
var options = {
// The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience
};
// array to hold logged-in users and the current logged-in user (owner)
var users = [];
var owner = null;
// Our logger
var log = bunyan.createLogger({
name: 'Microsoft Azure Active Directory Sample'
});
```

## Mongoose を使用した、MongoDB モデルおよびスキーマ情報の追加

以前に行った準備は、これらの 3 つのファイルを REST API サービスにまとめるときに役立ちます。

このチュートリアルでは、既に説明したように、タスクを格納するために MongoDB を使用します。

作成した `config.js` ファイルでは、データベースを **tasklist** と呼んでいました。それは、`mongoose_auth_local` 接続 URL の末尾に置くものでもありました。MongoDB では、事前にこのデータベースを作成する必要はありません。サーバー アプリケーションを初めて実行するときに、データベースが存在しない場合は、自動的に作成されます。

どの MongoDB データベースを使用するかを指定した後で、サーバー タスク用のモデルとスキーマを作成する追加コードを記述する必要があります。

### モデルの展開

このスキーマ モデルは、非常に単純です。必要に応じて拡張することができます。

`name`: だれがタスクに割り当てられているか。これは **string** です。

`task`: タスク自体。これは **string** です。

`date`: タスクの期限日。これは **datetime** です。

`completed`: タスクが完了したかどうか。これは **Boolean** です。

### コードでのスキーマの作成

コマンド ラインで、ディレクトリを `azuread` に変更します (まだ変更していなかった場合)。

`cd azuread`

エディターで `server.js` ファイルを開きます。構成エントリの下に、以下の情報を追加します。

```Javascript
// MongoDB setup
// Set up some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');
```
これは、MongoDB サーバーに接続し、スキーマ オブジェクトを返します。

### コードでモデルを作成するためのスキーマの使用

これまでに記述した以下のコードに、次のコードを追加します。

```Javascript
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
まずスキーマを作成してから、**ルート**を定義するときにコード全体でデータを格納するために使用するモデル オブジェクトを作成します。

## REST API タスク サーバーのルートの追加

これで、操作対象のデータベース モデルが作成されたので、REST API サーバー用に使用するルートを追加します。

### Restify でのルートについて

ルートは、Restify でも、Express スタックを使用する場合とまったく同じように動作します。ルートは、クライアント アプリが呼び出すことが想定される URI を使用して定義されます。多くの場合、ルートは個別のファイルで定義する必要があります。このチュートリアルでは、すべてのルートを `server.js` ファイルに置きます。運用環境では、それぞれ独自のファイルに格納することをお勧めします。

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

これは、最も基本的なレベルのパターンです。Restify および Express では、アプリケーション タイプの定義、異なるエンドポイントにまたがる複雑なルーティングなどのより高度な機能を提供できますが、このチュートリアルでは、ルートを単純なままにしておきます。

#### サーバーへの既定のルートの追加

ここで、**create**、**retrieve**、**update**、および **delete** の基本的な CRUD ルートを追加します。

コマンド ラインで、ディレクトリを `azuread` に変更します (まだ変更していなかった場合)。

`cd azuread`

エディターで `server.js` ファイルを開きます。上で作成したデータベース エントリの下に、以下の情報を追加します。

```Javascript
/**
*
* APIs for our REST task server
*/
// Create a task
function createTask(req, res, next) {
// Restify currently has a bug that doesn't allow you to set default headers
// The headers comply with CORS and allow us to mongodbServer our response to any origin
res.header("Access-Control-Allow-Origin", "*");
res.header("Access-Control-Allow-Headers", "X-Requested-With");
// Create a new task model, fill it up and save it to Mongodb
var _task = new Task();
if (!req.params.task) {
req.log.warn({
params: p
}, 'createTodo: missing task');
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
// Restify currently has a bug that doesn't allow you to set default headers
// The headers comply with CORS and allow us to mongodbServer our response to any origin
res.header("Access-Control-Allow-Origin", "*");
res.header("Access-Control-Allow-Headers", "X-Requested-With");
log.info("listTasks was called for: ", owner);
Task.find({
owner: owner
}).limit(20).sort('date').exec(function(err, data) {
if (err)
return next(err);
if (data.length > 0) {
log.info(data);
}
if (!data.length) {
log.warn(err, "There is no tasks in the database. Add one!");
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

#### ルートのエラー処理の追加

発生した問題に関してクライアントが理解できる方法で通信できるように、いくつかのエラー処理を追加する必要があります。

上で記述したコードに、以下のコードを追加します。

```Javascript
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


## サーバーの作成

ここまでで、データベースを定義し、ルートを設定できました。最後に、呼び出しを管理するサーバー インスタンスを追加します。

Restify と Express では REST API サーバーの詳細なカスタマイズが可能ですが、ここでは最も基本的な設定を使用します。

```Javascript
/**
* Our server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directory TODO Server",
version: "2.0.1"
});
// Ensure that we don't drop data on uploads
server.pre(restify.pre.pause());
// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());
// Handle annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());
// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());
// Allow five requests/second by IP, and burst to 10
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
}));
```
## サーバーへのルートの追加 (認証なし)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers is protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the passport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiment with removing API protection
/* by removing the passport.authenticate() method like this:
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
## OAuth サポートを追加する前のサーバーの実行

認証を追加する前に、サーバーをテストしておく必要があります。

これを行うための最も簡単な方法は、コマンド ラインで `curl` を使用することです。しかしその前に、出力を JSON として解析できるようにする単純なユーティリティが必要です。まず、JSON ツールをインストールします。

`$npm install -g jsontool`

これにより、JSON ツールがグローバルにインストールされます。JSON ツールをインストールしたら、サーバーをテストします。

MongoDB インスタンスが実行されていることを確認してください。

`$sudo mongodb`

`azuread` ディレクトリに移動して、`curl` を使用します。

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

次のように、タスクを追加します。

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
ユーザー "Brandon" のタスクを、次の方法で一覧表示できます。

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

これが正常に機能したら、OAuth を REST API サーバーに追加できます。

これで、MongoDB がある REST API サーバーが用意できました。

## REST API サーバーへの認証の追加

実行中の REST API サーバーを用意できたので、Azure AD にとって有益な動作をするように変更できます。

コマンド ラインで、ディレクトリを `azuread` に変更します (まだ変更していなかった場合)。

`cd azuread`

### passport-azure-ad に含まれている OIDCBearerStrategy の使用

ここまで、認証がまったく行われない REST ToDo サーバーを構築してきました。ここで、認証の組み込みを始めることができます。

最初に、Passport を使用することを指定する必要があります。他のサーバー構成のすぐ下に、これを追加します。

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts Passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
API を記述するときは、ユーザーがなりすますことができないトークンの一意の情報に常にデータをリンクする必要があります。サーバーは、ToDo 項目を保存するときに、"owner" フィールドに配置される (token.oid をとおして呼び出される) トークン内のユーザーの**オブジェクト ID** に基づいてそれらを保存します。これにより、そのユーザーだけが ToDo 項目にアクセスでき、他のユーザーは入力された ToDo 項目にアクセスできないようになります。API 内で "owner" が公開されることはないため、外部ユーザーは、他のユーザーの ToDo 項目を、認証される場合でも要求することができます。

次に、`passport-azure-ad` に含まれているベアラー戦略を使用します。ここでは、コードを見るだけです。 このコードを、上述のコードの後ろに置きます。

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementer.
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
var oidcStrategy = new OIDCBearerStrategy(options,
function(token, done) {
log.info('verifying the user');
log.info(token, 'was the token retrieved');
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
passport.use(oidcStrategy);
```

Passport は、Twitter や Facebook などのすべての戦略に、パターンを使用します。これは、すべての戦略記述者が準拠する戦略に似ています。それを、パラメーターとして `token` および `done` を持つ `function()` に渡します。戦略は、すべての作業が終わった後で返されます。もう一度要求しなくて済むように、ユーザーを格納し、トークンを保存する必要があります。

> [AZURE.IMPORTANT]
上記のコードでは、サーバーに認証を求めたすべてのユーザーを受け入れています。これは、自動登録と呼ばれます。運用サーバーでは、指定された登録プロセスを先に実行していないユーザーにはアクセスを許可しないように設定できます。これは、Facebook を使用した登録を許可するものの、後で追加情報の入力を求めるコンシューマー アプリで通常見られるパターンです。これがコマンド ライン プログラムでなければ、返されるトークン オブジェクトから電子メールを抽出した後、追加情報の入力を要求できたでしょう。これはテスト サーバーなので、単純にユーザーをメモリ内データベースに追加します。

### エンドポイントの保護

`passport.authenticate()` 呼び出しを指定するときに、使用するプロトコルを使用して、エンドポイントを保護します。

さらに興味深いことを行うためにサーバー コードのルートを編集できます。

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

## 自分が拒否されることを確認するために、サーバー アプリケーションを再実行

再び `curl` を使用して、エンドポイントに対して OAuth2 保護が有効になっていることを確認できます。この操作は、このエンドポイントに対して、クライアント SDK のいずれかを実行する前に行います。返されるヘッダーだけで、正しく操作できていることを確認するには十分です。

MongoDB インスタンスが実行されていることを確認してください。

	$sudo mongodb

ディレクトリに移動して、`curl` を使用します。

	$ cd azuread
	$ node server.js

基本的な POST を試してください。

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

401 エラーが、適切な応答です。これは、Passport レイヤーが承認エンドポイントへリダイレクトしようとしていることを示します。


## OAuth2 を使用する REST API サービスの確立

OAuth2 互換のクライアントを使用することなく、このサーバーを使用して最大限のことを実現できました。これ以上のことを行うには、他のチュートリアルが必要になります。

Restify および OAuth2 を使用して REST API を実装する方法についての情報だけが必要である場合は、既に十分なコードがあるため、サービスの開発を続けて、この例を基にして構築することができます。

参考のため、完全なサンプル (構成値を除く) が [.zip ファイルとして提供されています](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/complete.zip)。GitHub から複製することもできます。

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```


## 次のステップ

これ以降は、次のような、さらに高度なトピックに進むことができます。

[B2C で iOS を使用して Web API に接続する](active-directory-b2c-devquickstarts-ios.md)

<!---HONumber=AcomDC_0224_2016-->