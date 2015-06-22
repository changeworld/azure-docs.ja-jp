<properties
	pageTitle="Azure AD NodeJS の概要 | Microsoft Azure"
	description="認証のために Azure AD と連携する Node.js Web API を構築する方法"
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
	ms.date="04/28/2015"
	ms.author="brandwe"/>

# ノード用の Web API の概要

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

このチュートリアルでは、OAuth2 プロトコルを使用して API を保護するために Azure Active Directory と連携する REST API サービスを、すばやく簡単にセットアップする方法について説明します。ダウンロード ファイルに含まれるサンプル サーバーは、OSX と Linux を除く任意のプラットフォームで動作するように設計されています。

このチュートリアルを完了すると、次の機能を備え、実稼働する REST API サーバーを構築できるようになります。

* 永続ストレージとして MongoDB を使用する JSON との REST API インターフェイスを実行する node.js サーバー
* ベアラー トークンと Azure Active Directory を使用する OAuth2 API 保護を活用する REST API


この実稼働するサンプルに関連するすべてのソース コードが、Apache 2.0 ライセンスの下で、GitHub で提供されています。自由にクローン (できれば、フォーク) 操作を行って、フィードバックおよびプル リクエストを提供してください。

## Node.js モジュールとは

このチュートリアルでは、Node.js を使用します。モジュールは、アプリケーションに対して特定の機能を提供する読み込み可能な JavaScript パッケージです。モジュールは、通常、Node.js NPM コマンド ライン ツールを使用して NPM インストール ディレクトリにインストールされますが、HTTP モジュールなどの一部のモジュールは、コア Node.js パッケージに含まれています。インストールされたモジュールは、Node.js インストール ディレクトリのルートにある node_modules ディレクトリに保存されます。node_modules ディレクトリの各モジュールは、それが依存関係を持つすべてのモジュールを含む独自の node_modules ディレクトリを維持し、各必須モジュールは node_modules ディレクトリを持ちます。この再帰的なディレクトリ構造は、依存関係チェーンを表します。

この依存関係チェーンにより、アプリケーションのフットプリントは大きくなりますが、すべての依存関係が満たされ、開発で使用されるモジュールのバージョンが運用で使用されることが保証されます。このため、運用アプリの動作がより期待どおりになり、ユーザーに悪影響を及ぼすバージョン管理の問題を防ぐことができます。

## 手順 1. Azure AD テナントを登録する

このサンプルを使用するには、Azure Active Directory テナントが必要です。テナントについて不明な場合、または取得方法が不明な場合は、「[How to get an Azure Active Directory tenant (Azure Active Directory テナントを取得する方法)](active-directory-howto-tenant.md)」を参照してください。

## 手順 2. テナントを Web API に追加する

Azure Active Directory テナントを取得したら、このサンプルをテナントに追加して、API を保護するために使用できるようにします。

アプリケーションがユーザー認証を処理できるようにするには、まず、アプリケーションをテナントに登録する必要があります。

- Microsoft Azure の管理ポータルにサインインします。
- 左側のナビゲーションで **[Active Directory]** をクリックします。
- アプリケーションの登録先となるテナントを選択します。
- **[アプリケーション]** タブをクリックし、下部のドロアーで [追加] をクリックします。
- 画面の指示に従い、新しい **Web アプリケーションまたは WebAPI** を作成します。
    - アプリケーションの **[名前]** には、エンド ユーザーがアプリケーションの機能を把握できるような名前を設定します。
    -	**[サインオン URL]** は、アプリのベース URL です。スケルトンの既定値は、`https://localhost:8888` です。
    - **[アプリケーション ID/URI]** は、アプリケーションの一意識別子です。形式は、`https://<tenant-domain>/<app-name>` (たとえば、`https://contoso.onmicrosoft.com/my-first-aad-app`) です。
- 登録が完了すると、AAD により、アプリに一意のクライアント ID が割り当てられます。この値は次のセクションで必要になるので、[構成] タブからコピーします。

