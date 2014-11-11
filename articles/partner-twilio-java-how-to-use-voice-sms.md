<properties linkid="develop-java-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (Java) - Azure" metaKeywords="Twilio, Twilio API, phone calls, SMS message, TwiML responses, Azure Twilio Java" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in Java." metaCanonical="" services="" videoId="" scriptId="" documentationCenter="Java" title="How to Use Twilio for Voice and SMS Capabilities in Java" authors="MicrosoftHelp@twilio.com; robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm" />

# Java で音声および SMS 機能に Twilio を使用する方法

このガイドでは、Azure の Twilio API サービスを使用して一般的なプログラミング タスクを実行する方法を紹介します。電話の発信と Short Message Service (SMS) メッセージの送信の各シナリオについて説明します。Twilio の詳細、およびアプリケーションで音声と SMS を使用する方法については、「[次のステップ][次のステップ]」を参照してください。

## 目次

-   [Twilio とは][Twilio とは]
-   [Twilio の料金][Twilio の料金]
-   [概念][概念]
-   [Twilio アカウントを作成する][Twilio アカウントを作成する]
-   [電話番号を確認する][電話番号を確認する]
-   [Java アプリケーションの作成][Java アプリケーションの作成]
-   [Twilio ライブラリを使用するようにアプリケーションを構成する][Twilio ライブラリを使用するようにアプリケーションを構成する]
-   [方法: 発信通話する][方法: 発信通話する]
-   [方法: SMS メッセージを送信する][方法: SMS メッセージを送信する]
-   [方法: 独自の Web サイトから TwiML 応答を返す][方法: 独自の Web サイトから TwiML 応答を返す]
-   [方法: その他の Twilio サービスを使用する][方法: その他の Twilio サービスを使用する]
-   [次のステップ][次のステップ]

## <span id="WhatIs"></span></a>Twilio とは

Twilio は、既存の Web 言語およびスキルを使用して音声および SMS アプリケーションの作成を可能にするテレフォニー Web サービス API です。Twilio は、サードパーティ製のサービスです (Azure の機能および Microsoft 製品ではありません)。

**Twilio Voice** を使用すると、アプリケーションで音声通話の発着信処理を行うことができます。**Twilio SMS** を使用すると、アプリケーションで SMS メッセージの送受信を行うことができます。**Twilio Client** を使用すると、アプリケーションに (モバイル接続を含む) 既存のインターネット接続を使用した音声通信を組み込むことができます。

## <span id="Pricing"></span></a>Twilio の料金および特別プラン

Twilio の料金については、[Twilio の料金に関するページ][Twilio の料金に関するページ]でご確認ください。Azure ユーザーは、[特別プラン][特別プラン]として、1,000 件のテキストまたは 1,000 分の着信通話相当の無料クレジットが用意されています。この特別プランにサインアップする、または詳細を確認するには、[][特別プラン]<http://ahoy.twilio.com/azure></a> を参照してください。

## <span id="Concepts"></span></a>概念

Twilio API は、アプリケーションに音声および SMS 機能を提供する REST ベースの API です。クライアント ライブラリはさまざまな言語で用意されています。言語の一覧については、[Twilio API ライブラリに関するページ][Twilio API ライブラリに関するページ]を参照してください。

Twilio API の主要な側面として、Twilio 動詞と Twilio Markup Language (TwiML) が挙げられます。

### <span id="Verbs"></span></a>Twilio 動詞

API では、Twilio 動詞を使用します。たとえば、**\<Say\>** 動詞は、メッセージを音声で返すことを Twilio に指示します。

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

アカウントに対して、さまざまな電話番号を Twilio で確認する必要があります。たとえば、電話を発信する場合は、電話番号を発信元 ID として Twilio で確認する必要があります。同様に、電話番号を使用して SMS メッセージを受信する場合は、受信に使用する電話番号を Twilio で確認する必要があります。電話番号を確認する方法の詳細については、[番号の管理に関するページ][番号の管理に関するページ]を参照してください。次に示すコードの一部は、Twilio で確認する必要がある電話番号に依存しています。

アプリケーションで既存の番号を使用する代わりに、Twilio 電話番号を購入することができます。Twilio 電話番号の購入については、[Twilio 電話番号のヘルプに関するページ][Twilio 電話番号のヘルプに関するページ]を参照してください。

## <span id="create_app"></span></a>Java アプリケーションの作成

