<properties linkid="develop-net-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (.NET) - Azure" metaKeywords="Azure Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to use Twilio for voice and SMS capabilities from Azure" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com"></tags>

# Azure から音声および SMS 機能に Twilio を使用する方法

このガイドでは、Azure の Twilio API サービスを使用して一般的なプログラミング タスクを実行する方法を紹介します。電話の発信と Short Message Service (SMS) メッセージの送信の各シナリオについて説明します。Twilio の詳細とアプリケーションで音声と SMS を使用する方法については、「[次の手順][]」を参照してください。

## 目次

-   [Twilio とは][]
-   [Twilio の料金][]
-   [概念][]
-   [Twilio アカウントの作成][]
-   [電話番号の確認][]
-   [Azure アプリケーションの作成][]
-   [Twilio ライブラリを使用するアプリケーションの構成][]
-   [方法: 発信通話する][]
-   [方法: SMS メッセージを送信する][]
-   [方法: 独自の Web サイトから TwiML 応答を提供する][]
-   [方法: その他の Twilio サービスを使用する][]
-   [次のステップ][次の手順]

## <span id="WhatIs"></span></a>Twilio とは

Twilio は、開発者がアプリケーションに音声、VoIP、およびメッセージングを埋め込むことを可能にし、ビジネス コミュニケーションを強化していきます。必要なすべてのインフラストラクチャをクラウド ベースのグローバル環境で仮想化し、Twilio 通信 API プラットフォームを通じてそれを公開します。アプリケーションは構築しやすく、スケーラビリティも優れています。従量課金制の柔軟性と、クラウドの信頼性の利点を活用できます。

**Twilio Voice** を使用すると、アプリケーションで音声通話の発着信処理を行うことができます。**Twilio SMS** を使用すると、アプリケーションで SMS メッセージの送受信を行うことができます。**Twilio Client** では、任意の電話、タブレット、またはブラウザーから VoIP 通話を行うことができ、WebRTC がサポートされています。

## <span id="Pricing"></span></a>Twilio の料金および特別プラン

Azure ユーザーは、[特別プラン][]として、Twilio アカウントをアップグレードする際に、$10 の Twilio クレジットが提供されます。この Twilio クレジットは、任意の Twilio 使用に対して利用できます。$10 のクレジットは、約 1,000 件の SMS メッセージの送信、または最大で 1,000 分の受信音声に相当します (ご利用の電話番号の場所と、メッセージまたは通話の相手の場所に応じて異なります)。この Twilio クレジットを利用するには、[ahoy.twilio.com/azure][] にアクセスします。

Twilio は、従量課金制サービスです。セットアップ料金は不要で、いつでもアカウントを閉じることができます。詳細については、[Twilio の料金のページ][]をご覧ください。

## <span id="Concepts"></span></a>概念

Twilio API は、アプリケーションに音声および SMS 機能を提供する REST ベースの API です。クライアント ライブラリはさまざまな言語で用意されています。言語の一覧については、[Twilio API ライブラリに関するページ][]を参照してください。

Twilio API の主要な側面として、Twilio 動詞と Twilio Markup Language (TwiML) が挙げられます。

### <span id="Verbs"></span></a>Twilio 動詞

API では、Twilio 動詞を使用します。たとえば、**\<Say\>** 動詞は、メッセージを音声で返すことを Twilio に指示します。

Twilio 動詞の一覧を次に示します。他の動詞と機能については、[Twilio Markup Language のドキュメント][]を参照してください。

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

Twilio の動詞と属性、および TwiML の詳細については、[TwiML に関するページ][Twilio Markup Language のドキュメント]を参照してください。Twilio API の詳細については、[Twilio API に関するページ][]を参照してください。

## <span id="CreateAccount"></span></a>Twilio アカウントを作成する

Twilio アカウントを取得する準備ができたら、[Twilio のサインアップ ページ][]でサインアップします。無料アカウントで始め、後でアカウントをアップグレードすることができます。

Twilio アカウントにサインアップすると、アカウント ID と認証トークンが発行されます。Twilio API を呼び出すには、この両方が必要になります。自分のアカウントが不正にアクセスされないように、認証トークンを安全に保管してください。アカウント ID と認証トークンは、[Twilio アカウント ページ][]の **[ACCOUNT SID]** フィールドと **[AUTH TOKEN]** フィールドでそれぞれ確認できます。

