<properties linkid="dev-net-how-to-sendgrid-email-service" urlDisplayName="SendGrid 電子メール サービス" pageTitle="SendGrid 電子メール サービスの使用方法 (.NET) - Azure" metaKeywords="Azure SendGrid, Azure 電子メール サービス, Azure SendGrid .NET, Azure 電子メール .NET, Azure SendGrid C#, Azure 電子メール C#" description="Azure の SendGrid 電子メール サービスを使用して電子メールを送信する方法について説明します。コード サンプルは C# で記述され、.NET API を使用しています。" metaCanonical="" services="" documentationCenter=".NET" title="SendGrid を使用した Azure での電子メールの送信方法" authors=""  solutions="" writer="" manager="" editor=""  />





# SendGrid を使用した Azure での電子メールの送信方法

このガイドでは、Azure の SendGrid 電子メール サービスを使用して一般的なプログラム タスクを実行する方法を紹介します。サンプルは C# で記述され、
.NET API を利用しています。紹介するシナリオは、**電子メールの作成**、
**電子メールの送信**、**添付ファイルの追加**、および
**フィルターの使用**です。SendGrid と電子メールの送信の詳細については、「[次のステップ][]」を参照してください。

<h2><a name="toc"></a>目次</h2>

[SendGrid 電子メール サービスとは][]   
[SendGrid アカウントを作成する][]   
[SendGrid .NET クラス ライブラリを参照する][]   
[方法: 電子メールを作成する][]   
[方法: 電子メールを送信する][]   
[方法: 添付ファイルを追加する][]   
[方法: フィルターを使用してフッター、追跡、および分析を有効にする][]   
[方法: その他の SendGrid サービスを使用する][]   
[次のステップ][]

<h2><a name="whatis"></a><span  class="short-header">SendGrid 電子メール サービスとは</span>SendGrid 電子メール サービスとは</h2>

SendGrid は、信頼性の高い[トランザクション電子メール配信]、
拡張性、およびリアルタイム分析の機能を備えた[クラウドベース電子メール サービス]であり、
柔軟な API を備えているためカスタム統合も容易です。SendGrid の一般的な使用シナリオを
次に示します。

-   顧客に受信通知を自動送信する。
-   顧客に広告メールを月 1 回送信するための配布リストを
    管理する。
-   ブロックされた電子メールや顧客の応答性などを表す測定値を
    リアルタイムで収集する。
-   傾向を認識するために役立つレポートを生成する。
-   顧客の問い合わせを転送する。