1.  Twilio JAR を入手し、Java のビルド パスと WAR デプロイ アセンブリに追加します。[][]<https://github.com/twilio/twilio-java></a> で、GitHub のソースをダウンロードして独自の JAR を作成するか、ビルド済み JAR (依存関係あり/なし) をダウンロードすることができます。
2.  JDK の **cacerts** キーストアに Equifax Secure Certificate Authority 証明書と MD5 フィンガープリント 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 が格納されていることを確認します (シリアル番号は 35:DE:F4:CF、SHA1 フィンガープリントは D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A)。これは、[][1]<https://api.twilio.com></a> サービスの証明機関 (CA) 証明書であり、Twilio API の使用時に呼び出されます。JDK の **cacerts** キーストアに正しい CA 証明書が格納されているかどうかを確認する方法については、「[証明書を Java CA 証明書ストアに追加する方法][証明書を Java CA 証明書ストアに追加する方法]」を参照してください。

Java 用 Twilio クライアント ライブラリを使用する手順の詳細については、「[Azure 上の Java アプリケーションで Twilio を使用して通話する方法][Azure 上の Java アプリケーションで Twilio を使用して通話する方法]」で確認できます。

## <span id="configure_app"></span></a>Twilio ライブラリを使用するようにアプリケーションを構成する

コードのソース ファイルの先頭に、アプリケーションで使用する Twilio のパッケージまたはクラスの **import** ステートメントを追加できます。

Java ソース ファイルの場合:

    import com.twilio.*;
    import com.twilio.sdk.*;
    import com.twilio.sdk.resource.factory.*;
    import com.twilio.sdk.resource.instance.*;

Java Server Page (JSP) ソース ファイルの場合:

    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"

使用する Twilio のパッケージまたはクラスによって **import** ステートメントは異なる場合があります。

## <span id="howto_make_call"></span></a>方法: 発信通話する

次のコードでは、**CallFactory** クラスを使用して発信通話を行う方法を示しています。このコードは、Twilio から提供されるサイトも使用して、Twilio Markup Language (TwiML) 応答を返します。コードを実行する前に、**From** および **To** の電話番号の値を置き換えて、Twilio アカウントの **From** の電話番号を確認します。

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the CallFactory.
    Account account = client.getAccount();

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN"); // Use your own value for the second parameter.
    params.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    params.put("Url", Url);

    // Create an instance of the CallFactory class.
    CallFactory callFactory = account.getCallFactory();

    // Make the call.
    Call call = callFactory.create(params);

**CallFactory.create** メソッドに渡されるパラメーターの詳細については、[][2]<http://www.twilio.com/docs/api/rest/making-calls></a> を参照してください。

既に説明したように、このコードは Twilio から提供されるサイトを使用して、TwiML 応答を返します。代わりに独自のサイトを使用して TwiML 応答を返すことができます。詳細については、「[方法: 独自の Web サイトから TwiML 応答を返す][方法: 独自の Web サイトから TwiML 応答を返す]」を参照してください。

## <span id="howto_send_sms"></span></a>方法: SMS メッセージを送信する

次のコードは、**SmsFactory** クラスを使用して SMS メッセージを送信する方法を示しています。試用アカウントで SMS メッセージを送信できるように、**From** の番号として **4155992671** が Twilio から提供されます。コードを実行する前に、Twilio アカウントの **To** の番号を確認する必要があります。

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the SmsFactory.
    Account account = client.getAccount();

    // Send an SMS message.
    // Place the call From, To and Body values into a hash map. 
    HashMap<String, String> smsParams = new HashMap<String, String>();
    smsParams.put("From", "4155992671"); // The second parameter is a phone number provided
                                         // by Twilio for trial accounts.
    smsParams.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    smsParams.put("Body", "This is my SMS message.");

    // Create an instance of the SmsFactory class.
    SmsFactory smsFactory = account.getSmsFactory();

    // Send the message.
    Sms sms = smsFactory.create(smsParams);

**SmsFactory.create** メソッドに渡されるパラメーターの詳細については、[][3]<http://www.twilio.com/docs/api/rest/sending-sms></a> を参照してください。

## <span id="howto_provide_twiml_responses"></span></a>方法: 独自の Web サイトから TwiML 応答を返す

アプリケーションで Twilio API の呼び出しをインスタンス化する場合 (たとえば、**CallFactory.create** メソッドを使用した場合)、Twilio は TwiML 応答を返すことが想定されている URL にユーザーの要求を送信します。前の例では、Twilio から提供される URL [][4]<http://twimlets.com/message></a> を使用しています (TwiML は Web サービスで使用するように設計されており、ブラウザーで表示できます。たとえば、[][4]<http://twimlets.com/message></a> をクリックすると、空の **\<Response\>** 要素が表示されます。もう 1 つの例として、[][5]<http://twimlets.com/message?Message%5B0%5D=Hello%20World></a> をクリックすると、**\<Say\>** 要素を格納している **\<Response\>** 要素が表示されます)。

