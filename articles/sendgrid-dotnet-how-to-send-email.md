<properties linkid="dev-net-how-to-sendgrid-email-service" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (.NET) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid .NET, Azure email .NET, Azure SendGrid C#, Azure email C#" description="Learn how send email with the SendGrid email service on Azure. Code samples written in C# and use the .NET API." metaCanonical="" services="" documentationCenter=".NET" title="How to Send Email Using SendGrid with Azure" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="carolz" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />

# SendGrid を使用した Azure での電子メールの送信方法

更新: 2014 年 8 月 21 日

このガイドでは、Azure の SendGrid 電子メール サービスを使用して一般的な
プログラム タスクを実行する方法を紹介します。サンプルは C\\
 で記述され、.NET API を利用しています。紹介するシナリオは、**電子メールの作成**、
**電子メールの送信**、**添付ファイルの追加**、および**フィルターの使用**
です。SendGrid と電子メールの送信の詳細については、「
[次のステップ][次のステップ]」を参照してください。

## <a name="toc"></a>目次

[SendGrid 電子メール サービスとは][SendGrid 電子メール サービスとは]   

[SendGrid 電子メール サービスとは][SendGrid 電子メール サービスとは]   
[SendGrid アカウントの作成][SendGrid アカウントの作成][SendGrid アカウントの作成]   
[SendGrid .NET クラス ライブラリの参照][SendGrid .NET クラス ライブラリの参照]   [SendGrid .NET クラス ライブラリの参照][SendGrid .NET クラス ライブラリの参照]   
[方法: 電子メールを作成する][方法: 電子メールを作成する][方法: 電子メールを作成する]   
[方法: 電子メールを送信する][方法: 電子メールを送信する][方法: 電子メールを送信する]   
[方法: 添付ファイルを追加する][方法: 添付ファイルを追加する][方法: 添付ファイルを追加する]   
[方法: フィルターを使用してフッター、追跡、および分析を有効にする][方法: フィルターを使用してフッター、追跡、および分析を有効にする]   
[方法: フィルターを使用してフッター、追跡、および分析を有効にする][方法: フィルターを使用してフッター、追跡、および分析を有効にする]   
[方法: その他の SendGrid サービスを使用する][方法: その他の SendGrid サービスを使用する]   
[方法: その他の SendGrid サービスを使用する][方法: その他の SendGrid サービスを使用する]   
[次のステップ][次のステップ][次のステップ]   

## <a name="whatis"></a><span class="short-header">SendGrid 電子メール サービスとは</span>

SendGrid は、信頼性の高い[トランザクション電子メール配信][トランザクション電子メール配信]、拡張性、およびリアルタイム分析の機能を備えた
[クラウドベース電子メール サービス][クラウドベース電子メール サービス]であり、柔軟な API を備えているためカスタム統合も容易です。
SendGrid の一般的な使用シナリオを次に示します。

-   顧客に受信通知を自動送信する。
-   顧客に広告メールを月 1 回送信するための配布リストを管理する。
-   ブロックされた電子メールや顧客の応答性などを表す測定値をリアルタイムで収集する。
-   傾向を認識するために役立つレポートを生成する。
-   顧客の問い合わせを転送する。

詳細については、[][]<http://sendgrid.com></a> を参照してください。

## <a name="createaccount"></a><span class="short-header">SendGrid アカウントを作成する</span>

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference"></a><span class="short-header">SendGrid .NET クラス ライブラリの参照</span>SendGrid .NET クラス ライブラリの参照

[SendGrid NuGet パッケージ][SendGrid NuGet パッケージ]は、SendGrid API を取得し、すべての依存関係を
備えたアプリケーションを構成する最も簡単な方法です。NuGet は
Microsoft Visual Studio 2012 に含まれる Visual Studio 拡張機能であり、これを使用してライブラリおよび
ツールのインストールと更新を簡単に行うことができます。