## <span id="VerifyPhoneNumbers"></span></a>電話番号を確認する

アカウントに対して、さまざまな電話番号を Twilio で確認する必要があります。たとえば、電話を発信する場合は、電話番号を発信元 ID として Twilio で確認する必要があります。同様に、電話番号を使用して SMS メッセージを受信する場合は、受信に使用する電話番号を Twilio で確認する必要があります。電話番号を確認する方法の詳細については、[番号の管理に関するページ][]を参照してください。次に示すコードの一部は、Twilio で確認する必要がある電話番号に依存しています。

アプリケーションで既存の番号を使用する代わりに、Twilio 電話番号を購入することができます。Twilio 電話番号の購入については、[Twilio 電話番号のヘルプに関するページ][]を参照してください。

## <span id="create_app"></span></a>Azure アプリケーションの作成

Twilio 対応のアプリケーションをホストする Azure アプリケーションには、他の Azure アプリケーションとの違いはありません。Twilio .NET ライブラリを追加し、その Twilio .NET ライブラリを使用するようにロールを構成します。
最初の Azure プロジェクトの作成に関する詳細については、「[Visual Studio を使用した Azure プロジェクトの作成][]」を参照してください。

## <span id="configure_app"></span></a>Twilio ライブラリを使用するアプリケーションの構成

Twilio は、一連の .NET ヘルパー ライブラリを提供します。このライブラリは、Twilio のさまざまな側面をラップし、Twilio REST API および Twilio Client と対話して TwiML 応答を生成するためのシンプルで簡単な方法を提供します。

Twilio は、.NET 開発者向けに次の 5 つのライブラリを用意しています。

<table border="1">

    <tr>
        <th>ライブラリ</th>
        <th>説明</th>
    </tr>
    <tr>
        <td>Twilio.API</td>
        <td>扱いやすい .NET ライブラリで Twilio REST API をラップする主要な Twilio ライブラリです。このライブラリは、.NET、Silverlight、および Windows Phone 7 向けに用意されています。</td>
    </tr>
    <tr>
        <td>Twilio.TwiML</td>
        <td>.NET 対応の方法で TwiML マークアップを生成します。</td>
    </tr>
    <tr>
        <td>Twilio.MVC</td>
        <td>ASP.NET MVC を使用する開発者向けのこのライブラリは、TwilioController、TwiML ActionResult、および要求検証の属性を含んでいます。</td>
    </tr>
    <tr>
        <td>Twilio.WebMatrix</td>
        <td>Microsoft が無償で提供する WebMatrix 開発ツールを使用する開発者向けのこのライブラリは、Twilio のさまざまな操作に対応する Razor 構文ヘルパーを含んでいます。</td>
    </tr>
    <tr>
        <td>Twilio.Client.Capability</td>
        <td>Twilio Client JavaScript SDK と共に使用するための Capability トークン ジェネレーターを含んでいます。</td>
    </tr>
</table>
</p>
すべてのライブラリに .NET 3.5、Silverlight 4、または Windows Phone 7 以降が必要です。

このガイドに記載した例では、Twilio.API ライブラリを使用します。

ライブラリは、[Visual Studio 2010 および 2012 で使用可能な NuGet パッケージ マネージャー拡張機能を使用してインストール][]できます。ソース コードは、ライブラリを使用するための詳細なドキュメントを格納した Wiki が含まれる [GitHub][] にホストされます。

Microsoft Visual Studio 2010 では既定で NuGet の Version 1.2 がインストールされます。Twilio ライブラリをインストールするには、NuGet の Version 1.6 以降が必要です。NuGet のインストールまたは更新については、[][]<http://nuget.org/></a> を参照してください。

<div class="dev-callout">
<b>注</b>
<p>NuGet の最新バージョンをインストールするには、最初に Visual Studio Extension Manager を使用して、読み込み済みのバージョンをアンインストールする必要があります。そのためには、Visual Studio を管理者として実行する必要があります。そのようにしない場合、[アンインストール] ボタンは無効です。</p>
</div>

### <span id="use_nuget"></span></a>Twilio ライブラリを Visual Studio プロジェクトに追加するには

1.  Visual Studio でソリューションを開きます。
2.  **[参照]** を右クリックします。
3.  **[NuGet パッケージの管理]** をクリックします。
4.  **[オンライン]** をクリックします。
5.  [オンライン検索] ボックスに、「*twilio*」と入力します。
6.  Twilio パッケージで **[インストール]** をクリックします。