Twilio から提供される URL を使用する代わりに、HTTP 応答を返す独自の URL サイトを作成できます。HTTP 応答を返すサイトは任意の言語で作成できます。このトピックでは、JSP ページで URL をホストすることを前提としています。

次の JSP ページでは、通話時の TwiML 応答で "**Hello World**" というテキストが読み上げられます。

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello World</Say>
    </Response>

次の JSP ページでは、複数の言葉と間合いのポーズを含む TwiML 応答が返され、Twilio API バージョンと Azure ロール名に関する情報が読み上げられます。

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello from Azure</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>

**ApiVersion** パラメーターは、SMS 要求ではなく Twilio 音声要求に使用できます。Twilio 音声および SMS 要求に使用できる要求パラメーターを確認するには、<https://www.twilio.com/docs/api/twiml/twilio_request> および <https://www.twilio.com/docs/api/twiml/sms/twilio_request> をそれぞれ参照してください。**RoleName** 環境変数は Azure 展開の一部として使用できます (カスタム環境変数を追加して **System.getenv** から取得できるようにする場合は、[その他のロール構成設定に関するページ][その他のロール構成設定に関するページ]の環境変数に関するセクションを参照してください)。

TwiML 応答を提供するように JSP ページを設定したら、**CallFactory.create** メソッドに渡される URL として JSP ページの URL を使用します。たとえば、Azure ホステッド サービスに展開された MyTwiML という名前の Web アプリケーションがあり、JSP ページの名前が mytwiml.jsp である場合、次のコード例に示すように URL を **CallFactory.create** に渡すことができます。

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

TwiML で応答するための別の方法は **TwiMLResponse** クラスを使用することです。このクラスは **com.twilio.sdk.verbs** パッケージに含まれています。

Azure 上の Java で Twilio を使用する方法の詳細については、「[Azure 上の Java アプリケーションで Twilio を使用して通話する方法][Azure 上の Java アプリケーションで Twilio を使用して通話する方法]」を参照してください。

## <span id="AdditionalServices"></span></a>方法: その他の Twilio サービスを使用する

ここに示す例以外にも、Twilio が提供する Web ベースの API を使用して、Azure アプリケーションからその他の Twilio 機能を利用することができます。詳細については、[Twilio API に関するドキュメント][Twilio API に関するページ]を参照してください。

## <span id="NextSteps"></span></a> 次のステップ

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
  [電話番号を確認する]: #VerifyPhoneNumbers
  [Java アプリケーションの作成]: #create_app
  [Twilio ライブラリを使用するようにアプリケーションを構成する]: #configure_app
  [方法: 発信通話する]: #howto_make_call
  [方法: SMS メッセージを送信する]: #howto_send_sms
  [方法: 独自の Web サイトから TwiML 応答を返す]: #howto_provide_twiml_responses
  [方法: その他の Twilio サービスを使用する]: #AdditionalServices
  [Twilio の料金に関するページ]: http://www.twilio.com/pricing
  [特別プラン]: http://ahoy.twilio.com/azure
  [Twilio API ライブラリに関するページ]: https://www.twilio.com/docs/libraries
  [TwiML に関するページ]: http://www.twilio.com/docs/api/twiml
  [Twilio API に関するページ]: http://www.twilio.com/api
  [Twilio のサインアップ ページ]: https://www.twilio.com/try-twilio
  [Twilio アカウント ページ]: https://www.twilio.com/user/account
  [番号の管理に関するページ]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Twilio 電話番号のヘルプに関するページ]: https://www.twilio.com/help/faq/phone-numbers
  []: https://github.com/twilio/twilio-java
  [1]: https://api.twilio.com
  [証明書を Java CA 証明書ストアに追加する方法]: ../java-add-certificate-ca-store
  [Azure 上の Java アプリケーションで Twilio を使用して通話する方法]: ../partner-twilio-java-phone-call-example
  [2]: http://www.twilio.com/docs/api/rest/making-calls
  [3]: http://www.twilio.com/docs/api/rest/sending-sms
  [4]: http://twimlets.com/message
  [5]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
  [その他のロール構成設定に関するページ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh690945.aspx
  [Twilio に関するセキュリティ ガイドラインのページ]: http://www.twilio.com/docs/security
  [Twilio に関する方法とコード例のページ]: http://www.twilio.com/docs/howto
  [Twilio に関するクイック スタート チュートリアルのページ]: http://www.twilio.com/docs/quickstart
  [GitHub 上の Twilio に関するページ]: https://github.com/twilio
  [Twilio に関するサポートへの連絡のページ]: http://www.twilio.com/help/contact
