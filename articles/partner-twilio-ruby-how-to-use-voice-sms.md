<properties linkid="develop-ruby-how-to-twilio-sms-voice-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (Ruby) - Azure" metaKeywords="Azure Ruby Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in Ruby." metaCanonical="" services="" documentationCenter="Ruby" title="How to Use Twilio for Voice and SMS Capabilities in PHP" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com"></tags>

# Ruby で音声および SMS 機能に Twilio を使用する方法

このガイドでは、Azure の Twilio API サービスを使用して一般的なプログラミング タスクを実行する方法を紹介します。電話の発信と Short Message Service (SMS) メッセージの送信の各シナリオについて説明します。Twilio の詳細、およびアプリケーションで音声と SMS を使用する方法については、「[次のステップ][次のステップ]」を参照してください。

## 目次

-   [Twilio とは][Twilio とは]
-   [Twilio の料金][Twilio の料金]
-   [概念][概念]
-   [Twilio アカウントを作成する][Twilio アカウントを作成する]
-   [Ruby Sinatra アプリケーションを作成する][Ruby Sinatra アプリケーションを作成する]
-   [Twilio ライブラリを使用するようにアプリケーションを構成する][Twilio ライブラリを使用するようにアプリケーションを構成する]
-   [方法: 発信通話する][方法: 発信通話する]
-   [方法: SMS メッセージを受信する][方法: SMS メッセージを受信する]
-   [方法: その他の Twilio サービス][方法: その他の Twilio サービス]
-   [次のステップ][次のステップ]

## <span id="WhatIs"></span></a>Twilio とは

Twilio は、既存の Web 言語およびスキルを使用して音声および SMS アプリケーションの作成を可能にするテレフォニー Web サービス API です。Twilio は、サードパーティ製のサービスです (Azure の機能および Microsoft 製品ではありません)。

**Twilio Voice** を使用すると、アプリケーションで音声通話の発着信処理を行うことができます。**Twilio SMS** を使用すると、アプリケーションで SMS メッセージの送受信を行うことができます。**Twilio Client** を使用すると、アプリケーションに (モバイル接続を含む) 既存のインターネット接続を使用した音声通信を組み込むことができます。

## <span id="Pricing"></span></a>Twilio の料金および特別プラン

Twilio の料金については、[Twilio の料金に関するページ][Twilio の料金に関するページ]でご確認ください。Azure ユーザーは、[特別プラン][特別プラン]として、1,000 件のテキストまたは 1,000 分の着信通話相当の無料クレジットが用意されています。この特別プランにサインアップする、または詳細を確認するには、[][特別プラン]<http://ahoy.twilio.com/azure></a> を参照してください。

## <span id="Concepts"></span></a>概念

Twilio API は、アプリケーションに音声および SMS 機能を提供する REST ベースの API です。クライアント ライブラリはさまざまな言語で用意されています。言語の一覧については、[Twilio API ライブラリに関するページ][Twilio API ライブラリに関するページ]を参照してください。

### <span id="TwiML"></span></a>TwiML

TwiML は、通話または SMS をどのように処理するかを Twilio に通知する XML ベースの命令のセットです。

たとえば、次の TwiML は、テキスト **Hello World** を音声に変換します。

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

すべての TwiML ドキュメントには、`<Response>` がルート要素として存在します。ここから、Twilio 動詞を使用してアプリケーションの動作を定義します。

### <span id="Verbs"></span></a>TwiML 動詞

Twilio 動詞は、何を**実行する**かを Twilio に通知する XML タグです。たとえば、**\<Say\>** 動詞は、メッセージを音声で返すことを Twilio に指示します。

Twilio 動詞の一覧を次に示します。

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

Twilio の動詞と属性、および TwiML の詳細については、[TwiML に関するページ][TwiML に関するページ]を参照してください。Twilio API の詳細については、[Twilio API に関するページ][Twilio API に関するページ]を参照してください。

## <span id="CreateAccount"></span></a>Twilio アカウントを作成する

Twilio アカウントを取得する準備ができたら、[Twilio のサインアップ ページ][Twilio のサインアップ ページ]でサインアップします。無料アカウントで始め、後でアカウントをアップグレードすることができます。