## <span id="howto_make_call"></span></a>方法: 発信通話する

次のコードでは、**TwilioRestClient** クラスを使用して発信通話を行う方法を示しています。このコードは、Twilio から提供されるサイトも使用して、Twilio Markup Language (TwiML) 応答を返します。コードを実行する前に、**From** および **To** の電話番号の値を置き換えて、Twilio アカウントの **From** の電話番号を確認します。

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    string accountSID = "your_twilio_account";
    string authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Instantiate the call options that are passed
    // to the outbound call
    CallOptions options = new CallOptions();

    // Set the call From, To, and URL values to use for the call.
    // This sample uses the sandbox number provided by
    // Twilio to make the call.
    options.From = "+NNNNNNNNNN";
    options.To = "NNNNNNNNNN";
    options.Url = Url;

    // Make the call.
    var call = client.InitiateOutboundCall(options);

**client.InitiateOutboundCall** メソッドに渡されるパラメーターの詳細については、[][1]<http://www.twilio.com/docs/api/rest/making-calls></a> を参照してください。

既に説明したように、このコードは Twilio から提供されるサイトを使用して、TwiML 応答を返します。代わりに独自のサイトを使用して TwiML 応答を返すこともできます。詳細については、「[方法: 独自の Web サイトから TwiML 応答を返す][方法: 独自の Web サイトから TwiML 応答を提供する]

## <span id="howto_send_sms"></span></a>方法: SMS メッセージを送信する

次のスクリーンショットは、**TwilioRestClient** クラスを使用して SMS メッセージを送信する方法を示しています。試用アカウントで SMS メッセージを送信できるように、**From** の番号が Twilio から提供されます。コードを実行する前に、Twilio アカウントの **To** の番号を確認する必要があります。

        // Use your account SID and authentication token instead
        // of the placeholders shown here.
        string accountSID = "your_twilio_account";
        string authToken = "your_twilio_authentication_token";

        // Create an instance of the Twilio client.
        TwilioRestClient client;
        client = new TwilioRestClient(accountSID, authToken);

        // Send an SMS message.
        SMSMessage result = client.SendSmsMessage(
            "+14155992671", "+12069419717", "This is my SMS message.");

        if (result.RestException != null)
        {
            //an exception occurred making the REST call
            string message = result.RestException.Message;
        }

## <span id="howto_provide_twiml_responses"></span></a>方法: 独自の Web サイトから TwiML 応答を返す

アプリケーションで Twilio API の呼び出しをインスタンス化する場合 (たとえば、**client.InitiateOutboundCall** メソッドを使用した場合)、Twilio は TwiML 応答を返すことが想定されている URL にユーザーの要求を送信します。「[方法: 発信通話する][]」の例では、Twilio から提供される URL [][2]<http://twimlets.com/message></a> を使用して応答を返します。

