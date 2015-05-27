<properties 
	pageTitle="SendGrid 電子メール サービスの使用方法 (.NET) - Azure" 
	description="Azure で SendGrid 電子メール サービスを使用して電子メールを送信する方法について説明します。コード サンプルは C# で記述され、.NET API を使用しています。" 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="thinkingserious" 
	manager="sendgrid" 
	editor="erikre"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/24/2015" 
	ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork"/>





# SendGrid を使用した Azure での電子メールの送信方法

更新日: 2015 年 2 月 24 日

<h2><a name="overview"></a><span  class="short-header">概要</span></h2>

このガイドでは、Azure の SendGrid 電子メール サービスを使用して一般的なプログラム タスクを実行する方法を紹介します。サンプルは C# で記述され、.NET API を利用しています。紹介するシナリオは、**電子メールの作成**、**電子メールの送信**、**添付ファイルの追加**、および**フィルターの使用**です。SendGrid と電子メールの送信の詳細については、「[次のステップ][]」を参照してください。

<h2><a name="whatis"></a><span  class="short-header">SendGrid 電子メール サービスとは</span></h2>

SendGrid は、信頼性の高い[トランザクション電子メール配信]、拡張性、およびリアルタイム分析の機能を備えた[クラウドベース電子メール サービス]であり、柔軟な API を備えているためカスタム統合も容易です。SendGrid の一般的な使用シナリオを次に示します。

-   顧客に受信通知を自動送信する。
-   顧客に広告メールを月 1 回送信するための配布リストを管理する。
-   ブロックされた電子メールや顧客の応答性などを表す測定値をリアルタイムで収集する。
-   傾向を認識するために役立つレポートを生成する。
-   顧客の問い合わせを転送する。
-   受信電子メールを処理する。

