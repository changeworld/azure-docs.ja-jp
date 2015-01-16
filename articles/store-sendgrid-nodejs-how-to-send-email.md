<properties urlDisplayName="SendGrid Email Service" pageTitle="SendGrid 電子メール サービスの使用方法 (Node.js) - Azure" metaKeywords="Azure SendGrid, Azure 電子メール サービス, Azure SendGrid Node.js, Azure 電子メール Node.js" description="Azure で SendGrid 電子メール サービスを使用して電子メールを送信する方法について説明します。コード サンプルは Node.js API を使用して記述されています。" metaCanonical="" services="" documentationCenter="nodejs" title="How to Send Email Using SendGrid from Node.js" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com" solutions="" manager="wpickett" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="10/30/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com" />





# SendGrid を使用して Node.js から電子メールを送信する方法

このガイドでは、Azure の SendGrid 電子メール サービスを使用して
一般的なプログラム タスクを実行する方法を紹介します。サンプルは Node.js API を使用して記述されています。紹介するシナリオは、**電子メールの作成**、**電子メールの送信**、**添付ファイルの追加**、**フィルターの使用**、および**プロパティの更新**です。SendGrid と電子メールの送信の詳細については、「[次のステップ][]」を参照してください。

## 目次

* [SendGrid 電子メール サービスとは][]   
* [SendGrid アカウントの作成][]   
* [SendGrid Node.js モジュールの参照][]   
* [方法: 電子メールを作成する][]   
* [方法: 電子メールを送信する][]   
* [方法: 添付ファイルを追加する][]   
* [方法: フィルターを使用してフッター、追跡、および分析を有効にする][]   
* [方法: 電子メールのプロパティを更新する][]   
* [方法: その他の SendGrid サービスを使用する][]   
* [次のステップ][1]

## <a name="whatis"> </a>SendGrid 電子メール サービスとは

SendGrid は、信頼性の高い[トランザクション電子メール配信]、拡張性、およびリアルタイム分析の機能を備えた[クラウド ベースの電子メール サービス]であり、柔軟な API を備えているためカスタム統合も容易です。SendGrid の一般的な使用シナリオを次に示します。

-   顧客に受信通知を自動送信する
-   顧客に広告メールを月 1 回送信するための
    配布リストを管理する
-   ブロックされた電子メールや顧客の応答性などを表すメトリックをリアルタイムで
    収集する
-   傾向を認識するために役立つレポートを生成する
-   顧客の問い合わせを転送する
-   アプリケーションからの電子メール通知

詳細については、[http://sendgrid.com](https://sendgrid.com) を参照してください。

## <a name="createaccount"> </a>SendGrid アカウントの作成

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference"> </a>SendGrid Node.js モジュールの参照

Node.js 用の SendGrid モジュールは、次のコマンドを使用することによって、ノード パッケージ マネージャー (npm) でインストールできます。

    npm install sendgrid

インストールした後、次のコードを使用して、アプリケーションでモジュールを要求できます。

    var SendGrid = require('sendgrid')

SendGrid モジュールは、**SendGrid** 機能および **Email** 機能をエクスポートします。**SendGrid** は SMTP または Web API による電子メールの送信を処理します。**Email** は電子メール メッセージをカプセル化します。

## <a name="createemail"> </a>方法: 電子メールを作成する

SendGrid モジュールを使って電子メール メッセージを作成するには、
最初に Email 機能で電子メール メッセージを作成し、
次に SendGrid 機能でメッセージを送信します。以下は、Email 機能を使用した新しいメッセージの作成の
例です。

    var mail = new SendGrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

また、html プロパティを設定することによって、HTML メッセージをサポートするクライアント向けに H|TML メッセージを指定することもできます。次に例を示します。

    html: This is a sample <b>HTML<b> email message.

text プロパティと html プロパティの両方を設定することで、HTML メッセージをサポートできないクライアントに対しては、テキスト コンテンツへの正常なフォールバックを提供できます。

Email 機能でサポートされるすべてのプロパティについて詳しくは、[sendgrid-nodejs に関するページ][]を参照してください。

## <a name="sendemail"> </a>方法: 電子メールを送信する

Email 機能で電子メール メッセージを作成した後で、SendGrid の SMTP または Web API を使用してメッセージを送信できます。各 API の長所と API 間の違いの詳細については、[SMTP と Web API を比較した SendGrid ドキュメント][]を参照してください。

SMTP API または Web API を使用するには、最初に、次のように SendGrid アカウントのユーザーとキーを使用して、SendGrid 機能を初期化する必要があります。

    var sender = new SendGrid('user','key');

これで、SMTP または Web API を使用して、メッセージを送信できるようになりました。呼び出しは事実上同じで、電子メール メッセージとオプションのコールバック関数を渡します。このコールバックは、操作の成功または失敗を判断するために使用されます。次の例に、SMTP と Web API でメッセージを送信する方法を示します。

### SMTP

    sender.smtp(mail, function(success, err){
        if(success) console.log('Email sent');
        else console.log(err);
    });

### Web API

    sender.send(mail, function(success, err){
        if(success) console.log('Email sent');
        else console.log(err);
    });

<div class="dev-callout">
<strong>注</strong>
<p>前の例では、電子メール オブジェクトとコールバック関数の受け渡しを示していますが、電子メールのプロパティを直接指定することによって、send 関数と smtp 関数を直接呼び出すこともできます。次に例を示します。</p>
<pre class="prettyprint">sender.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
</pre>
</div>

## <a name="addattachment"> </a>方法: 添付ファイルを追加する

添付ファイルをメッセージに追加するには、**files** プロパティでファイル名とパスを指定します。次の例に、添付ファイルを送信する方法を示します。

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        files: {
            'file1.txt': __dirname + '/file1.txt',
            'image.jpg': __dirname + '/image.jpg'
        }
    });