Twilio アカウントにサインアップすると、アプリケーションで使用できる無料の電話番号が与えられます。さらに、アカウント SID と認証トークンも受け取ります。Twilio API を呼び出すには、この両方が必要になります。自分のアカウントが不正にアクセスされないように、認証トークンを安全に保管してください。アカウント SID と認証トークンは、[Twilio アカウント ページ][Twilio アカウント ページ]の **[ACCOUNT SID]** フィールドと **[AUTH TOKEN]** フィールドでそれぞれ確認できます。

### <span id="VerifyPhoneNumbers"></span></a>電話番号を確認する

Twilio から与えられた番号の他に、自分で管理している番号 (携帯電話や自宅の電話番号) を確認してアプリケーションで使用することもできます。

電話番号を確認する方法の詳細については、[番号の管理に関するページ][番号の管理に関するページ]を参照してください。

## <span id="create_app"></span></a>Ruby アプリケーションを作成する

Twilio サービスを使用し、Azure で動作している Ruby アプリケーションと、Twilio サービスを使用するその他の Ruby アプリケーションに違いはありません。Twilio サービスは REST ベースであり、Ruby から複数の方法で呼び出すことができますが、この記事では、Twilio サービスの呼び出しに [Ruby 用 Twilio ヘルパー ライブラリ][Ruby 用 Twilio ヘルパー ライブラリ]を使用する方法について重点的に説明します。

まず、新しい Ruby Web アプリケーションのホストとして動作する[新しい Azure Linux VM をセットアップ][新しい Azure Linux VM をセットアップ]します。Rails アプリケーションの作成に関する手順は無視し、VM のセットアップだけを行ってください。作成するエンドポイントは、外部ポートを 80、内部ポートを 5000 としてください。

以下の例では、Ruby 用の非常にシンプルな Web フレームワークである [Sinatra][Sinatra] を使用しています。もちろん、Ruby on Rails などの他の Web フレームワークで Twilio ヘルパー ライブラリを使用することもできます。

新しい VM に SSH で接続し、新しいアプリケーション用のディレクトリを作成します。そのディレクトリの中に Gemfile というファイルを作成し、次のコードをファイルにコピーします。

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

コマンド ラインで、`bundle install` を実行します。これにより、上に示した依存関係がインストールされます。次に、`web.rb` というファイルを作成します。これが Web アプリケーションのコードを記述する場所になります。このファイルに次のコードを貼り付けます。

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

この時点で、`ruby web.rb -p 5000` コマンドを実行できます。これにより、単純な Web サーバーがポート 5000 で動き始めます。Azure VM 用に設定した URL にブラウザーでアクセスすると、このアプリケーションを参照できます。ブラウザーで Web アプリケーションにアクセスできたら、Twilio アプリケーションの作成を始める準備ができています。

## <span id="configure_app"></span></a>Twilio を使用するようにアプリケーションを構成する

Twilio ライブラリを使用するように Web アプリケーションを構成するには、`Gemfile` を更新して次の行を追加します。

    gem 'twilio-ruby'

コマンド ラインで、`bundle install` を実行します。`web.rb` を開き、先頭に次の行を追加します。

    require 'twilio-ruby'

これで、Ruby 用の Twilio ヘルパー ライブラリを Web アプリケーションで使用するための設定が完了しました。

## <span id="howto_make_call"></span></a>方法: 発信通話する

次のコードでは、発信通話を行う方法を示します。ここで重要となるのは、Ruby 用の Twilio ヘルパー ライブラリを使用した REST API の呼び出しと、TwiML のレンダリングです。コードを実行する前に、**From** および **To** の電話番号の値を置き換えて、Twilio アカウントの **From** の電話番号を確認します。

次の関数を `web.md` に追加します。

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from = "NNNNNNNNNNN";

    # The number of the phone receiving call.
    to = "NNNNNNNNNNN";

    # Use the Twilio-provided site for the TwiML response.
    url = "http://yourdomain.cloudapp.net/voice_url";
      
    get '/make_call' do
      # Create the call client.
      client = Twilio::REST::Client.new(sid, token);
      
      # Make the call
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end

ブラウザーで `http://yourdomain.cloudapp.net/make_call` を開くと、通話を発信する Twilio API の呼び出しがトリガーされます。`client.account.calls.create` の最初の 2 つのパラメーターは、文字どおり、呼び出し `from` と呼び出し `to` の番号です。

3 つ目のパラメーター (`url`) は、通話がつながったときの動作に関する指示を受け取るために Twilio が要求する URL です。この例では、単純な TwiML ドキュメントを返す URL (`http://yourdomain.cloudapp.net`) を設定し、テキストを音声に変換する `<Say>` 動詞を使用して、通話の受信者に "Hello Monkey" という音声を届けます。