詳細については、[http://sendgrid.com](http://sendgrid.com) を参照してください。

<h2><a name="createaccount"></a><span  class="short-header">SendGrid アカウントの作成</span>SendGrid アカウントの作成</h2>

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

<h2><a name="reference"></a><span  class="short-header">SendGrid .NET クラス ライブラリの参照</span>SendGrid .NET クラス ライブラリの参照</h2>

SendGrid NuGet パッケージは、SendGrid API を取得し、すべての依存関係を
備えたアプリケーションを構成する最も簡単な方法です。NuGet は
Microsoft Visual Studio 2012 に含まれる Visual Studio 拡張機能であり、これを使用してライブラリおよびツールの
インストールと更新を簡単に行うことができます。

<div class="dev-callout">
<b>注:</b>
<p>Visual Studio 2012 よりも
前のバージョンの Visual Studio を利用している場合、NuGet をインストールするには、http://www.nuget.org<a href="http://www.nuget.org"></a> にアクセスして、<b>[Install NuGet]</b> をクリックしてください。</p>
</div>

アプリケーションに SendGrid NuGet パッケージをインストールするには、次のステップを行います。

1. **ソリューション エクスプローラー**で **[参照]** を右クリックし、
    **[NuGet パッケージの管理]** をクリックします。

2. **[NuGet パッケージの管理]** ダイアログ ボックスの左側のウィンドウで、**[オンライン]** をクリックします。

3. 「**SendGrid**」を検索し、検索結果の一覧から **SendGrid** を選択します。

    ![SendGrid NuGet パッケージ][SendGrid-NuGet-package]

4. **[インストール]** をクリックしてインストールを実行した後、このダイアログを閉じます。

SendGrid の .NET クラス ライブラリは、**SendGridMail** という名前です。これには
次の名前空間が含まれます。

-  **SendGridMail**: 電子メール アイテムの作成と処理の際に使用する名前空間です。
-  **SendGridMail.Transport**: **SMTP** プロトコルまたは **Web/REST** を使用した HTTP 1.1 プロトコルを使用して電子メールを送信する際の名前空間です。

プログラムを使用して SendGrid 電子メール サービスにアクセスするすべての C\# ファイルの冒頭部分に、名前空間を宣言する次のコードを追加します。
**System.Net** と **System.Net.Mail** は、.NET Framework 名前空間です。この 2 つが宣言されているのは、SendGrid API で一般的に使用される型が含まれているためです。

    using System.Net;
    using System.Net.Mail;
    using SendGridMail;
    using SendGridMail.Transport;

<h2><a name="createemail"></a><span  class="short-header">方法: 電子メールを作成する</span>方法: 電子メールを作成する</h2>

**SendGrid.GetInstance** 静的メソッドを使用して、**SendGrid** 型の
電子メール メッセージを作成します。メッセージが作成されたら、
**SendGrid**プロパティとメソッドを使用して、電子メール送信者、
電子メール受信者、件名、電子メール本文などの値を設定できます。

次の例に、すべての値が設定された電子メール オブジェクトの作成方法を示します。

    // 電子メールのプロパティをセットアップします。
    var from = new MailAddress("john@contoso.com");
    var to   = new MailAddress[] { new MailAddress("jeff@contoso.com") };
    var cc   = new MailAddress[] { new MailAddress("anna@contoso.com") };
    var bcc  = new MailAddress[] { new MailAddress("peter@contoso.com") };
    var subject = "Testing the SendGrid Library";
    var html    = "<p>Hello World!</p>";
    var text = "Hello World plain text!";
    var transport = SendGridMail.TransportType.SMTP;

    // 電子メールを作成し、8 個のプロパティを引数として渡します。
    SendGrid myMessage = SendGrid.GetInstance(from, to, cc, bcc, subject, html, text, transport);

次の例に、空の電子メール オブジェクトの作成方法を示します。

    // 先に電子メール オブジェクトを作成した後でプロパティを追加します。
    SendGrid myMessage = SendGrid.GetInstance();
     
    // メッセージのプロパティを追加します。
    MailAddress sender = new MailAddress(@"John Smith <john@contoso.com>");
    myMessage.From = sender;
     
    // 宛先のフィールドに複数のアドレスを追加します。
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@contoso.com>",
        @"Anna Lidman <anna@contoso.com>",
        @"Peter Saddow <peter@contoso.com>"
    };
     
    foreach (string recipient in recipients)
    {
        myMessage.AddTo(recipient);
    }
     
    // メッセージの本文を HTML 形式で追加します。
    myMessage.Html = "<p>Hello World!</p>";

    // 件名を追加します。
    myMessage.Subject = "Testing the SendGrid Library";

**SendGrid** 型でサポートされるすべてのプロパティとメソッドの詳細については、GitHub の [sendgrid-csharp][] に関するページを参照してください。

<h2><a name="sendemail"></a><span  class="short-header">方法: 電子メールを送信する</span>方法: 電子メールを送信する</h2>

電子メール メッセージを作成した後で、SMTP、または SendGrid の
Web API を使用してメッセージを送信することができます。各 API の長所と欠点の詳細については、[SMTP と Web API を比較した SendGrid ドキュメント][]を参照してください。

どちらのプロトコルを使用して電子メールを送信する場合も、
SendGrid アカウントの資格情報 (ユーザー名とパスワード) を指定する必要があります。次のコードに、
**NetworkCredential** オブジェクトで資格情報をラップする方法を示します。

    // SendGrid アカウントにアクセスするためのネットワーク資格情報を作成します。
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    var credentials = new NetworkCredential(username, pswd);

電子メール メッセージを送信するには、SMTP プロトコルを使用する
**SMTP** クラスか、SendGrid Web API を呼び出す **REST** トランスポート クラスに
対して、**Deliver** メソッドを使用します。次の例に、SMTP と Web API で
メッセージを送信する方法を示します。

### SMTP

    // 先に電子メール オブジェクトを作成した後でプロパティを追加します。
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // 資格情報を作成し、ユーザー名とパスワードを指定します。
    var credentials = new NetworkCredential("username", "password");

    // 電子メールを送信するための SMTP トランスポートを作成します。
    var transportSMTP = SMTP.GetInstance(credentials);

    // 電子メールを送信します。
    transportSMTP.Deliver(myMessage);

