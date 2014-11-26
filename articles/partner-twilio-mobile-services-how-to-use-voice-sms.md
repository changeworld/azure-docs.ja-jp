<properties linkid="develop-mobile-tutorials-twilio-for-voice-and-sms" pageTitle="Use Twilio for Voice and SMS Capabilities | Mobile Dev Center" metaKeywords="" description="Learn how to perform common tasks using the Twilio API with Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use Twilio for voice and SMS capabilities from Mobile Services" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com" />

# モバイル サービスから音声および SMS 機能に Twilio を使用する方法

このトピックでは、Azure モバイル サービスで Twilio API を使用して一般的なタスクを実行する方法について説明します。このチュートリアルでは、Twilio API を使用して通話を開始したりショート メッセージ サービス (SMS) メッセージを送信したりするカスタム API スクリプトの作成方法を学習します。

## <span id="WhatIs"></span></a>Twilio とは

Twilio は、開発者がアプリケーションに音声、VoIP、およびメッセージングを埋め込むことを可能にし、ビジネス コミュニケーションを強化していきます。必要なすべてのインフラストラクチャをクラウド ベースのグローバル環境で仮想化し、Twilio 通信 API プラットフォームを通じてそれを公開します。アプリケーションは構築しやすく、スケーラビリティも優れています。従量課金制の柔軟性と、クラウドの信頼性の利点を活用できます。

**Twilio Voice** を使用すると、アプリケーションで音声通話の発着信処理を行うことができます。**Twilio SMS** を使用すると、アプリケーションで SMS メッセージの送受信を行うことができます。**Twilio Client** では、任意の電話、タブレット、またはブラウザーから VoIP 通話を行うことができ、WebRTC がサポートされています。

## <span id="Pricing"></span></a>Twilio の料金および特別プラン

Azure ユーザーは、[特別プラン][特別プラン]として、Twilio アカウントをアップグレードする際に、$10 の Twilio クレジットが提供されます。この Twilio クレジットは、任意の Twilio 使用に対して利用できます。$10 のクレジットは、約 1,000 件の SMS メッセージの送信、または最大で 1,000 分の受信音声に相当します (ご利用の電話番号の場所と、メッセージまたは通話の相手の場所に応じて異なります)。この Twilio クレジットを利用するには、[ahoy.twilio.com/azure][特別プラン] にアクセスします。

Twilio は、従量課金制サービスです。セットアップ料金は不要で、いつでもアカウントを閉じることができます。詳細については、[Twilio の料金のページ][Twilio の料金のページ]をご覧ください。

## <span id="Concepts"></span></a>概念

Twilio API は、アプリケーションに音声および SMS 機能を提供する REST ベースの API です。クライアント ライブラリはさまざまな言語で用意されています。言語の一覧については、[Twilio API ライブラリに関するページ][Twilio API ライブラリに関するページ]を参照してください。各種の言語で記述された Azure アプリケーションで Twilio を使用するための追加のチュートリアルも用意されています。記述言語は、[.NET][.NET]、[node.js][node.js]、[Java][Java]、[PHP][PHP]、[Python][Python]、[Ruby][Ruby] です。

Twilio API の主要な側面として、Twilio 動詞と Twilio Markup Language (TwiML) が挙げられます。

### <span id="Verbs"></span></a>Twilio 動詞

API では、Twilio 動詞を使用します。たとえば、**\<Say\>** 動詞は、メッセージを音声で返すことを Twilio に指示します。

Twilio 動詞の一覧を次に示します。他の動詞と機能については、[Twilio Markup Language のドキュメント][Twilio Markup Language のドキュメント]を参照してください。

-   **\<Dial\>**: 呼び出し元を別の電話に接続します。
-   **\<Gather\>**: 電話キーパッドに入力された数字を収集します。
-   **\<Hangup\>**: 通話を終了します。
-   **\<Play\>**: 音声ファイルを再生します。
-   **\<Pause\>**: 何も行わずに指定された秒数待機します。
-   **\<Record\>**: 呼び出し元の声を録音し、声が録音されたファイルの URL を返します。
-   **\<Redirect\>**: 通話または SMS の制御を別の URL の TwiML に転送します。
-   **\<Reject\>**: Twilio 番号への着信通話を拒否します。課金はされません。
-   **\<Say\>**: テキストを音声に変換して返します。
-   **\<Sms\>**: SMS メッセージを送信します。

### <span id="TwiML"></span></a>TwiML

TwiML は、Twilio 動詞に基づいた XML ベースの命令のセットで、通話または SMS をどのように処理するかを Twilio に通知します。

たとえば、次の TwiML は、テキスト **Hello World** を音声に変換します。

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

アプリケーションで Twilio API を呼び出す場合は、API パラメーターの 1 つである URL によって TwiML 応答が返されます。開発用には、Twilio から提供される URL を使用して、アプリケーションで使用する TwiML 応答を提供することができます。また、独自に URL をホストして、TwiML 応答を生成することもできます。別のオプションとして、**TwiMLResponse** オブジェクトを使用することもできます。

