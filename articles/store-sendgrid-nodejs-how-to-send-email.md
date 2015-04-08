<properties 
	pageTitle="SendGrid 電子メール サービスの使用方法 (Node.js) - Azure" 
	description="Azure で SendGrid 電子メール サービスを使用して電子メールを送信する方法について説明します。コード サンプルは Node.js API を使用して記述されています。" 
	services="" 
	documentationCenter="nodejs" 
	authors="thinkingserious" 
	manager="sendgrid" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com"/>





# SendGrid を使用して Node.js から電子メールを送信する方法

このガイドでは、Azure の SendGrid 電子メール サービスを使用して
一般的なプログラム タスクを実行する方法を紹介します。サンプルは Node.js API を使用して記述されています。紹介するシナリオは、**電子メールの作成**、
**電子メールの送信**、**添付ファイルの追加**、**フィルターの使用**、
**プロパティの更新**です。SendGrid と電子メールの送信の詳細については、「[次のステップ][]」を参照してください。

## 目次

* [SendGrid 電子メール サービスとは][]   
* [SendGrid アカウントの作成][]   
* [SendGrid Node.js モジュールの参照][]   
* [方法:電子メールを作成する][]   
* [方法:電子メールを送信する][]   
* [方法:添付ファイルを追加する][]   
* [方法:フィルターを使用してフッター、追跡、および分析を有効にする][]   
* [方法:電子メールのプロパティを更新する][]   
* [方法:その他の SendGrid サービスを使用する][]   
* [次のステップ][1]

## <a name="whatis"> </a>SendGrid 電子メール サービスとは

SendGrid は[クラウドベースの電子メール サービス]であり、信頼性の高い
[トランザクション電子メール配信]、スケーラビリティ、およびリアルタイム分析の機能と柔軟な Api のためカスタム統合も容易です。SendGrid の一般的な使用シナリオを次に示します。

-   顧客に受信通知を自動送信する
-   顧客に広告メールを月 1 回送信するための配布リストを管理する
-   ブロックされた電子メールや顧客の応答性などを表す測定値をリアルタイムで収集する
-   傾向を認識するために役立つレポートを生成する
-   顧客の問い合わせを転送する
-   アプリケーションからの電子メール通知

詳細については、[https://sendgrid.com](https://sendgrid.com) を参照してください。

## <a name="createaccount"> </a>SendGrid アカウントを作成する

[AZURE.INCLUDE [sendgrid アップ記号](../includes/sendgrid-sign-up.md)]

## <a name="reference"></a>SendGrid Node.js モジュールを参照します。

Node.js 用の SendGrid モジュールは、次のコマンドを使用することによって、ノード パッケージ マネージャー (npm) でインストールできます。

    npm install sendgrid

インストールした後、次のコードを使用して、アプリケーションでモジュールを要求できます。

    var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);

SendGrid モジュールは、**SendGrid** 関数と **Email** 関数をエクスポートします。
**SendGrid**は Web API による電子メールの送信中に**電子メール**電子メール メッセージをカプセル化します。

## <a name="createemail"> </a>方法:電子メールを作成する

SendGrid モジュールを使用して電子メール メッセージを作成するには、まず、電子メールの関数を使用してし、SendGrid 関数を使用して送信する電子メール メッセージを作成する必要があります。Email 機能を使用して、新しいメッセージを作成する例を次に示します。

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

Html 形式のメッセージは、html プロパティを設定してサポートのクライアントにも指定できます。次に例を示します。

    html: This is a sample <b>HTML<b> email message.

text プロパティと html プロパティの両方を設定することで、HTML メッセージをサポートできないクライアントに対しては、テキスト コンテンツへの正常なフォールバックを提供できます。

Email 関数でサポートされているすべてのプロパティの詳細については、次を参照してください。 [sendgrid nodejs][]です。

## <a name="sendemail"> </a>方法:電子メールを送信する

Email 機能を使用して電子メール メッセージを作成した後で SendGrid Web API を使用して送信できます。 

### Web API

    sendgrid.send(email, function(err, json){
        if(err) { return console.error(err); }
        console.log(json);
    });

> [AZURE.NOTE] 上記の例については、電子メール オブジェクトとコールバック関数での受け渡しを示しても直接電子メールのプロパティを直接指定することによって、送信関数を呼び出すことができます。次に例を示します。  
>
>`````
sendgrid.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
`````