## <span id="howto_recieve_sms"></span></a>方法: SMS メッセージを受信する

前の例では、**発信**通話を開始しました。今度は、Twilio へのサインアップ時に受け取った電話番号を使用して、**受信** SMS メッセージを処理します。

まず、[Twilio ダッシュボード][Twilio アカウント ページ]にログインします。上部のナビゲーションで [Numbers] をクリックし、自分の Twilio 番号をクリックします。構成できる URL が 2 つ表示されます。Voice Request URL と、SMS Request URL です。これらは、該当する番号に電話がかかってきたとき、または SMS メッセージが送信されたときに Twilio から呼び出される URL です。これらの URL は "Web フック" とも呼ばれます。

今回は受信 SMS メッセージを処理するので、URL を `http://yourdomain.cloudapp.net/sms_url` に更新します。ページの下部にある [Save Changes] をクリックします。`web.rb` に戻り、アプリケーションをプログラミングして次の処理を追加します。

    post '/sms_url' do
      "<Response>
         <Sms>Hey, thanks for the ping! Twilio and Azure rock!</Sms>
       </Response>"
    end

変更を加えた後は、必ず Web アプリケーションを再起動してください。次に、電話を使用して Twilio 番号に SMS を送信します。"Hey, thanks for the ping! Twilio and Azure rock!" という SMS 応答がすぐに返されます。

## <span id="additional_services"></span></a>方法: その他の Twilio サービスを使用する

ここに示す例以外にも、Twilio が提供する Web ベースの API を使用して、Azure アプリケーションからその他の Twilio 機能を利用することができます。詳細については、[Twilio API に関するドキュメント][Twilio API に関するページ]を参照してください。

### <span id="NextSteps"></span></a> 次のステップ

これで、Twilio サービスの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

-   [Twilio に関するセキュリティ ガイドラインのページ][Twilio に関するセキュリティ ガイドラインのページ]
-   [Twilio に関する方法とコード例のページ][Twilio に関する方法とコード例のページ]
-   [Twilio に関するクイック スタート チュートリアルのページ][Twilio に関するクイック スタート チュートリアルのページ]
-   [GitHub 上の Twilio に関するページ][GitHub 上の Twilio に関するページ]
-   [Twilio に関するサポートへの連絡のページ][Twilio に関するサポートへの連絡のページ]

  [次のステップ]: #NextSteps
  [Twilio とは]: #WhatIs
  [Twilio の料金]: #Pricing
  [概念]: #Concepts
  [Twilio アカウントを作成する]: #CreateAccount
  [Ruby Sinatra アプリケーションを作成する]: #create_app
  [Twilio ライブラリを使用するようにアプリケーションを構成する]: #configure_app
  [方法: 発信通話する]: #howto_make_call
  [方法: SMS メッセージを受信する]: #howto_recieve_sms
  [方法: その他の Twilio サービス]: #additional_services
  [Twilio の料金に関するページ]: http://www.twilio.com/pricing
  [特別プラン]: http://ahoy.twilio.com/azure
  [Twilio API ライブラリに関するページ]: https://www.twilio.com/docs/libraries
  [TwiML に関するページ]: http://www.twilio.com/docs/api/twiml
  [Twilio API に関するページ]: http://www.twilio.com/api
  [Twilio のサインアップ ページ]: https://www.twilio.com/try-twilio
  [Twilio アカウント ページ]: https://www.twilio.com/user/account
  [番号の管理に関するページ]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Ruby 用 Twilio ヘルパー ライブラリ]: https://www.twilio.com/docs/ruby/install
  [新しい Azure Linux VM をセットアップ]: http://www.windowsazure.com/ja-jp/develop/ruby/tutorials/web-app-with-linux-vm/
  [Sinatra]: http://www.sinatrarb.com/
  [Twilio に関するセキュリティ ガイドラインのページ]: http://www.twilio.com/docs/security
  [Twilio に関する方法とコード例のページ]: http://www.twilio.com/docs/howto
  [Twilio に関するクイック スタート チュートリアルのページ]: http://www.twilio.com/docs/quickstart
  [GitHub 上の Twilio に関するページ]: https://github.com/twilio
  [Twilio に関するサポートへの連絡のページ]: http://www.twilio.com/help/contact