Twilio の動詞と属性、および TwiML の詳細については、[TwiML に関するページ][Twilio Markup Language のドキュメント]を参照してください。Twilio API の詳細については、[Twilio API に関するページ][Twilio API に関するページ]を参照してください。

## <span id="CreateAccount"></span></a>Twilio アカウントを作成する

Twilio アカウントを取得する準備ができたら、[Twilio のサインアップ ページ][Twilio のサインアップ ページ]でサインアップします。無料アカウントで始め、後でアカウントをアップグレードすることができます。

Twilio アカウントにサインアップすると、アカウント ID と認証トークンが発行されます。Twilio API を呼び出すには、この両方が必要になります。自分のアカウントが不正にアクセスされないように、認証トークンを安全に保管してください。アカウント ID と認証トークンは、[Twilio アカウント ページ][Twilio アカウント ページ]の **[ACCOUNT SID]** フィールドと **[AUTH TOKEN]** フィールドでそれぞれ確認できます。

## <span id="VerifyPhoneNumbers"></span></a>電話番号を確認する

アカウントに対して、さまざまな電話番号を Twilio で確認する必要があります。たとえば、電話を発信する場合は、電話番号を発信元 ID として Twilio で確認する必要があります。同様に、電話番号を使用して SMS メッセージを受信する場合は、受信に使用する電話番号を Twilio で確認する必要があります。電話番号を確認する方法の詳細については、[番号の管理に関するページ][番号の管理に関するページ]を参照してください。次に示すコードの一部は、Twilio で確認する必要がある電話番号に依存しています。

アプリケーションで既存の番号を使用する代わりに、Twilio 電話番号を購入することができます。Twilio 電話番号の購入については、[Twilio 電話番号のヘルプに関するページ][Twilio 電話番号のヘルプに関するページ]を参照してください。

## <span id="create_app"></span></a>モバイル サービスの作成

Twilio 対応のアプリケーションをホストするモバイル サービスには、他のモバイル サービスとの違いはありません。モバイル サービス カスタム API スクリプトから参照するために、Twilio node.js ライブラリを単に追加します。モバイル サービスを初めて作成する場合の詳細については、「[モバイル サービスの使用][モバイル サービスの使用]」を参照してください。

## <span id="VerifyPhoneNumbers"></span></a>Twilio Node.js ライブラリを使用するためのモバイル サービスの構成

Twilio は、Node.js ライブラリを提供します。このライブラリは、Twilio のさまざまな側面をラップし、Twilio REST API および Twilio Client と対話して TwiML 応答を生成するためのシンプルで簡単な方法を提供します。

モバイル サービスで Twilio node.js ライブラリを使用するには、モバイル サービス npm モジュールのサポートを利用する必要があります。そのためには、スクリプトをソース管理に格納します。チュートリアル「[Store Scripts in Source Control (ソース管理へのスクリプトの保存)][Store Scripts in Source Control (ソース管理へのスクリプトの保存)]」では、モバイル サービスでの初めてのソース管理のセットアップと、Git リポジトリへのサーバー スクリプトの保存について具体的に説明しています。

モバイル サービスのソース管理のセットアップが済んだら、モバイル サービス ダッシュボードの [構成] タブを開き、Git URL を探してコピーします。

この URL をブラウザーに貼り付け、リポジトリ名を */DebugConsole/index.html* に置き換えます。

たとえば、

    https://twilioSample.scm.azure-mobile.net/twilioSample.git

を次のように変更します。

    https://twilioSample.scm.azure-mobile.net/DebugConsole

入力を求めるメッセージが表示されたら、サービスのソース管理のセットアップに使用した資格情報を入力します。ログインすると、Azure モバイル サービス コンソールが表示されます。

![モバイル サービス コンソール][モバイル サービス コンソール]

コンソールで、次のようにして、scripts フォルダーに移動します。

    cd site\wwwroot\App_Data\config\scripts

api フォルダーで次のコマンドを実行して、Twilio ノード モジュールをインストールできます。

    npm install twilio

これで、カスタム API とテーブル スクリプトで Twilio ライブラリを参照し、使用できるようになりました。

## <span id="howto_make_call"></span></a>方法: 発信通話する

次のスクリプトは、**makeCall** 関数を使用してモバイル サービスから発信通話を開始する方法を示しています。このコードは、Twilio から提供されるサイトも使用して、Twilio Markup Language (TwiML) 応答を返します。コードを実行する前に、**From** および **To** の電話番号の値を置き換えて、Twilio アカウントの **From** の電話番号を確認します。

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.makeCall({
            to:'+16515556677', 
            from: '+14506667788',
            url: 'http://www.example.com/twiml.php' 

        }, function(err, responseData) {
            console.log(responseData.from); 
            response.send(200, '');
        });
    };

**client.makeCall** 関数に渡されるパラメーターの詳細については、[][]<http://www.twilio.com/docs/api/rest/making-calls></a> を参照してください。

既に説明したように、このコードは Twilio から提供されるサイトを使用して、TwiML 応答を返します。代わりに独自のサイトを使用して TwiML 応答を返すこともできます。詳細については、「[方法: 独自の Web サイトから TwiML 応答を返す][方法: 独自の Web サイトから TwiML 応答を返す]