<div class="dev-callout">
<b>注</b>
<p>BLOB 
Visual Studio 2012 よりも前のバージョンの Visual Studio を利用している場合、NuGet をインストールするには、<a href="http://www.nuget.org">http://www.nuget.org</a> にアクセスして、<b>[Install NuGet]</b> をクリックしてください。
</p>
</div>

アプリケーションに SendGrid NuGet パッケージをインストールするには、次のステップを行います。

1.  **ソリューション エクスプローラー**で **[参照]** を右クリックし、
    **[NuGet パッケージの管理]** をクリックします。

2.  **[NuGet パッケージの管理]** ダイアログ ボックスの左側のウィンドウで、**[オンライン]** をクリックします。

3.  「**SendGrid**」を検索し、検索結果の一覧から **SendGrid** を
    選択します。

    ![SendGrid NuGet パッケージ][1]

4.  **[インストール]** をクリックしてインストールを実行した後、
    このダイアログを閉じます。

SendGrid の .NET クラス ライブラリは、**SendGridMail** という名前です。これには次の名前空間が含まれます。

-   **SendGridMail**: 電子メール アイテムの作成と処理の際に使用する名前空間です。
-   **SendGridMail.Transport**: **SMTP** プロトコル
    (**Web/REST** を使用した HTTP 1.1 プロトコル) を使用して電子メールを送信する際の名前空間です。

プログラムを使用して SendGrid 電子メール サービスにアクセスするすべての C# ファイル
の冒頭部分に、名前空間を宣言する次のコードを追加します。
**System.Net** と **System.Net.Mail** は、.NET Framework 名前空間です。
この 2 つが宣言されているのは、SendGrid API で一般的に使用される型が
含まれているためです。

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

## <a name="createemail"></a><span class="short-header">方法: 電子メールを作成する</span>方法: 電子メールを作成する

**SendGrid.GetInstance** 静的メソッドを使用して、
**SendGrid** 型の電子メール メッセージを作成します。メッセージが作成されたら、
**SendGrid** のプロパティとメソッドを使用して、
電子メール送信者、電子メール受信者、件名、電子メール本文などの値を
設定できます。

次の例に、すべての値が設定された電子メール オブジェクトの作成方法を
示します。

    // Create the email object first, then add the properties.
    var myMessage = new SendGridMessage();

    // Add the message properties.
    myMessage.From = new MailAddress("john@example.com");

    // Add multiple addresses to the To field.
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@example.com>",
        @"Anna Lidman <anna@example.com>",
        @"Peter Saddow <peter@example.com>"
    };

    myMessage.AddTo(recipients);

    myMessage.Subject = "Testing the SendGrid Library";

    //Add the HTML and Text bodies
    myMessage.Html = "<p>Hello World!</p>";
    myMessage.Text = "Hello World plain text!";

**SendGrid** 型でサポートされるすべてのプロパティとメソッドの
詳細については、GitHub の [sendgrid-csharp に関するページ][sendgrid-csharp に関するページ]を参照してください。

## <a name="sendemail"></a><span class="short-header">方法: 電子メールを送信する</span>

電子メール メッセージを作成した後で、
SendGrid の Web API を使用してメッセージを送信することができます。または、[.NET の組み込みライブラリを使用][.NET の組み込みライブラリを使用]する方法もあります。

電子メールを送信する場合は、
SendGrid アカウントの資格情報 (ユーザー名とパスワード) を指定する必要があります。次のコードに、
**NetworkCredential** オブジェクトで資格情報をラップする
方法を示します。

    // Create network credentials to access your SendGrid account
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    /* Alternatively, you may store these credentials via your Azure portal
       by clicking CONFIGURE and adding the key/value pairs under "app settings".
       Then, you may access them as follows: 
       var username = System.Environment.GetEnvironmentVariable("SENDGRID_USER"); 
       var pswd = System.Environment.GetEnvironmentVariable("SENDGRID_PASS");
       assuming you named your keys SENDGRID_USER and SENDGRID_PASS */

    var credentials = new NetworkCredential(username, pswd);

