<properties linkid="develop-php-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (PHP) - Azure" metaKeywords="Azure PHP Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in PHP." metaCanonical="" services="" documentationCenter="" title="How to Use Twilio for Voice and SMS Capabilities in PHP" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com" />

# PHP で音声および SMS 機能に Twilio を使用する方法

このガイドでは、Azure の Twilio API サービスを使用して一般的なプログラミング タスクを実行する方法を紹介します。電話の発信と Short Message Service (SMS) メッセージの送信の各シナリオについて説明します。Twilio の詳細、およびアプリケーションで音声と SMS を使用する方法については、「[次のステップ][次のステップ]」を参照してください。

## 目次

-   [Twilio とは][Twilio とは]
-   [Twilio の料金][Twilio の料金]
-   [概念][概念]
-   [Twilio アカウントを作成する][Twilio アカウントを作成する]
-   [電話番号を確認する][電話番号を確認する]
-   [PHP アプリケーションの作成][PHP アプリケーションの作成]
-   [Twilio ライブラリを使用するようにアプリケーションを構成する][Twilio ライブラリを使用するようにアプリケーションを構成する]
-   [方法: 発信通話する][方法: 発信通話する]
-   [方法: SMS メッセージを送信する][方法: SMS メッセージを送信する]
-   [方法: 独自の Web サイトから TwiML 応答を返す][方法: 独自の Web サイトから TwiML 応答を返す]

## <span id="WhatIs"></span></a>Twilio とは

Twilio は、開発者がアプリケーションに音声、VoIP、およびメッセージングを埋め込むことを可能にし、ビジネス コミュニケーションを強化していきます。必要なすべてのインフラストラクチャをクラウド ベースのグローバル環境で仮想化し、Twilio 通信 API プラットフォームを通じてそれを公開します。アプリケーションは構築しやすく、スケーラビリティも優れています。従量課金制の柔軟性と、クラウドの信頼性の利点を活用できます。

**Twilio Voice** を使用すると、アプリケーションで音声通話の発着信処理を行うことができます。**Twilio SMS** を使用すると、アプリケーションでテキスト メッセージの送受信を行うことができます。**Twilio Client** では、任意の電話、タブレット、またはブラウザーから VoIP 通話を行うことができ、WebRTC がサポートされています。

## <span id="Pricing"></span></a>Twilio の料金および特別プラン

Azure ユーザーには、Twilio アカウントをアップグレードする際に、[特別プラン]$10 の Twilio クレジットが提供されます。この Twilio クレジットは、任意の Twilio 使用に対して利用できます。$10 のクレジットは、約 1,000 件の SMS メッセージの送信、または最大で 1,000 分の受信音声に相当します (ご利用の電話番号の場所と、メッセージまたは通話の相手の場所に応じて異なります)。この Twilio クレジットを利用するには、以下にアクセスします。[ahoy.twilio.com/azure]。

Twilio は、従量課金制サービスです。セットアップ料金は不要で、いつでもアカウントを閉じることができます。詳細については、[Twilio の料金のページ][Twilio の料金のページ]をご覧ください。

## <span id="Concepts"></span></a>概念

Twilio API は、アプリケーションに音声および SMS 機能を提供する REST ベースの API です。クライアント ライブラリはさまざまな言語で用意されています。言語の一覧については、[Twilio API ライブラリに関するページ][Twilio API ライブラリに関するページ]を参照してください。

Twilio API の主要な側面として、Twilio 動詞と Twilio Markup Language (TwiML) が挙げられます。

### <span id="Verbs"></span></a>Twilio 動詞

API では、Twilio 動詞を使用します。たとえば、**\<Say\>** 動詞は、メッセージを音声で返すことを Twilio に指示します。

