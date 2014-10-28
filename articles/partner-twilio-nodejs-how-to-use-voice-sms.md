<properties linkid="dev-nodejs-how-to-twilio-voice-sms-service" urlDisplayName="Twilio Voice and SMS Service" pageTitle="Using Twilio for Voice, VoIP, and SMS Messaging in Azure" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="Node.js" title=" VoIP" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com"></tags>

# Azure での音声、VoIP、および SMS メッセージングのための Twilio の使用

このガイドでは、Azure で Twilio と node.js によって通信するアプリケーションを構築する方法を示します。

## 目次

-   [Twilio とは][Twilio とは]
-   [Twilio へのサインアップ (Microsoft ディスカウント)][Twilio へのサインアップ (Microsoft ディスカウント)]
-   [node.js Azure Web サイトの作成およびデプロイ][node.js Azure Web サイトの作成およびデプロイ]
-   [Twilio モジュールの構成][Twilio モジュールの構成]
-   [発信通話の実行][発信通話の実行]
-   [SMS メッセージの送信][SMS メッセージの送信]
-   [次のステップ][次のステップ]

<span id="whatis"></span></a>

## Twilio とは

Twilio は、開発者が簡単に音声通話の発着信処理、テキスト メッセージの送受信、およびブラウザー ベースでネイティブのモバイル アプリケーションへの VoIP 通話の埋め込みができるようにする API プラットフォームです。ここでは、詳細について説明する前に、そのしくみを簡単に解説します。

### 通話とテキスト メッセージの受信

Twilio を使用すると、開発者は[プログラム可能な電話番号を購入][プログラム可能な電話番号を購入]できます。この番号は、通話およびテキスト メッセージの送信と受信の両方に使用できます。Twilio 番号が受信通話またはテキストを受け取ると、Twilio は Web アプリケーションに HTTP POST または GET 要求を送信し、通話またはテキストの処理方法に関する命令を求めます。サーバーは Twilio の HTTP 要求に [TwiML][TwiML] で応答します。これは、通話またはテキストの処理方法についての命令を含む XML タグの単純なセットです。TwiML の例は、すぐ後で示します。

### 通話の発信とテキスト メッセージの送信

Twilio Web サービス API に対する HTTP 要求を作成することで、開発者はテキスト メッセージを送信したり、発信通話を開始したりすることができます。発信通話では、開発者は発信通話の接続後の処理方法についての TwiML 命令を返す URL も指定する必要があります。

### UI コード (JavaScript、iOS、または Android) への VoIP 機能の埋め込み

Twilio にはクライアント側 SDK が用意されており、デスクトップ Web ブラウザー、iOS アプリ、または Android アプリを VoIP 電話にすることができます。この記事では、主にブラウザーで VoIP 通話を使用する方法について説明します。ブラウザーで実行される Twilio JavaScript SDK のほかに、サーバー側アプリケーション (node.js アプリケーション) を使用して、JavaScript クライアントに "機能トークン" を発行する必要があります。node.js での VoIP の使用については、[Twilio 開発ブログ][Twilio 開発ブログ]で詳細を読むことができます。

<span id="signup"></span></a>

## Twilio へのサインアップ (Microsoft ディスカウント)

Twilio サービスを使用する前に、まず、[アカウントにサインアップ][アカウントにサインアップ]する必要があります。Microsoft Azure のお客様は特別割引をご利用いただけます。[こちらからサインアップ][アカウントにサインアップ]してください。

<span id="azuresite"></span></a>

## node.js Azure Web サイトの作成およびデプロイ

次に、Azure 上で実行される node.js Web サイトを作成する必要があります。[そのための公式なドキュメントは、ここにあります][そのための公式なドキュメントは、ここにあります]。大まかには、次の作業を行います。

-   まだ Azure アカウントを持っていない場合は、サインアップする
-   Azure 管理コンソールを使用して新しい Web サイトを作成する
-   ソース管理サポートを追加する (git が使用されたことを想定)
-   シンプルな node.js Web アプリケーションでファイル `server.js` を作成する
-   このシンプルなアプリケーションを Azure にデプロイする