次の例に、Web API でメッセージを送信する方法を示します。

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Create credentials, specifying your user name and password.
    var credentials = new NetworkCredential("username", "password");

    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

    // Send the email.
    // You can also use the **DeliverAsync** method, which returns an awaitable task.
    transportWeb.Deliver(myMessage);

## <a name="addattachment"></a><span class="short-header">方法: 添付ファイルを追加する</span>

添付ファイルをメッセージに追加するには、**AddAttachment**
 メソッドを呼び出し、添付するファイルの名前とパスを指定します。
複数のファイルを添付するには、添付する各ファイルにつき 1 回このメソッドを
呼び出します。次の例に、メッセージに添付ファイルを追加する方法を示します。

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");

データの**ストリーム**から添付ファイルを追加することもできます。上記と同じメソッド **AddAttachment** を呼び出し、メッセージに表示させるファイル名をデータのストリームに渡すことでも、同じ操作を実行できます。

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }

## <a name="usefilters"></a><span class="short-header">方法: フィルターを使用してフッター、追跡、および分析を有効にする</span>

SendGrid では、フィルターを使用することでその他の電子メール機能も
利用することができます。その設定を電子メール メッセージに追加することで、クリック追跡、
Google 分析、サブスクリプション追跡などの独自の機能を
有効にすることができます。すべてのフィルターの一覧については、
[フィルター設定に関するページ][フィルター設定に関するページ]を参照してください。

フィルターは、**SendGrid** クラスの一部として実装されたメソッドを
使用する **SendGrid** 電子メール メッセージに適用できます。

次の例に、フッター フィルターとクリック追跡フィルターの使用方法を示します。

### フッター

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Add a footer to the message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### クリック追跡

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href=\"http://www.example.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";

    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

## <a name="useservices"></a><span class="short-header">方法: その他の SendGrid サービスを使用する</span>

SendGrid の Web ベース API を使用して、Azure アプリケーションから
その他の SendGrid 機能を利用することができます。詳細については、
[SendGrid API に関するドキュメント][SendGrid API に関するドキュメント]を参照してください。

## <a name="nextsteps"></a><span class="short-header">次のステップ</span>次のステップ

これで、SendGrid 電子メール サービスの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

-   SendGrid C# ライブラリ レポート: [sendgrid-csharp][sendgrid-csharp に関するページ]
-   SendGrid API に関するドキュメント: <http://docs.sendgrid.com/documentation/api/>
-   Azure ユーザー向けの SendGrid 特別プラン: [][]<http://sendgrid.com></a>

  [次のステップ]: #nextsteps
  [SendGrid 電子メール サービスとは]: #whatis
  [SendGrid アカウントの作成]: #createaccount
  [SendGrid .NET クラス ライブラリの参照]: #reference
  [方法: 電子メールを作成する]: #createemail
  [方法: 電子メールを送信する]: #sendemail
  [方法: 添付ファイルを追加する]: #addattachment
  [方法: フィルターを使用してフッター、追跡、および分析を有効にする]: #usefilters
  [方法: その他の SendGrid サービスを使用する]: #useservices
  [トランザクション電子メール配信]: http://sendgrid.com/solutions
  [クラウドベース電子メール サービス]: http://sendgrid.com/transactional-email
  []: http://sendgrid.com
  [SendGrid NuGet パッケージ]: https://www.nuget.org/packages/Sendgrid
  [1]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png
  [sendgrid-csharp に関するページ]: https://github.com/sendgrid/sendgrid-csharp
  [.NET の組み込みライブラリを使用]: https://sendgrid.com/docs/Code_Examples/csharp.html
  [フィルター設定に関するページ]: http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/
  [SendGrid API に関するドキュメント]: http://docs.sendgrid.com/documentation/api/