Twilio 動詞の一覧を次に示します。他の動詞と機能については、[Twilio Markup Language のドキュメント][<http://www.twilio.com/docs/api/twiml>] を参照してください。

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

Twilio の動詞と属性、および TwiML の詳細については、[TwiML に関するページ][TwiML に関するページ]を参照してください。Twilio API の詳細については、[Twilio API に関するページ][Twilio API に関するページ]を参照してください。

## <span id="CreateAccount"></span></a>Twilio アカウントを作成する

Twilio アカウントを取得する準備ができたら、[Twilio のサインアップ ページ][Twilio のサインアップ ページ]でサインアップします。無料アカウントで始め、後でアカウントをアップグレードすることができます。

Twilio アカウントにサインアップすると、アカウント ID と認証トークンが発行されます。Twilio API を呼び出すには、この両方が必要になります。自分のアカウントが不正にアクセスされないように、認証トークンを安全に保管してください。アカウント ID と認証トークンは、[Twilio アカウント ページ][Twilio アカウント ページ]の **[ACCOUNT SID]** フィールドと **[AUTH TOKEN]** フィールドでそれぞれ確認できます。

## <span id="VerifyPhoneNumbers"></span></a>電話番号を確認する

アカウントに対して、さまざまな電話番号を Twilio で確認する必要があります。たとえば、発信元 ID の既存の電話番号を使用して電話を発信する場合は、その電話番号を Twilio で確認する必要があります。同様に、アップグレードするまでは、電話番号に SMS メッセージを送信する場合、その電話番号を Twilio で確認する必要があります。アップグレードした後は、電話番号を確認することなくその番号に SMS メッセージを送信できます。電話番号を確認する方法の詳細については、[番号の管理に関するページ][番号の管理に関するページ]を参照してください。次に示すコードの一部は、Twilio で確認する必要がある電話番号に依存しています。

アプリケーションで既存の番号を使用する代わりに、Twilio 電話番号を購入することができます。Twilio 電話番号の購入については、[Twilio 電話番号のヘルプに関するページ][Twilio 電話番号のヘルプに関するページ]を参照してください。

## <span id="create_app"></span></a>PHP アプリケーションの作成

Twilio サービスを使用する Azure 上の PHP アプリケーションと、Twilio サービスを使用するその他の PHP アプリケーションに違いはありません。Twilio サービスは REST ベースであり、PHP から複数の方法で呼び出すことができますが、この記事では、Twilio サービスの呼び出しに [GitHub の PHP 用 Twilio ライブラリ][GitHub の PHP 用 Twilio ライブラリ]を使用する方法について重点的に説明します。PHP 用 Twilio ライブラリの使用方法の詳細については、[][]<http://readthedocs.org/docs/twilio-php/en/latest/index.html></a> を参照してください。

Azure 上で Twilio/PHP アプリケーションをビルドおよび展開する手順の詳細については、「[Azure 上の PHP アプリケーションで Twilio を使用して通話する方法][Azure 上の PHP アプリケーションで Twilio を使用して通話する方法]」を参照してください。

## <span id="configure_app"></span></a>Twilio ライブラリを使用するようにアプリケーションを構成する

PHP 用 Twilio ライブラリを使用するようにアプリケーションを構成するには、次の 2 つの方法があります。

1.  GitHub ([][GitHub の PHP 用 Twilio ライブラリ]<https://github.com/twilio/twilio-php></a>) から PHP 用 Twilio ライブラリをダウンロードし、**Services** ディレクトリをアプリケーションに追加します。

    - または -

2.  PHP 用 Twilio ライブラリを PEAR パッケージとしてインストールします。インストールには、次のコマンドを使用できます。

        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

PHP 用 Twilio ライブラリをインストールしたら、ライブラリを参照する **require\_once** ステートメントを PHP ファイルの先頭に追加できます。

        require_once 'Services/Twilio.php';

詳細については、[][1]<https://github.com/twilio/twilio-php/blob/master/README.md></a> を参照してください。

## <span id="howto_make_call"></span></a>方法: 発信通話する

次のコードでは、**Services\_Twilio** クラスを使用して発信通話を行う方法を示しています。このコードは、Twilio から提供されるサイトも使用して、Twilio Markup Language (TwiML) 応答を返します。コードを実行する前に、**From** および **To** の電話番号の値を置き換えて、Twilio アカウントの **From** の電話番号を確認します。

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the the call.
    // (Must be previously validated with Twilio.)
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "http://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

既に説明したように、このコードは Twilio から提供されるサイトを使用して、TwiML 応答を返します。代わりに独自のサイトを使用して TwiML 応答を返すことができます。詳細については、「[方法: 独自の Web サイトから TwiML 応答を返す][方法: 独自の Web サイトから TwiML 応答を返す]」を参照してください。

-   **メモ**:SSL 証明書の検証エラーのトラブルシューティングを行うには、[][2]<http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html></a> を参照してください。

## <span id="howto_send_sms"></span></a>方法: SMS メッセージを送信する

次のコードでは、**Services\_Twilio** クラスを使用して SMS メッセージを送信する方法を示しています。試用アカウントで SMS メッセージを送信できるように、**From** の番号が Twilio から提供されます。コードを実行する前に、Twilio アカウントの **To** の番号を確認する必要があります。

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->account->sms_messages->create($from_number, $to_number, $message);
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

## <span id="howto_provide_twiml_responses"></span></a>方法: 独自の Web サイトから TwiML 応答を返す

アプリケーションで Twilio API の呼び出しを開始すると、Twilio は TwiML 応答を返すことが想定されている URL にユーザーの要求を送信します。前の例では、Twilio から提供される URL [][3]<http://twimlets.com/message></a> を使用しています (TwiML は Twilio で使用するように設計されており、ブラウザーで表示できます。たとえば、[][3]<http://twimlets.com/message></a> をクリックすると、空の `<Response>` 要素が表示されます。もう 1 つの例として、[][4]<http://twimlets.com/message?Message%5B0%5D=Hello%20World></a> をクリックすると、`<Say>` 要素を格納している `<Response>` 要素が表示されます)。

Twilio から提供される URL を使用する代わりに、HTTP 応答を返す独自のサイトを作成できます。XML 応答を返すサイトは任意の言語で作成できます。このトピックでは、PHP を使用して TwiML を作成するとします。

次の PHP ページでは、通話時の TwiML 応答で "**Hello World**" というテキストが読み上げられます。

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

この例からわかるように、TwiML 応答は XML ドキュメントにすぎません。PHP 用 Twilio ライブラリには、TwiML を生成するクラスが用意されています。次の例では、前の例と同じ応答が生成されますが、PHP 用 Twilio ライブラリの **Services\_Twilio\_Twiml** クラスを使用しています。

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

TwiML の詳細については、[][5]<https://www.twilio.com/docs/api/twiml></a> を参照してください。

TwiML 応答を返すように PHP ページを設定したら、その PHP ページの URL を、`Services_Twilio->account->calls->create` メソッドに渡します。たとえば、Azure ホステッド サービスに展開された **MyTwiML** という名前の Web アプリケーションがあるとします。その PHP ページの名前が **mytwiml.php** である場合、次の例に示すように URL を **Services\_Twilio-\>account-\>calls-\>create** に渡すことができます。

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Azure で PHP に基づいて Twilio を使用する方法の詳細については、「[Azure 上の PHP アプリケーションで Twilio を使用して通話する方法][Azure 上の PHP アプリケーションで Twilio を使用して通話する方法]」を参照してください。

## <span id="AdditionalServices"></span></a>方法: その他の Twilio サービスを使用する

ここに示す例以外にも、Twilio が提供する Web ベースの API を使用して、Azure アプリケーションからその他の Twilio 機能を利用することができます。詳細については、[Twilio API に関するドキュメント][Twilio API に関するページ]を参照してください。

## <span id="NextSteps"></span></a> 次のステップ

これで、Twilio サービスの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

-   [Twilio に関するセキュリティ ガイドラインのページ][Twilio に関するセキュリティ ガイドラインのページ]
-   [Twilio のハウツー ガイドとコード例のページ][Twilio のハウツー ガイドとコード例のページ]
-   [Twilio に関するクイック スタート チュートリアルのページ][Twilio に関するクイック スタート チュートリアルのページ]
-   [GitHub 上の Twilio に関するページ][GitHub 上の Twilio に関するページ]
-   [Twilio に関するサポートへの連絡のページ][Twilio に関するサポートへの連絡のページ]

  [次のステップ]: #NextSteps
  [Twilio とは]: #WhatIs
  [Twilio の料金]: #Pricing
  [概念]: #Concepts
  [Twilio アカウントを作成する]: #CreateAccount
  [電話番号を確認する]: #VerifyPhoneNumbers
  [PHP アプリケーションの作成]: #create_app
  [Twilio ライブラリを使用するようにアプリケーションを構成する]: #configure_app
  [方法: 発信通話する]: #howto_make_call
  [方法: SMS メッセージを送信する]: #howto_send_sms
  [方法: 独自の Web サイトから TwiML 応答を返す]: #howto_provide_twiml_responses
  [Twilio の料金のページ]: http://www.twilio.com/pricing
  [Twilio API ライブラリに関するページ]: https://www.twilio.com/docs/libraries
  [TwiML に関するページ]: http://www.twilio.com/docs/api/twiml
  [Twilio API に関するページ]: http://www.twilio.com/api
  [Twilio のサインアップ ページ]: https://www.twilio.com/try-twilio
  [Twilio アカウント ページ]: https://www.twilio.com/user/account
  [番号の管理に関するページ]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Twilio 電話番号のヘルプに関するページ]: https://www.twilio.com/help/faq/phone-numbers
  [GitHub の PHP 用 Twilio ライブラリ]: https://github.com/twilio/twilio-php
  []: http://readthedocs.org/docs/twilio-php/en/latest/index.html
  [Azure 上の PHP アプリケーションで Twilio を使用して通話する方法]: http://windowsazure.com/ja-jp/documentation/articles/partner-twilio-php-make-phone-call
  [1]: https://github.com/twilio/twilio-php/blob/master/README.md
  [2]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
  [3]: http://twimlets.com/message
  [4]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
  [5]: https://www.twilio.com/docs/api/twiml
  [Twilio に関するセキュリティ ガイドラインのページ]: http://www.twilio.com/docs/security
  [Twilio のハウツー ガイドとコード例のページ]: http://www.twilio.com/docs/howto
  [Twilio に関するクイック スタート チュートリアルのページ]: http://www.twilio.com/docs/quickstart
  [GitHub 上の Twilio に関するページ]: https://github.com/twilio
  [Twilio に関するサポートへの連絡のページ]: http://www.twilio.com/help/contact