<div class="dev-callout">
<strong>注</strong>
<p><strong>files</strong> プロパティを使用する場合、
<a href="http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile">fs.readFile</a> を使ってファイルにアクセスできることが必要です。添付するファイルが、BLOB コンテナーなどの Azure ストレージでホストされている場合、<strong>files</strong> プロパティを使用して添付ファイルとして送信するには、最初にファイルをローカル ストレージまたは Azure ドライブにコピーする必要があります。</p>
</div>

## <a name="usefilters"> </a>方法: フィルターを使用してフッター、追跡、および Twitter を有効にする

SendGrid では、フィルターを使用することでその他の電子メール機能も利用することができます。その設定を電子メール メッセージに追加することで、クリック追跡、Google 分析、サブスクリプション追跡などの独自の機能を有効にすることができます。すべてのフィルターの一覧については、[フィルター設定に関するページ][]を参照してください。

フィルターは、**フィルター**のプロパティを使用してメッセージに適用できます。各フィルターは、フィルター固有の設定を格納したハッシュで指定します。次の例に、フッター フィルター、クリック追跡フィルター、および Twitter フィルターの使用方法を示します。

### フッター

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        filters: {
            'footer': {
                'settings': {
                    'enable': 1,
                    'text/plain': 'This is a text footer.'
                }
            }
        }
    });

### クリック追跡

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        filters: {
            'clicktrack': {
                'settings': {
                    'enable': 1
                }
            }
        }
    });

### Twitter

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        filters: {
            'twitter': {
                'settings': {
                    'enable': 1,
                    'username': 'twitter_username',
                    'password': 'twitter_password'
                }
            }
        }
    });

## <a name="updateproperties"> </a>方法: 電子メールのプロパティを更新する

一部の電子メールのプロパティは、**set*Property*** を使用して上書きすることや、**add*Property*** を使用して追加することができます。たとえば、次のようにして、新しい受信者を追加できます。

    email.addTo('jeff@contoso.com');

また、次のように、フィルターを設定することもできます。

    email.setFilterSetting({
      'footer': {
        'setting': {
          'enable': 1,
          'text/plain': 'This is a footer.'
        }
      }
    });

詳細については、[sendgrid-nodejs に関するページ][]を参照してください。

## <a name="useservices"> </a>方法: その他の SendGrid サービスを使用する

SendGrid の Web ベース API を使用して、Azure アプリケーションからその他の SendGrid 機能を利用することができます。詳細については、[SendGrid API に関するドキュメント][]を参照してください。

## <a name="nextsteps"> </a>次のステップ

これで、SendGrid 電子メール サービスの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

-   SendGrid Node.js モジュールのリポジトリ: [sendgrid-nodejs に関するページ][]
-   SendGrid API に関するドキュメント: 
    <https://sendgrid.com/docs>
-   Azure ユーザー向けの SendGrid 特別プラン: 
    [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

  [次のステップ]: http://www.windowsazure.com/ja-jp/develop/nodejs/how-to-guides/blob-storage/#next-steps
  [SendGrid 電子メール サービスとは]: #whatis
  [SendGrid アカウントの作成]: #createaccount
  [SendGrid Node.js モジュールの参照]: #reference
  [方法: 電子メールを作成する]: #createemail
  [方法: 電子メールを送信する]: #sendemail
  [方法: 添付ファイルを追加する]: #addattachment
  [方法: フィルターを使用してフッター、追跡、および分析を有効にする]: #usefilters
  [方法: 電子メールのプロパティを更新する]: #updateproperties
  [方法: その他の SendGrid サービスを使用する]: #useservices
  [1]: #nextsteps

  
  
  [特別プラン]: https://sendgrid.com/windowsazure.html
  
  
  [sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
  [SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
  
  [Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [SendGrid API documentation]: https://sendgrid.com/docs
  [cloud-based email service]: https://sendgrid.com/email-solutions
  [transactional email delivery]: https://sendgrid.com/transactional-email