## <a name="addattachment"> </a>方法:添付ファイルを追加する

添付ファイルは、ファイル名とでパスを指定して、メッセージに追加することができます、**ファイル**プロパティです。次の例は、添付ファイルの送信を示しています。

    sendgrid.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        files: [
            {
                filename:     '',           // required only if file.content is used.
                contentType:  '',           // optional
                cid:          '',           // optional, used to specify cid for inline content
                path:         '',           //
                url:          '',           // == One of these three options is required
                content:      ('' | Buffer) //
            }
        ],
    });

> [AZURE.NOTE] 使用する場合、**ファイル**プロパティ、このファイルはを通じてアクセスできる必要があります[fs.readFile](http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile)です。添付するファイルが BLOB コンテナーなどの Azure Storage でホストされている場合、ファイルをローカル ストレージまたは Azure ドライブにコピーしておくことで、**files** プロパティから添付ファイルとして送信できます。

## <a name="usefilters"> </a>方法:フィルターを使用してフッターと追跡を有効にする

SendGrid では、*フィルター* を使用することでその他の電子メール機能も利用することができます。その設定を電子メール メッセージに追加することで、クリック追跡、Google 分析、サブスクリプション追跡などの独自の機能を有効にすることができます。フィルターの一覧については、次を参照してください。[フィルター設定][]です。

フィルターは、**filters** プロパティを使用してメッセージに適用できます。
各フィルターは、フィルター固有の設定を格納したハッシュで指定します。
次の例に、フッター フィルターとクリック追跡フィルターの使用方法を示します。

### フッター

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });
    
    email.setFilters({
        'footer': {
            'settings': {
                'enable': 1,
                'text/plain': 'This is a text footer.'
            }
        }
    });

    sendgrid.send(email);

### クリック追跡

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });
    
    email.setFilters({
        'clicktrack': {
            'settings': {
                'enable': 1
            }
        }
    });
     sendgrid.send(email) です。

## <a name="updateproperties"> </a>方法:電子メールのプロパティを更新する

一部の電子メールのプロパティは、**set*Property*** を使用して上書きしたり、**add*Property*** を使用して追加したりすることができます。たとえばを使用してその他の受信者を追加することができます。

    email.addTo('jeff@contoso.com');

また、次のように、フィルターを設定することもできます

    email.addFilter('footer', 'enable', 1);
    email.addFilter('footer', 'text/html', '<strong>boo</strong>');

詳細については、次を参照してください。 [sendgrid nodejs][]です。

## <a name="useservices"> </a>方法:その他の SendGrid サービスを使用する

SendGrid の Web ベース API を使用して、Azure アプリケーションから
その他の SendGrid 機能を利用することができます。完全な詳細については、次を参照してください。、 [SendGrid API に関するドキュメント][]です。

## <a name="nextsteps"> </a>次のステップ

これで、SendGrid 電子メール サービスの基本を学習できました。さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

-   SendGrid Node.js モジュールのリポジトリ:[sendgrid nodejs][]
-   SendGrid API に関するドキュメント:<https://sendgrid.com/docs>
-   Azure ユーザー向けの SendGrid 特別プラン:[http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

  [次のステップ]: http://www.windowsazure.com/develop/nodejs/how-to-guides/blob-storage/#next-steps
  [SendGrid 電子メール サービスとは]: #whatis
  [SendGrid アカウントの作成]: #createaccount
  [SendGrid Node.js モジュールの参照]: #reference
  [方法:電子メールを作成する]: #createemail
  [方法:電子メールを送信する]: #sendemail
  [方法:添付ファイルを追加する]: #addattachment
  [方法:フィルターを使用してフッター、追跡、および分析を有効にする]: #usefilters
  [方法:電子メールのプロパティを更新する]: #updateproperties
  [方法:その他の SendGrid サービスを使用する]: #useservices
  [1]: #nextsteps

  
  
  [特別プラン]: https://sendgrid.com/windowsazure.html
  
  
  [sendgrid nodejs]: https://github.com/sendgrid/sendgrid-nodejs
  
  [フィルターの設定]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [SendGrid API に関するドキュメント]: https://sendgrid.com/docs
  [cloud-based email service]: https://sendgrid.com/email-solutions
  [transactional email delivery (トランザクション電子メール配信)]: https://sendgrid.com/transactional-email

<!--HONumber=47-->
