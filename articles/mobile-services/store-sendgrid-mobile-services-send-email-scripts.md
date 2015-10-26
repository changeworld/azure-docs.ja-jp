<properties 
	pageTitle="SendGrid を使用した電子メールの送信 | Microsoft Azure" 
	description="SendGrid サービスを使用して Azure Mobile Services アプリケーションから電子メールを送信する方法について説明します。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="Erikre" 
	manager="sendgrid" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="07/31/2015" 
	ms.author="Erikre"/>


# SendGrid を使用した Mobile Services からの電子メールの送信

このトピックでは、モバイル サービスに電子メール機能を追加する方法について説明します。このチュートリアルでは、サーバー側スクリプトを追加し、SendGrid を使用して電子メールを送信します。完了すると、モバイル サービスは、レコードが挿入されるたびに電子メールを送信します。

SendGrid は、信頼性の高い[トランザクション電子メール配信]、拡張性、およびリアルタイム分析の機能を備えた[クラウドベース電子メール サービス]であり、柔軟な API を備えているためカスタム統合も容易です。詳細については、<http://sendgrid.com> を参照してください。

このチュートリアルでは、電子メール機能を有効にするための、次の基本的な手順について説明します。

1. [SendGrid アカウントを作成する]
2. [電子メールを送信するためのスクリプトを追加する]
3. [データを挿入して電子メールを受け取る]

このチュートリアルは、Mobile Services のクイック スタートに基づいています。このチュートリアルを開始する前に、「[Mobile Services の使用]」を完了している必要があります。

## <a name="sign-up"></a>新しい SendGrid アカウントを作成する

[AZURE.INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## <a name="add-script"></a>電子メールを送信する新しいスクリプトを登録する

1. [Azure 管理ポータル]にログオンし、**[Mobile Services]** をクリックして、目的のモバイル サービスをクリックします。

2. 管理ポータルで、**[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

	![][1]

3. **[todoitem]** で、**[スクリプト]** タブをクリックし、**[挿入]** をクリックします。
   
	![][2]

	**TodoItem** テーブルで挿入が発生したときに呼び出される関数が表示されます。

4. 挿入関数を次のコードに置き換えます。

        var SendGrid = require('sendgrid').SendGrid;
        
        function insert(item, user, request) {    
            request.execute({
                success: function() {
                    // After the record has been inserted, send the response immediately to the client
                    request.respond();
                    // Send the email in the background
                    sendEmail(item);
                }
            });

            function sendEmail(item) {
                var sendgrid = new SendGrid('**username**', '**password**');       
                
                sendgrid.send({
                    to: '**email-address**',
                    from: '**from-address**',
                    subject: 'New to-do item',
                    text: 'A new to-do was added: ' + item.text
                }, function(success, message) {
                    // If the email failed to send, log it as an error so we can investigate
                    if (!success) {
                        console.error(message);
                    }
                });
            }
        }

5. スクリプトに含まれるプレースホルダーを次に示す正しい値に置き換えます。

	- **_username_ および _password_**: 「[SendGrid アカウントを作成する]」で指定した SendGrid の資格情報。

	- **_email-address_**: 電子メールの送信先アドレス。実際のアプリケーションでは、テーブルを使用して、電子メール アドレスを保存および取得することができます。アプリケーションをテストする際には、自分の電子メールを使用してください。

	- **_from-address_**: 電子メールの送信元アドレス。組織に属する登録済みドメイン アドレスの使用を検討してください。

     >[AZURE.NOTE]登録済みドメインがない場合は、Mobile Service のドメインを **notifications@_your-mobile-service_.azure-mobile.net* という形式で使用できます。ただし、モバイル サービス ドメインに送信されたメッセージは無視されます。

6. **[保存]** ボタンをクリックします。これで、**TodoItem** テーブルにレコードが挿入されるたびに電子メールが送信されるように、スクリプトを構成できました。

## <a name="insert-data"></a>テスト データを挿入して電子メールを受け取る

1. クライアント アプリケーション プロジェクトで、クイック スタート アプリケーションを実行します。 

	このトピックでは、クイック スタートの Windows ストア バージョンを示します。

2. アプリケーションで、**[Insert a TodoItem]** にテキストを入力し、**[Save]** をクリックします。

	![][3]

3. 次に示す通知のような電子メールが届きます。

	![][4]

	これで、SendGrid を使用して電子メールを送信できるようにモバイル サービスを構成できました。

## <a name="nextsteps"> </a>次のステップ

Mobile Services で SendGrid 電子メール サービスを簡単に使用できることがわかりました。SendGrid の詳細については、次のリンク先を参照してください。

-   SendGrid API に関するドキュメント: <https://sendgrid.com/docs>
-   Azure ユーザー向けの SendGrid 特別プラン: <https://sendgrid.com/windowsazure.html>

<!-- Anchors. -->
[SendGrid アカウントを作成する]: #sign-up
[電子メールを送信するためのスクリプトを追加する]: #add-script
[データを挿入して電子メールを受け取る]: #insert-data

<!-- Images. -->
[1]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-portal-data-tables.png
[2]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-insert-script-push2.png
[3]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-quickstart-push1.png
[4]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-receive-email.png

<!-- URLs. -->
[Mobile Services の使用]: /develop/mobile/tutorials/get-started
[sign up page]: https://sendgrid.com/windowsazure.html
[Multiple User Credentials page]: https://sendgrid.com/credentials
[Azure 管理ポータル]: https://manage.windowsazure.com/
[トランザクション電子メール配信]: https://sendgrid.com/email-solutions
[クラウドベース電子メール サービス]: https://sendgrid.com/transactional-email

 

<!---HONumber=Oct15_HO3-->