## <span id="howto_send_sms"></span></a>方法: SMS メッセージを送信する

次のコードでは、**sendSms** 関数を使用して SMS メッセージを送信する方法を示しています。試用アカウントで SMS メッセージを送信できるように、**From** の番号が Twilio から提供されます。コードを実行する前に、Twilio アカウントの **To** の番号を確認する必要があります。

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.sendSms({
            to:'[]',
            from:'[]',
            body:'ahoy hoy! Testing Twilio and node.js'
        }, function(error, message) {

            // The "error" variable will contain error information, if any.
            // If the request was successful, this value will be "false"
            if (!error) {
                console.log('Success! The SID for this SMS message is: ' + message.sid);
                console.log('Message sent on: ' + message.dateCreated);
            }
            else {
                console.log('Oops! There was an error.');
            }
            response.send(200, { error : error } );
        });
    };

## <span id="howto_provide_twiml_responses"></span></a>方法: 独自の Web サイトから TwiML 応答を返す

アプリケーションで Twilio API の呼び出しをインスタンス化する場合 (たとえば、client.InitiateOutboundCall メソッドを使用した場合)、Twilio は TwiML 応答を返すことが想定されている URL にユーザーの要求を送信します。「方法: 発信通話する」の例では、Twilio から提供される [URL http://twimlets.com/message][URL http://twimlets.com/message] を使用して応答を返します。

<div class="dev-callout">
<b>注</b>
<p>TwiML は Web サービスで使用するように設計されており、ブラウザーで表示できます。たとえば、<a href="http://twimlets.com/message">twimlet_message_url</a> をクリックすると、空の &lt;Response&gt; 要素が表示されます。もう 1 つの例として、<a href="http://twimlets.com/message?Message%5B0%5D=Hello%20World">twimlet_message_url_hello_world</a> をクリックすると、&lt;Say&gt; 要素を格納している &lt;Response&gt; 要素が表示されます。</p>
</div>

Twilio から提供される URL を使用する代わりに、HTTP 応答を返す独自の URL サイトを作成できます。HTTP 応答を返すサイトは、任意の言語で作成できます。このトピックでは、ASP.NET 汎用ハンドラーから URL をホストすることを想定しています。

次のスクリプトでは、通話時の TwiML 応答で "Hello World" というテキストが読み上げられます。

    var twilio = require('twilio');

    exports.post = function(request, response) {
        var resp = new twilio.TwimlResponse();
        resp.say({voice:'woman'}, 'ahoy hoy! Testing Twilio and node.js');
        response.set('Content-Type', 'text/xml');
        response.send(200, resp.toString());
    };

TwiML の詳細については、[][1]<https://www.twilio.com/docs/api/twiml></a> を参照してください。

TwiML 応答を提供する方法をセットアップしたら、次のコード サンプルで示すように、その URL を **client.makeCall** メソッドに渡すことができます。

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.makeCall({
            to:'+16515556677', 
            from: '+14506667788',
            url: 'http://<your_mobile_service>.azure-mobile.net/api/makeCall' 

        }, function(err, responseData) {

            console.log(responseData.from);
            response.send(200, '');
        });
    };

[WACOM.INCLUDE [twilio_additional_services_and_next_steps](../includes/twilio_additional_services_and_next_steps.md)]

  [特別プラン]: http://ahoy.twilio.com/azure
  [Twilio の料金のページ]: http://www.twilio.com/pricing
  [Twilio API ライブラリに関するページ]: https://www.twilio.com/docs/libraries
  [.NET]: /ja-jp/develop/net/how-to-guides/twilio-voice-and-sms-service/
  [node.js]: /ja-jp/develop/nodejs/how-to-guides/twilio-voice-and-sms-service/
  [Java]: /ja-jp/develop/java/how-to-guides/twilio-voice-and-sms-service/
  [PHP]: /ja-jp/develop/php/how-to-guides/twilio-voice-and-sms-service/
  [Python]: /ja-jp/develop/python/how-to-guides/twilio-voice-and-sms-service/
  [Ruby]: /ja-jp/develop/ruby/how-to-guides/twilio-voice-and-sms-service/
  [Twilio Markup Language のドキュメント]: http://www.twilio.com/docs/api/twiml
  [Twilio API に関するページ]: http://www.twilio.com/api
  [Twilio のサインアップ ページ]: https://www.twilio.com/try-twilio
  [Twilio アカウント ページ]: https://www.twilio.com/user/account
  [番号の管理に関するページ]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Twilio 電話番号のヘルプに関するページ]: https://www.twilio.com/help/faq/phone-numbers
  [モバイル サービスの使用]: http://www.windowsazure.com/ja-jp/develop/mobile/tutorials/get-started/
  [モバイル サービス コンソール]: ./media/partner-twilio-mobile-services-how-to-use-voice-sms/twilio-kuduconsole.png
  []: http://www.twilio.com/docs/api/rest/making-calls
  [方法: 独自の Web サイトから TwiML 応答を返す]: #howto_provide_twiml_responses
  [URL http://twimlets.com/message]: http://twimlets.com/message
  [1]: https://www.twilio.com/docs/api/twiml
