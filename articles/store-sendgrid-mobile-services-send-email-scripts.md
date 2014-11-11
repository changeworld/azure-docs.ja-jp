<properties linkid="develop-mobile-tutorials-send-email-with-sendgrid" urlDisplayName="Send Email Using SendGrid" pageTitle="Send email using SendGrid - Azure Mobile Services" metaKeywords="Azure SendGrid, SendGrid service, Azure emailing, mobile services email" description="Learn how to use the SendGrid service to send email from your Azure Mobile Services app." metaCanonical="" services="" documentationCenter="Mobile" title="Send email from Mobile Services with SendGrid" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />

# SendGrid を使用したモバイル サービスからの電子メールの送信

このトピックでは、モバイル サービスに電子メール機能を追加する方法について説明します。このチュートリアルでは、サーバー側スクリプトを追加し、SendGrid を使用して電子メールを送信します。完了すると、モバイル サービスは、レコードが挿入されるたびに電子メールを送信します。

SendGrid は、信頼性の高い[トランザクション電子メール配信][トランザクション電子メール配信]、拡張性、およびリアルタイム分析の機能を備えた[クラウドベース電子メール サービス][クラウドベース電子メール サービス]であり、柔軟な API を備えているためカスタム統合も容易です。詳細については、<http://sendgrid.com> を参照してください。

このチュートリアルでは、電子メール機能を有効にするための、次の基本的な手順について説明します。

1.  [SendGrid アカウントを作成する][SendGrid アカウントを作成する]
2.  [電子メールを送信するためのスクリプトを追加する][電子メールを送信するためのスクリプトを追加する]
3.  [データを挿入して電子メールを受け取る][データを挿入して電子メールを受け取る]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。このチュートリアルを開始する前に、「[モバイル サービスの使用][モバイル サービスの使用]」を完了している必要があります。

## <a name="sign-up"></a><span class="short-header">新しいアカウントの作成</span>新しい SendGrid アカウントを作成する

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="add-script"></a><span class="short-header">スクリプトの登録</span>電子メールを送信する新しいスクリプトを登録する

1.  [Azure の管理ポータル][Azure の管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、目的のモバイル サービスをクリックします。

2.  管理ポータルで、**[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

    ![][0]

3.  **[todoitem]** で、**[スクリプト]** タブをクリックし、**[挿入]** をクリックします。

    ![][1]

    **TodoItem** テーブルで挿入が発生したときに呼び出される関数が表示されます。

4.  挿入関数を次のコードに置き換えます。

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

5.  スクリプトに含まれるプレースホルダーを次に示す正しい値に置き換えます。

    -   <strong>*username* と ***password***: 「[SendGrid アカウントを作成する][SendGrid アカウントを作成する]」で特定した SendGrid の資格情報。

    -   ***email-address***: 電子メールの送信先アドレス。実際のアプリケーションでは、テーブルを使用して、電子メール アドレスを保存および取得することができます。アプリケーションをテストする際には、自分の電子メールを使用してください。

    -   ***from-address***: 電子メールの送信元アドレス。組織に属する登録済みドメイン アドレスの使用を検討してください。

      <div class="dev-callout"><b>注</b><br /> <p>登録済みドメインがない場合は、モバイル サービスのドメインを <strong>notifications@<i>your-mobile-service</i>.azure-mobile.net</strong> という形式で使用できます。ただし、モバイル サービス ドメインに送信されたメッセージは無視されます。</p><br /></div>

    </p>
6.  **[保存]** ボタンをクリックします。これで、**TodoItem** テーブルにレコードが挿入されるたびに電子メールが送信されるように、スクリプトを構成できました。

## <a name="insert-data"></a><span class="short-header">テスト データの挿入</span>テスト データを挿入して電子メールを受け取る

1.  クライアント アプリケーション プロジェクトで、クイック スタート アプリケーションを実行します。

    このトピックでは、クイック スタートの Windows ストア バージョンを示します。

2.  アプリケーションで、**[Insert a TodoItem]** にテキストを入力し、**[Save]** をクリックします。

    ![][2]

3.  次に示す通知のような電子メールが届きます。

    ![][3]

    これで、SendGrid を使用して電子メールを送信できるようにモバイル サービスを構成できました。

## <a name="nextsteps"> </a> 次のステップ

モバイル サービスで SendGrid 電子メール サービスを簡単に使用できることがわかりました。SendGrid の詳細については、次のリンク先を参照してください。

-   SendGrid API に関するドキュメント:
    <http://docs.sendgrid.com/documentation/api/>
-   Azure ユーザー向けの SendGrid 特別プラン:
    <http://sendgrid.com/azure.html>

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [トランザクション電子メール配信]: http://sendgrid.com/solutions
  [クラウドベース電子メール サービス]: http://sendgrid.com/transactional-email
  [SendGrid アカウントを作成する]: #sign-up
  [電子メールを送信するためのスクリプトを追加する]: #add-script
  [データを挿入して電子メールを受け取る]: #insert-data
  [モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started
  [sendgrid-sign-up]: ../includes/sendgrid-sign-up.md
  [Azure の管理ポータル]: https://manage.windowsazure.com/
  [0]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-portal-data-tables.png
  [1]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-insert-script-push2.png
  [2]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-quickstart-push1.png
  [3]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-receive-email.png