<span id="twiliomodule"></span></a>

## Twilio モジュールの構成

次に、Twilio API を使用するシンプルな node.js アプリケーションの記述を開始します。その前に、Twilio アカウント資格情報を構成する必要があります。

### システム環境変数での Twilio 資格情報の構成

Twilio バックエンドに対して認証された要求を行うには、アカウント SID と認証トークンが必要です。これらが Twilio アカウントのユーザー名とパスワードのセットとして機能します。これらを Azure のノード モジュールで使用するように構成する最も安全な方法は、システム環境変数を通じて行う方法です。システム環境変数は、Azure 管理コンソールで直接設定できます。

node.js Web サイトを選択し、[構成] リンクをクリックします。下へ少しスクロールすると表示される領域で、アプリケーションの構成プロパティを設定します。以下のように、Twilio アカウント資格情報を入力します ([Twilio ダッシュボードで確認できます][Twilio ダッシュボードで確認できます])。それぞれの名前を "TWILIO\_ACCOUNT\_SID" と "TWILIO\_AUTH\_TOKEN" にします。

![Azure 管理コンソール][Azure 管理コンソール]

これらの変数を構成したら、Azure コンソールでアプリケーションを再起動します。

### package.json での Twilio モジュールの宣言

次に、package.json を作成して、[npm][npm] を通じてノード モジュールの依存関係を管理する必要があります。Azure/node.js のチュートリアルで作成した "server.js" ファイルと同じレベルで、"package.json" という名前のファイルを作成します。このファイル内に、次のコードを追加します。

{
 "name": "application-name",
 "version": "0.0.1",
 "private": true,
 "scripts": {
 "start": "node server"
 },
 "dependencies": {
 "express": "3.1.0",
 "ejs": "*",
 "twilio":"*"
 }
 }

このコードは、一般的な [express Web フレームワーク][express Web フレームワーク]および EJS テンプレート エンジンと共に、twilio モジュールを依存関係として宣言します。これで設定はすべて終わりました。コードの記述を始めましょう。

<span id="makecall"></span></a>

## 発信通話の実行

選択した番号に電話をかける単純なフォームを作成しましょう。server.js を開き、次のコードを入力します。"CHANGE\_ME" と記述されている個所は、自分の Azure Web サイトの名前に置き換えてください。

// モジュールの依存関係
 var express = require('express'),
 path = require('path'),
 http = require('http'),
 twilio = require('twilio');

// Express Web アプリケーションを作成
 var app = express();

// Express を構成
 app.configure(function(){
 app.set('port', process.env.PORT || 3000);
 app.set('views', \_\_dirname + '/views');
 app.set('view engine', 'ejs');
 app.use(express.favicon());
 app.use(express.logger('dev'));
 app.use(express.bodyParser());
 app.use(express.methodOverride());
 app.use(app.router);
 app.use(express.static(path.join(\_\_dirname, 'public')));
 });
 app.configure('development', function(){
 app.use(express.errorHandler());
 });

// アプリケーションのホーム ページの HTML ユーザー インターフェイスをレンダリング
 app.get('/', function(request, response) {
 response.render('index');
 });

// 電話をかけるフォーム POST を処理
 app.post('/call', function(request, response) {
 var client = twilio();
 client.makeCall({
 // make a call to this number
 to:request.body.number,

          // Change to a Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // A URL in our app which generates TwiML
          // Change "CHANGE_ME" to your app's name
          url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });

});

// 発信通話を処理するための TwiML を生成
 app.post('/outbound\_call', function(request, response) {
 var twiml = new twilio.TwimlResponse();

      // Say a message to the call's receiver 
      twiml.say('hello - thanks for checking out Twilio and Azure', {
          voice:'woman'
      });

      response.set('Content-Type', 'text/xml');
      response.send(twiml.toString());

});

// サーバーを起動
 http.createServer(app).listen(app.get('port'), function(){
 console.log("Express server listening on port " + app.get('port'));
 });