### Web API

    // 先に電子メール オブジェクトを作成した後でプロパティを追加します。
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // 資格情報を作成し、ユーザー名とパスワードを指定します。
    var credentials = new NetworkCredential("username", "password");

    // 電子メールを送信するための REST トランスポートを作成します。
    var transportREST = Web.GetInstance(credentials);

    // 電子メールを送信します。
    transportREST.Deliver(myMessage);

<h2><a name="addattachment"></a><span  class="short-header">方法: 添付ファイルを追加する</span>方法: 添付ファイルを追加する</h2>

添付ファイルをメッセージに追加するには、**AddAttachment** メソッドを呼び出し、
添付するファイルの名前とパスを指定します。
複数のファイルを添付するには、添付する各ファイルにつき 1 回このメソッドを
呼び出します。次の例に、メッセージに添付ファイルを追加する
方法を示します。

    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");

<h2><a name="usefilters"></a><span  class="short-header">方法: フィルターを使用して、フッター、トラッキング、および分析機能を有効にする</span>方法: フィルターを使用して、フッター、トラッキング、および分析機能を有効にする</h2>

SendGrid では、"フィルター" を使用することでその他の電子メール機能も
利用することができます。その設定を電子メール メッセージに追加することで、クリック追跡、
Google 分析、サブスクリプション追跡などの独自の機能を
有効にすることができます。すべてのフィルターの一覧に
ついては、[フィルター設定に関するページ][]を参照してください。

フィルターは、**SendGrid** クラスの一部として実装されたメソッドを使用する
**SendGrid** 電子メール メッセージに適用できます。電子メール メッセージに対するフィルターを有効にする前に、
**InitalizeFilters** メソッドを呼び出して、使用できるフィルターの
一覧を初期化しておく必要があります。

次の例に、フッター フィルターとクリック追跡フィルターの使用方法を
示します。

### フッター

    // 先に電子メール オブジェクトを作成した後でプロパティを追加します。
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.InitializeFilters();
    // メッセージにフッターを追加します。
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### クリック追跡

    // 先に電子メール オブジェクトを作成した後でプロパティを追加します。
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href=\"http://www.windowsazure.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";

    myMessage.InitializeFilters();
    // true は、電子メールのプレーンテキスト部分のリンクも
    // リンク追跡のために上書きする必要があることを示します。
    myMessage.EnableClickTracking(true);

<h2><a name="useservices"></a><span  class="short-header">方法: その他の SendGrid サービスを使用する</span>方法: その他の SendGrid サービスを使用する</h2>

SendGrid の Web ベース API を使用して、Azure アプリケーション
からその他の SendGrid 機能を利用することができます。詳細については、
[SendGrid API に関するドキュメント][]を参照してください。

<h2><a name="nextsteps"></a><span  class="short-header">次のステップ</span>次のステップ</h2>

これで、SendGrid 電子メール サービスの基本を学習できました。
さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

*   SendGrid C\# ライブラリ レポート: [sendgrid-csharp][]
*   SendGrid API に関するドキュメント: <http://docs.sendgrid.com/documentation/api/>
*   Azure ユーザー向けの SendGrid 特別プラン: [http://sendgrid.com](http://sendgrid.com)

  [次のステップ]: #nextsteps
  [SendGrid 電子メール サービスとは]: #whatis
  [SendGrid アカウントを作成する]: #createaccount
  [SendGrid .NET クラス ライブラリを参照する]: #reference
  [方法: 電子メールを作成する]: #createemail
  [方法: 電子メールを送信する]: #sendemail
  [方法: 添付ファイルを追加する]: #addattachment
  [方法: フィルターを使用してフッター、追跡、および分析を有効にする]: #usefilters
  [方法: その他の SendGrid サービスを使用する]: #useservices
  
  
  [特別プラン]: http://www.sendgrid.com/azure.html
  
  
  
  [SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP と Web API を比較した SendGrid ドキュメント]: http://docs.sendgrid.com/documentation/get-started/integrate/examples/smtp-vs-rest/
  [フィルター設定に関するページ]: http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/
  [SendGrid API に関するドキュメント]: http://docs.sendgrid.com/documentation/api/
  
  [トランザクション電子メール配信]: http://sendgrid.com/solutions
  [クラウドベース電子メール サービス]: http://sendgrid.com/transactional-email