## 手順 3. プラットフォーム用の Node.js をダウンロードする
このサンプルを正常に使用するには、Node.js の実稼働するインストール環境が必要になります。

Node.js を [http://nodejs.org](http://nodejs.org) からインストールします。

## 手順 4. プラットフォームに MongoDB をインストールする

このサンプルを正常に使用するには、MongoDB の実稼働するインストール環境が必要になります。MongoDB を使用して、REST API がサーバー インスタンス間で持続されるようにします。

MongoDB を [http://mongodb.org](http://www.mongodb.org) からインストールします。

**注:** このチュートリアルでは、MongoDB の既定のインストール環境およびサーバー エンドポイント (チュートリアルの記述時点では mongodb://localhost) が使用されることを想定しています。


## 手順 5. Restify モジュールを Web API にインストールする

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


## 手順 6. Passport.js を Web API にインストールする

[Passport](http://passportjs.org/) は Node.js 用の認証ミドルウェアです。Passport は、非常に柔軟で高度なモジュール構造をしており、任意の Express ベースまたは Resitify Web アプリケーションに、支障をきたすことなくドロップされます。包括的な認証手法セットにより、ユーザー名とパスワードを使用する認証、Facebook、Twitter などをサポートします。Azure Active Directory 用の認証手法を開発しました。このモジュールをインストールし、Azure Active Directory 認証手法プラグインを追加します。

コマンド ラインで、azuread ディレクトリに移動します。

次のコマンドを入力して、Passport.js をインストールします。

`npm install passport`

コマンドの出力は次のように表示されます。

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## 手順 7. Passport.js ベアラー トークン サポートを Web API に追加する

次に、[Passport](http://passportjs.org/) 用のベアラー ハンドラーである passport-bearer-http を使用して、ベアラー手法を追加します。node-jwt を使用することによって、JWT トークン ハンドラー サポートも追加します。

**注:** OAuth2 は、任意の既知のトークン タイプを発行できるフレームワークを提供しますが、一部のトークン タイプのみが広範に使用されています。エンドポイントを保護するために、ベアラー トークンが広く使用されるようになっています。ベアラー トークンは、OAuth2 の最も広く発行されるタイプのトークンで、多くの実装では、発行されるトークンのタイプとしてベアラー トークンのみを想定しています。

コマンド ラインで、**azuread** ディレクトリに移動します。

次のコマンドを入力して、Passport.js モジュールをインストールします。

- `npm install passport-oauth`
- `npm install passport-http-bearer`
- `npm install node-jwt`

コマンドの出力は次のように表示されます。

	ms-passport-wsfed-saml2@0.3.8 node_modules\passport-oauth  
	├── xtend@2.0.3
	├── xml-crypto@0.0.9
	├── xmldom@0.1.13
	└── xml2js@0.1.14 (sax@0.5.2)


## 手順 8. MongoDB モジュールを Web API に追加する

データ ストアとして MongoDB を使用します。そのため、どちらも広く使用されている、Mongoose と呼ばれるモデルおよびスキーマを管理するためのプラグインと、MongoDB という名前の MongoDB 用のデータベース ドライバーの両方をインストールする必要があります。


* `npm install mongoose`
* `npm install mongodb`

## 手順 9. その他のモジュールをインストールする

次に、その他の必須モジュールをインストールします。


コマンド ラインで、**azuread** フォルダーに移動します (現在のディレクトリがこのディレクトリではない場合)。

`cd azuread`


次のコマンドを入力して、次のモジュールを node_modules ディレクトリにインストールします。

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


## 手順 10. 依存関係を定義する server.js を作成する

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
```

ファイルを保存します。この後すぐに、このファイルを使用します。

## 手順 11. Azure AD 設定を格納するための構成ファイルを作成する

このコード ファイルは、構成パラメーターを Azure Active Directory ポータルから Passport.js に渡します。これらの構成値は、チュートリアルの初期の手順で Web API をポータルに追加したときに作成されています。ここでは、コードをコピーした後に、これらのパラメーターにどのような値を設定するかについて説明します。


コマンド ラインで、**azuread** フォルダーに移動します (現在のディレクトリがこのディレクトリではない場合)。

`cd azuread`

お気に入りのエディターを使用して `config.js` ファイルを作成し、次の情報を追加します。

```Javascript
// Don't commit this file to your public repos
    exports.creds = {
    mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
    openid_configuration: 'https://login.microsoftonline.com/common/.well-known/openid-configuration', // For using Microsoft you should never need to change this.
    openid_keys: 'https://login.microsoftonline.com/common/discovery/keys', // For using Microsoft you should never need to change this. If absent will attempt to get from openid_configuration
}

```



**注:** ほとんどの場合、これらの値を変更する必要はありません。

**注:** キーは頻繁に展開されます。"openid_keys" URL からキーを常に取得し、アプリがインターネットにアクセスできるようにします。


## 手順 12: 構成を server.js ファイルに追加する

これらの値は、アプリケーション全体で、作成した構成ファイルから読み込む必要があります。これを行うには、.config ファイルを必須リソースとしてアプリケーションに追加し、グローバル変数を config.js ドキュメントに設定するだけです。

コマンド ラインで、**azuread** フォルダーに移動します (現在のディレクトリがこのディレクトリではない場合)。

`cd azuread`

お気に入りのエディターを使用して `server.js` ファイルを開き、次の情報を追加します。

```Javascript
var config = require('./config');
```
次に、`server.js` に新しいセクションを追加して、次のコードを記述します。

```Javascript
/**
* Setup some configuration
*/
var mongoose = require('mongoose/');
var serverPort = process.env.PORT || 8888;
var serverURI = ( process.env.PORT ) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

```
## 手順 13. メタデータ/トークンの解析を支援する metadata.js ヘルパー ファイルを作成する

目標は、アプリケーション ロジックのみを server.js ファイルに維持することなので、いくつかのヘルパー メソッドを別のファイルに配置します。これらのメソッドは、OpenID Connect メタデータを解析するのに役立つだけであり、主要なシナリオには関係しません。これらを別の場所に配置することをお勧めします。このチュートリアルの手順を進むに従って、より多くの情報をこのファイルに追加します。

***注:*** この metadata.js ファイルは、SAML および WS-Fed の XML だけでなく、OpenID Connect の JSON も解析することに注意してください。これは、設計上、このファイルを他のサンプルでも同様に使用できることを意味します。ここでは、このことを無視してかまいません。

コマンド ラインで、**azuread** フォルダーに移動します (現在のディレクトリがこのディレクトリではない場合)。

`cd azuread`

お気に入りのエディターを使用して `metadata.js` ファイルを作成し、次の情報を追加します。

```Javascript

'use strict';

var xml2js = require('xml2js');
var request = require('request');
var aadutils = require('./aadutils');
var async = require('async');

// Logging

var bunyan = require('bunyan');
var log = bunyan.createLogger({name: 'Microsoft OpenID Connect Passport Strategy'});

var Metadata = function (url, authtype) {


  if(!url) {
    throw new Error("Metadata: url is a required argument");
  }
  if(!authtype) {
    throw new Error('OIDCBearerStrategy requires an authentication type specified to metadata parser. Valid types are saml, wsfed, or odic"');
  }

  this.url = url;
  this.metadata = null;
  this.authtype = authtype;
  log.info(authtype, 'Metadata requested for authentication type');
};

Object.defineProperty(Metadata, 'url', {
  get: function () {
    return this.url;
  }
});

Object.defineProperty(Metadata, 'saml', {
  get: function () {
    return this.saml;
  }
});

Object.defineProperty(Metadata, 'wsfed', {
  get: function () {
    return this.wsfed;
  }
});

Object.defineProperty(Metadata, 'oidc', {
  get: function () {
    return this.oidc;
  }
});


Object.defineProperty(Metadata, 'metadata', {
  get: function () {
    return this.metadata;
  }
});

Metadata.prototype.updateSamlMetadata = function(doc, next) {
  log.info('Request to update the SAML Metadata');
  try {

    this.saml = {};

    var entity = aadutils.getElement(doc, 'EntityDescriptor');
    var idp = aadutils.getElement(entity, 'IDPSSODescriptor');
    var signOn = aadutils.getElement(idp[0], 'SingleSignOnService');
    var signOff = aadutils.getElement(idp[0], 'SingleLogoutService');
    var keyDescriptor = aadutils.getElement(idp[0], 'KeyDescriptor');
    this.saml.loginEndpoint = signOn[0].$.Location;
    this.saml.logoutEndpoint = signOff[0].$.Location;

    // copy the x509 certs from the metadata
    this.saml.certs = [];
    for (var j=0;j<keyDescriptor.length;j++) {
      this.saml.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
    }
    next(null);
  } catch (e) {
    next(new Error('Invalid SAMLP Federation Metadata ' + e.message));
  }
};

Metadata.prototype.updateOidcMetadata = function(doc, next) {
  log.info('Request to update the Open ID Connect Metadata');
  try {
    this.oidc = {};

    var issuer = doc['issuer'];
    var keyDescriptor = aadutils.getElement(idp[0], 'keys');

    // copy the x509 certs from the metadata
    this.oidc.certs = [];
    for (var j=0;j<keyDescriptor.length;j++) {
      this.oidc.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
    }
    next(null);
  } catch (e) {
    next(new Error('Invalid Open ID Connect Federation Metadata ' + e.message));
  }
};


Metadata.prototype.updateWsfedMetadata = function(doc, next) {
  log.info('Request to update the WS Federation Metadata');
  try {
    this.wsfed = {};
    var entity = aadutils.getElement(doc, 'EntityDescriptor');
    var roles = aadutils.getElement(entity, 'RoleDescriptor');
    for(var i = 0; i < roles.length; i++) {
      var role = roles[i];
      if(role['fed:SecurityTokenServiceEndpoint']) {
        var endpoint = role['fed:SecurityTokenServiceEndpoint'];
        var endPointReference = aadutils.getFirstElement(endpoint[0],'EndpointReference');
        this.wsfed.loginEndpoint = aadutils.getFirstElement(endPointReference,'Address');

        var keyDescriptor = aadutils.getElement(role, 'KeyDescriptor');
        // copy the x509 certs from the metadata
        this.wsfed.certs = [];
        for (var j=0;j<keyDescriptor.length;j++) {
          this.wsfed.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
        }
        break;
      }
    }

    return next(null);
  } catch (e) {
    next(new Error('Invalid WSFED Federation Metadata ' + e.message));
  }
};

Metadata.prototype.fetch = function(callback) {
  var self = this;
  log.info("Fetching metadata from the provided metadata URL: " + self.url);
  async.waterfall([
    // fetch the Federation metadata for the AAD tenant
    function(next){
      request(self.url, function (err, response, body) {
        if(err) {
          next(err);
        } else if(response.statusCode !== 200) {
          next(new Error("Error:" + response.statusCode +  " Cannot get AAD Federation metadata from " + self.url));
        } else {
          log.info(body, "retreived");
          next(null, body);
        }
      });
    },
    function(body, next){
      // parse the AAD Federation metadata xml

      if(self.authtype == "saml" || self.authtype == "wsfed") {
      log.info(body, "Parsing XML retreived from the endpoint");
      var parser = new xml2js.Parser({explicitRoot:true});
      // Note: xml responses from Azure AAD have a leading \ufeff which breaks xml2js parser!
      parser.parseString(body.replace("\ufeff", ""), function (err, data) {
        self.metatdata = data;
        next(err);

      });
    } else if(self.authtype == "oidc") {
      log.info(body, "Parsing JSON retreived from the endpoint");
      JSON.parse(body, function (err, data) {
        self.metatdata = data;
        next(err);
      });

    } else {

       next(new Error("Error: No Authentication type specified to metadata parser. Valid types are saml, wsfed, or odic"));
    }

    },

    function(next){
      if(self.authtype = "saml") {
      // update the SAML SSO endpoints and certs from the metadata
      self.updateSamlMetadata(self.metatdata, next);
    }},
    function(next){
      if(self.authtype = "wsfed") {
      // update the SAML SSO endpoints and certs from the metadata
      self.updateWsfedMetadata(self.metatdata, next);
    }},
    function(next){
      if(self.authtype = "oidc") {
      self.updateOidcMetadata(self.metadata, next);
    }},
  ], function (err) {
    // return err or success (err === null) to callback
    callback(err);
  });
};

exports.Metadata = Metadata;
```
コードからわかるように、`config.js` に渡された openid URL を受け取り、`server.js` ファイルで使用する情報向けに解析するだけです。このコードを調査して、必要に応じて拡張することをお勧めします。

### metadata.js ファイルを server.js ファイルに読み込む

記述したメソッドの取得場所をサーバーに通知する必要があります。

コマンド ラインで、**azuread** フォルダーに移動します (現在のディレクトリがこのディレクトリではない場合)。

`cd azuread`

お気に入りのエディターを使用して `server.js` ファイルを開き、次の情報を追加します。

```Javascript
var metadata = require('./metadata);
```
次に、`Configuration` セクションの末尾にこの呼び出しを追加し、`config.js` のメタデータ ドキュメントを、記述したばかりのパーサーに送信します。

```Javascript
this.aadutils = new var Metadata = require('./metadata').Metadata;
```

## 手順 14. Moongoose を使用して MongoDB モデルとスキーマ情報を追加する

これまでの準備が報われるときが来ました。これら 3 つのファイルを一緒に REST API サービスに取り込みます。

このチュートリアルでは、***手順 4.*** で説明しているように、MongoDB を使用してタスクを格納します。

***手順 11.*** で作成した `config.js` ファイルは、mogoose_auth_local 接続 URL の末尾に配置されているので、このファイルから再呼び出しを行うと、データベース `tasklist` が呼び出されました。このデータベースを MongoDB で事前に作成する必要はありません。存在しない場合、サーバー アプリケーションの初回実行時に作成されます。

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
/**
*
* Connect to MongoDB
*/

global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;  
```
これは、MongoDB サーバーに接続し、Schema オブジェクトを返します。

#### Schema を使用してコード内でモデルを作成する

これまでに記述した以下のコードに、次のコードを追加します。

```Javascript
/**
/ Here we create a schema to store our tasks. Pretty simple schema for now.
*/

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

## 手順 15. Task REST API サーバー用ルートを追加する

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

#### 既定のルートをサーバーに追加する

Create、Retrieve、Update、および Delete の基本的な CRUD ルートを追加します。

コマンド ラインで、**azuread** フォルダーに移動します (現在のディレクトリがこのディレクトリではない場合)。

`cd azuread`

お気に入りのエディターを使用して `server.js` ファイルを開き、これまでに作成したデータベース エントリの下に次の情報を追加します。

```Javascript

/**
 *
 * APIs
 */

function createTask(req, res, next) {

	// Resitify currently has a bug which doesn't allow you to set default headers
  	// This headers comply with CORS and allow us to mongodbServer our response to any origin

  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
  var _task = new Task();

        if (!req.params.task) {
                req.log.warn('createTask: missing task');
                next(new MissingTaskError());
                return;
        }


  _task.owner = req.params.owner;
   _task.task = req.params.task;
   _task.date = new Date();

  _task.save(function (err) {
  	if (err) {
        req.log.warn(err, 'createTask: unable to save');
        next(err);
    } else {
    res.send(201, _task);

			}
  });

  return next();

}


/**
 * Deletes a Task by name
 */
function removeTask(req, res, next) {

        Task.remove( { task:req.params.task }, function (err) {
                if (err) {
                        req.log.warn(err,
                                     'removeTask: unable to delete %s',
                                     req.params.task);
                        next(err);
                } else {
                        res.send(204);
                        next();
                }
        });
}

/**
 * Deletes all Tasks. A wipe
 */
function removeAll(req, res, next) {
        Task.remove();
        res.send(204);
        return next();
}    });
}


/**
 *
 *
 *
 */
function getTask(req, res, next) {


        Task.find(req.params.name, function (err, data) {
                if (err) {
                        req.log.warn(err, 'get: unable to read %s', req.params.name);
                        next(err);
                        return;
                }

                res.json(data);
        });

        return next();
}


/**
 * Simple returns the list of TODOs that were loaded.
 *
 */

function listTasks(req, res, next) {
  // Resitify currently has a bug which doesn't allow you to set default headers
  // This headers comply with CORS and allow us to mongodbServer our response to any origin

  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "X-Requested-With");

  console.log("server getTasks");

  Task.find().limit(20).sort('date').exec(function (err,data) {

    if (err)
      return next(err);

    if (data.length > 0) {
            console.log(data);
        }

    if (!data.length) {
            console.log('there was a problem');
            console.log(err);
            console.log("There is no tasks in the database. Did you initalize the database as stated in the README?");
        }

    else {

        res.json(data);

        }
  });

  return next();
}
```

### ルートに対するエラー処理を追加する

いくつかのエラー処理を追加して、問題が発生したクライアントに、クライアントが理解できる方法で通信できるようにすることが推奨されます。

次のコードを、これまでに記述したコードの下に追加します。

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


function TaskExistsError(name) {
        assert.string(name, 'name');

        restify.RestError.call(this, {
                statusCode: 409,
                restCode: 'TaskExists',
                message: name + ' already exists',
                constructorOpt: TaskExistsError
        });

        this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);


function TaskNotFoundError(name) {
        assert.string(name, 'name');

        restify.RestError.call(this, {
                statusCode: 404,
                restCode: 'TaskNotFound',
                message: name + ' was not found',
                constructorOpt: TaskNotFoundError
        });

        this.name = 'TaskNotFoundError';
}

util.inherits(TaskNotFoundError, restify.RestError);
```


## 手順 16. サーバーを作成する

データベースの定義およびルートの配置が完了したので、最後に、呼び出しを管理するサーバー インスタンスを追加します。

Restify (および Express) では、REST API サーバーに対してより高度なカスタマイズを実行できますが、デモの目的に沿って、最も基本的なセットアップを使用します。

```Javascript
/**
 * Our Server
 */


var server = restify.createServer({
        name: "Azure Active Directroy TODO Server",
    version: "1.0.0",
    formatters: {
        'application/json': function(req, res, body){
            if(req.params.callback){
                var callbackFunctionName = req.params.callback.replace(/[^A-Za-z0-9_.]/g, '');
                return callbackFunctionName + "(" + JSON.stringify(body) + ");";
            } else {
                return JSON.stringify(body);
            }
        },
        'text/html': function(req, res, body){
            if (body instanceof Error)
                        return body.stack;

                      if (Buffer.isBuffer(body))
                        return body.toString('base64');

                return util.inspect(body);
        },
        'application/x-www-form-urlencoded': function(req, res, body){
            if (body instanceof Error) {
                    res.statusCode = body.statusCode || 500;
                    body = body.message;
            } else if (typeof (body) === 'object') {
                body = body.task || JSON.stringify(body);
            } else {
                body = body.toString();
            }

        res.setHeader('Content-Length', Buffer.byteLength(body));
        return (body);
        }
    }
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

        // This lets us push JSON to the REST API endpoint as well. Maps x: y as /name:value

        server.use(restify.bodyParser({ mapParams: false }));

        /// Now the real handlers. Here we just CRUD

        server.get('/tasks', listTasks);
        server.head('/tasks', listTasks);
        server.get('/tasks/:name', getTask);
        server.head('/tasks/:name', getTask);
        server.post('/tasks/:name/:task', createTask);
        server.del('/tasks/:name/:task', removeTask);
        server.del('/tasks/:name', removeTask);
        server.del('/tasks', removeAll, function respond(req, res, next) { res.send(204); next(); });


        // Register a default '/' handler

        server.get('/', function root(req, res, next) {
                var routes = [
                        'GET     /',
                        'POST    /tasks/:name/:task',
                        'GET     /tasks',
                        'PUT     /tasks/:name',
                        'GET     /tasks/:name',
                        'DELETE  /tasks/:name/:task'
                ];
                res.send(200, routes);
                next();
        });

  server.listen(serverPort, function() {

  var consoleMessage = '\n Azure Active Directory Tutorial'
  consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++'
  consoleMessage += '\n %s server is listening at %s';
  consoleMessage += '\n Open your browser to %s/tasks\n';
  consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n'
  consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n'
  consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n'  

  console.log(consoleMessage, server.name, server.url, server.url, server.url);

});
```

## 手順 17. OAuth サポートを追加する前に、サーバーを実行する

チュートリアルの OAuth の説明に進む前に、誤りがないことを確認することが推奨されます。

これを行うための最も簡単な方法は、コマンド ラインで `curl` を使用することです。これを行うには、出力を JSON として解析することを可能にする単純なユーティリティが必要です。このため、以降のすべてのサンプルで使用する [JSON](https://github.com/trentm/json) ツールをインストールします。

	$npm install -g jsontool

これにより、JSON ツールがグローバルにインストールされます。ツールがインストールされたので、サーバーの操作を開始します。

まず、monogoDB インスタンスが動作していることを確認します。

	$sudo mongod

次に、ディレクトリを変更し、curl コマンドを実行してコンテンツを取得します。

	$ cd azuread
	$ node server.js

	$ curl -isS http://127.0.0.1:8888 | json
	HTTP/1.1 200 OK
	Connection: close
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 145
	Date: Wed, 29 Jan 2014 03:41:24 GMT

	[
  	"GET     /",
  	"POST    /tasks/:owner/:task",
  	"GET     /tasks",
  	"DELETE  /tasks",
  	"PUT     /tasks/:owner",
  	"GET     /tasks/:owner",
  	"DELETE  /tasks/:task"
	]

最後に、タスクを次のように追加します。

	$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello

次のような応答が返ります。

	HTTP/1.1 201 Created
	Connection: close
	Access-Control-Allow-Origin: *
	Access-Control-Allow-Headers: X-Requested-With
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 5
	Date: Tue, 04 Feb 2014 01:02:26 GMT

	Hello

Brandon 用のタスクを次の方法でリストできます。

	$ curl -isS http://127.0.0.1:8888/tasks/brandon/

これらのすべてが正常に機能した場合、OAuth を REST API サーバーに追加できます。

## 手順 18. Passport.js コードを REST API サーバーに追加する

実稼働する REST API を作成できたので、次に、Azure AD に対して使用できるようにします。

コマンド ラインで、**azuread** フォルダーに移動します (現在のディレクトリがこのディレクトリではない場合)。

`cd azuread`

### 手順 1. Passport モジュールを追加する

お気に入りのエディターを使用して `server.js` ファイルを開き、読み込むモジュールを以前に記述した場所の下に、次の情報を追加します。これは、ファイルの上部で、`var aadutils = require('./aadutils');` インポートの直下に配置する必要があります。

```Javascript
/*
*
* Load our old friend Passport for OAuth2 flows
*/

var passport = require('passport')
  , OAuth2Strategy = require('passport-oauth').OAuth2Strategy;
```

### 手順 2.認証を使用していることをサーバーに通知する

お気に入りのエディターを使用して `server.js` ファイルを開き、ルートを定義した **server.get() **と、**server.listen()** メソッドの間に、次の情報を追加します。


Restify に対して、その `authorizationParser()` の使用を開始し、Authorization ヘッダーのコンテンツを確認するように通知する必要があります。

```Javascript
        server.use(restify.authorizationParser());


```


### 手順 3.Passport OAuth2 モジュールをコードに追加する

ここでは、config.js ファイルに追加した OAuth2 固有のパラメーターを使用します。`aadutils.js` ファイルがフェデレーション メタデータ ドキュメントの解析を正常に行った場合、これらのすべての値を (config.js ファイルでブランクであっても)、設定する必要があります。

```Javascript
// Now our own handlers for authentication/authorization
// Here we only use Oauth2 from Passport.js

passport.use('provider', new OAuth2Strategy({
    authorizationURL: authEndpoint,
    tokenURL: tokenEndpoint,
    clientID: clientID,
    clientSecret: clientSecret,
    callbackURL: callbackURL
  },
  function(accessToken, refreshToken, profile, done) {
    User.findOrCreate({ UserId: profile.id }, function(err, user) {
      done(err, user);
    });
  }
));

// Let's start using Passport.js

server.use(passport.initialize());

```
### 手順 4. ルートを OAuth 認証に追加する

```Javascript
// Redirect the user to the OAuth 2.0 provider for authentication.  When
// complete, the provider will redirect the user back to the application at
//     /auth/provider/callback

server.get('/auth/provider', passport.authenticate('provider'));

// The OAuth 2.0 provider has redirected the user back to the application.
// Finish the authentication process by attempting to obtain an access
// token.  If authorization was granted, the user will be logged in.
// Otherwise, authentication has failed.

server.get('/auth/provider/callback',
  passport.authenticate('provider', { successRedirect: '/',
                                      failureRedirect: '/login' }));
```

### 手順 5. IsAuthenticated() ヘルパー メソッドをルートに追加する

```Javascript
// Simple route middleware to ensure user is authenticated.
//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.

var ensureAuthenticated = function(req, res, next) {
  if (req.isAuthenticated()) {
    return next();
  }
  res.redirect('/login');
};

```

### 手順 6. Cookie 用のキャッシング メカニズムを追加する

```Javascript
// Passport session setup.
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
```
### 手順 7. 最後に一部のエンドポイントを保護する

エンドポイントを保護するには、使用するプロトコルをパラメーターとして、passport.authenticate() 呼び出しを指定します。

次に、サーバー コードのルートを編集して、さらに興味深いことを実行します。

```Javascript
server.get('/tasks', passport.authenticate('provider', { session: false }), listTasks);
```


## 手順 19. サーバー アプリケーションを再実行し、自分が拒否されることを確認する

再び `curl` を使用して、エンドポイントに対して OAuth2 保護が有効になっていることを確認します。この操作は、このエンドポイントに対して、クライアント SDK のいずれかを実行する前に行います。返されるヘッダーは、正しいパスに沿っていることを確認するのに十分である必要があります。

まず、monogoDB インスタンスが動作していることを確認します。

	$sudo mongod

次に、ディレクトリを変更し、curl コマンドを実行してコンテンツを取得します。

	$ cd azuread
	$ node server.js

基本的な GET を試します。

	$ curl -isS http://127.0.0.1:8888/tasks/
	HTTP/1.1 302 Moved Temporarily
	Connection: close
	Location: https://login.windows.net/468a75f4-f9a7-4dc4-a527-4f4522734790/oauth2/authorize?response_type=code&redirect_uri=&client_id=123
	Content-Length: 0
	Date: Tue, 04 Feb 2014 02:15:14 GMT


ここでは、応答として 302 が期待されます。これは、Passport レイヤーが認証エンドポイントへのリダイレクトを試みていることを示しており、期待どおりの応答です。

## ご利用ありがとうございます。 OAuth2 を使用する REST API サービスが完成しました。

OAuth2 互換のクライアントを使用することなく、このサーバーを使用して最大限のことを実現できました。別のチュートリアルに進むことが必要になります。

Restify と OAuth2 を使用して REST API を実装する方法についての情報のみを探している場合は、サービスの開発を継続し、このサンプルで構築方法を習得するのに十分過ぎるコードを既に所有しています。

ADAL に関連するさらに高度な手順に興味がある場合は、学習を継続するために推奨される、サポートされる ADAL クライアントをいくつか次に示します。

開発マシンに複製するかダウンロードして、チュートリアルで説明されているように構成するだけで動作します。

[ADAL for iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[ADAL for Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)

[ADAL for .Net](http://msdn.microsoft.com/library/windowsazure/jj573266.aspx)

<!---HONumber=58--> 