<div class="dev-callout">
<b>注</b>
<p>TwiML は Web サービスで使用するように設計されており、ブラウザーで表示できます。たとえば、[http://twimlets.com/message] (twimlet_message_url) をクリックして、空の &lt;Response&gt; 要素を確認します。もう 1 つの例として、[http://twimlets.com/message?Message%5B0%5D=Hello%20World] (twimlet_message_url_hello_world) をクリックして、&lt;Say&gt; 要素を格納している &lt;Response&gt; 要素を確認します。</p>
</div>

Twilio から提供される URL を使用する代わりに、HTTP 応答を返す独自の URL サイトを作成できます。HTTP 応答を返すサイトは、任意の言語で作成できます。このトピックでは、ASP.NET 汎用ハンドラーから URL をホストすることを想定しています。

次の ASP.NET ハンドラーは、通話時の TwiML 応答で **Hello World** というテキストを生成します。

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;

    namespace WebRole1
    {
        /// <summary>
        /// Summary description for Handler1
        /// </summary>
        public class Handler1 : IHttpHandler
        {

            public void ProcessRequest(HttpContext context)
            {
                context.Response.Clear();
                context.Response.ContentType = "text/xml";
                context.Response.ContentEncoding = System.Text.Encoding.UTF8;
                string twiMLResponse = "<Response><Say>Hello World.</Say></Response>";
                context.Response.Write(twiMLResponse);
                context.Response.End();
            }

            public bool IsReusable
            {
                get
                {
                    return false;
                }
            }
        }
    }

この例からわかるように、TwiML 応答は XML ドキュメントにすぎません。Twilio.TwiML ライブラリには、TwiML を生成するクラスが用意されています。次の例では、前の例と同じ応答が生成されますが、TwilioResponse クラスを使用しています。

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;

    namespace WebRole1
    {
        /// <summary>
        /// Summary description for Handler1
        /// </summary>
        public class Handler1 : IHttpHandler
        {

            public void ProcessRequest(HttpContext context)
            {
                var twiml = new Twilio.TwiML.TwilioResponse();
                twiml.Say("Hello World.");

                context.Response.Clear();
                context.Response.ContentType = "text/xml";
                context.Response.Write(twiml.ToString());
                context.Response.End();
            }

            public bool IsReusable
            {
                get
                {
                    return false;
                }
            }
        }
    }

TwiML の詳細については、[][3]<https://www.twilio.com/docs/api/twiml></a> を参照してください。

TwiML 応答を提供する方法を設定したら、**client.InitiateOutboundCall** メソッドにその URL を渡すことができます。たとえば、Windows Azure クラウド サービスにデプロイされた MyTwiML という名前の Web アプリケーションがあり、ASP.NET ハンドラーの名前が mytwiml.ashx である場合、次のコード例に示すように URL を **client.InitiateOutboundCall** に渡すことができます。

    // Place the call From, To, and URL values into a hash map.
    // This sample uses the sandbox number provided by Twilio to make the call.
    options.From = "NNNNNNNNNN";
    options.To = "NNNNNNNNNN";
    options.Url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx";

    // Place the call.
    var call = client.InitiateOutboundCall(options);

ASP.NET を使用する Windows Azure での Twilio の使用に関する詳細については、「[Windows Azure の Web ロールで Twilio を使用して通話する方法][]」を参照してください。

[WACOM.INCLUDE [twilio\_additional\_services\_and\_next\_steps][]]

  [次の手順]: #NextSteps
  [Twilio とは]: #WhatIs
  [Twilio の料金]: #Pricing
  [概念]: #Concepts
  [Twilio アカウントの作成]: #CreateAccount
  [電話番号の確認]: #VerifyPhoneNumbers
  [Azure アプリケーションの作成]: #create_app
  [Twilio ライブラリを使用するアプリケーションの構成]: #configure_app
  [方法: 発信通話する]: #howto_make_call
  [方法: SMS メッセージを送信する]: #howto_send_sms
  [方法: 独自の Web サイトから TwiML 応答を提供する]: #howto_provide_twiml_responses
  [方法: その他の Twilio サービスを使用する]: #AdditionalServices
  [特別プラン]: http://www.twilio.com/azure
  [ahoy.twilio.com/azure]: http://ahoy.twilio.com/azure
  [Twilio の料金のページ]: http://www.twilio.com/voice/pricing
  [Twilio API ライブラリに関するページ]: https://www.twilio.com/docs/libraries
  [Twilio Markup Language のドキュメント]: http://www.twilio.com/docs/api/twiml
  [Twilio API に関するページ]: http://www.twilio.com/api
  [Twilio のサインアップ ページ]: https://www.twilio.com/try-twilio
  [Twilio アカウント ページ]: https://www.twilio.com/user/account
  [番号の管理に関するページ]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Twilio 電話番号のヘルプに関するページ]: https://www.twilio.com/help/faq/phone-numbers
  [Visual Studio を使用した Azure プロジェクトの作成]: http://msdn.microsoft.com/en-us/library/windowsazure/ee405487.aspx
  [Visual Studio 2010 および 2012 で使用可能な NuGet パッケージ マネージャー拡張機能を使用してインストール]: http://www.twilio.com/docs/csharp/install
  [GitHub]: https://github.com/twilio/twilio-csharp
  []: http://nuget.org/
  [1]: http://www.twilio.com/docs/api/rest/making-calls
  [2]: http://twimlets.com/message
  [3]: https://www.twilio.com/docs/api/twiml
  [Windows Azure の Web ロールで Twilio を使用して通話する方法]: ../partner-twilio-cloud-services-dotnet-phone-call-web-role/
  [twilio\_additional\_services\_and\_next\_steps]: ../includes/twilio_additional_services_and_next_steps.md