詳細については、[https://sendgrid.com](https://sendgrid.com)を参照してください。

<h2><a name="createaccount"></a>SendGrid アカウントの作成</h2>

[AZURE.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

<h2><a name="reference"></a>SendGrid .NET クラス ライブラリを参照する</h2>

[SendGrid NuGet パッケージ](https://www.nuget.org/packages/Sendgrid)は、SendGrid API を取得し、すべての依存関係を備えたアプリケーションを構成する最も簡単な方法です。NuGet は Microsoft Visual Studio 2012 に含まれる Visual Studio 拡張機能であり、これを使用してライブラリおよびツールのインストールと更新を簡単に行うことができます。

> [AZURE.NOTE]Visual Studio 2012 よりも前のバージョンの Visual Studio を利用している場合、NuGet をインストールするには、[http://www.nuget.org](http://www.nuget.org) にアクセスして、**[Install NuGet]** ボタンをクリックしてください。

アプリケーションに SendGrid NuGet パッケージをインストールするには、次のステップを行います。

1.  **[ソリューション エクスプローラー]** で、**[参照]** を右クリックし、**[NuGet パッケージの管理]** をクリックします。

2.  **[NuGet パッケージの管理]** ダイアログ ボックスの左側のウィンドウで、**[オンライン]** をクリックします。

3.  **SendGrid** を検索し、検索結果の一覧から **[SendGrid]** を選択します (現在のバージョンは 5.0.0)。

    ![SendGrid NuGet パッケージ][SendGrid-NuGet-package]

4.  **[インストール]** をクリックしてインストールを実行した後、このダイアログを閉じます。

SendGrid の .NET クラス ライブラリは、**SendGridMail** という名前です。これには次の名前空間が含まれます。

-   **SendGridMail**: 電子メール アイテムの作成と処理の際に使用する名前空間です。
-   **SendGridMail.Transport**: **SMTP** プロトコルまたは **Web/REST** を使用した HTTP 1.1 プロトコルを使用して電子メールを送信する際の名前空間です。

プログラムを使用して SendGrid 電子メール サービスにアクセスするすべての C# ファイルの冒頭部分に、名前空間を宣言する次のコードを追加します。**System.Net** と **System.Net.Mail** は、.NET Framework 名前空間です。この 2 つが宣言されているのは、SendGrid API で一般的に使用される型が含まれているためです。

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

<h2><a name="createemail"></a>方法: 電子メールを作成する</h2>

**SendGridMessage** オブジェクトを使用して電子メール メッセージを作成します。メッセージ オブジェクトを作成すると、電子メール送信者、電子メール受信者、電子メールの件名、本文などのプロパティやメソッドを設定することができます。

次の例に、すべての値が設定された電子メール オブジェクトの作成方法を示します。

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

**SendGrid** 型でサポートされるすべてのプロパティとメソッドの詳細については、GitHub の [sendgrid-csharp に関するページ][]を参照してください。

<h2><a name="sendemail"></a>方法: 電子メールを送信する</h2>

電子メール メッセージを作成した後で、SendGrid の Web API を使用してメッセージを送信することができます。または、[.NET の組み込みライブラリを使用](https://sendgrid.com/docs/Code_Examples/csharp.html)する方法もあります。

電子メールを送信する場合は、SendGrid アカウントの資格情報 (ユーザー名とパスワード) を指定する必要があります。次のコードに、**NetworkCredential** オブジェクトで資格情報をラップする方法を示します。
    
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

<h2><a name="addattachment"></a>方法: 添付ファイルを追加する</h2>

添付ファイルをメッセージに追加するには、**AddAttachment** メソッドを呼び出し、添付するファイルの名前とパスを指定します。複数のファイルを添付するには、添付する各ファイルにつき 1 回このメソッドを呼び出します。次の例に、メッセージに添付ファイルを追加する方法を示します。

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");
    
データの**ストリーム**から添付ファイルを追加することもできます。上記と同じメソッド **AddAttachment** を呼び出し、メッセージに表示させるファイル名をデータのストリームに渡すことでも、同じ操作を実行できます。この場合、System.IO ライブラリを追加する必要があります。

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }


<h2><a name="usefilters"></a><span  class="short-header">方法: アプリを使用してフッター、追跡、および分析を有効にする</span></h2>

SendGrid では、アプリを使用することでその他の電子メール機能も利用することができます。その設定を電子メール メッセージに追加することで、クリック追跡、Google 分析、サブスクリプション追跡などの独自の機能を有効にすることができます。すべてのアプリの一覧については「[アプリケーション設定][]」を参照してください。

アプリは、**SendGrid** クラスの一部として実装されたメソッドを使用する **SendGrid** 電子メール メッセージに適用できます。

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
    myMessage.Html = "<p><a href="http://www.example.com">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";
    
    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

<h2><a name="useservices"></a>方法: その他の SendGrid サービスを使用する</h2>

SendGrid の Web ベース API と webhook を使用して、Azure アプリケーションからその他の SendGrid 機能を利用することができます。詳細については、[SendGrid API に関するドキュメント][]を参照してください。

<h2><a name="nextsteps"></a>次のステップ</h2>

これで、SendGrid 電子メール サービスの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

* SendGrid C# ライブラリ レポート: [sendgrid-csharp][]
*   SendGrid API に関するドキュメント: <https://sendgrid.com/docs>
*   Azure ユーザー向けの SendGrid 特別プラン: [https://sendgrid.com](https://sendgrid.com)

[次のステップ]: #nextsteps
[What is the SendGrid Email Service?]: #whatis
[Create a SendGrid Account]: #createaccount
[Reference the SendGrid .NET Class Library]: #reference
[How to: Create an Email]: #createemail
[How to: Send an Email]: #sendemail
[How to: Add an Attachment]: #addattachment
[How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
[How to: Use Additional SendGrid Services]: #useservices
[special offer]: https://www.sendgrid.com/windowsazure.html
[SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[sendgrid-csharp に関するページ]: https://github.com/sendgrid/sendgrid-csharp
[SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
[アプリケーション設定]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API に関するドキュメント]: https://sendgrid.com/docs
[トランザクション電子メール配信]: https://sendgrid.com/email-solutions
[クラウドベース電子メール サービス]: https://sendgrid.com/transactional-email

<!--HONumber=54-->