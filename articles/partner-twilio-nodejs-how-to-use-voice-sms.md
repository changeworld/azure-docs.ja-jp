<properties linkid="dev-nodejs-how-to-twilio-voice-sms-service" urlDisplayName="Twilio Voice と SMS サービス" pageTitle="Azure での音声、VoIP、および SMS メッセージングのための Twilio の使用" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="Node.js" title="VoIP" authors="" solutions="" manager="" editor="" />


#Azure での音声、VoIP、および SMS メッセージングのための Twilio の使用

このガイドでは、Azure で Twilio と node.js によって通信するアプリケーションを構築する方法を示します。

## 目次

* [Twilio とは](#whatis)
* [Twilio へのサインアップ (Microsoft ディスカウント)](#signup)
* [node.js Azure Web サイトの作成およびデプロイ](#azuresite)
* [Twilio モジュールの構成](#twiliomodule)
* [発信通話の実行](#makecall)
* [SMS メッセージの送信](#sendmessage)
* [次のステップ](#nextsteps)

<a id="whatis"/>
##Twilio とは

Twilio は、開発者が簡単に音声通話の発着信処理、テキスト メッセージの送受信、およびブラウザー ベースでネイティブのモバイル アプリケーションへの VoIP 通話の埋め込みができるようにする API プラットフォームです。ここでは、詳細について説明する前に、そのしくみを簡単に解説します。

###通話とテキスト メッセージの受信

Twilio を使用すると、開発者は[プログラム可能な電話番号を購入][purchase_phone]できます。この番号は、通話およびテキスト メッセージの送信と受信の両方に使用できます。Twilio 番号が受信通話またはテキストを受け取ると、Twilio は Web アプリケーションに HTTP POST または GET 要求を送信し、通話またはテキストの処理方法に関する命令を求めます。サーバーは Twilio の HTTP 要求に [TwiML][twiml] で応答します。これは、通話またはテキストの処理方法についての命令を含む XML タグの単純なセットです。TwiML の例は、すぐ後で示します。

###通話の発信とテキスト メッセージの送信

Twilio Web サービス API に対する HTTP 要求を作成することで、開発者はテキスト メッセージを送信したり、発信通話を開始したりすることができます。発信通話では、開発者は発信通話の接続後の処理方法についての TwiML 命令を返す URL も指定する必要があります。

###UI コード (JavaScript、iOS、または Android) への VoIP 機能の埋め込み

Twilio にはクライアント側 SDK が用意されており、デスクトップ Web ブラウザー、iOS アプリ、または Android アプリを VoIP 電話にすることができます。この記事では、主にブラウザーで VoIP 通話を使用する方法について説明します。ブラウザーで実行される Twilio JavaScript SDK のほかに、サーバー側アプリケーション (node.js アプリケーション) を使用して、JavaScript クライアントに "機能トークン" を発行する必要があります。node.js での VoIP の使用については、[Twilio 開発ブログ][voipnode]で詳細を読むことができます。

<a id="signup"/>
##Twilio へのサインアップ (Microsoft ディスカウント)

Twilio サービスを使用する前に、まず、[アカウントにサインアップ][signup]する必要があります。Microsoft Azure のお客様は特別割引をご利用いただけます。[こちらからサインアップ][signup]してください。

<a id="azuresite"/>
##node.js Azure Web サイトの作成および展開

次に、Azure 上で実行される node.js Web サイトを作成する必要があります。[そのための公式なドキュメントは、ここにあります][azure_new_site]。大まかには、次の作業を行います。

* まだ Azure アカウントを持っていない場合は、サインアップする
* Azure 管理コンソールを使用して新しい Web サイトを作成する
* ソース管理サポートを追加する (git を使用したと想定)
* シンプルな node.js Web アプリケーションと共にファイル `server.js` を作成する
* このシンプルなアプリケーションを Azure に展開する

<a id="twiliomodule"/>
##Twilio モジュールの構成

次に、Twilio API を使用するシンプルな node.js アプリケーションの記述を開始します。その前に、Twilio アカウント資格情報を構成する必要があります。

###システム環境変数での Twilio 資格情報の構成

Twilio バックエンドに対して認証された要求を行うには、アカウント SID と認証トークンが必要です。これらが Twilio アカウントのユーザー名とパスワードのセットとして機能します。これらを Azure のノード モジュールで使用するように構成する最も安全な方法は、システム環境変数を通じて行う方法です。システム環境変数は、Azure 管理コンソールで直接設定できます。

node.js Web サイトを選択し、[構成] リンクをクリックします。下へ少しスクロールすると表示される領域で、アプリケーションの構成プロパティを設定します。以下のように、Twilio アカウント資格情報を入力します ([Twilio ダッシュボードで確認できます][twilio_dashboard]) - それぞれを "TWILIO_ACCOUNT_SID" と "TWILIO_AUTH_TOKEN" にします。

![Azure 管理コンソール][azure-admin-console]

これらの変数を構成したら、Azure コンソールでアプリケーションを再起動します。

###package.json での Twilio モジュールの宣言

次に、package.json を作成して、[npm] を通じてノード モジュールの依存関係を管理する必要があります。Azure/node.js のチュートリアルで作成した "server.js" ファイルと同じレベルで、"package.json" という名前のファイルを作成します。このファイル内に、次のコードを追加します。

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

このコードは、一般的な [express Web フレームワーク][express]および EJS テンプレート エンジンと共に、twilio モジュールを依存関係として宣言します。これで設定はすべて終わりました。コードの記述を始めましょう。- 

<a id="makecall"/>
##発信通話の実行

選択した番号に電話をかける単純なフォームを作成しましょう。server.js を開き、次のコードを入力します。"CHANGE_ME" と記述されている個所は、自分の Azure Web サイトの名前に置き換えてください。- 

  // Module dependencies
  var express = require('express'), 
      path = require('path'), 
      http = require('http'), 
      twilio = require('twilio');

  // Create Express web application
  var app = express();

  // Express configuration
  app.configure(function(){
      app.set('port', process.env.PORT || 3000);
      app.set('views', __dirname + '/views');
      app.set('view engine', 'ejs');
      app.use(express.favicon());
      app.use(express.logger('dev'));
      app.use(express.bodyParser());
      app.use(express.methodOverride());
      app.use(app.router);
      app.use(express.static(path.join(__dirname, 'public')));
  });
  app.configure('development', function(){
      app.use(express.errorHandler());
  });

  // Render an HTML user interface for the application's home page
  app.get('/', function(request, response) {
      response.render('index');
  });

  // Handle the form POST to place a call
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

  // Generate TwiML to handle an outbound call
  app.post('/outbound_call', function(request, response) {
      var twiml = new twilio.TwimlResponse();

      // Say a message to the call's receiver 
      twiml.say('hello - thanks for checking out Twilio and Azure', {
          voice:'woman'
      });

      response.set('Content-Type', 'text/xml');
      response.send(twiml.toString());
  });

  // Start server
  http.createServer(app).listen(app.get('port'), function(){
    console.log("Express server listening on port " + app.get('port'));
  });

次に、"views" という名前のディレクトリを作成します。このディレクトリ内に、次の内容を持つ "index.ejs" という名前のファイルを作成します。- 

  &lt;!DOCTYPE html&gt;
  &lt;html&gt;
  &lt;head&gt;
      &lt;title&gt;Twilio Test&lt;/title&gt;
      &lt;style&gt;
      input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
      &lt;/style&gt;
  &lt;/head&gt;
  &lt;body&gt;
      &lt;h1&gt;Twilio Test&lt;/h1&gt;
      &lt;form action="/call" method="POST"&gt;
          &lt;input placeholder="Enter a phone number" name="number"/&gt;
          &lt;br/&gt;
          &lt;input type="submit" value="Call the number above"/&gt;
      &lt;/form&gt;
  &lt;/body&gt;
  &lt;/html&gt;

ここで、Web サイトを Azure に展開し、ホームを開きます。自分の電話番号をテキスト フィールドに入力し、Twilio 番号からの通話を着信できるはずです。

<a id="sendmessage"/>
##SMS メッセージを送信する

ここでは、テキスト メッセージの送信ロジックを処理するユーザー インターフェイスとフォームをセットアップします。"server.js" を開いて、最後の "app.post" の呼び出しの後に次のコードを追加します。

  app.post('/sms', function(request, response) {
      var client = twilio();
      client.sendSms({
          // send a text to this number
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

  &lt;form action="/sms" method="POST"&gt;
      &lt;input placeholder="Enter a phone number" name="number"/&gt;
      &lt;br/&gt;
      &lt;input placeholder="Enter a message to send" name="message"/&gt;
      &lt;br/&gt;
      &lt;input type="submit" value="Send text to the number above"/&gt;
  &lt;/form&gt;

アプリケーションを Azure に再デプロイします。今度は、そのフォームを送信し、テキスト メッセージを自分 (または親しい友人) に送ることができるはずです。

<a id="nextsteps"/>
## 次のステップ

これで、通信するアプリケーションを node.js と Twilio を使用して構築する方法の基本を学習できました。ただし、これらは、Twilio と node.js でできることの初歩的な例にすぎません。Node.js と Twilio の使用方法の詳細については、次のリソースをご覧ください。

* [公式のモジュールのドキュメント][docs]
* [node.js アプリケーションによる VoIP のチュートリアル][voipnode]
* [Votr - node.js と CouchDB によるリアルタイムの SMS 投票アプリケーション (3 部構成)][votr]
* [node.js によるブラウザーでのペア プログラミング][pair]

Azure で node.js と Twilio を活用してください。

[purchase_phone]: https://www.twilio.com/user/account/phone-numbers/available/local
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: http://ahoy.twilio.com/azure
[azure_new_site]: http://www.windowsazure.com/ja-jp/develop/nodejs/tutorials/create-a-website-(mac)/
[twilio_dashboard]: https://www.twilio.com/user/account
[npm]: http://npmjs.org
[express]: http://expressjs.com
[voipnode]: http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: http://twilio.github.io/twilio-node/
[votr]: http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png