次に、"views" という名前のディレクトリを作成します。このディレクトリ内に、次の内容を持つ "index.ejs" という名前のファイルを作成します。

\<!DOCTYPE html\>
 \<html\>
 \<head\>
 \<title\>Twilio Test\</title\>
 \<style\>
 input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
 \</style\>
 \</head\>
 \<body\>
 \<h1\>Twilio Test\</h1\>
 \<form action="/call" method="POST"\>
 \<input placeholder="Enter a phone number" name="number"/\>
 \<br/\>
 \<input type="submit" value="Call the number above"/\>
 \</form\>
 \</body\>
 \</html\>

ここで、Web サイトを Azure に展開し、ホームを開きます。自分の電話番号をテキスト フィールドに入力し、Twilio 番号からの通話を着信できるはずです。

<span id="sendmessage"></span></a>

## SMS メッセージの送信

ここでは、テキスト メッセージの送信ロジックを処理するユーザー インターフェイスとフォームをセットアップします。"server.js" を開いて、最後の "app.post" の呼び出しの後に次のコードを追加します。

app.post('/sms', function(request, response) {
 var client = twilio();
 client.sendSms({
 // この番号にテキストを送信
 to:request.body.number,

          // A Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // The body of the text message
          body: request.body.message
          
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });

});

"views/index.ejs" で、最初のフォームの下に、番号とテキスト メッセージを送信するためのもう 1 つのフォームを追加します。

\<form action="/sms" method="POST"\>
 \<input placeholder="Enter a phone number" name="number"/\>
 \<br/\>
 \<input placeholder="Enter a message to send" name="message"/\>
 \<br/\>
 \<input type="submit" value="Send text to the number above"/\>
 \</form\>

アプリケーションを Azure に再デプロイします。今度は、そのフォームを送信し、テキスト メッセージを自分 (または親しい友人) に送ることができるはずです。

<span id="nextsteps"></span></a>

## 次のステップ

これで、通信するアプリケーションを node.js と Twilio を使用して構築する方法の基本を学習できました。ただし、これらの例は、Twilio と node.js できることのごく一部に触れたにすぎません。Twilio と node.js の使用の詳細については、以下のリソースを参照してください。

-   [公式のモジュールのドキュメント][公式のモジュールのドキュメント]
-   [node.js アプリケーションによる VoIP のチュートリアル][Twilio 開発ブログ]
-   [Votr - node.js と CouchDB によるリアルタイムの SMS 投票アプリケーション (3 部構成)][Votr - node.js と CouchDB によるリアルタイムの SMS 投票アプリケーション (3 部構成)]
-   [node.js によるブラウザーでのペア プログラミング][node.js によるブラウザーでのペア プログラミング]

Azure で node.js と Twilio を活用してください。

  [Twilio とは]: #whatis
  [Twilio へのサインアップ (Microsoft ディスカウント)]: #signup
  [node.js Azure Web サイトの作成およびデプロイ]: #azuresite
  [Twilio モジュールの構成]: #twiliomodule
  [発信通話の実行]: #makecall
  [SMS メッセージの送信]: #sendmessage
  [次のステップ]: #nextsteps
  [プログラム可能な電話番号を購入]: https://www.twilio.com/user/account/phone-numbers/available/local
  [TwiML]: https://www.twilio.com/docs/api/twiml
  [Twilio 開発ブログ]: http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
  [アカウントにサインアップ]: http://ahoy.twilio.com/azure
  [そのための公式なドキュメントは、ここにあります]: http://www.windowsazure.com/ja-jp/develop/nodejs/tutorials/create-a-website-(mac)/
  [Twilio ダッシュボードで確認できます]: https://www.twilio.com/user/account
  [Azure 管理コンソール]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
  [npm]: http://npmjs.org
  [express Web フレームワーク]: http://expressjs.com
  [公式のモジュールのドキュメント]: http://twilio.github.io/twilio-node/
  [Votr - node.js と CouchDB によるリアルタイムの SMS 投票アプリケーション (3 部構成)]: http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
  [node.js によるブラウザーでのペア プログラミング]: http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
